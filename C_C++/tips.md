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

## recursive vs iterative
adventofcode 2023 day 23,使用 backtracking 来找到所有路径，以得到最长路径，由于递归调用过深，导致 stack overflow，需要更改栈的大小。
```cmake
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /STACK:10000000")
```
改成由 vector 模拟 stack，没想到运行更慢，可能是需要维护已访问节点，以保证每个节点只访问一次。对于 set 的拷贝花费了更多时间。
```cpp
    vector<tuple<size_t, set<size_t>, size_t>> dfstack;    // path_last_index,visited,steps
    dfstack.push_back({{startinx}, {startinx}, 0});

    size_t maxstep = 0;

    while (!dfstack.empty()) {
        auto curr = dfstack.back();
        dfstack.pop_back();
        auto& [path, visited, step] = curr;
        if (path == endinx) {
            maxstep = max(maxstep, step);
            // dfstack.pop_back();
            continue;
        }
        // dfstack.pop_back();
        for (auto [adj, num] : adjmap[path]) {
            if (!visited.contains(adj)) {
                // auto tmpath = path;
                // tmpath.push_back(adj);
                auto tmpvisited = visited;
                tmpvisited.insert(adj);
                dfstack.push_back({adj, move(tmpvisited), step + num});
            }
        }
    }
```

## array initialization
array initialization!!! 十分重要！！！在两个不同的环境下得到了不同的初始化值。
+ Archlinux gcc13.2.1 初始化为零
+ Windows11 msys64 ucrt64 gcc 13.2.0 初始化为随机值
+ Windows11 visual studio 17.8.3 c++ latest，locals watch 窗口查看c值报错

code from aoc2023 day24
```cpp
array<int64_t, 3> matvecmul(const array<array<int64_t, 3>, 3>& mat, const array<int64_t, 3>& b) {
    // array<int64_t, 3>  // wrong value in msys64 ucrt gcc
    array<int64_t, 3> c{0ll, 0ll, 0ll}; // important 
    for (size_t i = 0; i < 3; ++i) {
        for (size_t j = 0; j < 3; ++j) {
            c[i] += mat[i][j] * b[j];
        }
    }
    return c;
}
```