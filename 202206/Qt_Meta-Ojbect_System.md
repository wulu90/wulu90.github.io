# 元对象系统

元对象系统提供信号槽机制，运行时类型信息，动态属性系统

元对象系统基于
+ QObject基类
+ Q_OBJECT 宏，在类声明的private部分，使类可以使用元对象系统功能，如动态类型，信号，槽
+ moc 元对象编译器，

moc工具读取C++源文件，如果发现类的声明中包含Q_OBJECT宏，就会给每一个类生成包含元对象代码的另一个C++文件。

元对象系统有一下特性：
+ *singals and slots* 工具链，引入元对象系统的主要原因
+ *QObject::metaObject()* 返回关联的元对象
+ *QMetaObject::className()* 运行时返回类名字符串，无需C++编译器原生运行时类型信息RTTI的支持
+ *QObject::inherits()* 判断一个对象是否是QObject继承树上特定类的对象
+ *QObject::tr()* 国际化翻译字符串
+ *QObject::setPropetry() and QObject::property()* 通过名称动态设置和获取属性
+ *QMetaObject::newInstance()* 给一个类构造新的实例
+ *qobject_cast()* 如同标准C++中的*dynamic_cast()*，但无需RTTI支持，且可以跨动态库转换，此函数尝试将其参数转换为中括号中的类型，运行时类型正确返回非零指针，否则为nullptr

当QObject的派生类没有Q_OBJECT宏及元对象代码时，信号槽或上述其他元对象系统特性也可以使用，在元对象系统看来，此派生类等同于它最近的有元对象代码的祖先，即*QMetaObject::className*会返回祖先类名，而不是派生类的名称。

```cpp
    // MyWidget继承自QWidget，且声明中带有Q_OBJECT宏
    QObject *obj=new MyWidget;                  
    QWidget *widget=qobject_cast<QWidget *>(obj); //obj 实际上是MyWidget，QWidget的派生类
    MyWidget *myWidget=qobject_cast<MyWidget *>(obj); //QT自有类和自定义类对于qobject_cast 没有区别
    QLabel *label=qobject_cast<QLabel *>(obj); // label is 0

    // 运行时处理类型转换示例：
    if(QLabel *label=qobject_cast<QLabel *>(obj)){
        label->setText(tr("ping"));
    }else if(QPushButton *button=qobject_cast<QPushButton *>(obj)){
        button->setText(tr("pong!"));
    }
```