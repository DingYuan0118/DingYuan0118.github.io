# Pytorch学习笔记

- [Pytorch学习笔记](#pytorch学习笔记)
    - [dataloader与dataset](#dataloader与dataset)
    - [CUDA的使用](#cuda的使用)

## dataloader与dataset
- dataloader与dataset之间的调用关系如图
![dataloader and dataset](../images/dataloader.jpg)
  > 图参考[https://zhuanlan.zhihu.com/p/76893455](https://zhuanlan.zhihu.com/p/76893455)

  其中`Sample`是`iterable`对象，其`__iter__()`方法返回一个`iterator`对象，`torch.utils.data.Dataloader`通过调用`next(iter(Sample))`得到`indices`。再通过调用`dataset[indices]`得到数据流。

  源码如下

  ```python
  class DataLoader(object):
      ...
      
      def __next__(self):
          if self.num_workers == 0:  
              indices = next(self.sample_iter)  # Sampler
              batch = self.collate_fn([self.dataset[i] for i in indices]) # Dataset
              if self.pin_memory:
                  batch = _utils.pin_memory.pin_memory_batch(batch)
              return batch
  ```
  
  > pytorch 1.0 document，高版本的dataloader将`__next__()`方法去除了，实现更为复杂，但流程相同。

## CUDA的使用
- `pytorch`中无论模型的参数还是数据均以`tensor`的形式存储。`tensor`的运算均需要运算数在同一设备中，否则将会报错。

  ```python
  import torch
  a = torch.tensor([1,2,3,4], device=torch.device('cuda'))
  b = torch.tensor([2,3,4,5], device=torch.device("cpu"))
  c = a + b

  ---------------------------------------------------------------------------
  RuntimeError                              Traceback (most recent call last)
  <ipython-input-1-3317b81b183d> in <module>
        2 a = torch.tensor([1,2,3,4], device=torch.device('cuda'))
        3 b = torch.tensor([2,3,4,5], device=torch.device("cpu"))
  ----> 4 c = a + b

  RuntimeError: expected device cuda:0 but got device cpu
  ---------------------------------------------------------------------------

  c = a + b.cuda()
  c

  >>>tensor([3, 5, 7, 9], device='cuda:0')
  ```