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
