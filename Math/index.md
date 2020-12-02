# 数学相关笔记

- [数学相关笔记](#数学相关笔记)
  - [t-SNE可视化](#t-sne可视化)

## t-SNE可视化
步骤如下：
- 1、将高维空间中按**高斯分布**表达式计算样本条件概率视作样本相似性也称距离
- 2、在映射后的低维空间使用**t分布**计算样本之间距离
- 3、损失函数设置为两空间中联合分布的**KL散度**

示例
```py
from sklearn.manifold import TSNE
from sklearn.datasets import load_iris, load_digits
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt 
import numpy as np

digits = load_digits()
X_tsne = TSNE(n_components=2, random_state=33).fit_transform(digits.data)
X_pca = PCA(n_components=2).fit_transform(digits.data)

font = {"color":"darkred", "size": 13, "family" : "serif"}

plt.style.use("dark_background")
plt.figure(figsize=(8.5, 4))
plt.subplot(1,2,1)
plt.scatter(X_tsne[:, 0], X_tsne[:, 1], c=digits.target, alpha=0.6,
            cmap=plt.cm.get_cmap("rainbow", 10))
plt.title("t-SNE", fontdict=font)
cbar = plt.colorbar(ticks=range(10))
cbar.set_label(label="digit value", fontdict=font)
plt.clim(-0.5, 9.5)
plt.subplot(1,2,2)
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=digits.target, alpha=0.6,
            cmap=plt.cm.get_cmap("rainbow", 10))
plt.title("PCA", fontdict=font)
cbar = plt.colorbar(ticks=range(10))
cbar.set_label(label="digit value", fontdict=font)
plt.clim(-0.5, 9.5)
plt.tight_layout()
plt.show()
```
> 详见[高维数据可视化之t-SNE算法](https://zhuanlan.zhihu.com/p/57937096)  