---
date created: 2023-11-02 10:36
---
# JAVA

## 常见问题

1. 使用comparator排序时，返回非正数则不交换次序；基本类型不能使用Comparator进行排序

2. 各种排序
		1. int数组
				1. 升序：`Arrays.sort(nums);`
				2. 降序：自己实现，基本类型不能使用Comparator进行排序，但Integer数组可以，或者二维int数组对第一维进行排序
		2. 列表
				1. 升序：`List.sort(list)`或者`list.sort()`
				2. 降序：`List.sort(list, Comparator)`
		3. 对于一个数组，如何获取其排序后的坐标
			创建一个index数组，使用comparator排序

3. Java取模可能出现负数，不想出现负数应该怎么处理
		1. 加上一个模数
		2. 使用Math.floorMod() 方法

4. 基本类型是值传递，其他的为引用传递，其他类型均为引用类型

> 应该说java中只有值传递，只是普通对象引用类型传递的是对象地址的拷贝副本，如果将形参指向一个新对象，不影响实参指向的对象，但如果形参对源对象进行修改，实参指向的对象也会相应变化。但对于String以及基本数据类型的装箱类引用类型------形参改变，因实参和形参指向的目标实例对象不一致，因此实参不变。

```java
	Integer i = 1;
	Integer j = i;
	j = 2;
	System.out.println(i);  // i = 1
	System.out.println(j);  // j = 2
```

1. java中的负数取余和对负数取余
		1. Java 的取余运算（%）的结果符号与被除数的符号相同
		2. 负数取余：
				1. `-1%4 = -1`
		3. 对负数取余
				1. `1%-4 = 1`

2. 一个变量 i 的取值在 {0, 1, 2, 3} 中循环，并且每次对 i 进行加一或减一操作时，避免出现负数，可以使用取模运算来实现循环取值的效果。
		1. 加一操作：`i = (i + 1) % 4;`
		2. 减一操作：`i = (i + 3) % 4;`

3. 自定义类的对象放入 HashSet 中时，需要确保重写 equals 和 hashCode 方法，以便正确比较对象的相等性。

4. int数相除向上取整：`(x-1) / y + 1`

## 基础类型

- `Integer.MAX_VALUE = 2147483647`
- `Long.MAX_VALUE = 9223372036854775807`

## String类型

- 方法：
	- String[] string.split(String regex)
		- "100e".split("e"): 返回长度为1的数组 "100"
		- "e100".split("e"): 返回长度为2的数组 "" 和 "100"
		- "e".split("e"): 返回长度为0的数组
		- "100ee666".split("e"): 返回长度为3的数组 "100"、"" 和 "666"

## 集合类

各个集合类的出入接口不统一，感觉记得很乱

### Stringbuilder

- 添加：append()
- 插入：insert()
- 删除：delete()
- 替换：replace()

### List

- 添加：add()
- 获取：get()
- 删除：remove()（传入int删除指定索引元素，传入object删除指定元素）
- 修改：set()

- List转Array：
	- toArray(T[]): `list.toArray(new String[list.size()])`
- Array转List：
	- Arrays.asList(strArray)：将数组转换List后，不能对List增删，只能查改，否则抛异常。
	- `ArrayList<String> list = new ArrayList<String>(Arrays.asList(strArray))`
	- Collections.addAll(arrayList, strArray)：最高效

### Map

- 添加：put()
- 获取：get()
- 包含：containsKey(Object k)、containsValue(Object v)
- 删除：remove()
- 清空：clear()

### Set

- 添加：add()
- 包含：contians()
- 删除：remove()
- 遍历：for-each

### Stack

- 添加：push()
- 取出：pop()
- 获取：peek()

### Queue

- 添加：add() / offer()
- 取出：remove() / poll()
- 获取：element() / peek()

## Junit单元测试

### 测试分类

- 黑盒测试:不需要写代码
- 白盒测试:需要写代码,关注程序具体的执行测试

> Junit是白盒测试

### Junit测试步骤

1. 定义一个测试类(测试用例)
		- 建议
			- 测试类名：被测试的类名Test    CalculatorTest
			- 包名：xxx.xxx.xx.test     cn.incast.test

2. 定义测试方法:可以独立运行
		- 建议
			- 方法名：test测试的方法名      testAdd()
			- 返回值：void
			- 参数列表：空参

3. 给方法加@Test

4. 导入Junit依赖环境

### 判定结果

- 红色：失败
- 绿色：成功
- 一般是我们使用断言操作来处理结果
	- `Assert.assertEquals(excepted,result);`

### 补充

- @Before
	- 修饰的方法会在测试方法之前被自动执行
- @After
	- 修饰的方法会在测试方法执行之后被自动执行

## 反射：框架设计的灵魂

- 框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
- 反射：将类的各个组成部分封装成其他对象，这就是反射机制
	- 好处：
		1. 可以在程序运行过程中，操作这些对象
		2. 可以解耦，提高程序的可拓展性

![JavaClass](pics/JavaClass.png "javaclass")

### Class对象的获取方式

1. `Class.forname("全类名")`：包名+类名  将字节码文件加载进内存，返回class对象
		- 多用于配置文件，将类名定义在配置文件中，读取文件，加载类
2. 类名.class:通过类名的属性class获取
		- 多用于参数的传递
3. 对象.getClass():getClass()方法在Object类中定义着。
		- 多用于对象的获取字节码的方式

