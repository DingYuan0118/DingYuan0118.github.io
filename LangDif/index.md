# JAVA、python、JavaScript、C++等语言区别

- [JAVA、python、JavaScript、C++等语言区别](#javapythonjavascriptc等语言区别)
  - [for循环](#for循环)
  - [变量可变属性与变量传递](#变量可变属性与变量传递)
  - [关于字符串](#关于字符串)


## for循环
|语言|循环方式|效果|
|:-|:-|:-|
|JAVA|1、`for (int i;i < n; i++){...}`|同正常C++循环使用一致|
||2、`for (int n : ns){...}`|从数组`ns`中抽取每个元素放入`n`|
|python|`for i in iterable:`|从可迭代对象中抽取每个元素赋予`i`,与`JAVA`第二种`for`循环方式类似|
|JavaScript|1、`for (int i;i < n; i++){...} `|同正常C++循环使用一致|
||2、`for (var key in o){...}`|将`o`对象的属性名赋予变量`key`|
||3、`for (var x of iterable){...}`|与`python`循环类似，将可迭代对象元素赋予变量`x`|

## 变量可变属性与变量传递
- 可变与不可变：即变量名指定的变量是否可以修改。可变变量修改时在内存上直接修改，**不**改动变量名的引用地址。不可变变量修改赋值会导致**变量名的引用地址**发生变化。
- 值传参与引用传参：主要区别在于函数内部的操作是否会影响函数外部变量的值

|语言|变量属性|变量传递|
|:-|:-|:-|
|C++|变量**不**区分**可变与不可变**，变量改动均在内存上体现，即均为**可变变量**|传递方式分为**值传递与引用传递**|
|python|变量区分**可变变量与不可变变量**，改变变量值时有所区别|传递方式均为引用传递，可变变量传递类似于C++的引用传递，不可变变量类似于C++的值传递|
|Java|**暂且**所知变量均为不可变变量|TODO|

## 关于字符串

|语言|变量传递|
|:-|:-|
|JavaScript|字符串不区分单引号与双引号|
|python|字符串不区分单引号与双引号|
|Java|字符类型 `Char` 使用单引号，字符串类型 `String ` 使用双引号，需要区分，容易出现类型错误|
|C++|字符类型 `Char` 使用单引号，字符串类型 `String ` 使用双引号，需要区分，容易出现类型错误|