# 属性系统
Qt 提供了一个复杂的属性系统，与其他编译器厂商提供的依赖编译器或者平台的不同，Qt 不依赖任何非标准的特性如 __property或[property]。在任何受支持的平台或编译器上都可用。基于元对象系统，通过信号槽同样支持内部对象之间的通信。

## Requirements
QObject的派生类中使用Q_PROPERTY宏可以声明一个属性。
```cpp
Q_PROPERTY(type name
           (READ getFunction [WRITE setFunction] |
            MEMBER memberName [(READ getFunction | WRITE setFunction)])
           [RESET resetFunction]
           [NOTIFY notifySignal]
           [REVISION int | REVISION(int[, int])]
           [DESIGNABLE bool]
           [SCRIPTABLE bool]
           [STORED bool]
           [USER bool]
           [BINDABLE bindableProperty]
           [CONSTANT]
           [FINAL]
           [REQUIRED])
```
示例
```cpp
    Q_PROPERTY(bool focus READ hasFoucs)
    Q_PROPERTY(bool enabled READ isEnabled WRITE setEnabled)
    Q_PROPERTY(QCursor cursor READ cursor WRITE setCursor RESET unsetCursor)
```
使用MEMBER关键字可以将成员变量导出为Qt属性，需要注意的是NOTIFY信号必须被指定，以允许QML属性绑定。
```cpp
    Q_PROPERTY(QColor color MEMBER m_color NOTIFY colorChanged)
    Q_PROPERTY(qreal spacing MEMBER m_spacing NOTIFY spacingChanged)
    Q_PROPERTY(QString text MEMBER m_text NOTIFY textChanged)
    ...
signals:
    void colorChanged();
    void spacingChanged();
    void textChanged(const QString &newText);
private:
    QColor  m_color;
    qreal   m_spacing;
    QString m_text;
```

| 关键字 |理解|
| --- | --- |
|READ|**没有指定MEMBER时，READ必须指定**，读取属性值的函数，最好是const函数，返回值必须是属性类型或属性类型的const引用|
|WRITE|可选，用来设定属性值，必须返回void，且只有一个参数，类型是属性类型或该类型的指针或引用|
|MEMBER|READ没有指定时，需要指定MEMBER；在没有指定READ或WRITE时，使成员变量可读可写，且可以配合READ或WRITE来控制读写权限，但不能同时使用|
|RESET|可选，设置属性为特定上下文默认值，返回void，没有参数|
|NOTIFY|可选，|
|REVISION||
|DESIGNABLE||
|SCRIPTABLE||
|STORED||
|USER||
|BINDABLE||
|CONSTANT||
|FINAL||
|REQUIRED||

READ、WRITE、RESET函数可以被继承，也可以是虚函数。
属性类型可以是QVariant支持的任何类型，或者自定义类型，例如QDate，当然必须包含 \<QDate\> 头文件
```cpp
    Q_PROPERTY(QDate data READ getDate WRITE setDate)
```
由于历史原因，QMap和QList用作属性类型时，实际上是QVariantMap和QVariantList

## Reading and Writing Properties with the Meta_Object System
使用 *QObject::property()* 和 *QObject::setProperty()* 这两个通用函数来读写属性时，可以不用知道所属类的其他信息，除了属性名称。例如：
```cpp
    QPushButton* button = new QPushButton;
    QObject* object=button;

    button->setDown(true);
    object->setProperty("down",true);
```
通过 WRITE 访问器来访问属性更好一些，因为它更快，编译时能提供更好的诊断，但是编译时需要了解这个类。通过属性名来访问则不需要在编译时了解这个类。还可以通过查询类的 QObject，QMetaObject和 QMetaProperties，来探索这个类的属性。
```cpp
    QObject* object=...
    // 获取元对象
    const QMetaObject* metaobject=object->metaObject();
    // 属性个数
    int count=metaobject->propertyCount();
    for(int i=0;i<count;++i>){
        // 获取属性元数据
        QMetaProperty metaproperty=metaobject->property(i);
        // 获取属性名称
        const char* name=metaproperty.name();
        // 获取属性值
        QVariant value=object->property(name);
        ...
    }
```

## A Simple Example
枚举类型必须使用 Q_ENUM() 注册到元对象系统，枚举器名称才能在 QObject::setProperty() 中可用。
```cpp
    class MyClass:public QObject{
        Q_OBJECT
        Q_PROPERTY(Priority priority READ priority WRITE setPriority NOTIFY priorityChanged)

    public:
        MyClass(QObject* parent=nullptr);
        ~MyClass();

        enum Priority{ High,Low,VeryHigh,VeryLow};
        Q_ENUM(Priority) // 注册枚举类型

        // WRITE 函数
        void setPriority(Priority priority){
            m_priority=priority;
            emit priorityChanged(priority);
        }
        
        // READ 函数
        Priority priority() const{return m_priority;}

    signals:
        void priorityChanged(Priority);

    private:
        Priority m_priority;
    };
```
```cpp
    // 指针 指向MyClas实例
    MyClass* myinstance = new MyClass;
    // 指向QObject的指针，是一个MyClass实例
    QObject* object = myinstance;

    // 两种方式设置属性
    myinstance->setPriority(MyClass::VeryHigh);
    objcet->setProperty("priority","VeryHigh");
```
如果枚举类型定义在其他类中，那么在设置属性时需要完全限定名，OtherClass::Prioority，此外该类必须继承自QObject，且该枚举类型使用 Q_ENUM() 宏注册了。
Q_FLAG() 是一个类似的宏，也注册枚举类型，但是将其标记为一系列 *flag*，可以被 OR 在一起，例如IO类中的枚举值 Read 和 Write，在设置属性时就可以接受 Read|Write的参数，即可读也可写。

## Dynamic Properties
QObject::setProperty()既可以设置属性，也可以添加属性；
+ name对应的属性存在，value是属性对应的类型，属性值被修改，返回true；value类型错误，属性值不变，返回false；
+ name对应的属性不存在，例如没有被Q_PROPERTY()宏声明，新的属性添加到QObject中，并赋予value值，返回false；

所以返回false不能用来判断特定属性是否被设置，除非知道该属性已经存在；

动态属性添加到每个实例的基础上，即添加到QObject，而不是QMetaObject；

实例移除属性，传递一个属性名称和一个无效的 QVariant，QVariant默认构造一个无效的QVariant。

动态属性可以通过 QObject::property() 查询，如同那些用 Q_RROPERTY() 宏在编译时声明的一样。

## 属性与自定义类型
自定义类型必须用 Q_DECLARE_METATYPE() 宏注册，使其值可以存储在 QVariant 中。

## 类添加附加信息
Q_CLASSINFO() 宏可以用来给类添加附加信息，*name--value* 添加到类的元对象中，例如：
```cpp
    Q_CLASSINFO("Version","3.0.0")
```
如同其他元数据，类信息可以在运行时通过元对象获取，参照 QMetaObject::classInfo()。