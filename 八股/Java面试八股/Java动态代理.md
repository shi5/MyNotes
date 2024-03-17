
**从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**

动态代理只能代理接口
## JDK动态代理

**`InvocationHandler` 接口和 `Proxy` 类是核心。**

`Proxy` 类中使用频率最高的方法是：`newProxyInstance()` ，这个方法主要用来生成一个代理对象。
