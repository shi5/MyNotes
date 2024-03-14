# JavaWeb

## 三层框架（单体架构）

![](2023-06-04-17-32-36.png)

## JDBC

1. 概念：Java DataBase Connectivity，java语言操作数据库
    本质：官方定义的操作所有关系型数据库的规则（接口）。各个数据库厂商实现这套接口，提供数据库驱动的jar包。我们可以使用这套接口（JDBC）编程，但真正执行的代码时驱动jar包中的实现类

2. 快速入门：
   - 步骤
     1. 导入驱动jar包
        1. 复制jar包到项目的libs目录(自己建)下
        2. 右键-->Add As Library
     2. 注册驱动
     3. 获取数据库连接对象 Connection
     4. 定义sql
     5. 获取执行sql语句的对象 Statement
     6. 执行sql，接受返回结果
     7. 处理结果
     8. 释放资源

    ```java
    Class.forName("com.mysql.cj.jdbc.Driver");
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/bookshop", "root", "1874155789");
    String sql = "update book set price = 100 where id = 13";
    Statement stmt = conn.createStatement();
    int count = stmt.executeUpdate(sql);
    System.out.println(count);
    stmt.close();
    conn.close();
    ```

    - 注意事项：
      - 资源释放最好放在finally中处理
      - 资源释放之前需要判断是否为null

3. 详解各个对象
   1. DriverManager:驱动管理对象
      - 功能
        1. 注册驱动:告诉程序该使用哪一个数据库驱动jar
            mysql5之后可以不用自己手动注册(但建议写上)

           ```java
           static void registerDriver(Driver driver)//注册与给定的驱动程DriverManager
           Class.forName("com.mysql.cj.jdbc.Driver");//写代码使用
           //通过查看源码发现:在com.mysql.cj.jdbc.Driver类中存在静态代码块执行registerDriver函数
           ```

        2. 获取数据库连接
           - 方法:static Connection getConnection(String url, String user, String password)
           - 参数
             - url:指定连接的路径
               - 语法:jdbc:mysql://ip地址:端口号/数据库名称
               - 例子:jdbc:mysql://localhost:3306/bookshop
               - 细节:如果连接的时本机MySQL服务器,且MySQL默认端口为3306,那么url可以简写为jdbc:mysql:///数据库名称
             - user:用户名
             - password:密码
   2. Connection:数据库连接对象
      1. 获取执行sql语句的对象
         - Statement createStatement()
         - PreparedStatement createPreparedStatement(String sql)
      2. 管理事务:
         - 开启事务:void setAutoCommit(boolean autoCommit):调用该方法设置参数为false,即开启事务
         - 关闭事务:void commit();
         - 回滚事务:void rollback();
   3. Statement:执行sql的对象
      1. 执行sql
         1. boolean execute(String sql) ：可以执行任意的sql(不常用)
         2. int executeUpdate(String sql) ：执行DML(insert、update、delete)语句、DDL(create、alter、drop)语句（DDL不常用）
            1. 返回值：语句影响的表的行数，可以通过这个影响的行数判定DML语句是否执行成功(DDL返回0)
         3. ResultSet executeQuery(String sql)：执行DQL（select）语句
   4. ResultSet:结果集对象，封装查询结果 （也需要释放资源）
      - boolean next() : 游标（指向表中的行）向下移动一行（游标默认指向**列名行**），返回的bool值表示是否指向最后一行的末尾（是否已经结束）
      - getXxx(参数) : 获取游标指向的行的一列数据
         - Xxx：代表数据类型  如： int getInt()，String getString
         - 参数
           - int：代表列的编号，从1开始
           - String：代表列的名称
      - 可以定义一个类来接收每一行
   5. PreparedStatement:执行sql的对象，实现预编译的SQL
      1. SQL注入问题：在拼接sql是，有一些sql的特殊关键字参与字符串的拼接，造成安全性问题
         如：输入密码:  'a' or 'a' = 'a
      2. 解决sql注入问题：使用PreparedStatement对象解决
      3. 预编译的SQL：参数使用？作为占位符
      4. 步骤：
         1. 导入驱动jar包
            1. 复制jar包到项目的libs目录(自己建)下
            2. 右键-->Add As Library
         2. 注册驱动
         3. 获取数据库连接对象 Connection
         4. 定义sql
            - sql的参数使用?作为占位符。 如： `select * from user where username = ? and password = ?`
         5. 获取执行sql语句的对象  PreparedStatement conn.prepareStatement(String sql)
         6. 给?赋值：
            - 方法： setXxx（参数1，参数2）
              - 参数1：?的位置编号，从1开始
              - 参数2：?的值
         7. 执行sql（不需要传递sql），接受返回结果
         8. 处理结果
         9. 释放资源
      5. 使用PreparedStatement而不是Statement

