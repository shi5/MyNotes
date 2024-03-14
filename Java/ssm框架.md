# SSM

## spring框架

- 架构图
  - core container：核心容器
  - AOP：面向切面编程（在不修改原始程序的基础上，增强功能
  - Aspects：AOP思想实现（spring实现了AOP，aspects也实现了AOP思想，spring将其收录
![](2023-06-29-10-47-53.png)

- spring配置文件：applicationContext.xml

### SSM文件框架

- main
  - java
    - config：
      - SpringConfig：
      - JdbcConfig：
        - DataSource
        - PlatformTransactionManager
      - MyBatisConfig：
        - SqlSessionFactoryBean
        - MapperScannerConfigurer
      - ServletConfig：
        - getRootConfigClasses:
        - getServletConfigClasses:需要MVC配置类
        - getServletMappings
      - SpringMVCConfig
      - SpringMVCSupport（可被SpringConfig替代，但侵入式较强）
        - addResourceHandlers
        - addInterceptors
    - controller(web)
      - code
      - result
      - ***controller***
      - ProjectExceptionAdvice：@RestControllerAdvice
      - interceptor
    - domain(pojo)
    - dao(mapper)
    - service
      - impl
    - exception
      - SystemException
      - BusinessException
  - resources
    - static：存放网页资源
    - templates
- test

### IoC和DI

- IoC（Inversion of control）：控制反转
  - 对象的创建控制权由程序转移到外部，这种思想为控制反转
  - IoC容器充当`外部`需要管理大量的对象，负责对象的创建、初始化等一系列的工作，被创建或管理的对象在IOC容器中统称为`Bean`
  - spring配置文件：applicationContext.xml
  - **通过ApplicationContext接口从applicationContext.xml中获取IoC容器**
- DI（dependence injection）：依赖注入
  - 在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入
- IoC容器对bean对象的创建进行控制，并绑定bean对象之间的依赖关系
- 目的：充分解耦

### bean

#### bean配置

- `resources/applicationContext.xml`中配置bean
  - `<bean>`标签定义，
  - id标识bean，使用容器可以通过id值（getbean）获取对应的bean，唯一
  - 可以通过name取别名，' '、','、;'分隔多个别名
  - class标识bean对应的类
  - 通过在`<bean>`标签内设置`<property>`属性进行依赖注入

> spring默认bean为单例（即通过getBean()方法获得的是同一个bean对象），可通过设定scope属性设置为prototype（非单例）

- 适合交给spring管理的bean：
  - 表现层对象
  - 业务层对象
  - 数据层对象
  - 工具对象
- 不适合：
  - 封装实体的域对象

- 实例化bean的三种方法
  - 构造方法（常用）
    - 无参构造方法如果不存在将会抛出异常
  - 静态工厂（了解）
  - 实例工厂（了解）
  - 使用**FactoryBean**实例化bean(重要！！！)
    - 改为非单例：在实现的时候将isSingleton函数返回false

#### bean与IoC容器

- bean生命周期
  - 生命周期
    1. 初始化容器
       1. 创建对象（内存分配）
       2. 执行构造方法
       3. 执行属性注入（set操作）
       4. 执行bean初始化方法
    2. 使用bean
       1. 执行业务操作
    3. 关闭/销毁容器
       1. 执行bean销毁方法
  - 初始化和销毁
    - 两种方法
      - 在配置文件中配置
        - init-method
        - destroy-method
      - 继承（推荐使用）
        - InitializingBean——afterPropertiesSet()
        - DisposableBean——destroy()
    - bean销毁时机
      - 容器关闭前触发bean的销毁
      - 关闭容器方法：
        - 手工关闭
        - 注册关闭钩子

#### bean与DI

- 依赖注入方式
  - setter注入(需提供相应的setter方法)
    - 简单类型（基本数据类型和String类型）
      - `<property name="***" value="***"/>`
    - 引用类型
      - `<property name="***" ref="***"/>`
  - 构造器注入（了解）
    - 简单类型
      - `<constructor-arg name="构造方法形参名" value="***">`
      - `<constructor-arg type="形参类型" value="***"/>`
      - `<constructor-arg index="形参顺序" value="***"/>`
    - 引用类型
      - `<constructor-arg name="构造方法形参名" ref="***"/>`
  - 方法选择
    - 强制依赖使用构造器进行，保证执行
    - 可选依赖使用setter注入进行，灵活性强
    - 自己开发的模块推荐使用setter注入
