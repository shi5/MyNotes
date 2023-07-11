# 一些想法

- 将system hazard作为模型的一个参数进行训练优化,初始的system hazard可由heuristic算法生成。（system hazard定义为$Θ(τ,P,m)=\underset{τ_i∈τ}{max}⁡(R_i/T_i )$)

- 使用RL对task set进行调度，task set调度成功获得奖励。对于单个task set设置goals（采取多智能体？每个agent对应不同的goals

- 生成足够的样本进行Offline RL

- 在MADDPG中，$x#是所有智能体的observation的集合，但是可以增加额外的环境的信息

- 将强化学习嵌到传统的算法中，让强化学习去充当一个小的部件，整体的框架依然是传统的方法