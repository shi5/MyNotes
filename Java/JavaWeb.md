# JavaWeb

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