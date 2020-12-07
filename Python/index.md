# Python学习笔记

- [Python学习笔记](#python学习笔记)
  - [基本变量类型要点](#基本变量类型要点)
  - [迭代器（iterator）与生成器（generator）](#迭代器iterator与生成器generator)
  - [File open文件操作](#file-open文件操作)
  - [HDF5 数据文件操作](#hdf5-数据文件操作)
  - [glob文件搜索](#glob文件搜索)
  - [assert断言语句](#assert断言语句)
  - [zip函数](#zip函数)
  - [pickle存取python数据类型](#pickle存取python数据类型)
  - [decorator装饰器](#decorator装饰器)


## 基本变量类型要点
- `list、dict`为**可变变量**，传入函数中时，如果函数改动了其内容，在函数外部其内容也发生相应改变。
- `int,string,float,tuple`为**不可变变量**，当改变其值时相当于改变其引用。
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

## zip函数
- `zip(*iterables)`返回一个元组的迭代器，其中的第 i 个元组包含来自每个参数序列或可迭代对象的第 i 个元素。 当所输入可迭代对象中最短的一个被耗尽时，迭代器将停止迭代。 当只有一个可迭代对象参数时，它将返回一个单元组的迭代器。 不带参数时，它将返回一个空迭代器。
  > [Python文档](https://docs.python.org/zh-cn/3/library/functions.html#zip)

  ```py
  def zip(*iterables):
      # zip('ABCD', 'xy') --> Ax By
      sentinel = object()
      iterators = [iter(it) for it in iterables]
      while iterators:
          result = []
          for it in iterators:
              elem = next(it, sentinel)
              if elem is sentinel:
                  return
              result.append(elem)
          yield tuple(result)
  ```

  注意使用`zip(*zipped)`能够将实现同`zip`相反的效果，即将聚合后产生的元组重新拆散为两个列表。

  ```py
  >>> x = [1, 2, 3]
  >>> y = [4, 5, 6]
  >>> zipped = zip(x, y)
  >>> list(zipped)
  [(1, 4), (2, 5), (3, 6)]
  >>> x2, y2 = zip(*zip(x, y))
  >>> x == list(x2) and y == list(y2)
  True
  ```

  此处需注意`*`运算符的作用，将变量`zipped`的元组打散连续输入。即`zip(*zipped)`与`zip(zipped[0], zipped[1],···)`同义，将所有元组的元素按顺序映射，达到与`zip`相反的操作效果。该方法可以用于同时打乱长度相同的不同列表，并且保持列表内映射关系不变。

  ```py
  import random
  x = [1,2,3]
  y = [4,5,6]
  zipped = list(zip(x,y))
  random.shuffle(zipped)
  x2, y2 = zip(*zipped)
  print(x2,y2)

  >>> (1, 3, 2) (4, 6, 5)
  ```

## pickle存取python数据类型
`pickle` 模块能够实现对于 `python` 数据类型如字典、列表等的存取功能
- `dump` 实现存储功能
    ```py
    import pickle
    data = list[1,2,3]
    with open('my_file.pkl', 'wb') as f:
        pickle.dump(data, f)
    ```
- `load` 实现读取功能
    ```py
    import pickle
    # "rb" mean open with binary mode
    with open("my_file.pkl", "rb") as f:
        data = pickle.load(f)
    ```

## decorator装饰器
`装饰器` 放在函数定义前，将修饰的函数视作参数传入装饰器实现特定的功能。

- 普通自定义装饰器

    ```py
    # 这是装饰函数
    def timer(func):
        def wrapper(*args, **kw):
            t1=time.time()
            # 这是函数真正执行的地方
            func(*args, **kw)
            t2=time.time()

            # 计算下时长
            cost_time = t2-t1 
            print("花费时间：{}秒".format(cost_time))
        return wrapper

    import time

    @timer
    def want_sleep(sleep_time):
        time.sleep(sleep_time)

    want_sleep(10)
    >>> 花费时间：10.0073800086975098秒
    ```

- 带参数的函数装饰器
    ```py
    def say_hello(contry):
        def wrapper(func):
            def deco(*args, **kwargs):
                if contry == "china":
                    print("你好!")
                elif contry == "america":
                    print('hello.')
                else:
                    return

                # 真正执行函数的地方
                func(*args, **kwargs)
            return deco
        return wrapper
    
    # 小明，中国人
    @say_hello("china")
    def xiaoming():
        pass

    # jack，美国人
    @say_hello("america")
    def jack():
        pass

    xiaoming()
    print("------------")
    jack()
    
    >>>你好!
    >>>------------
    >>>hello.
    ```

- `python` 内置装饰器 `@property`
  被`@property`修饰的函数视作类的一个属性，属性的值为该函数返回值。实际上该函数为装饰器`property`的一个实例。`property`为一个**描述符类**，基于`python`的**描述符协议**实现函数的装饰。`TestProperty`为模仿其结构的自定义实现。  

  ```py
  class TestProperty(object):
    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        self.__doc__ = doc

    def __get__(self, obj, objtype=None):
        print("in __get__")
        if obj is None:
            return self
        if self.fget is None:
            raise AttributeError
        return self.fget(obj)

    def __set__(self, obj, value):
        print("in __set__")
        if self.fset is None:
            raise AttributeError
        self.fset(obj, value)

    def __delete__(self, obj):
        print("in __delete__")
        if self.fdel is None:
            raise AttributeError
        self.fdel(obj)


    def getter(self, fget):
        print("in getter")
        return type(self)(fget, self.fset, self.fdel, self.__doc__)

    def setter(self, fset):
        print("in setter")
        return type(self)(self.fget, fset, self.fdel, self.__doc__)

    def deleter(self, fdel):
        print("in deleter")
        return type(self)(self.fget, self.fset, fdel, self.__doc__)

  class Student:
    def __init__(self, name):
        self.name = name

    # 其实只有这里改变
    @TestProperty
    def math(self):
        return self._math

    @math.setter
    def math(self, value):
        if 0 <= value <= 100:
            self._math = value
        else:
            raise ValueError("Valid value must be in [0, 100]")
  ```
  > [王炳明的知乎专栏](https://zhuanlan.zhihu.com/p/269012332)
  
  `Student`类中的`math()`定义了两次，第一次将`math()`变为`TestProperty`的一个实例，初始化实现了`self.fget()`方法。第二次使用`@math.setter`修饰重定义`math()`变为`TestProperty`的一个新实例调用`setter()`实现了`self.fget()`与`self.fset()`方法
