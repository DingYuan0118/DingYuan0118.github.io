# JAVA、python、JavaScript、C++等语言区别

- [JAVA、python、JavaScript、C++等语言区别](#javapythonjavascriptc等语言区别)
  - [for循环](#for循环)


## for循环
|语言|循环方式|效果|
|:-|:-|:-|
|JAVA|1、`for (int i;i < n; i++){...}`|同正常C++循环使用一致|
||2、`for (int n : ns){...}`|从数组`ns`中抽取每个元素放入`n`|
|python|`for i in iterable:`|从可迭代对象中抽取每个元素赋予`i`,与`JAVA`第二种`for`循环方式类似|
|JavaScript|1、`for (int i;i < n; i++){...} `|同正常C++循环使用一致|
||2、`for (var key in o){...}`|将`o`对象的属性名赋予变量`key`|
||3、`for (var x of iterable){...}`|与`python`循环类似，将可迭代对象元素赋予变量`x`|