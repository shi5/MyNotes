
## 常用注解

### @RequiredArgsConstructor

会生成一个包含常量，和标识了NotNull的变量的构造方法

- 用途：
	- 在springboot项目中，controller或service层中需要注入多个mapper接口或者另外的service接口，这时候代码中就会有多个@AutoWired注解，使得代码看起来什么的混乱。
	- `@RequiredArgsConstructor`  写在类上面可以`代替@AutoWired注解`，需要注意的是：在注入的时候需要用final定义，或者使用@notnull注解

### @SneakyThrows

在java的异常体系中Exception异常有两个分支，一个是运行时异常RuntimeException，一个是编译时异常，在Exception下的所有非RuntimeException异常，比如IOException、SQLException等；所有的运行时异常不捕获，编译时异常是一定要捕获，否则编译会报错。@SneakyThrows就是利用了这一机制，将当前方法抛出的异常，包装成RuntimeException，骗过编译器，使得调用点可以不用显示处理异常信息。


- 用途：
	- 减少程序的异常捕获。