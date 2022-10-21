# Maximizing Contention-Free Executions in Multiprocessor Scheduling

## 研究对象概述

- real-time tasks with shorter deadline(constrained deadline or implicit)
- contention-free slot
- work_conserving, preemptive

## 概念

- contention free slots：竞争任务数量不超过可用处理器数量的时段

## 主要思想（CF policy）

- 可以和任何working-conserving、preemptive的算法结合
- 将contending slots 的任务转移到contention-free slots，以减少contending slots中的竞争，如果一个任务的剩余执行时间不超过contention-free slots到它的deadline之前的时间，我们就将它推迟到contention-free slots执行(通过设定为低优先级)
- 可以实现interference reduction，来提高interference-based schedulability test算法的性能
- 采用deadline reduction来获取更多contention-free slots,实现deadline reduction的策略是将按照一定的顺序(如:最大destiny,最小松弛时间,最大"可调度条件"),每个任务的deadline迭代式地减少

## 缺陷

- 可能引发更多的preemption