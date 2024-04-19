`Stream` 就好像一个高级的迭代器，但只能遍历一次。
在流的过程中，可以对流的对象进行一些操作（可以理解为链式的操作过程，操作完上一个才能操作下一个）
流的操作可以分为两种：
1. 中间操作，可以有多个，每个中间操作都会返回一个新的流（比如过滤大于10的值）
3. 终端操作，只能有一个，因为不是返回流，所以无法继续进行流操作

## 用法

### 1. 创建流

- 数组：`Arrays.stream()` 或者 `Stream.of()`
- 集合：可以直接使用 `stream()` 方法创建流，因为该方法已经添加到 Collection 接口中。

### 2. 操作流

1. `filter()`：从流中筛选需要的元素，接收的是[[函数式接口#Predicate]]类型的参数
2. `forEach()`：方法接收的是一个[[函数式接口#Consumer]]类型的参数
3. `map()`：把一个流中的元素转化成新的流中的元素，接收的是一个[[函数式接口#Function]]类型的参数
4. 匹配：进行元素匹配，接收的都是`Predicate`类型参数
	1. `anyMatch()`，只要有一个元素匹配传入的条件，就返回 true。
	2. `allMatch()`，只有有一个元素不匹配传入的条件，就返回 false；如果全部匹配，则返回 true。
	3. `noneMatch()`，只要有一个元素匹配传入的条件，就返回 false；如果全部不匹配，则返回 true。 
5. 组合：把 Stream 中的元素组合起来
	1. 无起始值：`Optional reduce(BinaryOperator accumulator)`
	2. 有起始值：`T reduce(T identity, BinaryOperator accumulator)`

### 3. 转换流

`collect()`：将流转换为集合
`toArray()` ：可以将流转换成数组，接收参数为`IntFunction`