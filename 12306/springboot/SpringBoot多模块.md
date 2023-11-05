## 流程

1. 创建springboot项目：spring initializer
	- 
2. 构建子module ：右键springboot项目创建module
3. 建立父子module依赖：
	1. 修改parent项目packaging

		```xml
		<packaging>pom</packaging>
		
		<modules>
		    <module>bootdemo-remote-api</module>
		</modules>
		```

	1. 修改module Pom.xml信息
	2. 删除不必要文件：parent 项目下 src 包

> `dependencies` 和 `dependencyManagement` 标签区别：
> **dependencies** ： 如果 Parent 项目中使用 `dependencies` 标签，标签内的依赖默认传递子 Module，不用子 Module 进行显示书写依赖。
> **dependencyManagement**：`dependencyManagement` 与`dependencies` 不同的是，标签内的依赖不会默认传递子 Module，其作用只是为了统一版本声明。如果子 Module 依赖 Parent 项目中 `dependencyManagement` 标签内的 pom 依赖，需要显示在子 Module 的 pom 文件中进行书写。