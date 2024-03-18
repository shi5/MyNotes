> [Java 代理模式详解 | JavaGuide](https://javaguide.cn/java/basis/proxy.html#_3-1-jdk-%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E6%9C%BA%E5%88%B6)

**从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**

动态代理只能代理`接口`
## JDK动态代理

**`InvocationHandler` 接口和 `Proxy` 类是核心。**

`Proxy` 类中使用频率最高的方法是：`newProxyInstance()` ，这个方法主要用来生成一个代理对象。

### 使用步骤

1. 定义一个接口及其实现类；
2. 自定义 `InvocationHandler` 并重写`invoke`方法（可以采用lambda匿名类实现），在 `invoke` 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑；
3. 通过 `Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)` 方法创建代理对象；

### 函数说明

#### `Proxy` 类的`newProxyInstance()`

```java
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,    InvocationHandler h) throws IllegalArgumentException{
	......
}
```

1. **loader** :类加载器，用于加载代理对象。—— [[类加载器]] 
2. **interfaces** : 被代理类实现的一些接口；—— 通过接口指定生成的代理有哪些方法
3. **h** : 实现了 `InvocationHandler` 接口的对象；—— 指定生成的代理对象要干什么事



#### `InvocationHandler` 接口中的`invoke()`

1. **proxy** :动态生成的代理类——可以将其返回，继续调用真实对象方法
2. **method** : 与代理类对象调用的方法相对应——传入真实对象需要代理的方法
3. **args** : 当前 method 方法的参数

> proxy参数是invoke方法的第一个参数，是jdk环境给我们的参数，我们可以在`invoke()`中将其返回，这样可以继续调用其真实对象的方法（链式调用）