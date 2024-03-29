# Reinforcement Learning

## 一些库

- import matplotlib.pyplot as plt # 画图的库
- from tqdm import tqdm  # tqdm是显示循环进度条的库

>强化学习算法有两个重要的评价指标：一个是算法收敛后的策略在初始状态下的期望回报，另一个是样本复杂度，即算法达到收敛结果需要在真实环境中采样的样本数量

## 统计知识

### KL散度

- 通常在概率和统计中，我们会用更简单的近似分布来代替观察到的数据或复杂的分布。KL散度帮助我们衡量在选择近似值时损失了多少信息。

## 探索策略

1. $\epsilon$-贪心算法：
    $$
    a^*_t =
    \begin{cases}
    argmaxa^*\in\Alpha\hat{Q}(a),\quad 采样概率：1-\epsilon \\
    从\Alpha中随机选择，\quad 采样概率：\epsilon
    \end{cases}
    $$

    - 固定$\epsilon$值
    - $\epsilon$随时间减少

2. 上置信界（upper confidence bound，UCB）算法是一种经典的基于不确定性的策略算法

   - 霍夫丁不等式：令$X_1,...,X_n$为n个独立同分布的随机变量，取值范围为[0,1]，其经验期望为$\bar{x_n}=1/n\sum_{j=1}^{n}{X_j}$,则有$$\mathbb{P}\{\mathbb{E}[X]\ge\bar{x_n}+u\}\le e^{-2nu^2} $$

    将参数替换后，可以得出不确定性度量的公式

3. 汤普森采样（Thompson sampling）：先假设每个动作的奖励服从一个相应的概率分布，每次根据这个概率分布选取最大期望奖励的动作，并根据该动作的结果更新动作的概率分布。
   - 汤普森采样是一种计算所有动作的最高奖励概率的蒙特卡洛采样方法。

## 价值函数的估算

1. 通过马尔可夫奖励过程（MRP）解析解的方法
   - $O(n^3)$不适合状态动作集合比较大的情况

2. 蒙特卡洛方法（Monte-Carlo methods）：也被称为统计模拟方法，是一种基于概率统计的数值计算方法
      - 使用重复随机抽样，然后运用概率统计方法来从抽样结果中归纳出我们想求的目标的数值估计
      - 不需要知道 MDP 的状态转移函数和奖励函数，可以得到一个近似值，并且采样数越多越准确。
      - 占用度量：用于衡量在一个智能体决策与一个动态环境的交互过程中，采样到一个具体的状态动作对（state-action pair）的概率分布。

3. 动态规划算法：要求事先知道环境的状态转移函数和奖励函数，也就是需要知道整个马尔可夫决策过程。
   1. 策略迭代（policy iteration）：策略评估（policy evaluation）和策略提升（policy improvement）
      - 策略评估和策略提升不断循环交替，直至最后得到最优策略的过程。
        1. 策略评估：计算一个策略的状态价值函数。通过贝尔曼期望方程和动态规划。
           1. 策略迭代中的策略评估需要进行很多轮才能收敛得到某一策略的状态函数，这需要很大的计算量，尤其是在状态和动作空间比较大的情况下。
        2. 策略提升：根据策略评估计算得到当前策略的状态价值函数来改进该策略（贪心法，选择最大价值的动作）
        3. 当策略提升之后得到的策略$\pi'$和之前的策略$\pi$一样时，说明策略迭代达到了收敛，此时$\pi'$和$\pi$就是最优策略。
   2. 价值迭代（value iteration）：不存在显式的策略，只维护一个状态价值函数
      1. 可以被认为是一种策略评估只进行了一轮更新的策略迭代算法。
   >1.策略迭代中的策略评估使用贝尔曼期望方程来得到一个策略的状态价值函数，这是一个动态规划的过程；而价值迭代直接使用贝尔曼最优方程来进行动态规划，得到最终的最优状态价值。
   >2.策略迭代和价值迭代通常只适用于有限马尔可夫决策过程，即状态空间和动作空间是离散且有限的。

4. 时序差分（temporal difference，TD）算法：用当前获得的奖励加上下一个状态的价值估计来作为在当前状态会获得的回报
   - 价值估计更新的步长取为常数，不再像蒙特卡洛那样严格地取期望
   - 时序差分（TD）误差：$R_t+\gamma V(s_{t+1})-V(s_t)$
   1. sarsa算法：
      1. on-policy
      2. 可以拓展为多步sarsa
      ![sarsa](imgs/Reinforcement%20Learning/2023-03-02-09-53-59.png)
   2. Q-learning算法：
      1. off-policy
      ![Q-learning](imgs/Reinforcement%20Learning/2023-03-02-09-55-08.png)

