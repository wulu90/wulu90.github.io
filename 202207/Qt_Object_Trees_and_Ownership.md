# 对象树和所有权
QObjects组织在一棵对象树中。当创建一个QObject，且指定了另一个对象为父对象时，就被加入到父对象的子列表中,当父对象删除时，子对象也删除了。正好满足了GUI对象的需要，例如，一个快捷键QShortcut是对应窗口的子对象，当用户关闭窗口时，快捷键也删除了。

QQuickItem是Qt Quick模块的基础虚元素，派生自QObject，TODO。

QWidget是Qt Widgets模块的基础类，扩展了上述父子关系。一个子对象通常是一个子部件，它显示在父对象的坐标系统中，父对象的边界也裁剪了它的图形。应用程序在用户关闭后删除了消息框，消息框上的按钮和标签也一同被删除了，这正是我们所需要的，因为这些按钮和标签都是消息框的子对象。

也可以自己删除子对象，会自动从父对象的子列表中删除，例如，当用户移除一个工具条时，应用程序会删除一个QToolBar对象，工具条的QMainWindow父对象会探知到这个变化，并重新配置屏幕空间。

调试函数 QObject::dumpObjectTree()，QObjcet::dumpObjectInfo()

## 创建与销毁
当QObjects在使用new堆上创建时，对象树可以用任意顺序构造，对象树上的对象可以用任意顺序被销毁。对象树上的任何对象被删除了，当它有父对象时，析构函数会自动从父对象的子列表中删除它；当它有子对象时，析构函数会删除它的每一个子对象。没有对象会被删除两次，忽略销毁的顺序。

当QObjects在栈上被创造时，看一下两个例子
```cpp
    int main(){
        QWidget window;
        QPushButton quit("Quit",&window);
        ...
    }
```
父对象window，子对象quit，都是QObjects，QPushButton派生自QWidget，QWidget派生自QObject。quit的析构函数不会被调用两次，因为C++标准规定局部对象的销毁顺序正好与其创造顺序相反。所以，子对象quit的析构函数首先调用，将自己从父对象window中删除，然后父对象windows的析构函数被调用。

当交换创建顺序时，
```cpp
    int main(){
        QPushButton quit("Quit");
        QWidget window;

        quit.setParent(&window);
        ...
    }
```
销毁的顺序就会导致一个问题。父对象的析构函数首先被调用，因为最后创建的。然后在父对象的析构函数中会调用子对象的析构函数，当quit超过了作用域后，它的析构函数会再次调用，然而它已经被销毁了。