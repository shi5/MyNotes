# Deep Learning

## self-attention

将一整个sequence的vector作为self-attention的输入,self-attention根据整个sequence输出相同数量的vectors

- 计算两个向量的关联程度的方法
  - Dot-product(更常用)
    - $q = W^qa^1$
    - $k = W^ka^2$
    - 关联度:$α = q \cdot k$
  - ADDitive
    - 用上述的q和k经过 $ +、tanh、W $ 得到α

- 基于attention抽取信息
  - 各个$α_{i,j}$经过softmax(也可以使用其他的激活函数)得到$α_{i,j}'$
  - 求出$v$:$v^i = W^v a^i$
  - 得出 $b^1 = \sum_iα_{1,i}' v^i$

- Multi-head Self-attention
  - 用于寻找两个向量不同种类的关联
  - 由$q^i$得到$q^{i,1},q^{i,2}$, $k^i$得到$k^{i,1},k^{i,2}$, $v^i$得到$v^{i,1},v^{i,2}$,分别运算得到$b^{i,1},b^{i,2}$,经过$W^O$得到$b ^i$

- Positional Encoding
  - self-attention是没有位置信息的,可以使用Positional Encoding来得到位置信息
  - 每个位置有一个唯一的position vector $e^i$,将$e^i$与$a^i$相加即可

- Truncated(截断的) Self-attention
  - 对于语音识别等任务sequence会很大,难以训练
  - 只看一小部分范围内的attention

- self-attention需要计算N*N的矩阵,当N很大时计算量随之增大
  - Truncated Attention
  - Stride Attention
    - 空几个key看之前的
  - Global attention
    - 给初始sequence添加特殊的token
    - special token会收集全局信息,其他的则不会
  - Reformer && Rounting Transformer
    - 先将query和key进行聚类Clustering
    - 同一类的query和key才计算attention,否则直接设为0
  - Sinkhorn Sorting Network
    - learnable patterns
    - 用另外的一个网络来产生矩阵,决定计不计算attention
  - linformer
    - self-attention产生的attention matrix是low-rank的
    - 挑选出具有代表性的几个K(用CNN或用相应矩阵产生想要的K个key)

## RNN

- 具有短时记忆力,相同的输入,sequence的顺序不同,产生的输出也不同

- 结构变型
  - Elman Network
    - 将隐藏层的值存起来给下一时刻的隐藏层使用
  - Jordan Network
    - 将整个网络的输出的值存起来给下一时刻的隐藏层使用
  - Bidirectional RNN
    - 从正反两个方向迭代,根据两个结果产生最后的输出,可以同时观察前后两个方向.
    - 双向可以用于encoder,但不用于decoder
  - Long Short-term Memory(LSTM)
    - 4个input,1个output
    - 三个gate
      - input gate:决定之前的数据是否要存入memory cell中
      - output gate:决定是否将memory cell中的值用于产生输出
      - forget gate:决定是否将memory cell中的值丢弃
    - 通常使用sigmoid(0~1)激活函数对三个门处理,代表它们被打开的程度
    - input乘以不同的weight作为不同gate的输入

## seq2seq

- 编码器-解码器细节
  - 解码器和编码器都是RNN
  - 编码器是没有输出的RNN
  - 编码器最后时间步的隐状态用作解码器的初始隐状态
- 训练
  - 训练时解码器使用目标句子作为输入(即使输出是错的也用正确翻译作为下一次输入)
- 衡量生成序列的好坏:BLEU
