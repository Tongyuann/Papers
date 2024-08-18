# Modeling and Optimization of Parallel Disassembly Line Balancing Problem With Parallel Workstations
## 基于并行工作站的并行拆解线平衡问题建模与优化
## 摘要
1. 提出了一种具有并行工作站的并联拆装线平衡问题(PW-PDLBP)
2. 根据问题特点建立了最小化生产线长度、工作站数量、空闲时间平衡指数和能耗的混合整数非线性规划(MINLP)模型，并使用GUROBI优化器进行求解
3. 提出了一种多目标增强差分进化算法(MEDE)
## 简介
研究了并行工作站的并行布局，以实现车间空间的合理利用。  
由于DLBP是np困难问题，精确的方法无法在有限的时间内得到大规模实例的有效分解方案。然而，智能算法的优点之一是能够快速获得可行的解本文提出了一种多目标增强差分进化算法(MEDE)
##  贡献
1. 提出了PW-PDLBP布局，并与没有并行工作站的平行线布局进行了比较
2. 利用GUROBI优化器对PW-PDLBP的MINLP模型进行解释和求解
3. 开发MEDE优化PW-PDLBP，还设计了PW-PDLBP的编解码