## 抽取JDBC工具类 : JDBCUtils

- 目的：上述过程有很多重复操作，进行抽取来简化书写,实现代码复用
- 分析：
  - 抽取 注册驱动 (放在**静态代码块**处理)
  - 抽取方法 获取数据库连接
    - 使用配置文件来定义 url、user、password （**静态代码块**来实现读取配置文件）
  - 抽取方法 释放资源
  > 都是static方法

## JDBC控制事务

1. 事务：一个包含多个步骤的业务操作。
2. 操作：
   1. 开启事务
   2. 提交事务
   3. 回滚事务
3. 使用Connection对象来管理事务
   - 开启事务:void setAutoCommit(boolean autoCommit):调用该方法设置参数为false,即开启事务
     - 执行sql前开启
   - 关闭事务:void commit();
     - 当所有sql执行完关闭
   - 回滚事务:void rollback();
     - catch中回滚事务，需要判断connection是否为null

## 数据库连接池

- 定义：存放数据库连接的容器
- 优势：节约资源、高效
- 实现：
  1. 标准接口：DataSource   javax.sql包下
     1. 方法：
        1. 获取连接：getConnection();
        2. 归还连接：Connection.close();  如果Connection是从连接池中调用的，那么调用close()方法则不会关闭连接，而是归还连接
  2. 由数据库厂商来实现
     1. C3P0：数据库连接池技术
     2. Druid：同上，由阿里巴巴提供

- C3P0
  - 步骤
    1. 导入jar包（两个）  c3p0-0.9.5.2.jar   mchange-commons-java-0.2.12.jar
       1. 不要忘记导入数据库jar包
    2. 定义配置文件：
       - 名称： c3p0.properties 或者 c3p0-config.xml
       - 路径：直接将文件放在src目录下即可
    3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
    4. 获取连接： getConnection

- ***Druid***(最常使用)
   1. 步骤
      1. 导入jar包 druid-1.0.9.jar
      2. 定义配置文件(需要手动加载)
         1. 是properties形式的
         2. 可以叫任意名称，可以放在任意目录下
      3. 加载配置文件
      4. 获取数据库连接池对象：通过工厂类来获取： `DruidDateSourceFactory.createDataSource(pro);`
      5. 获取连接： getConnection
   2. 定义工具类
      1. 定义一个类JDBCUtils
      2. 提供静态代码块加载配置文件，初始化连接池
      3. 提供方法
         1. 获取连接方法：通过数据库连接池获取连接
         2. 释放资源
         3. 获取连接池的方法

## Spring JDBC

