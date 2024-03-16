
## 常用注解

### @RequiredArgsConstructor

会生成一个包含常量，和标识了NotNull的变量的构造方法

- 用途：
	- 在springboot项目中，controller或service层中需要注入多个mapper接口或者另外的service接口，这时候代码中就会有多个@AutoWired注解，使得代码看起来什么的混乱。
	- `@RequiredArgsConstructor`  写在类上面可以`代替@AutoWired注解`，需要注意的是：在注入的时候需要用final定义，或者使用@notnull注解