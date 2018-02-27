# 使用C++/CLI封装FileGDBAPI过程中的一些点滴

## 交叉引用
在Geodatabase类和Table类中会互相引用对方的头文件，若不处理则编译时就会产生许多错误，处理方法就是在**命名空间**内前置声明该类，**类的声明应加上ref**,枚举类型为enum class
```cpp
// tGeodatabase.h
namespace FileGDB {
	ref class Table;
	enum class FieldType;
	public ref  class  Geodatabase sealed{
```
```cpp
// tTable.h
namespace FileGDB {
	ref class Geodatabase;
	public ref class Table sealed {
```
此时若在头文件中只有类的引用或指针，则不需include该类的头文件

## S_OK等重定义
将#include <msclr/marshal_cppstd.h>放到所有引用的最前面

## vector \<wstring> to cli::array<String^>^
扩展marshal_as方法，在MyMarshal.h文件中

## 参数 out
使用OutAttribute，定义参数时记得**加%**
```cpp
static void  GetErrorRecord(int recordNmu, [Runtime::InteropServices::Out] int %errorCode, [Runtime::InteropServices::Out] String^ %errorDescription);
```