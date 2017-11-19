# 记录Git及GitHub使用过程中的一些问题及解决方法

1. 使用ssh同步GitHub项目

在本地机器生成key并添加到Github后，使用测试命令添加github仓库的访问权限，参照[https://help.github.com/articles/testing-your-ssh-connection/](https://help.github.com/articles/testing-your-ssh-connection/ "test conn")
```git
ssh -T git@github.com
```
若仓库使用https协议clone下来的，要修改远程仓库的地址为ssh地址，命令为
```git
git remote set-url origin git@github.com:XXXXXXXX.git
```
或直接修改config文件，或先删除现有远程仓库，再添加新的远程仓库。

2. 本地在用vscode编写markdown时，图片路径不区分大小写；github中路径区分大小写。
