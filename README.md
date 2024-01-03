# VRP
**Usable**： 已经可以使用

**Exploring**： 还在探索中

2023.12.22: 增加Attention Mechanism方法。

2023.12.25: HGS在自习室台式机上跑通；更新XML-100使用方法以及实例说明，更新HGS中有关python API/自行生成可执行文件优劣的Comment，更新HGS编译方式以及parameters调教, output说明。

2023.12.26-2023.12.27: POMO环境配置，POMO方法初步跑通inference示例，未进行训练，代码还得看看，没太看明白测试用的什么数据。

2023.12.28-2023.12.29: AM环境配置，跑通AM方法示例，添加HGS、POMO、AM说明。POMO、AM方法仍然存在一些疑问（POMO是否可以采用别的Policy Network，AM测试Dataset如何载入）。

2024.1.3: L2D环境配置， ~~ 代码与现版本numpy有一些冲突，已解决。~~ 正在尝试跑完 example(这个example非常详细，也非常耗时)。


## 问题定义
### Vehicle Routing Problem

$n$: 节点数；

$m$: amount of vehicles

$c_{i,j}$: 节点i到节点j的cost

$x^k_{i,j}$: 0或1.若车辆k从节点i移动到节点j，值为1，否则为0

#### 目标：

$Min \sum \limits_{k=1}^{m} \sum \limits_{i=0}^{n} \sum \limits_{j=0}^{n}{c_{i,j} \cdot x_{i,j}^k }$

#### Constraints
$\sum \limits_{k=1}^{m} \sum \limits_{j=0}^{n}{ x_{i,j}^k }=1, \forall i=1,...n$

$\sum \limits_{j=1}^{n}{ x_{0,j}^k }=1, \forall i=1,...n$

$\sum \limits_{i=1}^{n}{ x_{i,0}^k }=1, \forall i=1,...n$


