# QT使用

## 项目说明

- .pro文件是项目文件,其中包含了项目的相关说明
- .ui文件是设计师设计的界面对应的界面文件

## Q_OBJECT

只有继承了QObject类的类,才具有信号槽能力.凡是QObject类（不管是直接子类还是间接子类），都应该在**第一行代码**写上**Q_OBJECT**。不管是不是使用信号槽，都应该添加这个宏。这个宏的展开将为我们的类提供信号槽机制、国际化机制以及 Qt 提供的不基于 C++ RTTI 的反射能力。  
由于moc( Meta-Object Compiler，也就是“元对象编译器”)只处理头文件中的标记了Q_OBJECT的类声明,所以应将这个宏放在头文件.

## tr()函数

用于实现程序的多语言翻译，tr最终是通过调用QCoreApplication::translate来实现翻译功能的。  
***建议程序中所有需要显示到界面上的字符串都使用tr()函数括起来。***

1. 用tr包住英文(最最推荐的用法，源码英文，然后提供英文到其他语言的翻译包)
2. 用tr包住中文(源码用中文，然后提供中文到其他语言的翻译包)

## qRegisterMetaType(自定义类型信号槽)的使用

自定义的类型想使用signal/slot来传递,需要进行以下步骤(以自定义TextAndNumber类型为例）:  

``` c++
1. 自定一种类型，在这个类型的顶部包含：#include <QMetaType>
2. 在类型定义完成后，加入声明：Q_DECLARE_METATYPE(TextAndNumber);
3. 在main()函数中注册这种类型：qRegisterMetaType<TextAndNumber>("TextAndNumber");
```

## QAction

提供了抽象的用户界面action，这些action可以被放置在窗口部件中。  应用程序可以通过菜单，工具栏按钮以及键盘快捷键来调用通用的命令。由于用户期望每个命令都能以相同的方式执行，而不管命令所使用的用户界面，这个时候使用action来表示这些命令就显得十分有用。

## QTextCodec

主要用于非Unicode编码数据与Unicode编码数据之间的转换