## 基于模型

1. Dyna-Q:环境模型是通过采样数据估计得到
   1. 使用Q-planing方法来基于模型生成一些模拟数据，然后用模拟数据和真实数据一起改进策略
      1. Q-planning 每次选取一个曾经访问过的状态$s$，采取一个曾经在该状态下执行过的动作$a$，通过模型得到转移后的状态$s'$以及奖励$r$，并根据这个模拟数据$(s,a,r,s')$，用 Q-learning 的更新方式来更新动作价值函数。
      2. 在每次与环境进行交互执行一次 Q-learning 之后，Dyna-Q 会做$n$次 Q-planning。

## DQN算法

>用函数拟合的方法来估计Q值，这种函数拟合的方法存在一定的精度损失，因此被称为近似方法。

- 使用神经网络来进行函数拟合
  - 动作连续（无限）：同时将状态s和动作a作为输入，然后输出一个标量，表示在状态s下采取动作a能获得的价值。
  - 动作离散：除了可以采取动作连续情况下的做法，还可以只将状态s输入到神经网络中，使其同时输出每一个动作的Q值
   >DQN和QLearning只能处理动作离散的情况
- Q网络的损失函数：
  ![1](imgs/Reinforcement%20Learning/2023-03-07-21-06-02.png)
- 经验回放：维护一个回放缓冲区，将每次从环境中采样得到的四元组数据（状态、动作、奖励、下一状态）存储到回放缓冲区中，训练 Q 网络的时候再从回放缓冲区中随机采样若干数据来进行训练。
  - 使样本满足独立假设
  - 提高样本效率
- 目标网络

>状态连续，动作离散（连续空间进行max操作太复杂）

- 改进算法
  - Double DQN：解决Q值过高问题
    - 利用两个独立训练的神经网络估算$max_{a'}Q_*(s',a')$,即利用一套神经网络$Q_w$的输出选取价值最大的动作，但在使用该动作的价值时，用另一套神经网络$Q_w^-$计算该动作的价值。
    - 传统的DQN会导致对Q值得过高估计，对于动作空间较大的任务，DQN 中的过高估计问题会非常严重，造成 DQN 无法有效工作的后果.
  - Dueling DQN：能够很好地学习到不同动作的差异性
    - 优势函数：状态动作价值函数Q减去状态价值函数V，即$A(s,a)=Q(s,a)-V(s)$.
    - 在Dueling DQN中，Q网络被建模为：$Q_{\eta,\alpha,\beta}(s,a)=V_{\eta,\alpha}(s)+A_{\eta,\beta}(s,a)$
      - $\eta$状态价值函数和优势函数共享的网络参数，一般用在神经网络中，用来提取特征的前几层；而$\alpha$和$\beta$分别为状态价值函数和优势函数的参数
      - 我们不再让神经网络直接输出Q值，而是训练神经网络的最后几层的两个分支，分别输出状态价值函数和优势函数，再求和得到Q值
      - 存在V值和A值建模不唯一性的问题

## 策略梯度

- 基于值函数的方法主要是学习值函数，然后根据值函数导出一个策略，学习过程中并不存在一个显式的策略；而基于策略的方法则是直接显式地学习一个目标策略。
- PG利用带权重的梯度下降方法更新策略,其中奖励值就是权重
- 在线策略
![1](imgs/Reinforcement%20Learning/2023-03-08-20-18-12.png)

## Actor-Critic

- actor
  - 时序差分残差
- critic
  - 策略梯度

### TRPO算法

- 策略梯度算法有一个明显的缺点：当策略网络是深度模型时，沿着策略梯度更新参数，很有可能由于步长太长，策略突然显著变差，进而影响训练效果。
- 针对以上问题，考虑在更新时找到一块信任区域（trust region），在这个区域上更新策略时能够得到某种策略性能的安全性保证，这就是信任区域策略优化（trust region policy optimization，TRPO）算法的主要思想。

- 借助当前策略来找到一个更优的策略，并使用KL散度来保证新旧策略足够接近（信任区域），KL散度作为约束值存在
- 直接求解带约束的优化问题比较麻烦，TRPO 在其具体实现中做了一步近似操作来快速求解。
- 共轭梯度法（conjugate gradient method）
- 线性搜索
- 广义优势估计

### PPO算法：TRPO算法的改进

- PPO-惩罚：将KL散度作为惩罚值
  - ![1](imgs/Reinforcement%20Learning/2023-03-10-09-59-37.png)
- PPO-截断
  - ![2](imgs/Reinforcement%20Learning/2023-03-10-10-00-10.png)