## 研究现状
## 求解算法
### 启发式方法（heuristic methods） **[Exploring...]**
#### [LKH-3](http://webhotel4.ruc.dk/~keld/research/LKH-3/) **[Usable]**
一个TSP/VRP问题求解器，使用LKH算法，可使用已有python [API](https://github.com/ben-hudson/pylkh)，或者自己编写parameter文件 

#### [Hybrid Genetic Search(HGS for CVRP)](https://arxiv.org/abs/2012.10384)**[Usable]**
HGS有针对不同VRP问题的变体，例如：VRP-MTW的变体[HGVNS](https://ieeexplore.ieee.org/document/7969457/)。HGS for CVRP现有[代码仓库](https://github.com/vidalt/HGS-CVRP)需要自己编译，也有[python](https://github.com/chkwon/PyHygese), C, Julia API供调用。

python API使用起来比较麻烦，需要对dataset进行预处理，自己编译则可以直接使用CVRP Dataset。（Dependency: cmake; 如果使用python API: gcc, make, cmake）

注意代码仓库中的HGS参数针对n<=1000的情况，如果要跑更大规模的实例可以联系论文作者。

build process:
```
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -G "Unix Makefiles"
make bin
```

Example: Running an instance
```
./hgs ../Instances/CVRP/X-n157-k13.vrp mySolution.sol -seed 1 -t 30
```

Running Params:
```
Call with: ./hgs instancePath solPath [-it nbIter] [-t myCPUtime] [-bks bksPath] [-seed mySeed] [-veh nbVehicles] [-log verbose]
[-it <int>] sets a maximum number of iterations without improvement. Defaults to 20,000                                     
[-t <double>] sets a time limit in seconds. If this parameter is set, the code will be run iteratively until the time limit           
[-seed <int>] sets a fixed seed. Defaults to 0                                                                                    
[-veh <int>] sets a prescribed fleet size. Otherwise a reasonable UB on the fleet size is calculated                      
[-round <bool>] rounding the distance to the nearest integer or not. It can be 0 (not rounding) or 1 (rounding). Defaults to 1. 
[-log <bool>] sets the verbose level of the algorithm log. It can be 0 or 1. Defaults to 1.                                       

Additional Arguments:
[-nbIterTraces <int>] Number of iterations between traces display during HGS execution. Defaults to 500
[-nbGranular <int>] Granular search parameter, limits the number of moves in the RI local search. Defaults to 20               
[-mu <int>] Minimum population size. Defaults to 25                                                                            
[-lambda <int>] Number of solutions created before reaching the maximum population size (i.e., generation size). Defaults to 40
[-nbElite <int>] Number of elite individuals. Defaults to 5                                                                    
[-nbClose <int>] Number of closest solutions/individuals considered when calculating diversity contribution. Defaults to 4     
[-nbIterPenaltyManagement <int>] Number of iterations between penalty updates. Defaults to 100
[-targetFeasible <double>] target ratio of feasible individuals between penalty updates. Defaults to 0.2
[-penaltyIncrease <double>] penalty increase if insufficient feasible individuals between penalty updates. Defaults to 1.2
[-penaltyDecrease <double>] penalty decrease if sufficient feasible individuals between penalty updates. Defaults to 0.85
```
**[Critical]**: There exist different conventions regarding distance calculations in the academic literature. **The default code behavior is to apply integer rounding**, as it should be done on the X instances of Uchoa et al. (2017). To change this behavior (e.g., when testing on the CMT or Golden instances), give a flag ```-round 0```, when you run the executable.

Standard Output: ```It [N1] [N2] | T(s) [T] | Feas [NF] [BestF] [AvgF] | Inf [NI] [BestI] [AvgI] | Div [DivF] [DivI] | Feas [FeasC] [FeasD] | Pen [PenC] [PenD]```

```
[N1] and [N2]: Total number of iterations and iterations without improvement
[T]: CPU time spent until now
[NF] and [NI]: Number of feasible and infeasible solutions in the subpopulations 
[BestF] and [BestI]: Value of the best feasible and infeasible solution in the subpopulations 
[AvgF] and [AvgI]: Average value of the solutions in the feasible and infeasible subpopulations 
[DivF] and [DivI]: Diversity of the feasible and infeasible subpopulations
[FC] and [FD]: Percentage of naturally feasible solutions in relation to the capacity and duration constraints
[PC] and [PD]: Current penalty level per unit of excess capacity and duration
```

### 机器学习方法 **[Exploring...]**
#### [Attention Mechanism](https://arxiv.org/abs/1803.08475)  **[Exploring...]**
[代码仓库](https://github.com/wouterkool/attention-learn-to-route/tree/master)里有作者实现的简单可视化，示例已经跑通。

Dependency: 
1. Python>=3.8, 
2. NumPy
3. SciPy
4. PyTorch>=1.7
5. tqdm
5. tensorboard_logger
6. Matplotlib (optional, only for plotting)

dataset随机生成，有一个data generator。

#### [NeuOpt（A Novel L2D Solver）](https://arxiv.org/abs/2310.18264)  **[Exploring...]**

#### [Omni-VRP](https://github.com/RoyalSkye/Omni-VRP/tree/main)  **[Exploring...]**
给出了Pretrained model，包括实验中涉及到的其他模型

#### [POMO(Policy Optimization with Multiple Optima)](https://arxiv.org/abs/2010.16011) **[Exploring...]**
[POMO代码仓库](https://github.com/yd-kwon/POMO/tree/master)

POMO给出了Pretrained Model，也可以自己训练。POMO中实例参照Attention Mechanism。在论文的实验部分是POMO框架结合AM来训练模型的，是否可以使用别的策略（论文claim POMO并不绑定特定的Policy Network）？

Dependency: torch

![Run inference](https://github.com/L1ttlepsycho/Ningjun_Xu/blob/main/POMO_test.jpg)

初步跑通老版本（ipynb）的inference，代码仓库中使用的是随机生成CVRP实例进行测试（貌似用的AM的generator）；如果要使用CVRP Dataset可能需要使用新版本（py）并且搞清楚模型输入，自行编写测试API。


#### AMDKD-POMO **[Exploring...]**

#### [L2D(Learning to Delegate)](https://arxiv.org/abs/2107.04139) **[Exploring...]**（**这篇的Related work很有用**）
大致看了一下，是通过机器学习方法优化搜索过程（把指数问题分为若干子问题，通过改善子问题求解来加速搜索过程）？ [代码仓库](https://github.com/mit-wu-lab/learning-to-delegate)未给出Pretrained model，要自己训练。


## Evaluation metrics
1. Solution quality：gap
2. solving speed
3. Convergence speed


## 数据集
[CVRPLIB](http://vrp.atd-lab.inf.puc-rio.br/index.php/en/)，VRP数据集，有标明是否现有最优解（Optimum Solution），包含CVRP以及VRP-TW，有自动实例生成工具XML-100。

关于自行生成的CVRP实例可以尝试通过VRPsolver寻找最优解？

XML-100使用说明：
***
The instance name format is XML100_ABCD_EF, such that ABCD identifies the group as follows:
   A: Depot positioning (1. random, 2. centered, 3. cornered)
   B: Customer positioning (1. random, 2. clustered, 3. random-clustered)
   C: Demand distribution (1. unitary, 2. small values with large CV, 3. small values with small CV, 4. large values with large CV, 5. large values with small CV, 6. depending on quadrant, 7. many small values and few large values)
   D: Average route size (1. very short, 2. short, 3. medium, 4. long, 5. very long, 6. ultra long)
    
EF identifies the instance inside the group ABCD. For example, instance XML100_2216_01 is the first instance of the group with centered depot, clustered customers, unitary demands, and ultra long routes. There are 378 groups generated by the Cartesian product of {1,2,3}x{1,2,3}x{1,2,3,4,5,6,7}x{1,2,3,4,5,6} (possible values to ABCD), where the first 172 groups have 27 instances, whereas the other 206 groups have 26 instances.

All the solutions in <solutions> were proven to be optimal. The vast majority of these optimal solutions were found by VRPSolver (Pessoa et al., 2020; vrpsolver.math.u-bordeaux.fr), sometimes using customized parameterizations. Eighteen instances could only be solved using special branching rules and seventeen instances were solved by a Branch-and-Cut algorithm, as in Lysgaard et al. (2004) and Subramanian et al. (2011).  Both VRPSolver and BC approaches take advantage of very good initial solutions provided by HGS-CVRP (Vidal, 2022; github.com/vidalt/HGS-CVRP). 

A Python script <generate.py> is available to produce new XML100-like instances. 
Ex: "python generate.py 200 2 1 4 5 1 0" produces XML200_2145_01.vrp (call "python generator.py" to understand the arguments).

Script genXML100.sh produces the original XML100 instances, if run with Python 2.7.  Running with Python versions  >= 3 produces different instances.

***

[DVRP-Set-Kilby](https://data.mendeley.com/datasets/3fwc3twwn6/1)
