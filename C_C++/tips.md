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