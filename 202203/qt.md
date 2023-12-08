### 自动连接信号槽
qt自动连接信号槽
~~~cpp
void QMetaObject::connectSlotsByName(QObject* object);
~~~
匹配模式
~~~cpp
void on_<object name>_<signal name>(<signal parameters>);
~~~
子object必须设置objectname才可以查找到。

todo：代码如下时没有弹出MessageBox，原因有待查找
~~~cpp
void on_toolbar1_clicked() {
    QMessageBox::information(nullptr, QString("aaa"), QString("bbb"));
}

int main(int argc, char* argv[])
{
	QMainWindow MistMainWindow;
	QToolBar* toolbar = new QToolBar(&MistMainWindow);
    toolbar->setObjectName(QString("toolbar1"));
	MistMainWindow.addToolBar(Qt::TopToolBarArea, toolbar);
	QMetaObject::connectSlotsByName(&MistMainWindow);
	MistMainWindow.show();
	return MistApp.exec();
}
~~~

### 平台抽象层