> 同一个字节码文件(.class)在一次程序运行中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个

### Class对象功能

- 获取功能：
	1. 获取成员变量们

	```java
	Field[] getFields()   :获取所有public修饰的成员变量
	Field getField(String name)    :获取指定名字的public修饰的成员变量
	Field getDeclaredFields() :获取所有的成员变量，不考虑修饰符（可以获取私有变量，不过要忽略访问权限修饰符的安全检查--field.setAccesible(true);//暴力反射）
	Field getDeclaredField(String name)
	```

	2. 获取构造函数们

	```java
	Constructor<?>[] getConstructors()
	Constructor<T> getConstructor(类<?>... parameterTypes)
	Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)
	Constructor<?>[] getDeclaredConstructors()
	```

	3. 获取成员方法们

	```java
	Method[] getMethods()
	Method getDeclaredMethod(String name, 类<?>... parameterTypes)
	Method[] getDeclaredMethods()
	Method getDeclaredMethod(String name, 类<?>... parameterTypes)
	```

	4. 获取类名
	`String getname(); //全类名`

### Field：成员变量

- 操作
	1. 设置值
			- void set(Object obj, Object value)
	2. 获取值
			- get(Object obj)
	3. 忽略访问权限修饰符的安全检查
			- field.setAccesible(true)l;//暴力反射

### Constructor：构造方法

- 创建对象：
	- T newInstance(Object... initargs)
	- 如果使用空参构造方法创建对象，操作可以简化：class对象的newInstance
	- 忽略访问权限修饰符的安全检查
		- field.setAccesible(true)l;//暴力反射

### Method：成员方法

- 执行方法：
	- Object invoke(Object obj, Object.. args)
	- 忽略访问权限修饰符的安全检查
		- field.setAccesible(true)l;//暴力反射
- 获取方法名称：
	- String getName：获取方法名

### 案例

- 需求：写一个“框架”，在不改变该类的任何代码的前提下，可以帮助我们创建任意类的对象，并且执行其中任意的方法
	- 实现：
		1. 配置文件
		2. 反射
	- 步骤：
		1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件(.properties)中

		2. 在程序中加载读取配置文件（使用**静态代码块**来读取配置文件）

		```java
		//1. 创建Properties集合类
		Properties pro = new Properties();
		//获取src路径下的文件的方式--->ClassLoader(类加载器)
		ClassLoader classloader = JDBCUtils.class.getClassLoader();
		URL res = classlaoder.getResource("Filepath"); //相对路径：./ = src/ 
		String path = res.getPath();
		//2. 加载文件
		//pro.load(new FileReader("Filename"));
		pro.load(new FileReader("path"));
		//3. 获取数据，赋值
		value = pro.getProperty("value");
		```

		3. 使用反射技术来加载类文件进内存

		4. 创建对象

		5. 执行方法

## 注解

- 概念:也叫元数据，一种代码级别的说明。JDK1.5之后  @注解名称
- 作用分类：
	- 编写文档：通过注解生成文档
	- 代码分析：通过注解对代码进行分析（使用反射）
	- 编译检查：通过注解让编译器实现基本的编译检查（Override）

### JDK预定义的一些注解

- @Override：检测被该注解标注的方法是否继承自父类（接口）
- @Deprecated：将该注解标注的内容，表示已过时
- @SuppressWarnings：压制警告
	- 一般传递参数＂all＂    @SuppressWarning(＂all")

### 自定义的注解

- 格式：
	- 元注解
	- public @interface 注解名称{}

- 本质：注解本质上就是一个接口，默认继承继承Annotation接口
	- public interface MyAnno extends java.lang.annotation.Annotation{}

- 属性：接口中可以定义的**成员方法**
	- 要求：
		1. 属性的返回值类型
				1. 基本数据类型
				2. String
				3. 枚举
				4. 注解
				5. 以上类型的数组
		2. 定义了属性，在使用时需要给属性**赋值**（类似于成员变量那样赋值）
				1. 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值。
				2. 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可
				3. 数组赋值时，使用{}包裹。如果数组只有一个值，则{}可以省略

- 元注解：用于描述注解的注解
	- @Target：描述注解能够作用的位置
		- ElementType取值：（不止以下几种）
			- TYPE：可以作用与类上
			- METHOD：可以作用于方法上
			- FILED：可以作用于成员变量上
	- @Retention：描述注解被保留的阶段
		- @Retention(RetentionPolicy.RUNTIME)：当前描述的注解，会保留到class字节码文件中，并被JVM读取到
	- @Documented：描述注解是否被抽取到api文件中
	- @Inherited：描述注解是否被子类继承

### 在程序中使用（解析）注解：获取注解中定义的属性的值

1. 获取注解定义的位置的对象（Class、Method、Field）
2. 获取指定的注解

getAnnotation(Class)
// 其实就是在内存中生成了一个该注解接口的子类实现对象

````
```java
/*
public Class ProImpl implements Pro {
    public String className()
    {
        return "cn.itcast.annotation.Demo1";
    }
    public String methodName()
    {
        return "show";
    }
}
  */
```
````

3. 调用注解中的抽象方法获取配置的属性值

### 小结

1. 以后大多数时候，我们会使用注解，而不是自定义注解

2. 注解给谁用？
		1. 编译器
		2. 解析程序

3. 注解不是程序的一部分，可以理解为注解就是一个标签
