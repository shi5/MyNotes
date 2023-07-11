# Developing Real-Time Scheduling Policy by Deep Reinforcement Learning

## 研究贡献

- 将实时调度过程规划为Markov游戏,并利用深度强化学习框架来学习调度策略(Markov game是MDP的多智能体形式延申)
- 提出一种多智能体自合作学习的方法来解决Markov游戏中的credit assignment problem
- 利用python和PyTorch实现了上述框架和算法
- (论文实验的对象是aperiodic tasks,按照作者的说法,其他模式的任务系统也可以应用)

## 具体应用的强化学习

- 策略架构设计
  - MADDPG
  - 选择NN policy而不是encoder-decoder,因为RST对时间要求高,NN的浮点运算操作少,同样,因为时间问题,NN结构采用轻量级的（同时，因为大部分的RTS只有cpu，只采用cpu进行实验）
  - 选择deterministic policy 而不是stochastic policy,确保最佳优先级顺序
  - NN结构为1个隐藏层,8个神经元,采用ReLU作为激活函数
- 将RTS问题转换为Markov Game
  - 因为是aperiodic tasks，系统中的job数量是变化的，每一个job作为一个agent,当job完成时或错过deadline时,将其移除
  - 系统state的状态由n个agent的状态组成,agent的状态由对应的job的参数构成(如剩余执行时间,绝对deadline),agent的动作就是自身的优先级
  - 奖励函数定义为 $r_t=|SJ_t|-|FJ_t|$($SJ_t$定义为在t时刻完成的任务集合,$FJ_t$定义为在t时刻错过deadline的任务集合)
- Self-Cooperative Learning
  - 主要思想是通过在分散的Q函数上应用对称函数来近似一个集中的Q函数（对称函数是指输入的顺序变换不改变输出的结果,如+和*就是对称二元运算符）
  - target networks使用软更新（soft update）

## 疑问

- 怎么处理job的数量变化
  - 采用对称函数聚合每个agent的信息，对称函数以n维向量作为输入，输出一个新的定维的向量
- 对称函数的具体表达式是什么
  - 似乎就是简单地将每个agent的Q函数相加取平均
- 每个agent是不是都具有单独的网络模型
  - 采用的方法是 集中训练，分布运行？这也是多智能体合作学习的常用方法
- Self-Cooperative Learning这一块没懂
- 为什么选择G-EDF、EDF-BF和LSF作为baseline
  - 因为这些启发式算法可以应用于aperiodic tasks

## 想法

- 应用于periodic和sporadic的时候，固定时间段的任务数量是固定的，那么是不是可以更好的设计一下全局Q函数（比如加入一些启发式方法中一些概念作为参数
