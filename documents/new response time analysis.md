# response time analysis

## new RTA(2009)

### problem setting

- global fixed priority scheduling
- sporadic task set
- 假定所有任务按照优先级排序
- 考虑constrained-deadline 和 arbitrary-deadline；对于arbitrary-deadline，假定$J_i^h$只有在其前一个$J_i^{h-1}$完成之后才能执行，因此存在一个ready time $\gamma _i^h = max(r_i^h,f_i^{h-1})$,在时刻$t \in [\gamma_i^h,f_i^h]$,称$J_i^h$是ready的。（constrained-deadline 和 arbitrary-deadline皆是如此）。
- 假定任务是严格periodic的，得出的结果是可以用在sporadic任务上的.
- 使用一些特殊符号表达式：<br>![](imgs/new%20response%20time%20analysis/2023-03-22-15-33-29.png)

> 单处理器中的*critic instant*：所有比$\tau_k$ 优先级高的task和$\tau_k$同时释放，此时$\tau_k$的响应时间最大。
> 多处理器的*critic instant*未知，通过计算在level-k busy period中的interference界限

- $\tau_k$在level-k busy period中的最坏情况可分为三部分
  - body：所有release time和deadline都在level-k busy period中的所有job（称为body job）执行
  - carry-in：release time在level-k busy period之前，deadline在其之中的最多一个这样的job（carry-in job）
  - carry-out：release time在level-k busy period之中，deadline在其之后的最多一个这样的 job（carry-out job）

### constrained-deadline

#### Specific description

- busy period extension
  - 将busy period扩展至$(t_0,f_k)$,$t_0$位于$r_k$之前，并且在$[t_0,r_k)$中的任何一个时间点，所有的处理器都被比$\tau_k$优先级高的任务占据。如果不存在这种情况，则令$t_0=r_k$。同时令$\varphi=r_k-t_0$
  - 这样可以保证从$t_0-1$开始，最多只有M-1个更高优先级的任务是活跃的，那么最多只有M-1个任务有carry-in，且carry-in job最多只有$C_i-1$执行时间
- $\Phi^B$ :the upper bound of $\varphi$
  - $\varphi$是个未知的变量，$\Phi^B$作为$\varphi$的上界，检测$[0,\Phi^B]$的所有值
  - $\varphi$取0即可保证

- Workload：$W_k(\tau_i,x)$表示比$\tau_k$优先级更高的任务$\tau_i$在长度为$x$的level-k busy period中的workload上界
  - $W_k^{NC}(\tau_i,x)$：$\tau_i$没有carry-in job的workload
  - $W_k^{CI}(\tau_i,x)$：$\tau_i$有carry-in job的workload
  - 计算方式：<br> ![](imgs/new%20response%20time%20analysis/2023-03-23-15-31-08.png)
  - 示意图：<br> ![](imgs/new%20response%20time%20analysis/2023-03-23-15-31-29.png)

- interference：$I_k(\tau_i,x)$
  - 与workload类似：![](imgs/new%20response%20time%20analysis/2023-03-23-15-35-38.png)
  - total interference：$\Omega_k(x)$ <br> ![](imgs/new%20response%20time%20analysis/2023-03-23-15-39-19.png)<br>   其中，$\mathcal{Z}\in\tau\times\tau$包含了所有NC和CI的分配可能，$|\tau^{CI}|\le M-1$，$I_k(\tau_i,x)$可以在线性时间内计算出来

- 可见 $W_k^{NC}(\tau_i,x)$和$W_k^{CI}(\tau_i,x)$的计算与$\varphi$无关，因此$\Omega_k(x)$ 也与$\varphi$无关，因此可以将$\varphi$设为0也不影响WCRT的计算（没看懂为什么,似乎是因为$\varphi=0$时是最坏情况

#### RTA过程

![](imgs/new%20response%20time%20analysis/2023-03-23-16-31-51.png)

### arbitrary-deadline

#### Specific description

- 考虑如下情况：
  - $J_k^1$满足$\gamma_k^1=r_k^1$
  - $t_0$定义同上
  - $J_k^H$定义为$J_k^1$之后第一个response time在周期时间内的，即$R_k^H\le T_k$
  - 示意图：<br>![](imgs/new%20response%20time%20analysis/2023-03-23-16-39-01.png)

- workload 和 interference
  - $W_k^{NC}(\tau_i,x)$和$W_k^{CI}(\tau_i,x)$同上
  - interference：<br>![](imgs/new%20response%20time%20analysis/2023-03-23-16-53-37.png)
  - total interference：<br>![](imgs/new%20response%20time%20analysis/2023-03-23-16-57-33.png)

#### RTA过程（待补充

## Improved RTA Based on the New Carry-In Estimation Method（2017

- carry-in job的估算可以更加乐观：<br>![](imgs/new%20response%20time%20analysis/2023-03-24-10-25-00.png)
  - 其中：$MIN_i(x)=max_{1\le y\le x}\{||y-\lfloor\Omega_i(y)/m\rfloor||^{C_i}_0\}$，$MAX_i(x)=C_i-MIN_i(T_i-x)$

- Improved RTA Based on the New Carry-In Estimation Method：RTA test $\mathbb{R}$, improved RTA test $\mathbb{IR}$, task set $\mathcal{T}$
  1. 在$\mathcal{T}$执行$\mathbb{R}$，如果可以调度，返回OK；否则，计算每个$\tau_i\in\mathcal{T}$的$MAX_i(x)$，$0\le x\le T_i$
  2. 用$MAX_i(x)$来作为carry-in job $J_i$的workload上界，$x$设为$J_i$的周期和busy period的重叠长度。使用该值重新测试$\mathcal{T}$的可调度性
  3. 如果$\mathcal{T}$可调度，返回OK，否则重新计算$MAX_i(x)$。如果$MAX_i(x)$存在变化，重新执行2，否则返回failure
