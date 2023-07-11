# Improve d schedulability analysis of the contention-free policy for real-time systems 

## 论文贡献

- 提出了 pseudo-response time的概念，代表job优先级被CF policy降级的时间（也就是说和CF policy绑定的），并探讨了其性质
- 将 pseudo-response time应用于schedulability analysis构成新的schedulability analysis框架PRTA，可以显著提升DA（deadline analysis）的调度性能？
- 提出可以利用pseudo-slack time来进一步提高PRTA的调度性能
- 将PRTA应用于EDF和RM调度算法（使用了CF policy），并于DA进行比较

## 主要思想

- 前置知识：CF策略，contention-free time slot的计算；PTA框架，
- 将job降级的时间或完成执行的时间定义为Pseudo-finishing time，并通过Pseudo-finishing time可以计算出Pseudo-response time，用Pseudo-response time替换PTA框架中的Response time得出PRTA
- 考虑一个特殊的任务集$\hat{\tau}$，$\hat\tau_k$的WCET为$C_i$，其他任务$\hat\tau_i$为$C_i-\Phi_i$,计算得出$\hat{\tau}$的Response time即是Pseudo-finishing time
- 提出Pseudo slack time的概念，可以收束 load $W_i$ 的计算，提升PRTA的性能

## 性能比较

- PRTA的时间复杂度为$O(n^2D_{max}^2)$，和RTA是相同的
- PRTA比RTA和DA的性能更好

## 疑问

- 如何理解RTA框架中min(i，l-C+1)
- 为什么使用$\hat{\tau}$计算Pseudo-response time的上界
  - 考虑的WCET不同，简化情况讨论对于需要计算Pseudo-response time的job的WCET是$C_i$，其他的是$C_i-\Phi_i$
