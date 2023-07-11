# web

## html

>存在加载顺序：自顶向下

## JavaScript

### BOM

- Browser Object Model(浏览器对象模型)
  - 将各个浏览器的各个组成部分封装成对象
- 组成
  - Navigator:浏览器对象,对应着浏览器
  - **Window**:窗口对象,对应着标签页
    - 包含DOM(Document)对象
  - Location:地址栏对象,对应着url
  - History:历史记录对象,记录改标签页的历史记录,实现前进后退功能
  - Screen:显示器对象(不重要)

1. Window:窗口对象
    - 创建
    - 方法
      1. 与弹出框相关的方法
         1. alert():显示包含一段信息和一个确认按钮的窗口
         2. confirm():显示带有一段信息和一个确认（true）按钮、一个取消（false）按钮的窗口
         3. prompt():显示可提示用户输入的窗口，返回值为用户输入的值
      2. 与开发有关的方法
         1. close()：关闭当前浏览器窗口。
         2. open()：打开一个新的浏览器窗口。参数为 url
      3. 定时器方法
         1. setTimeOut():在指定毫秒数后调用函数或计算表达式
            1. 参数
               1. js代码片段或方法对象
               2. 毫秒数
            2. 返回值：唯一编号，标识timeout
         2. clearTimeOut():取消由setTimeOut()设定的timeout
         3. setInterval():按照指定周期来调用函数或计算表达式
         4. clearInterval():取消 setInterval()设定的timeout
    - 属性
      1. 获取其他BOM对象：history, navigator, location, screen
      2. 获取DOM对象：document
    - 特点
      - 无须创建可以直接使用window对象。 window.方法名()
      - window引用可以省略，直接使用  方法名()

2. location对象
   1. 创建（获取）：window.location, location
   2. 方法: reload():重新加载当前文件
   3. 属性： href()：设置或返回完整的url

3. history对象
   1. 创建（获取）：通过window的属性
   2. 方法：
      1. back():以前的页面
      2. forword():更新的页面
      3. go():参数为正数为前进的历史记录数目，负数反之
   3. 属性： length：当前窗口历史记录的url数目

### DOM

- Document Object Model 文档对象模型
  - 将标记语言文档的各个组成部分封装为对象。可以使用这些对象对标记语言文档进行CRUD的动态操作
  - DOM树

- W3C DOM 标准被分为 3 个不同的部分：
  - 核心 DOM - 针对任何结构化文档的标准模型
    - Document：文档对象
    - Element：属性对象
    - Attribute：属性对象
    - Text：文本对象
    - Comment：注释对象
    - node：其他五个的父对象
  - XML DOM - 针对 XML 文档的标准模型
  - HTML DOM - 针对 HTML 文档的标准模型

- 核心DOM
  - Document：文档对象
    1. 创建（获取）：在html中使用window的属性获取
    2. 方法：
       1. 获取Element对象
          1. getElementByID():id
          2. getElementByTagName():元素名称
          3. getElementByClassName():Class属性值
          4. getElementByName():Name属性值
       2. 创建其他DOM对象

  - Element：属性对象
    1. 获取：通过document方法
    2. 方法：
       1. removeAttribute():移除属性
       2. setAttribute()：设置属性
  - node：其他五个的父对象
    - 特点：所有dom对象均可视为一个node
    - 方法：
      - CURD dom树：
        - appendChild()
        - removeChild()
        - replaceChild()

- HTML DOM
  1. 标签体的设置和获取：innerHTML
  2. 使用HTML元素对象的属性
  3. 控制样式
- 功能控制html文档的内容
- 代码:获取页面标签(元素)对象 Element
  - `document.getElementById("id值")`:通过元素id获取元素对象
    >注意:html元素从上往下加载

- 操作Element对象:
  - 设置属性值
  - 修改标签体内容
    - 属性:innerHTML

### 事件

- 功能:某些组件被执行后某些操作后,触发某些代码的执行
- 绑定事件:
  - 在html标签上指定事件的属性(操作),属性值就是js代码(完整语句,记得分号)
    - 如: onclick = "fun();" (直接调用)
  - 通过js获取元素对象,指定事件属性,设置一个函数
    - 如: `light.onclick = fun;` 在script中执行,函数后不加括号(事件绑定)

## bootstrap

- web前端的框架

## XML

