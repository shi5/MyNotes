- SpringBoot可理解为一个可拔插式的插件，简化以前繁杂的配置，无需过多的配置和依赖，可以合并多个依赖，并且将其统一集成到一个 Starter 中。
- 所有依赖模块都遵循着约定成俗的默认配置，并允许我们调整这些配置，即遵循“约定大于配置”的理念
- 可用于实现公共组件库

##  Starter命名

- 官方： spring-boot-starter-{name}，如spring-boot-starter-web，spring-boot-starter-activemq 等
- 非官方：{name}-spring-boot-starter，如mybatis-spring-boot-starter。

## 自定义SpringBoot Starter

1. 创建springboot项目

2. Pom 依赖配置：除了项目的基本信息和父类引用，只需引用 spring-boot-starter 即可。

3. 自动配置类 
	1. 创建一个注册为 Spring Bean 的 Service 类，提供一个 sayHello 方法以供后续测试。 

	```java
	public class ServiceBean {
	    public String sayHello(String name) {
	        return String.format("Hello World, %s", name);
	    }
	}
	```

	2. 创建**自动配置类**，将 ServiceBean 进行声明 Bean，等待扫描后交付给 Spring Ioc 容器。

	```java
	@Configuration
	public class AutoConfigurationTest {
	
	    @Bean
	    public ServiceBean getServiceBean() {
	        return new ServiceBean();
	    }
	}
	```

4.  spring.factories
	- 项目` Resources` 目录下新建 `META-INF` 文件夹，然后创建 `spring.factories` 文件。
	- 文件中定义` Autoconfigure` 指定配置类为自动装配的配置。 
	```xml
	org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	  cn.machen.starter.demospringbootstarter.AutoConfigurationTest
	```

5. 打包仓库
	- 提供的Starter要被其他项目使用，需要将项目打包后发到仓库中
	- maven常用指令
		- **package**：该命令完成了项目编译、单元测试、打包功能三个过程。
		- **install**：在 package 命令的前提下新增一个步骤，将新打好的包部署到本地 Maven 仓库。
		- **deploy**：在 install 命令的前提下新增一个步骤，将新打的包部署到远端仓库（相当于本地和远端仓库同时部署一份）。

6. 测试
	- 其他项目引用： 
	```xml
	<dependency>
		<groupId>cn.machen.starter</groupId>
		<artifactId>demo-spring-boot-starter</artifactId>
		<version>${project.version}</version>
	</dependency>
	```

## 可插拔 Starter

> 自定义注解+条件注解

1. 首先在Starter项目中创建自定义注解。 
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface EnableAutoConfigTest { }
```

2. AutoConfigurationTest 类中添加条件注解，然后重新打包至本地仓库。
```java
@Configuration
@ConditionalOnBean(annotation = EnableAutoConfigTest.class)
public class AutoConfigurationTest {

    @Bean
    public ServiceBean getServiceBean() {
        return new ServiceBean();
    }
}
```

3. 在主程序引用 @EnableAutoConfigTest 注解 。
```java
@EnableAutoConfigTest
@SpringBootApplication
public class DemoTestSpringBootStarterApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoTestSpringBootStarterApplication.class, args);
    }
}
```

## 配置元数据

> 项目配置文件的元数据

1. 通过建立 META-INF/spring-configuration-metadata.json 文件，开发者手动配置。

2. 还有一种是通过注解 @ConfigurationProperties 方式自动生成。
	1. 提供引用 pom.xml 中加入 spring-boot-configuration-processor 包。
	2. 编写 Properties 配置类，以 Swagger 示例。
	```java
	@Data
	@Configuration
	@ConfigurationProperties(prefix = "swagger")
	public class SwaggerProperties {
	
	    /**
	     * 文档扫描包路径
	     */
	    private String basePackage = "";
	
	    /**
	     * title 示例: 订单创建接口
	     */
	    private String title = "平台系统接口详情";
	
	    /**
	     * 服务条款网址
	     */
	    private String termsOfServiceUrl = "https://www.xxxx.com/";
	
	    /**
	     * 版本号
	     */
	    private String version = "V_1.0.0";
	
	}
	```
	
	3. 最后执行打包命令，更新本地仓库 Jar 包 ： `mvn clean install`

最终效果：
![[Pasted image 20231026151231.png]]