- 依赖自动装配
  - IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中
  - 自动装配方法（不能对简单类型操作）
    - 按名称（根据属性名，存在耦合）
    - 按类型（主要用这个，保证类唯一）
    - 按构造方法（不推荐）
    - 不启用自动装配
  - 自动装配优先级低于setter注入和构造器注入

- 集合注入
  - setter注入和`<property>`

#### Spring加载properties文件

"classpath*:*.properties"

### 注解开发（重要）

#### bean定义

- 注解开发定义bean
  - 使用@Component定义bean
  - 核心配置文件中通过组件扫描加载bean
  - 三个衍生注解：
    - @Controller： 用于表现层bean定义
    - @service：业务层
    - @Repository：数据层

- 纯注解开发
  - 使用Java类代替Spring核心配置文件
    - @Configuration注解用于设定当前类为配置类
    - @ComponentScan注解用于设定扫描路径，此注解只能添加一次，多个数据请使用数组格式

#### bean管理

- 使用@scope定义bean作用范围
- init和destroy
  - 给init方法增加注解@PostConstructor
  - 给destroy方法增加注解@PreDestroy

#### 依赖注入

- 自动装配
- @Autowired
  - 在类变量声明处使用（按类型）
    - 可以使用@Qualifier("***")指定名称（很少用）
  - 暴力反射，无需setter方法
  - 需要无参构造方法
- 简单类型：@value
  - 可从properties中加载
  - @propertySource("不支持使用通配符")

#### 注入List

``` java
@Autowired
List<Validator> validators;
```

- Spring会自动把所有类型为Validator的Bean装配为一个List注入进来，每新增一个Validator类型，就自动被Spring装配到Validators中

#### 第三方bean管理

- 在其他配置类中定义@Bean方法
- 在主配置类中使用@import(***.class)来导入其他类

#### 第三方bean依赖注入

- 简单类型：成员变量
- 引用类型：方法形参

### 一些问题

- @Component 和 @Bean 的区别是什么？
  - 在Configuration中声明@Bean方法
  - 可以用@Bean方法创建第三方Bean，本质上@Bean方法就是工厂方法
  - ![](2023-07-04-17-54-40.png)

### 总结

![](2023-07-03-17-43-04.png)

## Spring整合MyBatis

## AOP

- 面向切面编程
- 在不惊动原始设计的基础上为其进行功能增强
- AOP核心本质是采用代理模式实现的

### 核心概念

- 连接点： 方法
- 切入点：匹配连接点的式子
  - 可以是具体的一个方法，也可以是匹配的多个方法
  - 切入点一定是连接点
- 通知：在切入点处执行的操作，也就是共性功能
  - 功能最终以方法的形式呈现
- 通知类：定义通知的类
- 切面：描述通知与切入点的对应关系
- 目标对象：原始功能去掉共性功能对应的类产生的对象，这种对象是无法直接完成最终工作的
- 代理：目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现

### 工作流程

- 通知类：
  - @Component
  - @Aspect
- 通知：
- 切入点：空参无返回值空函数体方法，@Pointcut("excution(...)")
- spring配置类，添加APO注解配置
  - @EnableAspectJAutoProxy

### AOP切入点表达式

- 切入点表达式标准格式：动作关键字（访问修饰符  返回值  包名.类名.方法名(参数)  异常名）
- 可以使用通配符
- 书写技巧：![](2023-07-04-15-00-54.png)

### AOP通知类型

- 前置通知：@Before
- 后置：@After
- 环绕(zhongyao)：@Around
  - 参数：ProceedingJoinPoint pjp
  - 调用原始操作：`pjp.proceed()`;不调用则不会执行目标对象的方法，可以起到隔离
  - 需要返回返回值Object
  - 需要抛出异常
- 返回后通知：@AfterReturning
- 抛出异常后通知：@AfterThrowing

### AOP通知获取数据

- 获取参数
  - 获取方法：
    - 除环绕外：JoinPoint.getArgs
    - 环绕：ProceedingJoinPoint.getArgs
  - 可以对参数进行处理

- 获取返回值
  - @Around
  - @AfterReturning

- 接受异常
  - @AfterThrowing

## 事务

- SPring事务作用：在数据层或业务层保障一系列的数据库操作同成功同失败
- Spring的事务管理是通过AOP实现的
- @Transactional

