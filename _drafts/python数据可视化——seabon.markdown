---
layout: "post"
title: "Python数据可视化——seabon"
date: "2017-03-11 16:21"

tags:
  - 数据可视化
  - Python
  - Data Science

categories:
  - 数据可视化
  - Data Science
  - Python
---


Seaborn是Python的数据可视化工具库。它在matplotlib的基础上作了一层API封装。用Seaborn可以更简单的作一些复杂的数据可视化。
> Seaborn is a Python visualization library based on matplotlib. It provides a high-level interface for drawing attractive statistical graphics.

Seaborn官网提供了示例
[example-gallery][9426acb8]


## [FacetGrid][d76c3588]

以下是官网示例源码


```
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
sns.set(style="ticks")

# Create a dataset with many short random walks
rs = np.random.RandomState(4)
pos = rs.randint(-1, 2, (20, 5)).cumsum(axis=1)
pos -= pos[:, 0, np.newaxis]
step = np.tile(range(5), 20)
walk = np.repeat(range(20), 5)
df = pd.DataFrame(np.c_[pos.flat, step, walk],
                  columns=["position", "step", "walk"])

# Initialize a grid of plots with an Axes for each walk
grid = sns.FacetGrid(df, col="walk", hue="walk", col_wrap=5, size=1.5)

# Draw a horizontal line to show the starting point
grid.map(plt.axhline, y=0, ls=":", c=".5")

# Draw a line plot to show the trajectory of each random walk
grid.map(plt.plot, "step", "position", marker="o", ms=4)

# Adjust the tick positions and labels
grid.set(xticks=np.arange(5), yticks=[-3, 3],
         xlim=(-.5, 4.5), ylim=(-3.5, 3.5))

# Adjust the arrangement of the plots
grid.fig.tight_layout(w_pad=1)

```


`grid = sns.FacetGrid(df, col="walk", hue="walk", col_wrap=5, size=1.5)`

FacetGrid将数据拆分绘制到多个子图中，以显示某些条件下的相关性

> Subplot grid for plotting conditional relationships.

-----

  [9426acb8]: http://seaborn.pydata.org/examples/index.html#example-gallery "example-gallery"
  [d76c3588]: http://seaborn.pydata.org/examples/many_facets.html "FacetGrid"
