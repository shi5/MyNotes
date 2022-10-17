# QT使用

## 项目说明

- .pro文件是项目文件,其中包含了项目的相关说明
- .ui文件是设计师设计的界面对应的界面文件

## Qt 项目视图组(Item Views)与项目控件组(Item Widget)

两者的关系:
Item Views（Model-Based）类内的控件是Item Widgets（Item-Based）内对应控件的父类，如QTreeWidget是从QTreeView派生的。

两者的区别：Item Views（Model-Based）的对象进行数据操作相对比较复杂，但处理及展示大数据量时性能高；Item Widgets的数据操作比较简单，但处理及展示大数据量时性能相对低。Item Widgets在开发中没有Item Views灵活，实际上Item Widgets就是在Item Views的基础上绑定了一个默认的存储并提供了相关方法。

总结：Item Widgets是简化版的控件，使用简单，但无法对item进行比较深度的定制；Item View是高级版控件，可以做更多的定制，遵循的是model/view模式。

## Q_OBJECT

只有继承了QObject类的类,才具有信号槽能力.凡是QObject类（不管是直接子类还是间接子类），都应该在**第一行代码**写上**Q_OBJECT**。不管是不是使用信号槽，都应该添加这个宏。这个宏的展开将为我们的类提供信号槽机制、国际化机制以及 Qt 提供的不基于 C++ RTTI 的反射能力。  
由于moc( Meta-Object Compiler，也就是“元对象编译器”)只处理头文件中的标记了Q_OBJECT的类声明,所以应将这个宏放在**头文件**.

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

## 内存管理

在Qt中销毁父对象的时候会自动销毁子对象(不是继承,是在创建子对象是指定parents)

---

## QT多线程

- 默认的线程在QT中称之为窗口线程,也叫**主线程** ,负责窗口事件处理或者窗口控件数据的更新.  
- 子线程负责后台的业务逻辑处理,子线程中不能对窗口对象做任何操作,这些事情需要交给窗口线程处理.  
- 主线程和子线程之间如果要进行数据的传递,需要使用QT中的信号槽机制.

### 1. 线程类:QThread

QThread是QT中的一个线程类,通过这个类就可以创建子线程了。

1. 线程退出

    ```cpp
    //退出线程,停止底层事件循环
    void QThread::exit(int returnCode = 0);//退出线程的工作函数
    void QThread::wait(unsigned long time = ULONG_MAX);//等待任务完成,然后退出线程
    ```

2. 信号槽函数

    ```cpp
    slot:
        void QThread::quit();//和exit()一样,但它是槽函数
        void QThread::start(Priority priority = InheritPriority);//启动子线程
        void QThread::terminate();//线程退出,可能会立马终止线程,一般不使用
    signal:
        void QThread::finished();//任务函数中的处理逻辑执行完毕
        void QThread::started();//开始工作前发出,不常用
    ```

3. 任务处理函数

    ```cpp
        [virtual protected] void QThread::run();//处理任务
    ```

### 2. 使用方法1

1. 创建一个线程类的子类,让其继承Qt中的线程类QThread
2. 重写父类的run()方法.
3. 在主线程中创建子线程对象
4. 启动子线程,调用start()方法

> 不能在类的外部调用run()方法启动子线程,在外部调用start()相当于让run()开始运行  
> 注意事项:  
>
> - 在QT中子线程不允许操作程序中的窗口类型对象,如果操作了程序就会挂
> - 只有主线程才能操作程序中的窗口对象

### 3. 使用方法2

弥补了第一种的缺点,用起来更加灵活,但写起来相对复杂一些,具体步骤如下:

1. 创建一个新的类,让这个类从QObject派生

    ```cpp
        class MyWork:public QObject
        {
            ......
        }
    ```

2. 在这个类中添加一个公共成员函数,函数体就是我们要执行的业务逻辑

    ```cpp
        class MyWork:public QObject
        {
        pubilc:
            ......
            //函数名自己指定,参数可以根据实际需求添加
            void working();
        }
    ```

3. 在主线程创建一个QThread对象,这就是子线程的对象

    ```cpp
    QThread* sub = new QThread;
    ```

4. 在主线程中创建工作的类对象(千万不要给创建的对象指定对象)

   ```cpp
   MyWork* work = new MyWork(this);//error
   MyWork* work = new MyWork;//ok
   ```

5. 将MyWork对象移动到创建的子线程对象中,需要调用QObject类提供的mooveToThread()方法

   ```cpp
   //如果work指定了父对象,该函数调用失败
    work->moveToThread(sub);
    ```

6. 启动子线程,调用start(),这时候线程启动,但是移动到线程中的对象并没有工作

7. 调用MyWork类对象的工作函数,让这个函数开始执行,这时候是在移动到的那个子线程中运行的

>该方法的优势
>
> - 使用灵活,任务对象可以切换不同的线程
> - 多个任务对象可以指定同一个线程
> - 任务对象可以调用不同的工作函数,工作函数可以带有参数

### 线程资源释放

- 创建线程对象时指定父对象(该方法结束程序时貌似会报错QThread：Destroyed while thread is still running)
- 在当前窗口的析构函数中析构掉(1.quit(); 2.wait(); 3.deleteLater())

### 线程池

一般我们不需要再QT程序中创建线程池对象,直接使用Qt为每个应用程序提供的线程池全局对象即可.得到的线程池对象之后,调用start()方法就可以将一个任务添加到线程池中,这个任务就可以被线程池内部的线程池处理掉了.

```cpp
//每个QT应用程序中有一个全局的线程池对象,通过这个给函数直接访问这个对象
static QThreadPool *QThreadPool::globalInstance90;

//给线程池添加任务,任务需继承QRunnable类型
//如果没有空闲线程了,则会放入任务队列中,等待线程处理
void QThreadPool::start(Qrunnable* runnable, int priority = 0);
```