- 流程
  1. 在业务层接口上添加Spring事务管理
  2. 设置事务管理器
  3. 开启注解式事务驱动

### 事务角色

- 事务管理员：发起事务方
- 事务协调员：加入事务方

### 事务相关配置

- 可以配置回滚的异常

- 可以设置事务的开启：
  - ![](2023-07-04-17-48-02.png)

## SpringMVC

### 快速入门

1. 导入SpringMVC和servlet坐标
   - javax.servlet-api
   - spring-webmvc
2. 创建SpringMVC控制器类
3. 初始化SpringMVC环境
4. 初始化Servlet容器，加载SpringMVC环境

- 分工
  - SpringMVC控制表现层Bean
  - Spring控制的业务bean和功能bean
  - 可配置@ComponentScan的扫描路径

- 请求与响应：
  - @RequestMapping：定义请求访问路径
  - 请求参数
    - 普通参数：与形参对应，可使用@RequestParam绑定参数关系
    - POJO参数：请求参数名与形参对象属性名相同
    - 嵌套POJO参数：按层次结构写请求参数名
    - 数组参数：多个同名请求参数
    - 集合参数：@RequestParam，多个同名请求参数
    - json参数：再SpringMVCconfig配置类中开启@EnableWebMVC， 同时参数@RequestBody
    - 日期类型参数：@DateTimeFormat(pattern="")指定格式
  - 响应
    - 响应页面
    - 响应文本：方法加上@ResponseBody
    - 响应json数据：方法加上@ResponseBody，

### REST风格

- REST（Representational State Transfer）：表现形式状态转换
- ![](2023-07-05-15-58-07.png)
- 在@RequestMapping中定义method属性，参数通过指定@PathVariable,请求路径中也需修改(user/{id})
  - 可以进一步简化

## SSM整合

![](2023-07-06-16-24-29.png)

### 表现层数据封装

```json
{
  "@code": "末尾：0=异常，1=正常",
  "code": 20031,  
  "data": true,
  "msg": "数据查询失败，请重试！"
}
```

### 异常处理器

- 异常分类处理
- 在表现层处理
- 使用AOP思想

#### 项目异常处理

- 项目异常分类
  - 业务异常
  - 系统异常
  - 其他异常

## 拦截器

- 拦截器作用：
  - 在指定方法调用前后执行预先设定的代码
  - 阻止原始方法的执行
- 拦截器和过滤器区别
  - 归属不同：Filter属于servlet技术，Interceptor属于SpringMVC技术
  - 拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强

- 方法
  - preHandle：false不放行
  - postHandle
  - afterCompletion

- 参数：
  - handler：执行的方法的包装
  - ModelAndView：SpringMVC页面跳转的相关数据
  - exception

- 多拦截器执行顺序：![](2023-07-06-19-28-36.png)

## SpringBoot

- 起步依赖：starter
- 启动方式：启动引导类的main方法
- 配置文件：`application.*`
  - properties（优先级最高）
  - yml(主要，次高)
  - yaml

- 配置文件的作用:修改SpringBoot自动配置的默认值(SpringBoot在底层都给我们自动配置好的默认值);

### 创建方式

1. 在线创建：<https://start.spring.io/>
2. 

### yaml

- yaml语法规则：![](2023-07-07-15-07-49.png)

- 读取配置信息
  - 使用@value读取单个数据
  - @Autowired修饰的Environment类
  - 可以通过读取配置信息构造domain类的bean

### 多环境

- 配置
  - yml：【---】分隔环境
  - properties：文件分隔环境

- 命令行切换：
  - --spring.profiles.active=test
  - --server.port=88

### 配置文件分类

![](image.png.png)

## MyBatisPlus

- Dao实现BaseMapper<>

- lombok：jar包，帮助快速开发实体类
  - @Data 注解：用于自动生成类的所有常规方法，例如 Getter、Setter、equals、hashCode 和 toString 等
- 分页查询需要实现分页拦截器
- Wrapper接口封装了复杂条件查询
  - 非空查询：null处理
  - 查询投影
- 字段映射
  - @TableField
  - @TableName
  
- ID生成策略控制
  - @TableID
  - 默认是ASSIGN_ID

- 逻辑删除：为数据设置可用状态字段，数据会保留
  - @TableLogic

- 乐观锁
  - 乐观锁拦截器

- 代码生成器