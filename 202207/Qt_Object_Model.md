# 对象模型
标准C++对象模型运行高效但在某些领域不够灵活，在GUI编程领域，Qt结合了C++的速度与Qt对象模型的灵活性。

Qt给C++添加了以下特性：
+ 强大的无缝的对象通信机制，信号与槽
+ 可查询可设计的对象属性
+ 强大的事件与事件过滤器
+ 用于国际化的上下文字符串翻译
+ 复杂的间隔驱动计时器，在事件驱动GUI中优雅的整合许多任务
+ 分层且可查询的对象树，以一种自然的方式组织对象所有权
+ 受保护的指针 QPointer，当其引用的对象销毁后自动置为0
+ 跨域库的动态转换
+ 自定义类型创建
以上大部分特性都使用标准C++实现，基于对QObject类的继承。其他如对象通信和动态属性，需要元对象系统，由Qt自己的元对象编译器（moc）提供。

## 重要的类
|Class|Description|
|---|---|
|QMetaClassInfo|类的附加信息|
|QMetaEnum|枚举器元数据|
|QMetaMethod|成员函数的元数据|
|QMetaObject|包含Qt对象的元信息|
|QMetaProperty|属性的元数据|
|QMetaSequence|Allows type erased access to sequential containers|
|QMetaType|在元对象系统中管理已命名类型|
|QObject|所有Qt对象的基类|
|QObjectCleanupHandler|观察多个对象的生命周期|
|QPointer|模板类，为QObject提供受保护的指针|
|QSignalBlocker|异常安全的QObject::blockSignals()包装器|
|QSignalMapper|从可识别的发送者出收集整合信号|
|QVariant|对大部分Qt数据类型来说像union|

## 标识 Identity 与 值 Value
Qt对象模型扩展出的特性，要求Qt对象被视为标识而不是值，值是用来拷贝或者赋值，标识用来克隆（clone），克隆意味着创建一个新的标识，而不是精确的拷贝一个旧的。就像双胞胎有不同的名字，虽然长得像，但有不同的位置不同的社交圈。

克隆标识是一个比拷贝或赋值更复杂的操作，对于Qt对象来说需要：
+ 可能有唯一的 QObject::objectName()，拷贝一个Qt对象时如何给新对象命名
+ 对象层级中有特定的位置，拷贝时新对象该放在那里
+ 能与其他对象连接来发送或接收信号，拷贝时如何转移这些连接
+ 运行时动态添加了属性，拷贝的对象应该包含这些动态添加的属性吗？
QObject类和其派生类，无论直接派生还是间接派生，这些类的拷贝构造和赋值操作符都被禁用了。