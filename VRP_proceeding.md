**Usable**： 已经可以使用

**Exploring**： 还在探索中

2023.12.22: 增加Attention Mechanism方法。



# VRP
## 问题定义
### Vehicle Routing Problem

$n$: 节点数；

$m$: amount of vehicles

$c_{i,j}$: 节点i到节点j的cost

$x^k_{i,j}$: 0或1.若车辆k从节点i移动到节点j，值为1，否则为0

#### 目标：

$Min \sum \limits_{k=1}^{m} \sum \limits_{i=0}^{n} \sum \limits_{j=0}^{n}{c_{i,j} \cdot x_{i,j}^k }$

#### Constraints
$
\sum \limits_{k=1}^{m} \sum \limits_{j=0}^{n}{ x_{i,j}^k }=1
, \forall i=1,...n
$

$
\sum \limits_{j=1}^{n}{ x_{0,j}^k }=1
, \forall i=1,...n
$

$
\sum \limits_{i=1}^{n}{ x_{i,0}^k }=1
, \forall i=1,...n
$


## 研究现状
## 求解算法
### 启发式方法（heuristic methods） **[Exploring...]**
[LKH-3](http://webhotel4.ruc.dk/~keld/research/LKH-3/)：一个TSP/VRP问题求解器，使用LKH算法，可使用已有python [API](https://github.com/ben-hudson/pylkh)，或者自己编写parameter文件 **[Usable]**

[Hybrid Genetic Search(HGS for CVRP)](https://arxiv.org/abs/2012.10384)：HGS有针对不同VRP问题的变体，例如：VRP-MTW的变体[HGVNS](https://ieeexplore.ieee.org/document/7969457/)。HGS for CVRP现有[代码仓库](https://github.com/vidalt/HGS-CVRP)需要自己编译，也有[python](https://github.com/chkwon/PyHygese), C, Julia API供调用，**[Exploring...]**




### 机器学习方法 **[Exploring...]**
[Attention Mechanism](https://arxiv.org/abs/1803.08475)  **[Exploring...]**

[NeuOpt（A Novel L2D Solver）](https://arxiv.org/abs/2310.18264)  **[Exploring...]**

[Omni-VRP](https://github.com/RoyalSkye/Omni-VRP/tree/main) ：给出了Pretrained model，包括实验中涉及到的其他模型 **[Exploring...]**

[POMO](https://arxiv.org/abs/2010.16011)： **[Exploring...]**

AMDKD-POMO **[Exploring...]**

[L2D(Learning to Delegate)](https://arxiv.org/abs/2107.04139) （**这篇的Related work很有用**）：大致看了一下，是通过机器学习方法优化搜索过程（把指数问题分为若干子问题，通过改善子问题求解来加速搜索过程）？ [代码仓库](https://github.com/mit-wu-lab/learning-to-delegate)未给出Pretrained model，要自己训练 **[Exploring...]**


## Evaluation metrics
1. Solution quality
2. Convergence speed


## 数据集
[CVRPLIB](http://vrp.atd-lab.inf.puc-rio.br/index.php/en/)，VRP数据集，有标明是否现有最优解（Optimum Solution），包含CVRP以及VRP-TW，有自动实例生成工具XML-100。

[DVRP-Set-Kilby](https://data.mendeley.com/datasets/3fwc3twwn6/1)
