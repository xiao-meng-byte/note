# 📦 目录
# 📦 C++ 常用函数与容器速查手册

> 竞赛必备 STL & 内置函数大全，附用法示例与坑点

---

## 📑 目录

1. [常用头文件](#一常用头文件)
2. [输入输出优化](#二输入输出优化)
3. [STL 容器](#三stl-容器)
    - 3.1 vector
    - 3.2 string
    - 3.3 queue / deque
    - 3.4 priority_queue
    - 3.5 stack
    - 3.6 set / multiset
    - 3.7 map / multimap
    - 3.8 unordered_set / unordered_map
    - 3.9 pair / tuple
    - 3.10 array / bitset
4. [STL 算法](#四stl-算法)
    - 4.1 排序相关
    - 4.2 查找相关
    - 4.3 二分查找
    - 4.4 排列与去重
    - 4.5 最值与统计
    - 4.6 修改操作
    - 4.7 数值算法
5. [常用数学函数](#五常用数学函数)
6. [位运算技巧](#六位运算技巧)
7. [常用宏与技巧](#七常用宏与技巧)
8. [字符串处理](#八字符串处理)
9. [内存操作](#九内存操作)
10. [其他常用函数](#十其他常用函数)

---

## 📦 一、常用头文件

### 📦 万能头（竞赛推荐）


```cpp
#include <bits/stdc++.h>
using namespace std;
```


> **提示**：包含所有标准库，省时间。GCC 支持，部分 OJ 可能不支持（但大部分都支持）。

### 📦 按需引用的常用头


```cpp
#include <iostream>      // cin, cout
#include <cstdio>        // printf, scanf
#include <cstring>       // memset, memcpy, strlen
#include <algorithm>     // sort, lower_bound, max, min ...
#include <vector>        // vector
#include <queue>         // queue, priority_queue
#include <stack>         // stack
#include <set>           // set, multiset
#include <map>           // map, multimap
#include <unordered_set> // 哈希集合
#include <unordered_map> // 哈希映射
#include <string>        // string
#include <cmath>         // sqrt, pow, sin, abs ...
#include <cstdlib>       // rand, srand, abs
#include <ctime>         // clock, time
#include <bitset>        // bitset
#include <tuple>         // tuple
#include <numeric>       // accumulate
#include <deque>         // deque
#include <list>          // list
#include <functional>    // greater, less
```



---

## 🔧 二、输入输出优化

### 🔧 2.1 scanf / printf（速度快，推荐）


```cpp
int a, b;
scanf("%d %d", &a, &b);      // 读int
printf("%d %d\n", a, b);     // 输出int

long long x;
scanf("%lld", &x);           // long long
printf("%lld\n", x);

double d;
scanf("%lf", &d);            // double
printf("%.6f\n", d);         // 保留6位小数

char s[100];
scanf("%s", s);              // 读字符串（遇空格停止）
printf("%s\n", s);

char c;
scanf(" %c", &c);            // 注意前面空格，吃掉换行/空白
```



**📋 格式符速查：**
| 类型 | 格式符 |
| :--- | :--- |
| int | `%d` |
| long long | `%lld` |
| unsigned long long | `%llu` |
| double | `%lf` (输入) / `%f` (输出) |
| char | `%c` |
| 字符串 | `%s` |
| 十六进制 | `%x` |
| 八进制 | `%o` |
| 占位宽度 | `%5d` (右对齐5位) / `%05d` (补零) |

---

### 2.2 cin / cout 加速
```cpp
ios::sync_with_stdio(false);  // 取消与stdio同步
cin.tie(nullptr);             // 解除cin与cout绑定
cout.tie(nullptr);
```
> 加了这两行后速度接近 scanf/printf。但不能混用 scanf 和 cin。

---

### 2.3 整行读取
```cpp
string s;
getline(cin, s);   // 读一整行（包含空格）
// 注意：前面如果有cin >> 会残留换行，需要先getchar()吃掉
```

---

### 🔧 2.4 快速输出（极端卡常）


```cpp
// 输出到字符串再一次性输出
char buf[1<<20], *p = buf;
sprintf(p, "%d\n", x); p += strlen(p);
// 最后 fwrite(buf, 1, p-buf, stdout);
```



---

## 三、STL 容器

### 3.1 vector（动态数组）

**定义：**
```cpp
vector<int> v;                // 空vector
vector<int> v(10);            // 10个元素，默认0
vector<int> v(10, 5);         // 10个5
vector<int> v = {1, 2, 3};    // 初始化列表
vector<vector<int>> g(n);     // 二维（邻接表常用）
```

**常用操作：**
```cpp
v.push_back(x);       // 尾部加元素 O(1)均摊
v.pop_back();         // 尾部删元素 O(1)
v.size();             // 元素个数
v.empty();            // 是否为空
v.clear();            // 清空
v.front();            // 第一个元素
v.back();             // 最后一个元素
v[i];                 // 下标访问（不检查越界）
v.at(i);              // 下标访问（越界抛异常）
v.resize(n);          // 改变大小为n
v.resize(n, val);     // 新增元素初始化为val
v.insert(v.begin()+i, x);  // 在第i位插入x O(n)
v.erase(v.begin()+i);      // 删除第i位 O(n)
v.erase(v.begin(), v.begin()+k); // 删除前k个
```

**遍历：**
```cpp
// 下标
for (int i = 0; i < v.size(); i++) cout << v[i];

// 迭代器
for (auto it = v.begin(); it != v.end(); it++) cout << *it;

// 范围for（C++11）
for (int x : v) cout << x;
for (auto& x : v) x++;  // 引用可修改
```

**坑点：**
- push_back 可能导致扩容，迭代器失效
- reserve(n) 预分配空间，避免多次扩容
- 二维 vector 初始化：`vector<vector<int>> dp(n, vector<int>(m, 0))`

---

### 3.2 string（字符串）

**定义：**
```cpp
string s;                    // 空串
string s = "hello";          // 初始化
string s(5, 'a');            // "aaaaa"
string s = to_string(123);   // 数字转字符串 "123"
int x = stoi(s);             // 字符串转int（还有stoll, stod）
```

**常用操作：**
```cpp
s.size(); s.length();        // 长度（一样）
s.empty();
s.clear();

s += "abc";                  // 尾部追加
s.push_back('a');
s.append("abc");
s.append(3, 'x');            // 加3个'x'

s[i];                        // 下标访问
s.substr(pos, len);          // 从pos开始取len个
s.substr(pos);               // 从pos到末尾

s.find("abc");               // 查找，返回下标，找不到返回string::npos
s.find("abc", pos);          // 从pos开始找
s.rfind("abc");              // 从后往前找

s.insert(pos, "abc");        // 插入
s.erase(pos, len);           // 删除
s.replace(pos, len, "new");  // 替换

s.compare(s2);               // 比较（字典序），<0则s小
```

**字符串与数字转换：**
```cpp
// 数字 → 字符串
string s = to_string(num);   // int/long long/double 都可以

// 字符串 → 数字
int a = stoi(s);             // string to int
long long b = stoll(s);      // string to long long
double c = stod(s);          // string to double

// 或用sscanf
int x; sscanf(str.c_str(), "%d", &x);
```

**坑点：**
- find 找不到返回 `string::npos`（是个很大的数，注意判断）
- substr 第二个参数是长度不是结束位置

---

### 3.3 queue / deque

#### 📦 queue（队列，FIFO）


```cpp
queue<int> q;

q.push(x);          // 入队
q.pop();            // 出队（不返回值）
q.front();          // 队首
q.back();           // 队尾
q.size();
q.empty();
```



#### deque（双端队列）
```cpp
deque<int> dq;

dq.push_front(x);   // 头插
dq.push_back(x);    // 尾插
dq.pop_front();     // 头删
dq.pop_back();      // 尾删
dq.front();
dq.back();
dq[i];              // 支持随机访问
dq.size();
dq.empty();
dq.clear();
```
> 0-1 BFS 常用：边权为0加队首，边权为1加队尾

---

### 3.4 priority_queue（优先队列 / 堆）

**默认是大根堆（最大值优先）：**
```cpp
priority_queue<int> pq;        // 大根堆
pq.push(x);
pq.pop();
pq.top();                      // 堆顶（最大值）
pq.size();
pq.empty();
```

**小根堆：**
```cpp
priority_queue<int, vector<int>, greater<int>> pq;  // 小根堆
```

**自定义结构体：**
```cpp
struct Node {
    int dis, u;
    bool operator>(const Node& b) const {  // 配合greater用>
        return dis > b.dis;
    }
};
priority_queue<Node, vector<Node>, greater<Node>> pq;
```
> Dijkstra 常用小根堆存 {距离, 节点}

**坑点：**
- top() 不是 front()，和 queue 不一样
- 默认大根堆，要小根堆记得加 greater

---

### 3.5 stack（栈，LIFO）
```cpp
stack<int> st;

st.push(x);
st.pop();
st.top();           // 栈顶
st.size();
st.empty();
```
> 单调栈、表达式求值、DFS非递归 常用

---

### 3.6 set / multiset（有序集合，红黑树）

#### set（元素不重复，有序）
```cpp
set<int> s;

s.insert(x);        // 插入 O(log n)
s.erase(x);         // 删除值为x的所有元素
s.erase(it);        // 删除迭代器指向的元素
s.count(x);         // x出现次数（0或1）
s.find(x);          // 查找，返回迭代器，找不到返回s.end()
s.size();
s.empty();
s.clear();

s.lower_bound(x);   // 第一个 >= x 的迭代器
s.upper_bound(x);   // 第一个 > x 的迭代器

// 遍历（从小到大）
for (int x : s) cout << x;
```

#### multiset（可重复）
```cpp
multiset<int> s;
s.erase(x);         // ⚠️ 删除所有值为x的元素
s.erase(s.find(x)); // 只删一个x
s.count(x);         // x的个数
```

**坑点：**
- multiset 直接 erase(value) 会删掉所有相同的，删一个要先 find
- set 是有序的，遍历是从小到大
- 不能通过迭代器修改元素（会破坏有序性），只能删了重插

---

### 📦 3.7 map / multimap（有序映射，红黑树）

#### 📦 map（key不重复，有序）


```cpp
map<string, int> mp;

mp["key"] = 100;    // 插入或修改
mp.insert({"abc", 5});
mp.erase("key");    // 删除
mp.count("key");    // 是否存在（0或1）
mp.find("key");     // 查找，返回迭代器
mp.size();
mp.empty();
mp.clear();

mp.lower_bound(k);  // 第一个 key >= k
mp.upper_bound(k);

// 遍历
for (auto& p : mp) {
    cout << p.first << " " << p.second << endl;
}
```



#### 📦 multimap（key可重复）


```cpp
multimap<int, string> mp;
mp.insert({1, "a"});
mp.insert({1, "b"});  // 可以重复
mp.count(1);          // key=1的个数
// 没有 [] 操作符！
```



**⚠️ 坑点：**
- 🔸 用 `mp[key]` 访问时，如果 key 不存在会自动插入默认值（如0）
- 🔸 只是想判断存在性用 count 或 find，别用 []
- 🔸 按 key 排序，不是按插入顺序

---

### 3.8 unordered_set / unordered_map（哈希容器）

**特点：**
- 内部是哈希表，平均 O(1)，最坏 O(n)
- 无序（遍历顺序不确定）
- 自定义类型需要写哈希函数

```cpp
unordered_set<int> s;
unordered_map<string, int> mp;

// 操作和set/map基本一样
s.insert(x);
mp["abc"] = 1;
s.find(x) != s.end();
```

**坑点：**
- 数据容易被卡哈希（构造数据让全部冲突）
- 复杂题卡时常可以考虑换 unordered 加速
- 没有 lower_bound / upper_bound（无序）

---

### 3.9 pair / tuple

#### pair（二元组）
```cpp
pair<int, string> p;
p.first = 1;
p.second = "hello";

pair<int, int> p = {1, 2};        // C++11
pair<int, int> p = make_pair(1, 2);

// 比较：先比first，first相同比second
if (p1 < p2) { ... }

// 结构化绑定（C++17）
auto [a, b] = p;
```

#### 📦 tuple（多元组）


```cpp
tuple<int, string, double> t = {1, "abc", 3.14};
get<0>(t);   // 第一个元素
get<1>(t);
get<2>(t);

auto t = make_tuple(1, "a", 3.14);

// C++17 结构化绑定
auto [x, y, z] = t;
```



---

### 📦 3.10 array / bitset

#### 📦 array（固定大小数组）


```cpp
array<int, 5> arr = {1, 2, 3, 4, 5};
arr[0];
arr.size();       // 固定大小
arr.fill(0);      // 全部填0
```


> **提示**：比原生数组多了size等接口，但大小必须是编译期常量

#### 🧩 bitset（位集合）


```cpp
bitset<100> bs;      // 100位，初始全0
bitset<100> bs(string("10101"));

bs.set(3);           // 第3位置1
bs.set();            // 全置1
bs.reset(3);         // 第3位置0
bs.reset();          // 全置0
bs.flip(3);          // 第3位翻转
bs[3];               // 访问第3位

bs.count();          // 1的个数
bs.any();            // 是否有1
bs.none();           // 是否全0
bs.all();            // 是否全1

bs.to_ulong();       // 转unsigned long
bs.to_string();      // 转字符串

// 位运算
bs1 & bs2;
bs1 | bs2;
bs1 ^ bs2;
~bs;
```


> **💡 提示**：大小必须是编译期常量。压位DP、状态压缩常用。

---

## 🔧 四、STL 算法

### 4.1 排序相关

#### sort（快速排序，不稳定）
```cpp
sort(v.begin(), v.end());          // 默认升序
sort(v.begin(), v.end(), greater<int>());  // 降序

// 自定义比较函数
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // 降序
});

// 结构体排序
sort(arr, arr+n, [](Node& a, Node& b) {
    if (a.w != b.w) return a.w < b.w;
    return a.id < b.id;
});
```

#### 📦 stable_sort（稳定排序）


```cpp
stable_sort(v.begin(), v.end());  // 相等元素保持原顺序
```



#### partial_sort（部分排序）
```cpp
// 前k个元素有序（最小的k个），后面不管
partial_sort(v.begin(), v.begin() + k, v.end());
```

#### 📦 nth_element（找第k大/小）


```cpp
// 第k小的元素放到第k位，左边都<=它，右边都>=它（但两边都无序）
nth_element(v.begin(), v.begin() + k, v.end());
cout << v[k];  // 第k小（0开始）
```


> **💡 提示**：时间复杂度 O(n)，快速选择算法

---

### 🔍 4.2 查找相关

#### 🔍 find（线性查找）

```cpp
auto it = find(v.begin(), v.end(), val);
if (it != v.end()) { /* 找到了 */ }
```



#### count（计数）
```cpp
int cnt = count(v.begin(), v.end(), val);
```

---

### 4.3 二分查找（必须有序！）

#### lower_bound（第一个 >= val）
```cpp
auto it = lower_bound(v.begin(), v.end(), val);
int pos = it - v.begin();  // 下标（vector）
// 找不到返回v.end()
```

#### upper_bound（第一个 > val）
```cpp
auto it = upper_bound(v.begin(), v.end(), val);
```

#### equal_range（相等区间）
```cpp
auto [l, r] = equal_range(v.begin(), v.end(), val);
int cnt = r - l;  // val出现的次数
```

#### binary_search（是否存在）
```cpp
bool exist = binary_search(v.begin(), v.end(), val);
```

**坑点：**
- 必须是升序序列！降序要传 greater 比较器
- lower_bound 是 >=，upper_bound 是 >，别搞混
- set/map 自带的 lower_bound 比通用算法快（O(log n) vs O(n)）

---

### 4.4 排列与去重

#### next_permutation（下一个排列）
```cpp
// 生成下一个字典序排列，成功返回true
sort(v.begin(), v.end());  // 要先排序才能生成全排列
do {
    // 处理当前排列
} while (next_permutation(v.begin(), v.end()));
```

#### prev_permutation（上一个排列）
```cpp
prev_permutation(v.begin(), v.end());
```

#### unique（去重）
```cpp
// 去除相邻重复元素，返回去重后的尾迭代器
// 一般先排序再去重
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```
> ⚠️ unique 只是把重复元素移到后面，不会真的删除，要配合 erase

---

### 4.5 最值与统计

#### max / min（两个数）
```cpp
int a = max(x, y);
int b = min(x, y);
```

#### max_element / min_element（区间最值）
```cpp
auto it = max_element(v.begin(), v.end());
int max_val = *it;

auto it2 = min_element(v.begin(), v.end());
```

#### minmax_element（同时找最大最小）
```cpp
auto [mn, mx] = minmax_element(v.begin(), v.end());
```

---

### 4.6 修改操作

#### reverse（翻转）
```cpp
reverse(v.begin(), v.end());      // 翻转整个数组
reverse(s.begin(), s.end());      // 翻转字符串
reverse(v.begin(), v.begin() + k); // 翻转前k个
```

#### fill / fill_n（填充）
```cpp
fill(v.begin(), v.end(), val);    // 全部填val
fill_n(v.begin(), n, val);        // 前n个填val
```

#### swap（交换）
```cpp
swap(a, b);                       // 交换两个变量
swap(v[i], v[j]);                 // 交换两个元素
iter_swap(v.begin(), v.end()-1);  // 交换迭代器指向的元素
```

#### copy（复制）
```cpp
copy(v1.begin(), v1.end(), v2.begin());  // v1拷贝到v2
```

#### rotate（旋转）
```cpp
// 将第middle个元素旋转到开头
rotate(v.begin(), v.begin() + k, v.end());
```

---

### 4.7 数值算法（<numeric>）

#### accumulate（求和）
```cpp
int sum = accumulate(v.begin(), v.end(), 0);  // 初始值0，求和
// 注意：初始值类型决定返回类型，long long要用0LL
long long sum = accumulate(v.begin(), v.end(), 0LL);
```

#### iota（递增填充）
```cpp
iota(v.begin(), v.end(), 1);  // 1, 2, 3, 4, ...
```

#### adjacent_difference（相邻差）
```cpp
adjacent_difference(v.begin(), v.end(), res.begin());
```

---

## 五、常用数学函数

### 5.1 基本运算
```cpp
#include <cmath>

abs(x);          // 整数绝对值（cstdlib也有）
fabs(x);         // 浮点绝对值

sqrt(x);         // 平方根
cbrt(x);         // 立方根（C++11）
pow(a, b);       // a的b次方（注意精度问题）

ceil(x);         // 向上取整
floor(x);        // 向下取整
round(x);        // 四舍五入
trunc(x);        // 向零取整

fmod(a, b);      // 浮点数取模
```

### 5.2 三角函数
```cpp
sin(x);    // 正弦（弧度）
cos(x);    // 余弦
tan(x);    // 正切

asin(x);   // 反正弦
acos(x);   // 反余弦
atan(x);   // 反正切
atan2(y, x); // 点(x,y)的极角，范围(-π, π]，常用！

// 角度转弧度：deg * PI / 180
// 弧度转角度：rad * 180 / PI
const double PI = acos(-1.0);
```

### 5.3 对数与指数
```cpp
exp(x);         // e的x次方
log(x);         // 自然对数 ln
log10(x);       // 以10为底
log2(x);        // 以2为底（C++11）
// 换底公式：log_b(a) = log(a) / log(b)
```

### 5.4 其他
```cpp
hypot(x, y);    // sqrt(x² + y²)，更精确
```

### 5.5 浮点数比较
```cpp
const double eps = 1e-8;

int sign(double x) {
    if (fabs(x) < eps) return 0;
    return x < 0 ? -1 : 1;
}
// 相等：sign(a - b) == 0
// 小于：sign(a - b) < 0
```

---

## 六、位运算技巧

### 6.1 基本位运算
```cpp
a & b;      // 与（都为1则1）
a | b;      // 或（有1则1）
a ^ b;      // 异或（不同则1）
~a;         // 取反
a << k;     // 左移k位（相当于 * 2^k）
a >> k;     // 右移k位（相当于 / 2^k，注意负数算术右移）
```

### 6.2 常用技巧
```cpp
// 1. 判断第k位是否为1
if (x >> k & 1) { ... }

// 2. 第k位置1
x |= (1 << k);

// 3. 第k位清零
x &= ~(1 << k);

// 4. 第k位翻转
x ^= (1 << k);

// 5. 最低位的1（lowbit）
lowbit = x & -x;

// 6. 去掉最低位的1
x &= x - 1;

// 7. 判断是否2的幂
if (x > 0 && (x & (x-1)) == 0)

// 8. 二进制中1的个数
__builtin_popcount(x);      // unsigned int
__builtin_popcountll(x);    // unsigned long long

// 9. 前导零个数
__builtin_clz(x);           // count leading zeros
__builtin_clzll(x);

// 10. 尾随零个数
__builtin_ctz(x);           // count trailing zeros

// 11. 交换两个数（不用临时变量）
a ^= b; b ^= a; a ^= b;

// 12. 取绝对值（位运算版）
int abs(int x) {
    int mask = x >> 31;
    return (x ^ mask) - mask;
}
```

### 6.3 子集枚举
```cpp
// 枚举mask的所有非空子集
for (int s = mask; s; s = (s - 1) & mask) {
    // 处理s
}
```

---

## 七、常用宏与技巧

### 7.1 常用宏
```cpp
// 加速
#define IOS ios::sync_with_stdio(false); cin.tie(0)

// 循环
#define rep(i, a, b) for (int i = a; i <= b; i++)
#define per(i, a, b) for (int i = a; i >= b; i--)

// 常用值
#define INF 0x3f3f3f3f
#define LINF 0x3f3f3f3f3f3f3f3fLL
#define MOD 1000000007
#define PI acos(-1.0)
#define eps 1e-8

// 简写
#define pb push_back
#define mp make_pair
#define fi first
#define se second
#define all(x) x.begin(), x.end()
#define sz(x) (int)x.size()

// 取最大最小
#define max(a, b) ((a) > (b) ? (a) : (b))
#define min(a, b) ((a) < (b) ? (a) : (b))
// （不过建议用std::max，宏有副作用问题）
```

### 7.2 常用类型别名
```cpp
typedef long long ll;
typedef unsigned long long ull;
typedef double db;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef vector<int> vi;
typedef vector<ll> vl;
```

### 7.3 调试输出
```cpp
#define debug(x) cerr << #x << " = " << x << endl
```

---

## 八、字符串处理

### 8.1 字符判断（<cctype>）
```cpp
isdigit(c);    // 是否数字
isalpha(c);    // 是否字母
isalnum(c);    // 是否字母或数字
islower(c);    // 是否小写
isupper(c);    // 是否大写
isspace(c);    // 是否空白（空格、换行、制表符等）

tolower(c);    // 转小写
toupper(c);    // 转大写
```

### 8.2 字符串分割
```cpp
// 按空格分割字符串到vector
vector<string> split(string& s) {
    vector<string> res;
    stringstream ss(s);
    string t;
    while (ss >> t) res.push_back(t);
    return res;
}
```

### 8.3 stringstream
```cpp
#include <sstream>

string s = "123 456 789";
stringstream ss(s);
int a, b, c;
ss >> a >> b >> c;  // 像cin一样读

// 数字转字符串也可以用
stringstream ss;
ss << 123 << "abc";
string res = ss.str();
```

---

## 九、内存操作

### 9.1 memset
```cpp
#include <cstring>

memset(arr, 0, sizeof(arr));      // 清零（最常用）
memset(arr, -1, sizeof(arr));     // 全部设为-1（int也可以，因为全1补码）
memset(arr, 0x3f, sizeof(arr));   // 全部设为0x3f3f3f3f（约1e9，常用INF）
```
> ⚠️ memset 是按字节赋值的！只有 0、-1、0x3f 这类每个字节相同的值才能正确设 int

### 9.2 memcpy
```cpp
memcpy(dest, src, sizeof(src));   // 拷贝内存
```

### 9.3 其他
```cpp
memcmp(a, b, n);    // 比较前n个字节
memmove(dest, src, n); // 重叠区域也安全的拷贝
```

---

## 十、其他常用函数

### 10.1 随机数
```cpp
#include <cstdlib>
#include <ctime>

srand(time(0));           // 设种子（只设一次）
int x = rand();           // 0 ~ RAND_MAX
int x = rand() % n;       // 0 ~ n-1
// 生成 [l, r] 范围：rand() % (r-l+1) + l
```
> 注意：rand() 随机性一般，对拍够用，题目不会考随机算法

### 10.2 时间
```cpp
clock_t start = clock();
// ... 代码 ...
clock_t end = clock();
double time_used = (double)(end - start) / CLOCKS_PER_SEC;
```

### 10.3 __gcd（最大公约数）
```cpp
// <algorithm> 中的内置函数（注意是双下划线）
int g = __gcd(a, b);

// C++17 有 std::gcd 在 <numeric>，但竞赛常用自己写的
long long gcd(long long a, long long b) {
    while (b) { a %= b; swap(a, b); }
    return a;
}

long long lcm(long long a, long long b) {
    return a / gcd(a, b) * b;  // 先除后乘防溢出
}
```

### 10.4 排序比较器技巧
```cpp
// 结构体排序：多关键字
struct Node {
    int a, b, c;
    bool operator<(const Node& t) const {
        if (a != t.a) return a < t.a;       // 第一关键字升序
        if (b != t.b) return b > t.b;       // 第二关键字降序
        return c < t.c;                     // 第三关键字升序
    }
};
// 定义了 operator< 就可以直接 sort，也可以放进 set/priority_queue
```

### 10.5 整数溢出注意
```cpp
// 两个int相乘可能溢出，要先转long long
long long mul(int a, int b) {
    return 1LL * a * b;  // 1LL 把后面都提升为long long
}

// 取模注意负数
int mod(int x, int p) {
    return (x % p + p) % p;  // 保证结果非负
}
```

---

## 附录：STL 时间复杂度速查

| 容器 | 插入 | 删除 | 查找 | 说明 |
|------|------|------|------|------|
| vector | 尾O(1)均摊 | 尾O(1) | O(n) | 随机访问O(1) |
| string | 尾O(1)均摊 | 尾O(1) | O(n) | 随机访问O(1) |
| deque | 头尾O(1) | 头尾O(1) | O(n) | 随机访问O(1) |
| set/map | O(log n) | O(log n) | O(log n) | 红黑树，有序 |
| unordered_set/map | O(1)平均 | O(1)平均 | O(1)平均 | 哈希，最坏O(n) |
| priority_queue | O(log n) | O(log n) | O(1)堆顶 | 堆结构 |
| stack/queue | O(1) | O(1) | O(1)首尾 | 适配器 |

| 算法 | 时间 | 说明 |
|------|------|------|
| sort | O(n log n) | 快速排序 |
| nth_element | O(n) | 快速选择 |
| lower_bound | O(log n) | 二分，需有序 |
| find | O(n) | 线性查找 |
| next_permutation | O(n) | 下一个排列 |

---

> 使用建议：
> 1. 优先用 STL，少手写，减少出错
> 2. 记清楚每个容器的迭代器失效条件
> 3. 卡常的时候考虑换 unordered 或手写数组
> 4. 边界条件多注意：空容器、单元素、越界访问
> 5. 多写多用自然就记住了，不用死背
