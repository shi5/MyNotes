# NEURAL COMBINATORIAL OPTIMIZATION WITH REINFORCEMENT LEARNING

## 研究对象

- 组合优化 combinatorial optimization
- neural network，reinforcement learning
- TSP问题

## 主要思想

- policy gradient的强化学习方法
  - 两种search startegy
    - RL pretrainning
    - active search

- 采用指针网络和强化学习相结合

- 基线函数
  - 通过估计reward值减小整体梯度方差
  - 通过critic网络得出
    - critic网络也需要训练