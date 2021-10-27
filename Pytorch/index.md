# Pytorch学习笔记

- [Pytorch学习笔记](#pytorch学习笔记)
  - [dataloader与dataset](#dataloader与dataset)
  - [CUDA的使用](#cuda的使用)
  - [Batchnormal层的使用](#batchnormal层的使用)
  - [ResNet解析](#resnet解析)
  - [Performance Tuning Guide](#performance-tuning-guide)
  - [Parameters与Buffer的区别](#parameters与buffer的区别)
  - [常用画图函数](#常用画图函数)

## dataloader与dataset
- dataloader与dataset之间的调用关系如图
![dataloader and dataset](../images/dataloader.jpg)
  > 图参考 https://zhuanlan.zhihu.com/p/76893455

  其中`Sample`是`iterable`对象，其`__iter__()`方法返回一个`iterator`对象，`torch.utils.data.Dataloader`通过调用`next(iter(Sample))`得到`indices`。再通过调用`dataset[indices]`得到数据流。

  源码如下

  ```python
  class DataLoader(object):
      ...
      def __iter__(self):
          return _DataLoaderIter(self)

  class __DataLoaderIter(object):
      ···
      def __next__(self):
          if self.num_workers == 0:  
              indices = next(self.sample_iter)  # Sampler
              batch = self.collate_fn([self.dataset[i] for i in indices]) # Dataset
              if self.pin_memory:
                  batch = _utils.pin_memory.pin_memory_batch(batch)
              return batch
  ```
  
  > [pytorch 1.0 document](https://pytorch.org/docs/1.0.0/_modules/torch/utils/data/dataloader.html#DataLoader)，高版本实现更为复杂，但流程相同。

- `daloader`的`__len__`属性等于该`dataloader`内部`Batch_Sampler`的长度。因此，如果自定义`Batch_sampler`则需要直接指明其`__len__`属性以便告知一个`epoch`内需要迭代多少次。如果使用默认的`Batch_sampler`则其`__len__`属性值为`sampler`的`__len__`属性值除以`batch_size`，而`sampler`的`__len__`属性值等于`dataset`的`__len__`属性，因此默认`Batch_Sampler`的`__len__`属性值为`dataset.__len__`除以`batch_size`。

## CUDA的使用
- `pytorch`中无论模型的参数还是数据均以`tensor`的形式存储。`tensor`的运算均需要运算数在同一设备中，否则将会报错。

  ```python
  import torch
  a = torch.tensor([1,2,3,4], device=torch.device('cuda'))
  b = torch.tensor([2,3,4,5], device=torch.device("cpu"))
  c = a + b

  RuntimeError                              Traceback (most recent call last)
  <ipython-input-1-3317b81b183d> in <module>
        2 a = torch.tensor([1,2,3,4], device=torch.device('cuda'))
        3 b = torch.tensor([2,3,4,5], device=torch.device("cpu"))
  ----> 4 c = a + b

  RuntimeError: expected device cuda:0 but got device cpu

  c = a + b.cuda()
  c

  >>>tensor([3, 5, 7, 9], device='cuda:0')
  ```

## Batchnormal层的使用
具体原理参考[Juliuszh的高赞文章](https://zhuanlan.zhihu.com/p/33173246)
- `torch.nn.BatchNorm2d(num_features, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)`  
针对每个channel，给一组映射参数$\alpha$, $\beta$, 长度为`C`
    - **num_features** - C from an expected input of size `(N,C,H,W)`
    - **affine** - a boolean value that when set to True, this module has learnable affine parameters. Default: `True`
    - **track_running_stats** - a boolean value that when set to `True`, this module tracks the running mean and variance, and when set to `False`, this module does not track such statistics and uses batch statistics instead in both training and eval modes if the running mean and variance are None. Default: `True`  
  
  ```py
  import torch
  import torch.nn as nn
  w = nn.BatchNorm2d(100)
  m = nn.BatchNorm2d(100, affine=False)
  n = nn.BatchNorm2d(100, affine=False, track_running_stats=False)
  input = torch.randn(20, 100, 35, 45)
  output_w = w(input)
  output_m = m(input)

  print(w.state_dict().keys())
  print(m.state_dict().keys())
  print(n.state_dict().keys())
  print(output_m.size())
  print(output_w.size())


  >>> odict_keys(['weight', 'bias', 'running_mean', 'running_var',  'num_batches_tracked'])
  >>> odict_keys(['running_mean', 'running_var',  'num_batches_tracked'])
  >>> odict_keys([])
  >>> torch.Size([20, 100, 35, 45])
  >>> torch.Size([20, 100, 35, 45])
  ```

## ResNet解析
- resnet架构如"[Deep Residual Learning for Image Recognitin](https://arxiv.org/abs/1512.03385)"所述，由残差块`residual block`堆叠而成，其具体结构如图所示：
  <div  align="center"> 
  <img src="../images/ResidualBlock.JPG" width="80%" height="80%">
  </div >
  

  其网络整体结构如下：
  <div  align="center"> 
  <img src="../images/ResidualNetwork.JPG" width="100%" height="100%">
  </div >

  具体来说，实验使用较多的为`resnet18`即18层的`resnet`。论文中所有`resnet`均由4个"大"`layer`组成，每个`layer`按结构不同又可以由不同数量的`block`构成。
  `block`又由卷积层与`batchnorm`层组成。`block`有两种基本结构：`BasicBlock`与`Bottleneck`。
  - `BasicBlock`含有2层卷积层
  - `Bottleneck`含有3层卷积层
  
  每个卷积层后均使用`batchnorm`进行归一化，具体实现可参考[pytorch源码](https://pytorch.org/docs/stable/_modules/torchvision/models/resnet.html#resnet18)


## Performance Tuning Guide 
> 参考[pytorch tutorial](https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html)
- **Enable async data loading and augmentation**: set the `num_workers` > 0, if use GPU traninig, better set the `pin_memory=True`

- **Disable gradient calculation for validation or inference**: use torch.no_grad() context manager

- **Disable bias for convolutions directly followed by a batch norm**: if a conv layer followed by a batch norm layer, use `nn.Conv2d(..., bias=False, ....)` to disable the bias compute.

- **Avoid unnecessary CPU-GPU synchronization**: When possible, avoid operations which require synchronizations, for example:
  - print(cuda_tensor)
  - cuda_tensor.item()
  - memory copies: tensor.cuda(), cuda_tensor.cpu() and equivalent tensor.to(device) calls
  - cuda_tensor.nonzero()
  - python control flow which depends on results of operations performed on cuda tensors e.g. if (cuda_tensor != 0).all()

- **Create tensors directly on the target device**:use `torch.rand(size, device=torch.device('cuda'))` instead of `torch.rand(size).cuda()`

## Parameters与Buffer的区别
- `parameters`记录反向传播时需要`optimizer`更新的参数
- `buffer`记录反向传播时不需要`optimizer`更新的参数

二者均记录至`model.state_dict()`方法中，`state_dict()`返回一个`OrderedDict`，其中存储着模型的所有参数.

- `register_parameter()`与`register_buffer()`用于将自定义的参数手动添加至`OrderedDict`中

## 常用画图函数

```py
import matplotlib.pyplot as plt
def plot(imgs, with_orig=True, row_title=None, **imshow_kwargs):
    if not isinstance(imgs[0], list):
        # Make a 2d grid even if there's just 1 row
        imgs = [imgs]

    num_rows = len(imgs)
    num_cols = len(imgs[0]) + with_orig
    fig, axs = plt.subplots(nrows=num_rows, ncols=num_cols, squeeze=False)
    for row_idx, row in enumerate(imgs):
        row = [orig_img] + row if with_orig else row
        for col_idx, img in enumerate(row):
            ax = axs[row_idx, col_idx]
            ax.imshow(np.asarray(img), **imshow_kwargs)
            ax.set(xticklabels=[], yticklabels=[], xticks=[], yticks=[])

    if with_orig:
        axs[0, 0].set(title='Original image')
        axs[0, 0].title.set_size(8)
    if row_title is not None:
        for row_idx in range(num_rows):
            axs[row_idx, 0].set(ylabel=row_title[row_idx])

    plt.tight_layout()
```