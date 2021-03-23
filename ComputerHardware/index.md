# PC 硬件常识

## PCIE
- *PCIE(peripheral component interconnect express)* 是一种高速串行计算机扩展总线标准。其在主板上主要以**通道和接口**形式存在（例如**显卡**使用PCIE x16接口，*NVME固态硬盘*使用PCIE x4）。PCIE各版本速度与通道数对应如下:
  |PCIE版本|x1|x4|x8|x16|
  |-|-|-|-|-|
  |PCIE 1.0|256MB/s |1GB/s |2GB/s |4GB/s |
  |PCIE 2.0|512MB/s |2GB/s |4GB/s |8GB/s |
  |PCIE 3.0|1GB/s |4GB/s |8GB/s |16GB/s |
  |PCIE 4.0|2GB/s |8GB/s |16GB/s |32GB/s |
  
  **注意**：此处速率使用符号为**GB/s**而非**Gb/s**。一般来说`8Gb/s = 1GB/s`，但对于**SATA3接口**来说，实际使用中需要抵消部分电气缺陷，一般采用**8b / 10b Encoding**的方式，因此对于SATA3接口的**6Gb/s**的带宽，实际传输速度为**6*8/10 = 4.8Gb/s**，即**600MB/s**。
  > [CSDN](https://blog.csdn.net/culul01313/article/details/108840296)

## 内存
- 民用PC一般是双通道，四内存插槽（DIMM），一般使用远离CPU的2，4插槽组成双通道达到最好效果。