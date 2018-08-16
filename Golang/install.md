# golang

## install
golang的下载页面[https://golang.org/dl/](https://golang.org/dl/)，在敲下这段文字的时候，最新预编译好的版本为1.10.3。在Windows 10 中，我使用的是msi格式的安装程序，下载地址请点击[此处](https://dl.google.com/go/go1.10.3.windows-amd64.msi)。

默认情况下，golang会安装到`C:\Go`中，安装完成后会在系统环境变量中新增`GOROOT`，值为`C:\Go\`，并在`Path`中加入`C:\Go\bin`。

## workspace
workspace是一个目录，就是用户环境变量中`GOPATH`的值，在安装完成后，默认值是`%USERPROFILE%\go`，即`C:\Users\YourName\go`。我的理解是`import`通过`GOPATH`来找到导入包的位置。

workspace目录包含三个子文件夹。
- src 包含代码源文件
- pkg 包含包对象,库文件
- bin 包含可执行命令

## hello world
在workspace中新建一个hello文件夹，在文件家中创建一个Untitled-2.go文件，不一定要是hello.go，用文本编辑器写入以下代码：
```go
package main
import "fmt"
func main(){
    fmt.Printf("hello,world\n")
}
```
此时，就可以直接用go build命令来对源文件进行编译，需要的注意的是，如果build命令之后不加Untitled-2.go文件的名称，那么编译的结果是hello.exe,反之则是Untitled-2.exe,两个可执行文件都可以输出"hello,world"。
![](Image/install/build.png "build")

使用go run同样可以得到结果。
```powershell
PS D:\code\Golang\src\hello> go run .\Untitled-2.go
hello,world
```

使用go install命令会将hello.exe安装（移动）到bin文件夹下，而Untitled-2.exe则不会移动。

使用go clean命令会将编译完成的可执行文件全部删除，即删除hello文件夹中的hello.exe，Untitled-2.exe。使用go clean -i，除了删除上述文件外，还会删除bin文件中hello.exe。

使用go fmt命令格式化源代码文件，代码会变成如下格式：
```go
package main

import "fmt"

func main() {
	fmt.Printf("hello,world\n")
}
```
*build、install、clean、fmt都是在hello文件夹中执行。*
