Optional类提供了一种用于表示可选值而非空引用的类级别解决方案，可以用于避免没必要的null值检查。

## 应用场景
`TODO`

## 用法

### 创建

1. 可以使用静态方法 `empty()` 创建一个空的 Optional 对象
```java
Optional<String> empty = Optional.empty();  
System.out.println(empty); // 输出：Optional.empty
```

2. 可以使用静态方法 `of()` 创建一个非空的 Optional 对象
```java
Optional<String> opt = Optional.of("沉默王二");
System.out.println(opt); // 输出：Optional[沉默王二]
```
> 传递给 `of()` 方法的参数必须是非空的，也就是说不能为 null，否则仍然会抛出 NullPointerException。

3. 可以使用静态方法 `ofNullable()` 创建一个即可空又可非空的 Optional 对象
```java 
String name = null;  
Optional<String> optOrNull = Optional.ofNullable(name);  
System.out.println(optOrNull); // 输出：Optional.empty
```
> `ofNullable()` 方法内部有一个三元表达式，如果为参数为 null，则返回私有常量 EMPTY；否则使用 new 关键字创建了一个新的 Optional 对象——不会再抛出 NPE 异常了。

### 判断值是否存在

通过方法 `isPresent()` 判断一个 Optional 对象是否存在，如果存在，该方法返回 true，否则返回 false——取代了 `obj != null` 的判断。
```java
Optional<String> opt = Optional.of("沉默王二");  
System.out.println(opt.isPresent()); // 输出：true  
  
Optional<String> optOrNull = Optional.ofNullable(null);  
System.out.println(opt.isPresent()); // 输出：false
```

Java 11 后还可以通过方法 `isEmpty()` 判断与 `isPresent()` 相反的结果。


### 非空表达式

`ifPresent()`，允许我们使用函数式编程的方式执行一些代码。
```java
Optional<String> opt = Optional.of("沉默王二");  
opt.ifPresent(str -> System.out.println(str.length()));
```

可以直接将 Lambda 表达式传递给该方法，如果Optional对象不为空就执行该表达式：
```java
Optional<String> opt = Optional.of("沉默王二");  
opt.ifPresent(str -> System.out.println(str.length()));
```

Java 9 后还可以通过方法 `ifPresentOrElse(action, emptyAction)` 执行两种结果，非空时执行 action，空时执行 emptyAction。
```java
Optional<String> opt = Optional.of("沉默王二");  
opt.ifPresentOrElse(str -> System.out.println(str.length()), () -> System.out.println("为空"));
```

### 获取默认值

如果 Optional 对象需要一个默认值，可以使用`orElse()` 和 `orElseGet()`。

`orElse()` 方法用于返回包裹在 Optional 对象中的值，如果该值不为 null，则返回；否则返回默认值。该方法的参数类型和值得类型一致。
```java
String nullName = null;  
String name = Optional.ofNullable(nullName).orElse("沉默王二");  
System.out.println(name); // 输出：沉默王二
```

`orElseGet()` 方法与 `orElse()` 方法类似，但参数类型不同。如果 Optional 对象中的值为 null，则执行参数中的函数`Supplier`。

> `orElseGet()`性能更加，其方法不一定会调用
> 
> `get()`是获取Optional值存在问题，假如 Optional 对象的值为 null，该方法会抛出NoSuchElementException 异常。这完全与我们使用 Optional 类的初衷相悖。
> 
> 建议 `orElseGet()` 方法获取 Optional 对象的值。

### 过滤值

`filter()`用于过滤，方法的参数类型为[[函数式接口#Predicate|Predicate]]，也就是说可以将一个 Lambda 表达式传递给该方法作为条件，如果表达式的结果为 false，则返回一个 EMPTY 的 Optional 对象，否则返回过滤后的 Optional 对象。

### 转换值

`map()` 方法，该方法可以按照一定的规则将原有 Optional 对象转换为一个新的 Optional 对象，原有的 Optional 对象不会更改。