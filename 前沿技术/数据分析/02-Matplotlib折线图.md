

但是目前存在以下几个问题：
1、设置图片大小（想要一个高清无码大图）
2、保存到本地
3.描述信息，比如x轴和y轴表示什么，这个图表示什么
4.调整x或者y的刻度的间距
5.线条的样式（比如颜色，透明度等）
6.标记出特殊的点（比如告诉别人最高点和最低点在哪里）
7.给图片添加一个水印（防伪，防止盗用

```python
from matplotlib import pyplot as plt

fig = plt.figure(figsize=(20, 8), dpi=80)

x = range(2, 26, 2)
y = range(12)
plt.plot(x, y)
plt.show()
```

