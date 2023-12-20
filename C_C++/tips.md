# Some C++ Tips

## error examples
#### Container iterator 失效
STL Algorithm 中 Destination 要么提前分配空间，使用位于起始位置的 iterator，要么用临时容器，back_inserter(tmp_Cont);

```cpp
#include <iostream>
#include <numeric>
#include <vector>
using namespace std;

int main() {
    vector<int> aa{2, 3, 5, 8};
    auto it = aa.begin();
    for (int i = 0; i < 4; ++i) {
        cout << *it++ << " ";
    }
    cout << endl;
    adjacent_difference(aa.begin(), aa.end(), back_inserter(aa));
    
    // use tmp container
    // vector<int> bb
    // adjacent_difference(aa.begin(), aa.end(), back_inserter(bb));
    // copy(bb.begin(),bb.end(),back_inserter(aa))
    // output 2 3 5 8 2 1 2 3

    // 扩充原容器的空间
    // aa.reserve(aa.size()*2)
    // adjacent_difference(aa.begin(), aa.end(), back_inserter(aa));
    // aa.

    for (auto a : aa) {
        cout << a << " ";
    }
    cout << endl;
    // 此时输出的就不是原始vector中的值了，
    for (int i = 0; i < 4; ++i) {
        cout << *it++ << " ";
    }
    cout << endl;
}

/**
 * 2 3 5 8
 * 2 3 5 8 2 -2 -2013012712 406016066 后三个数每次都会变化
 * 0 0 4113 0 每次输出都不同
 */
```

由于vector 重新分配了空间，原始迭代器指向的值就不可预测了，调试过程中，vector 的 raw_pointer (first) 内存地址发生了变化。

## CMakePresets

CMakePresets.json 中使用指定的 cmake 和 ninja 可执行文件。在 configurePresets 设置 cmakeExecutable 的值，在 cacheVariables 中设置 CMAKE_MAKE_PROGRAM 的值。
```jsonc
"cmakeExecutable": "E:/msys64/ucrt64/bin/cmake.exe", 
"cacheVariables" : {
    "CMAKE_MAKE_PROGRAM":"E:/msys64/ucrt64/bin/ninja.exe"
}
```

## awk grep
grep s 过滤所有含有 s 的行；awk '{ print $1 }' ORS=' ' 将分隔符由 new line 改为空格，输出到一行；
```bash
pacman -Q | grep mingw-w64-ucrt-x86_64 | awk '{ print $1 }' ORS=' '
```
查找所有已安装的包，过来条件是包名含有 mingw-w64-ucrt-x86_64，输出所有空格前的字符串到一行。

## 派生类 initialization list 构造
派生类在使用 initialization list 构造时，list 中只能包含直接成员，继承的基类不能包含在其中。
```cpp
struct base{
    int m;
    base(int _m):m(_m){}
};

struct derived:public base{
    int n;
    derived(int _m,int _n):n(_n),m(_m){} // error，不能用 n(_n),m(_m) 初始化 m
    derived(int _m,int _n):base{_m},n{_n}{} // ok
};
```
