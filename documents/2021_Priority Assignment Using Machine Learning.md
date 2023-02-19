# Priority Assignment Using Machine Learning

## Introduction

- 研究对象: global fixed-priority preemptive(gFP) scheduling on a multiprocessor platform
- 关注的问题:对于给定现有的启发式优先级分配算法无法调度的任务集,找到一种可以被调度的优先级分配策略
- 提出的架构: PAL (Priority Assignment Learning Framework)
  - pointer network
  - 监督学习,学习的样本是相同任务数量n的可调度的任务集
  - 输入输出：任务参数序列作为输入；任务索引序列作为输出，其中每个指标的位置表示其优先级
  - 选取具有最小*system hazard*的priority assignment作为样本（对于一个任务集，可能存在多种优先级分配策略)