- 概念： extensible markup language（可扩展标记语言）
  - 可扩展：标签自定义的
  - 标记语言：基于标签的语言

- 功能
  - 存储数据：
    - 配置文件
    - 在网络传输文件

- 与html语法类似，但更严格

### 快速入门

- 基本语法
  - 第一行必须定义为文档声明
  - xml文档中有且只有一个根标签，其属性值必须用引号引起来（单双均可
  - 标签必须被关闭
  - 标签区分大小写

- 组成部分
  - 文档说明
    - 格式： <?xml 属性列表 ?>
      - 属性列表：
        - version： 版本号，必须的（1.0）
        - encoding： 编码方式
        - standalone：是否独立
          - yes：不依赖于其他文件
          - no
  - 指令（了解）：结合css
  - 标签：标签名称自定义
  - 属性：id属性值唯一
  - 文本
    - CDATA区：在该区域的数据会被原样展示
      - 格式： <![CDATA[数据]]>

### 约束：规定xml文档的编写规则

- 分类：
  - DTD：一种简单的约束技术
  - Scheme：复杂的

- DTD：
  - 引入dtd文档到xml文档中
    - 内部dtd：将约束定义在xml文档中
    - 外部dtd：外部的dtd文件中
      - 本地   <!DOCTYPE 根标签名 SYSTEM "dtd文件位置">
      - 网络   <!DOCTYPE 根标签名 PUBLIC  “dtd文件名” "dtd文件位置URL">

- Scheme：确实很复杂

- 解析
  - DOM（服务端：将标记语言文档一次性读入内存，形成一颗dom树
    - 优点：操作方便，可以对文档进行所有的CRUD操作
    - 缺点：内存消耗大
  - SAX（移动端：逐行读取，基于事件驱动
    - 优点：不占内存
    - 缺点：只能读取，不能增删改

### xml解析器

- 常见解析器
  - JAXP：sun公司提供的
  - DOM4J：优秀
  - Jsoup：
  - PULL：安卓内置的，SAX

- Jsoup：
  - 快速入门：
    1. 导入jar包
    2. 获取Document对象
    3. 获取对应标签ELement对象
    4. 获取数据
  - 对象的使用：
    1. Jsoup：工具类，可以解析html文档或xml文档，返回Document
       1. parse：用于解析，很多重载方法
    2. Document：文档对象
       1. 主要用于获取Elements对象
    3. Elements： 元素ELement对象的集合
    4. Element：
       1. 获取子元素对象
       2. 获取属性值
       3. 获取文本内容
    5. Node：节点对象
       1. 是Document和Element的父类
  - 快捷查询方式：
    - selector：
    - Xpath：XMLl路径语言
      - 使用Jsoup的Xpath需要额外导入jar包

## HTTP

### 请求消息

- 请求行
  - 请求方式 请求url 请求协议/版本
  - GET /login.html HTTP/1.1
  - 请求方式：7种，常用2种
    - GET：
      - 请求参数在请求行中，在url后
      - 请求的url长度有限
      - 不太安全
    - POST
      - 请求参数在请求体中
      - url长度没限制
      - 相对安全
- 请求头
  - 请求头名称：请求头值
    1. User-Agent：浏览器版本信息
    2. Referer：告诉服务器，当前请求从哪里来
       1. 防盗链（可以用于认证）
       2. 用于统计
- 请求空行
  - 空行
- 请求体（正文）
  - 封装POST请求消息的请求参数的

### 响应消息

1. 响应行
   1. 组成：协议/版本 响应状态码 状态码描述
2. 响应头
   1. 格式：头名称：值
   2. 常见响应头：
      1. Content——Type：响应体数据格式和编码方式
      2. Content-disposition:以什么格式打开响应体
         1. in-line：默认值，当前页面打开
         2. attachment：以附件形式打开响应体。文件下载
3. 响应空行
4. 响应体

## AJAX

- ASynchronous JavaScript And XML
- 可以实现网页部分更新

- 实现方式：
  - 原生的JS实现方式（了解）
  - JQeury实现方式
    - $.ajax()
    - $.get()
    - $.post()

## nginx

- 高性能的http服务器/反向代理服务器以及电子邮件代理服务器
- 可以做静态网站的服务器
- 应用：
  - http服务器
  - 虚拟主机：在同一个服务器上部署多个项目
  - 反向代理/负载均衡
    - 反向代理：服务器端的代理，可以方便进行负载均衡