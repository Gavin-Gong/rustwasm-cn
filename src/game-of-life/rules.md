# 康威生命游戏规则

_提示：如果你已经熟悉康威生命游戏及其规则，你可以跳到下一章节!_

[Wikipedia 对康威生命游戏规则给出了很好的描述：][wikipedia]

> 生命游戏的宇宙是一个无限二维正交的正方形单元格的网格，每个单元格处于两种可能状态之一，活动或
> 已死，或者是“有人”或“无人”。每个细胞与它水平，垂直，和对角相邻的那 8 个相邻细胞相互作用。在每个步骤中，会发生以下转换:

> 1. 任何活细胞附近少于两个存活状态的细胞会导致该细胞死亡，就好像是由人口稀少所导致的。
>
> 2. 任何活细胞附近存活两个或者三个细胞，那么该细胞下一代就会存活
>
> 3. 任何活细胞附近超过三个活细胞就会死亡，就好像是由人口过剩所导致的。
>
> 4. 任意死细胞附近有三个存活细胞就会变成存活状态，就好像是细胞繁殖。

> 初始模式构成系统的种子。
> 每个单元格同时应用上述规则创建出第一代，
> 这个过程中，种子，出生和死亡的情况同时发生，且为离散时刻
> 这种情况有时被称为滴答(换句话说，每一代都是上一代的纯函数)。
> 这些规则适用于重复地创建更多代。

[wikipedia]: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life

思考以下初始宇宙：

<img src='../images/game-of-life/initial-universe.png' alt='Initial Universe' width=80 />

通过思考每一个细胞计算生成下一代。最左上角的细胞处于死亡状态。
第四条规则是唯一一条适用于死细胞的规则。然而，由于最左上角的细胞没有三个相邻存活的细胞，转化规则无法使用，
这个细胞在下一代的时候任然保持死亡状态。第一行的其他每个细胞也是同样的。

当我们开始思考第二行三列上的活细胞时，事情开始变得有趣起来。
对于活细胞而言，上述前三条规则都是有可能适用的。
就这个细胞而言，它只有一个存活的相邻细胞，所以第一条规则适用：这个细胞将会在下一代死亡。
同样的命运在等待着下面的活细胞。

中间的活细胞有两个相邻的活细胞：上面的活细胞和下面的活细胞。
这意味着第二条规则适用，因此它会在下一代中保持存活状态。

最后一个有意思的场景是中间活细胞左右两边的死细胞的情况。这三个活细胞是周边的死细胞
这意味着第四条规则适合，那么这些细胞将会在下一代中变成存活状态。

把他们放到一起, 然后我们会在下一刻得到这个样子的状况：

<img src='../images/game-of-life/next-universe.png' alt='Next Universe' width=80 />

从这些简单的，确定的规则，产生了奇怪和令人兴奋的  演变行为：

| Gosper's glider gun                                                                                | Pulsar                                                                                 | Space ship                                                                                                   |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| ![Gosper's glider gun](https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif) | ![Pulsar](https://upload.wikimedia.org/wikipedia/commons/0/07/Game_of_life_pulsar.gif) | ![Lighweight space ship](https://upload.wikimedia.org/wikipedia/commons/3/37/Game_of_life_animated_LWSS.gif) |

<center>
<iframe width="560" height="315" src="https://www.youtube.com/embed/C2vgICfQawE?rel=0&amp;start=65" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</center>

## 练习

- 计算上述例子宇宙下一个时刻的情况。注意到什么熟悉的东西了吗?

  <details>
    <summary>答案</summary>

  它应该是例子中宇宙的初始状态:

    <img src='../images/game-of-life/initial-universe.png' alt='Initial Universe' width=80 />

  这个模式是 _周期性的_: 每两个周期之后返回初始状态。

  </details>

- 你能找到一个稳定的初始宇宙吗?也就是说，在一个宇宙中每一代人都是一样的。

    <details>
      <summar>答案</summary>

  存在无数稳定的宇宙！简单的稳定宇宙是空宇宙。一个 2 乘 2 平方的活细胞也是一个稳定宇宙。

    </details>
