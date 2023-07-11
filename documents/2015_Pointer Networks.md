# Pointer Networks

## 研究对象

- 组合优化问题——输出的类别取决于输入的类别/输出序列是输入序列的子集

## 主要思想

- 传统attention求得的vector $u^i$维度和输入保持一致,那么将其进行softmax就可以得出输出字典的条件概率,且输出字典与输入保持一致.(相当于将输入文本的attention的概率分布作为输出词汇表的概率分布)
- 使用attention作为指针,选取输入序列中的一员作为输出

## trick

- glimpse

## 应用方向

- 组合优化
- Summarization
- 处理翻译中的人名地名