- SPring提供一个JDBCTemplate对象简化JDBC的开发
- 步骤:
   1. 导入jar包
   2. 创建jdbcTempalte对象,依赖于数据源DataSource
      `JdbcTemplate template = new JdbcTemplate(ds)`
   3. 调用jdbcTemplate的方法来完成CRUD操作
      1. update():执行DML语句。增、删、改语句
      2. queryForMap():查询结果并将结果集封装成map集合
         1. 注意：这个方法查询的结果集长度只能为1（一个map只能包含一行，key为列名，value为值）
      3. queryForList()：查询结果并将结果集封装成list集合
      4. query()：查询结果并将结果集封装成JavaBean对象
         1. 参数：RowMapper
            1. 一般我们使用BeanPropertyRowMapper实现类，完成到JavaBean的自动封装
            2. `new BeanPropertyRowMapper<类型>(类型.class)
      5. queryForObject：查询结果并将结果集封装成对象
         1. 一般用于聚合函数的查询

- 不需要获取连接、释放资源，JdbcTemplate会自动执行

## Tomcat

- 部署项目的方式：
  1. 直接将项目放在webapps文件夹里
     - 将项目打包成一个war包，再将war包放入webapps文件夹下面，war包会自动解压缩，删除也只需删除war包
  2. 在conf/server.xml文件
     - 在<Host>标签体中配置
  3. 在conf/Catalina/localhost创建任意名称的xml文件，在文件中编写<省略>，虚拟名称就是文件的名称

- 静态项目和动态项目
  - Java的动态项目
    - 项目的根目录
      - WEB-INF目录
        - web.xml：web项目的核心配置文件
        - classes目录：放置字节码文件的目录
        - lib目录：放置依赖的jar包

## Servlet

- 概念：运行在服务端的小程序
  - Servlet就是一个接口，定义了java类被tomcat所识别的规则
  - 自定一个类，实现Servlet接口，复写方法

- 快速入门：
  1. 创建Java项目
  2. 定义一个Java类实现Servlet接口
  3. 实现接口中的抽象方法
  4. 在web.xml文件中配置Servlet

- 执行原理：![](2023-05-29-21-50-07.png)

- Servlet中的生命周期：
  1. 被创建：执行init方法，只执行一次
     - 什么时候被创建？
       - 第一次被访问时，创建
       - 在服务器启动时，创建
       - <load-on-startup>设置为负数（默认-1）则为第一次被访问时
     - Servlet是单例的
       - 多个用户同时访问时，可能存在线程安全问题
       - 解决：尽量不要在Servlet中定义成员变量，即使定义了成员变量，也尽量不要修改值
  2. 提供服务：执行service方法，执行多次
     - 每次访问Servlet时，service方法都会被调用一次
  3. 被销毁：执行destroy方法，只执行一次
     - 只有服务器正常关闭时，才会执行destroy方法
     - 在Servlet销毁之前调用

- Servlet3.0
  - 好处
    - 支持注解配置，可以不需要web.xml
  - 步骤：
    1. 在类上使用@WebServlet注解，进行配置
       - @WebServlet("资源路径")

- 体系结构
  - Servlet -- GenericServlet 和 HttpServlet
    - GenericServlet：将Servlet接口中的方法做了默认空实现，只将service()方法抽象
    - HttpServlet：对http协议封装
      1. 定义类继承HttpServlet
      2. 复写doGet或doPost方法

- Servlet相关配置
  - urlpartten：Servlet访问路径（可以多个）
    - 路径定义规则
      - /xxx（*代表通配符）
      - /xxx/xxx
      - *.do

## Request

1. request对象和response对象由服务器创建

2. request对象继承体系结构

3. request功能
   1. 获取请求消息数据
   2. 其他功能
      1. 获取请求参数（通用方式）：不论get还是post
         1. 存在问题：中文乱码
            1. get：tomcat8已解决
            2. post：获取参数前，设置request的编码
      2. 请求转发：一种在服务器内部的资源跳转方式
         1. 方式：通过RequestDispatcher()
         2. 特点：
            1. 浏览器地址栏路径不会发生变化
            2. 只能转发到当前服务器内部资源中
            3. 转发是一次请求
      3. 共享数据
         1. 域对象：有作用范围的对象
         2. request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
            1. request.setAttribute()
            2. request.getAttribute()
            3. request.removeAttribute()
      4. 获取ServletContext
         1. request.getServletContext

4. BeanUtils工具类，简化数据封装（用于封装JavaBean,需要导入包
   1. JavaBean：标准的Java类，用于封装数据
   2. 概念
      1. 成员变量：
      2. 属性：setter和getter方法截取后的产物
         1. 例如：getUsername()的*Username*
   3. 方法：
      1. setProperty()  //操作属性，而不是成员变量
      2. getProperty()
      3. populate()：将map集合的键值对信息，封装到对应的JavaBean对象中

## response对象

- 功能：设置响应消息
  1. 设置响应行
     1. 设置状态吗：setStatus()
  2. 设置响应头:setHeader()
  3. 设置响应行:
     1. 使用步骤：
        1. 获取输出流
        2. 使用输出流，将数据输出到客户端浏览器

- 重定向：
  - response.sendRedirect()
  - 特点：
    - 地址栏发生变化
    - 重定向可以访问其他站点的资源
    - 重定向是两次请求，不能使用request对象共享数据
  - 路径写法：
    - 给客户端用，加虚拟目录
    - 给服务端用，不加虚拟目录
    - 虚拟目录最好通过动态获取

- 中文乱码
  - 获取流之前，设置流的默认编码
  - 默认为ISO-8859-1
  - 设置编码：response.setContentType("text/html;charset=utf-8")

## ServletContext对象

- 概念：代表整个web应用，可以和服务器通信
- 获取：
  1. 通过request对象获取
  2. 通过HttpServlet对象获取
- 功能：
  - 获取MIME类型：
    - MIME类型：在互联网通信过程中定义的一种文件数据类型
      - 格式：大类型/小类型   text/html   image/jpg
  - 域对象：共享数据
    - setAttribute()
    - getAttribute()
    - removeAttribute()
    - ServletContext对象范围：所有用户所有请求的数据
  - 获取文件的真实（服务器）路径
    - getRealPath()

## 会话技术

1. 会话：包含多次请求和响应
2. 功能：在一次会话的范围内的多次请求间，共享数据
3. 方式
   1. 客户端：Cookie
   2. 服务端：Session

### Cookie

- 细节
  - 一次可不可以发送多个cookie
    - 可以
    - 可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可
  - cookie在浏览器中保存多长时间
    - 默认情况，关闭浏览器即销毁cookie
    - 持久化存储：
      - setMaxAge(int seconds)
        - 正数：将Cookie数据写到硬盘的文件中，持久化存储
        - 负数：默认值
        - 零：删除cookie
  - cookie能不能存中文
    - 在tomcat8之前，cookie不能直接存储中文数据
    - 即使是tomcat8之后的版本，不支持特殊字符，建议使用URL编码存储，URL解码解析
  - cookie共享问题
    - 同一个tomcat服务器多个webapp项目之间的Cookie默认情况不能共享
      - 可以设置Cookie.setPath()来设置cookie的获取范围，默认情况为当前虚拟目录
      - 可以设置为setPath("/")
    - 不同tomcat服务器间cookie共享
      - setDomain():如果设置一级域名相同，则可以共享
- 特点和作用
  1. 存储在客户端
  2. 单个cookie大小限制4Kb，以及同一个域名下的总cookie数限制20个
  3. 作用
     1. cookie用于存储少量不太敏感的数据
     2. 在不登陆的情况下，完成服务器对客户端的身份识别 

### session

- 在一次会话的多次请求中共享数据
- 获取HttpSession
  - setAttribute()
  - getAttribute()
  - removeAttribute()

- 原理
  - session是依赖cookie的
  - cookie ： JSSESSION = XXXXXXXX

- 细节
  1. 当客户端关闭后， 服务器不关闭，两次获取的session是否为同一个？
     - 默认情况不是
     - 可以使用cookie设置存活时间，实现持久化存储
  2. 当客户端不关闭后， 服务器关闭，两次获取的session是否为同一个？
     - 不是同一个，但是要确保数据不丢失
       - session的钝化：
         - 在服务器正常关闭之前，将session对象序列化到硬盘上
       - session的活化：
         - 在服务器启动后，将session文件转化为内存中的session对象即可
  3. session的失效时间
     1. 服务器关闭
     2. session对象调用invalidate()
     3. session默认失效时间30分钟
        1. 可以选择性配置修改

- 特点
  - session可以存储任意类型，任意大小的数据

- 与cookie的区别
  - session存储数据在服务器端，cookie在客户端
  - session没有数据大小限制，cookie有
  - session数据安全，cookie相对于不安全

## JSP

- 概念： Java Server pages，Java服务器端页面
  - 既可以定义html标签，又可以定义Java代码
  - 用于简化书写

- 原理
  - 本质上就是一个servlet

- 指令
  - 作用：用于配置JSP页面，导入资源文件
  - 格式：<%@ 指令名称 属性名1=属性值1 ... %>
  - 分类：
    - page    ：配置JSP页面
      - contentType
      - import：导包
      - errorPage：页面发生错误跳转到的页面
      - isErrorPage：表示当前页面是否是错误页面
    - include ：页面包含的，导入页面的资源文件
    - taglib  ：导入资源

- 注释：<%--   --%>

- JSP的脚本：JSP定义JAVA代码的方式
  1. <% 代码 %>：定义的Java代码，在service方法中，service方法可以定义什么，就可以定义什么
  2. <%！ 代码 %>：定义的Java代码，在jsp转换后的Java类的成员位置
  3. <%= 代码 %>：定义的Java代码，会输出到页面上。输出语句中可以定义什么，就可以定义什么

- JSP内置对象
  - 一共9个
    1. pageContext
    2. request
    3. response
    4. session
    5. application
    6. page
    7. out：字符输出流对象，可以将数据输出到页面上，和response.getWriter()类似
       1. response.getWriter()数据输出永远在out.write()之前。（建议只使用out）
    8. config
    9. exception

## EL表达式（expression language

- 作用：替换和简化JSP页面中的Java代码的编写
- 语法：${表达式}
- 注意：
  - jsp默认支持el表达式（可以忽略el表达式
- 功能：
  1. 运算
     1. empty：用于判断字符串、集合、数组对象是否为null且长度不为0  （not empty
  2. 获取值
     1. 只能从域对象中获取值
     2. 语法：
        1. ${域名称.键名}：从指定域中获取指定键的值
           1. pageScope       --> pageContext
           2. requestScope    --> request
           3. sessionScope    --> session
           4. applicationScope--> application
        2. ${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止
     3. 获取对象、list集合、map集合的值
  3. 隐式对象（共11个
     1. pageContext：获取jsp其他八个内置对象
        1. ${pageContext.request.contextPath}:动态获取虚拟目录

## JSTL标签（JavaServer Pages Tag Library

- 作用：替换和简化JSP页面中的Java代码
- 使用步骤
  1. 导入jstl相关的jar包
  2. 引入标签库：taglib指令： <%@ taglib %> （一般prefix为 c
  3. 使用标签

- 常用的标签
  - if（if语句）
    - 属性：
      - test（必须）：接受boolean表达式，一般会与el表达式结合
    - 注意：c:if没有else标签，只能再定义一个c:if
  - choose（switch语句）
    - choose、when、otherwise（自己查吧
  - forEach（for语句）
    - <c:forEach begin="1" end="10" var="i" step="1" varStatus="s">
    - 包含end
    - varStatus是循环状态对象，index（索引）、count（次数）

## Filter

- 当访问服务器的资源时，过滤器可以将请求拦截下来，完成一些特殊的功能
- 作用：
  - 一般用于完成通用的操作，如：登录验证，统一编码处理，敏感字符过滤...

- 快速入门：
  1. 创建Filter类(javax.servlet)
  2. 复写方法
  3. 拦截
     1. 配置拦截路径

- 过滤器细节
  - web.xml配置（和Servlet类似
  - 过滤器执行流程
    1. 拦截，对request增强
    2. 放行
    3. 收尾，对response增强
  - 过滤器配置
    - 拦截路径配置
      - 具体文件
      - 目录
      - 后缀名
      - 全部
    - 拦截方式配置：资源被访问的方式满足某种条件时，进行拦截
      - 注解配置
        - 设置dispatcherTypes属性
          - REQUEST：默认浏览器直接请求资源
          - FORWARD：转发访问资源
          - INCLUDE：包含访问资源
          - ERROR：错误跳转资源
          - ASYNC：异步访问资源
        - web.xml配置
  - 过滤器链（多个过滤器）
    - 先后顺序：
      1. 注解配置：按照类名的字符串顺序
      2. web.xml配置：

## Listener：监听器

- 事件监听机制
  - 事件
  - 事件源：事件发生的地方
  - 监听器：一个对象
  - 注册监听：将事件、事件源、监听器绑在一起（配置监听器）

- ServletContextListener：监听ServletContext对象的创建于销毁

## maven

- 目录结构
  - src：源码
    - main：主工程代码
      - java：主工程代码
      - resources：需要使用的资源文件
      - webapp：web项目的资源目录
    - test：测试代码
      - java：测试代码
      - resources：测试需要使用的资源文件
  - **pom.xml**:项目的核心配置文件

- 生命周期（maven项目）
  - 三套独立的生命周期
    - Clean Lifecycle
    - Default Lifecycle：编译、测试、打包、安装
    - site Lifecycle
    - >同一套生命周期中，执行后边的操作，会自动执行之前的所有操作

- 坐标：被Maven管理的资源的唯一标识
  - groupId：组织名称
  - artifactId：模块名称
  - version：版本号

- 分模块开发
- 依赖传递
  - 可选依赖：`<Optional>` ，隐藏当前工程所依赖的资源
  - 排除依赖：`<exclusion>`

- 聚合与继承（保证包版本统一）
  - 设计型模块
  - 聚合（快速构建）
    - 将多个模块组织成一个整体，同时进行项目的构建（同时编译、测试、打包、安装）
    - 有一个主工程`<packaging>`来整合所有模块`<moduls>`
    - 主工程`pom`格式打包
  - 继承（快速配置）
    - 父工程：`<parent>`
    - 父工程配置子工程可选依赖：`<dependecyManagement>`

- 属性
  - `<properties>`

- 版本管理
  - 工程版本：
    - SNAPSHOT（快照版本）
    - RELEASE（发布版本）
  - 发布版本：
    - alpha版本
    - beta版本
    - 纯数字版本

- 多环境开发
  - 配置多环境：`<profile>`，在其中设置properties
  - 使用多环境：`-P`
- 跳过测试
  - 可以指定跳过某些测试

- 私服：Nexus
  - 私服配置：maven本地仓库`setting.xml`进行配置，pom文件修改`<distributionManagement>

## MyBatis

- 核心配置文件
- sql映射文件
- mapper
  - 注解开发
  - sql映射文件