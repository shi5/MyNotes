# Scheduling Arbitrary-Deadline Sporadic Task Systems on Multiprocessors

## 研究对象概述

- SPoradic， arbitrary-deadline
- multiprocessors
  
## 主要思想

将task按deadline从大到小的顺序进行分配（不拆分）给各个处理器（根据公式14），对于剩下未分配的tasks，按deadline从小到大的顺序进行任务拆分（task split）分配给处理器p和处理器p+1.（尽可能多分配给处理器p）
