# Python学习笔记

- [Python学习笔记](#python学习笔记)
    - [迭代器（iterator）与生成器（generator）](#迭代器iterator与生成器generator)
    - [File open文件操作](#file-open文件操作)
    - [HDF5 数据文件操作](#hdf5-数据文件操作)
    - [glob文件搜索](#glob文件搜索)
    - [assert断言语句](#assert断言语句)




## 迭代器（iterator）与生成器（generator）
- 迭代器：指实现了`__next__()`与`__iter__()`方法的对象
- 生成器：生成器的建立有两种方式：
  - 1、使用生成式表达式建立生成器`(i for i in range(10))`
  - 2、使用`yield`建立生成器

    ```python
        def _generator(num=10):
            for i in range(num):
                yield i
    ```

  生成器也具有`__next__()`与`__iter__()`方法，是迭代器的一种。使用迭代的方式能够将数据作为数据流读入，防止同时占用大量内存。
 
  需要注意`iterable`与`iterator`区别，只包含`__iter__()`方法的对象不是`iterator`，但是是`iterable`对象，可以使用`for,list`等方式读取，因为其`__iter__()`方法可以返回`iterator`对象。

  `__iter__()`方法需要返回`iterator`对象，即含有`__next__()`方法。否则使用迭代方式如`list`,`for`循环读取时将会陷入死循环。因为这些循环方法先会调用调用`__iter__()`函数，再调用`__next__()`方法。对于已经含有`__next__()`方法的`iterator`对象，其`__iter__()`方法返回其自身。
 
  `yield`返回方式与`return`有些不同，`yield`返回时记录返回时代码执行的位置，下次循环调用`__next__()`时从上次停止的地方开始执行。

  ```python
  def odd():
      print('step 1')
      yield 1
      print('step 2')
      yield(3)
      print('step 3')
      yield(5)

  >>> o = odd()
  >>> next(o)
  step 1
  1
  >>> next(o)
  step 2
  3
  >>> next(o)
  step 3
  5
  >>> next(o)
  Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
  StopIteration
  ```

  > 廖雪峰的[python教程](https://www.liaoxuefeng.com/wiki/1016959663602400/1017318207388128)
- 迭代器与生成器的判别方法：

  ```python
  from collections.abc import Iterator, Iterable
  from inspect import isgenerator

  isinstance(obj, Iterator)
  isinstance(obj, Iterable)
  isgenerator(obj)
  ```

## File open文件操作
- `file.write(str)`写入字符串
- `file.writelines(sequence)`写入序列字符串列表
- `file.next()`返回文件下一行
- `file.readline([size])`读取整行
- `file.readlines([sizeint])`读取所有行并返回列表，若给定`sizeint>0`，则是设置一次读多少字节，这是为了减轻读取压力。
- `file.seek(offset[, whence])`方法用于移动文件读取指针到指定位置。
  - offset -- 开始的偏移量，也就是代表需要移动偏移的字节数
  - whence：可选，默认值为 0。给offset参数一个定义，表示要从哪个位置开始偏移；0代表从文件开头开始算起，1代表从当前位置开始算起，2代表从文件末尾算起。
  > [菜鸟编程](https://www.runoob.com/python3/python3-file-methods.html)

## HDF5 数据文件操作
- The most fundamental thing to remember when using h5py is:
  Groups work like dictionaries, and datasets work like NumPy arrays
- `HDF`表示`Hierarchical Data Format`,即结构化数据矩阵。
  > [`HDF5`文档](https://docs.h5py.org/en/stable/quick.html#quick)
- `h5py.File(filename, mode)`用于打开`HDF5`文件
- `File.create_dataset(name, shape, dtype)`用于创建数据集`dataset`,返回`dataset`对象
- `File.create_group(name)`用于创建群组`group`,返回`group`对象

## glob文件搜索
- `glob.glob(pathname, *, recursive=False)`返回匹配`pathname`路径名列表，可以为空
- `glob.iglob(pathname, *, recursive=False)`返回匹配`pathname`路径名迭代器，可以为空


## assert断言语句
- `assert "expression1"[, "expression2"]`在`expression1`不满足时，输出`expression2`。

  ```py
  assert 1==2 , "value error"

  ---------------------------------------------------------------------------
  AssertionError                            Traceback (most recent call last)
  <ipython-input-1-a43ee3a2371b> in <module>
  ----> 1 assert 1==2 , "value error"

  AssertionError: value error
  ```

