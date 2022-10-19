# Schedulability Analysis of Global Scheduling Algorithms on Multiprocessor Platforms

## 研究对象描述

- periodic和sporadic, constrained deadlines
- preemptively, multiprocessor
- work conserve, task migration

## contribution

- 提出对多处理器全局调度算法有着显要提升的可调度分析
- 提出更大比例调度heavy tasks的可调度算法
- 指出了文章测试的缺陷所在

## 概念

- density: $λ_K = C_K/D_K$ , 代表着一般时间间隔内任务的"最坏情况"请求

## 主要思想

- 利用workload作为interference的上界来代替计算可调度充分条件
  $$
  \sum_{i \ne k}min(W_k(D_k), D_k-C_k+1) \lt m(D_k-C_k+1)
  $$
- 计算Slack值来收束对interference的估算,计算Slack的方法是迭代的：如果针对每个任务计算其Slack，并用这个Slack更新其他任务的Slack，不断迭代，直到任务集可调度或一轮无Slack更新。