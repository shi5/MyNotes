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
         2. int executeUpdate(String sql) ：执行DML
   4. ResultSet:结果集对象
   5. PreparedStatement:执行sql的对象