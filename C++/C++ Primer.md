# C++ Primer

[TOC]

## 第 2 章：变量和基本类型

### 2.1 基本内置类型

#### 初始值溢出的类型转换

- 把超出范围的初始值赋给 unsigned 类型的时候，结果是初始值对 unsigned 类型表示的数的总数取模。比如把 -1 赋给 8 bit 的 unsigned char 结果就是 (-1 % 256) = 255。实际上本质是按照 unsigned 类型的大小对初始值进行**截断**。 
- 把超出范围的初始值赋给 signed 类型的时候结果是 UB（undefined behavior）。

#### 含有无符号数的表达式

- unsigned 和 signed 的加减运算会按照的二进制加减运算直接进行，结果是 unsigned 类型。所以避免 unsigned 和 signed 混用造成设想之外的结果。尤其典型例子 STL 中各个容器的 `size()` 方法和 `sizeof` 运算符返回的类型为 `size_t`，是一个 unsigned 类型的数据，千万要注意它和 signed 数据运算的异常结果。

#### 字面值常量

**整形和浮点型：**

- 十进制字面值常量的默认是有符号的，其类型是 int、long、long long 当中能容纳该数值的最小者。
- 八进制和十六进制的字面值常量是能容纳其数值的 int、unsigned int、long、unsigned long、long long 中尺寸最小者（优先使用有符号类型）。
- 如果一个字面值常量关联的最大数据类型都不能将其存下，将会产生错误。
- short 类型没有对应的字面值。
- 负的字面值其负号并不存在于字面值之内，负号的作用是对字面值取负。
- 浮点数的字面值中 0 的几种表示方法 `.0`、`0.`、`0e0`。



**字符串：**

```c++
// 分多行书写的字符串字面值
std::cout << "a really, really long string literal "
  					 "that spans two lines." << std::endl;
```



**转义序列：**

The question mark escape sequence `\?` is used to prevent [trigraphs](https://en.cppreference.com/w/cpp/language/operator_alternative) from being interpreted inside string literals: a string such as `"??/"` is compiled as `"\"`, but if the second question mark is escaped, as in `"?\?/"`, it becomes `"??/"`. As trigraphs have been removed from C++, the question mark escape sequence is **no longer necessary**. It is preserved for compatibility with C++14 (and former revisions) and C. (since C++17)

由于 `\?` 引出的 [operator alternative](https://en.cppreference.com/w/cpp/language/operator_alternative)（装逼利器），C++ 中存在一些替换，可以在上古时期用三个字符代替一个可能不存在在键盘上的字符，或者例如可以用 `and`、 `or` 代替 `&&` 和 `||`。



**指定字面值的类型**

| 前缀 |              含义               |    类型    |
| :--: | :-----------------------------: | :--------: |
|  u   |           Unicode 16            | `char16_t` |
|  U   |           Unicode 32            | `char32_t` |
|  L   |            宽字符型             | `wchar_t`  |
|  u8  | UTF-8（仅用于字符串字面量常量） |   `char`   |

`wchar_t` 用于表示 UTF-16 或者 Unicode 码点（视平台而定）。通常为 16 位或者 32 位。

|   后缀   | 最小匹配类型  | 适用类型 |
| :------: | :-----------: | :------: |
|  u or U  |  `unsigned`   |   整形   |
|  l or L  |    `long`     |   整形   |
| ll or LL |  `long long`  |   整形   |
|  f or F  |    `float`    |   浮点   |
|  l or L  | `long double` |   浮点   |

最小匹配类型：如果后缀中有 U，该字面值将从 `unsigned int`、`unsigned long`、`unsigned long long` 中选择能匹配的空间最小的一个作为其数据类型。同时 U 可以和 L 组合成 UL 后缀。

### 2.2 变量

#### 变量定义

**初始值：**

C++ 中，初始化和赋值是**两个完全不同的操作**，即使 C++ 中初始化和赋值都可能会用到等号 =。



**列表初始化（list initialization）：**

```c++
int a = 0;
int a(0);
// since C++11 
int a = {0};
int a{0};
// list initialization 重要特点：当初始化存在丢失信息的风险时，编译器将报错
long double ld = 3.1415926
int a{ld}, b = {ld};	// 错误：转换未执行，因为存在丢失信息的危险
int c(ld), d = ld;		// 正确：转换执行，且确实丢失了部分信值
```



**默认初始化：**

> 定义变量时没有指定初始值，则变量被**默认初始化**，默认值取决于变量类型，同时变量被定义的位置也会对此有影响。

> 内置类型的变量没有被显示初始化时，值由定义的位置所决定。定义于任何函数体之外的变量被初始化位 0。一种例外情况是，定义于函数体内部的内置类型的变量将**不被初始化（uninitialized）**。未被初始化的内置类型变量的值是未定义的。

#### 变量声明和定义的关系

> 为了允许把程序拆分成多个逻辑部分来编写，C++ 支持**分离式编译**机制，即把程序分割为若干个文件，每个文件可被独立编译。

如果将程序分为多个文件，则需要有在文件间共享代码的方法。

> 为了支持分离式编译，C++ 将声明和定义区分开啦。**声明**使得名字为程序所知，一个文件如果想使用别处定义的名字则必须包含对那个名字的声明；**定义**负责创建于名字关联的实体。

> 变量声明规定了变量的类型和名字，这一点上定义与之相同。但是除此以外，**定义还申请存储空间，也可能会为变量赋一个初值**。

如果想声明一个变量而非定义它，需要添加关键字 **extern**，而且**不要显示地初始化变量**。

```c++
extern int i;	// 声明 i 而非定义 i
int j; 				// 声明并定义了 j
```

**任何包含显示初始化的声明即成为定义**。我们可以给 extern 关键字标记的变量赋一个初始值，但是这演顾总也就抵消了 extern 的作用。在函数体内部，如果试图初始化一个由 extern 关键字标记的变量，将引发错误。

```c++
extern double pi = 3.14159；	// 定义
int func() {
    extern int t = 1;	// 错误
}
```

#### 标识符

**嵌套作用域：**

当全局变量被局部变量覆盖后，可以显示地访问全局变量，如 `::global`。全局作用域本身没有名字，所以作用域符左侧为空。

### 2.3 复合类型

> **复合类型**（compound type）是指基于其他类型定义的类型，因此**指针**和**引用**也是复合类型。

> 一条声明语句由一个**基本数据类型（base type）**和紧随其后的一个**声明符（declarator）**列表组成。

这条说明了为什么语法上都允许，但是习惯上要用 `int *a` 而不用 `int* a`，因为 `*a` 是作为一个声明符(declarator)，是一个整体。

#### 引用

> 引用一旦完成初始化，将和它的初始值对象一直绑定在一起。无法令引用重新绑定到另一个对象，因此**引用必须初始化**。

> **引用即别名**。因为引用本身不是一个对象，所以**不能定义引用的引用**。

#### 指针

**空指针**

C 中 NULL 是 `(void*)0`, C++ 中 NULL 是字面量 0，定义如下：

```c++
#undef NULL
#if defined(__cplusplus)
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

> C 当中允许 `(void*)0` **隐式转换**为任意类型的空指针。

> 1. C++ 中不允许 `void*` 类型的指针**隐式转换**为其他类型的指针。
> 2. C++ 中字面量 0 可以隐式转换成任意类型的空指针常量。

`NULL` 的缺陷在于 0 的二义性，字面量 0 可以隐式转换成任意类型的空指针常量，`NULL` 作为实参的时候函数重载决议会产生二义性（`int` or `int*`）。`nullptr` 的引入解决了这一问题，但是其中机制暂不清楚。除此以外还有一个关于 C++ 中为什么不用 `(void*)0` 来取代 `NULL` 的问题，暂且不清楚，其中一个原因是 C++ 中不允许 `void*` 类型的指针**隐式转换**为其他类型的指针。

```c++
#include<iostream>
using namespace std;
void test(void *p)
{
    cout<<"p is pointer "<<p<<endl;
}
void test(int num)
{
    cout<<"num is int "<<num<<endl;
}
int main(void)
{

    test(NULL);
    return 0;
}
```

因此，C++11 引入了 `nullptr`，作为一种更好的空指针类型，它可以被转换为任意其他类型的空指针。



**指针的赋值和比较**

> 把 int 变量直接赋值给指针是错误操作，即使能把 NULL（本质是一个 0 字面量）直接赋值给指针。

```c++
int zero = 0;
int *p = zero;// 编译错误
int *q = 0;		// 编译正确
```

> 任何非 0 指针对应的条件值都是 `true`。

> 两个类型相同的合法指针可以做相等比较，如果地址相同，则结果是 `true`。



**void* 指针：**

> `void*` 是一种特殊的指针类型，可以用于存放任意对象的地址而不用关心其类型。



**指向指针的引用：**

> 引用本身不是一个对象，所以不能定义指向引用的指针。但指针是对象，所以存在对指针的引用。

```c++
int i = 42;
int *p = &i;
// 再次强调，* or & 作为类型修饰符，是整个声明符的一部分，将类型修饰符和解引用操作符（*）以取地址操作符（*）分开来，二者意义是完全不同的。类型修饰符是表示声明的变量是一个引用或者指针抑或是多个修饰符的逻辑组合。
// 同时面对复杂的声明语句时，从右往左读有助于弄清其真实含义，离变量名最近的符号对变量的类型有最直接的影响
int *&r = p;
// 修饰符反过来编译错误
int &*p_to_r; //error: 'p_to_r' declared as a pointer to a reference of type 'int &'
```

### 2.4 const 限定符

#### 常量对象跨文件使用

> 默认状态下，const 对象仅在文件内有效。

**注意，默认状态下，普通对象是在所有文件内有效的，只要在其他文件内有有效的声明。**如果在多个文件里同时定义多个相同的符号（相同作用域下），会出现重复符号的错误。

**但是 const 对象默认情况下只在本文件内有效，可以同时在多个文件内定义相同的相同的符号，相互间没有冲突和影响。**

如果要共享 const 对象，需要**显示**地用 **extern** 修饰 **const 对象的定义**。

```c++
// file_1.cc 定义并初始化了一个常量，该常量能被其他文件访问
extern const int bufSize = fcn();// 这里的 extern 表示将这个常量暴露给其他文件

// file_1.h 定义了一个常量
extern const int bufSize；// 这里的 extern 表示在文件外部寻找这个常量符号
```

这其中值得注意的两点细节：

1. 在其他文件中存在带有 extern 修饰符的「常量定义」，并不妨碍在当前文件中「定义相同符号的常量」（必须是没有被 extern 修饰的）。这种情况下两个相同的符号并不会冲突， C++ 会优先使用当前文件内的常量定义。总结起来就是：如果一个「常量的定义」要能够被其他文件使用，就必须使用 extern 修饰。但是多个不同的文件中出现相同的被 extern 修饰的「常量定义」，就会出现重复符号的编译错误（这个错误很好理解，新增一个文件寻找外部相同符号的常量时，无法决议使用哪一个）。
2. 这个例子中，file_1.cc include file_1.h，同时声明和定义，相当于定义一次，声明两次（即使是多次声明并不会有任何影响），是不会有任何错误的。

#### const 的引用

const 的引用和普通对象的引用别无二致，但是有一点是例外：

> 初始化常量引用时允许用任意表达式作为初始值，只要该表达式的结果能转换成引用对应的类型即可。尤其地，允许一个常量引用绑定非常量的对象、字面值、表达式。

```c++
int i = 42;
const int &r1 = i;	// 正确
const int &r2 = 42;	// 正确
const int &r3 = r1 * 2; // 正确
int &r4 = r1 * 2; // 错误，r4 是一个普通的非常量引用

double d = 3.0；
int &r5 = d; // 正确，类型能够正确的转换
```

常量引用之所以会有这种例外，是因为当一个常量引用绑定到一个对象上时，往往伴随着临时变量的产生。只有当初始化时要绑定的对象和常量引用的类型相同时，才不会产生临时变量（这种情况下如果绑定的对象值变化，常量引用的值也会变化）。其他情况都会产生临时变量。

```c++
double d = 42;
const int &r = d;
// 编译器的实际处理如下
// double d = 42;
// const int tmp = d;
// const int &r = tmp;

//如果类型相同，则不会产生临时变量
int i = 42;
const int &r = i;
i = 32; // r 的值也会跟着变成 32，说明 r 是绑定到对象 i 本身的
```



#### 常量表达式和 constexpr

**常量表达式**

> 常量表达式是指**值不会改变**并且在**编译过程**就能得到计算结果的表达式。

```c++
const int max_files = 20;					// max_files 是常量表达式
const int limit = max_files + 1;	// limit 是常量表达式
int staff_size = 27;							// staff_size 不是常量表达式，因为它是一个变量
const int sz = get_size();				// sz 不是常量表达式，因为它的值直到运行时才能获得
```

注意：

- 常量表达式只看是否符合上述定义，与是否有 constexpr 修饰无关，这是两个概念。

**constexpr 变量**

被 constexpr 修饰的变量会在编译期检查其值是否是一个「常量表达式」。

![image-20210812010157699](C++ Primer.assets/image-20210812010157699.png)

**指针和 constexpr**

![image-20210812013848374](C++ Primer.assets/image-20210812013848374.png)

**指针、引用和 constexpr**

尽管指针和引用都能定义成 constexpr，但它们的初始值都受到严格限制。即必须指向或者绑定到一个固定地址（全局变量或者静态变量或者字符串字面量等，即编译期能确定变量地址）。

### 2.5 处理类型

#### 类型别名

有两种方法可以用于定义类型别名（type alias）。传统的方法是使用关键字 `typedef`，C++11 引入了别名声明（alias declaration）。

```c++
typedef double wages;
using INT = int;
```



**指针、常量和类型别名**

当类型别名与指针、常量复合时，类型别名应该作为一个整体，而不应该替换成别名本来的样子，这样是错误的。

```c++
typedef char *pstring;
const pstring cstr = nullptr;	// 指向 char 的常量指针
const char * cstr_ = nullptr;	// 指向 char 常量的指针
const pstring *ps = nullptr;	// 指向一个指向 char 的常量指针的指针
const char **ps_ = nullptr;		// 指向一个指向 char 常量的指针的指针
```

#### auto 类型说明符

> `auto` 定义的变量必须有初始值。
>
> `auto` 一条语句同时声明多个变量，多个变量的类型应该一致。



**复合类型、常量和 auto**

编译器推断出来的 `auto` 类型有时候和初始值的类型并不完全一致，编译器会适当地改变结果类型使其更符合初始化规则。

1. 引用其实是使用引用的对象。
2. auto 一般会忽略顶层 const，同时底层 const 则会保留下来。
3. 如果希望推断出的 auto 类型是一个顶层 const，需要显示地指出。
4. 如果希望推断出的 auto 类型是一个引用，需要显示地指出。
5. 设置一个类型为 auto 的引用时，初始值中的顶层 const 属性仍然保留。
6. 要在一条语句中定义多个变量，切记饿，符号 & 和 * 只从属于某个声明符，而非基本数据类型的一部分，因此初始值必须是同一种类型。

总结起来就是：在保持声明合法的同时尽量减少限定符（例如去掉顶层 const 声明一样合法，但是去掉底层 const 声明是非法的），来达到提高灵活性的目的。

```c++
int i = 0, &r = i;
auto a = r;								// 1: a 是一个整数
const int ci = i, &cr = ci;
auto b = ci;							// 2: b 是一个整数（顶层 const 被忽略）
auto c = cr;							// 2: c 是一个整数
auto d = &i;							// d 是一个指针
auto e = &ci;							// 2: e 是一个指向常量的针（底层 const 保留）
const auto f = ci;				// 3: f 是 const int
auto &g = ci;							// 5: g 是一个整型常量引用
auto &h = 42;							// 错误，不能为非常量引用绑定到字面值
const auto &j = 42;				// 正确：可以为常量引用绑定字面值
auto k = ci, &l = i; 			// 6: k 是 int，l 是 引用
auto &m = ci, *p = &ci;		// 6: m 是对整型常量对引用， p 是指向整型常量的指针
auto &n = i, *p2 = &ci;		// 错误， i 的类型是 int 而 ci 的类型是 const int
```

##### decltype 类型提示符

**decltype 和引用**

1. 引用从来都作为其所指的对象的同义词出现，只有在 `decltype` 处是个例外。
2. <u>有些**表达式**将向 decltype 返回一个引用类型，一般来说这种情况下意味着该表达式的结果对象能作为一条赋值语句的左值。</u>
3. decltype 的结果类型与**表达式**形式密切相关。
4. 对于 decltype 使用的是一个不加括号的变量，则得到的结果就是该变量的类型；如果给变量加上了一层或多层括号（结果为左值的表达式），结果将会是引用。

```c++
int i = 42, *p = &i, &r = i;
decltype(r + 0) b;	// b 是 int 类型
decltype(*p) c;			// 错误，c 是引用类型，必须初始化（*p 也是表达式，结果是一个左值）

decltype((i)) d;		// 错误，d 是引用类型，必须初始化
decltype(i) e;			// 正确，e 是一个（为初始化的）int
```

### 2.6 自定义数据结构

> C++11 新标准规定，可以为数据成员提供一个**类内初始值（in-class initializer）**。没有初始值的成员变量将被默认初始化。

⚠️注意：类内初始化可以使用 list initialization 或者 = 赋值的方式，但不能使用 `(expr)` 的方式。

```c++
struct data {
    std::string bookNo;			// 默认初始化为 ""
  	unsigned int a = 0;			// 正确
  	unsigned int b = {0};		// 正确
  	unsigned int c{0};			// 正确
  	unsigned int d(0);			// 编译错误
}

// 之所以不能使用 () 的方式初始化，是因为可能会对编译器产生语义问题
struct S {
    typedef int x;
  	int func(x);	// 应该被当作函数声明
}

struct S {
  	static int x;
  	int func(x);	// 应该被当作变量初始化
}
```

## 第 3 章 字符串、向量和数组

### 3.1 命名空间的 using 声明

> 头文件不应该包含 using 声明。

### 3.2 标准库类型 string

```c++
std::string word;
while (cin >> word)	// 遇到 EOF 或非法输入时会停止
	std::cout << word << std::endl;

std::string line;
// getline 第一个参数表示从哪个输入流去读取数据,和 cin 一样会返回流参数，可以用来判断输入是否结束
// getline 每次读取一行，换行符也被读进来了，但是这个换行符会被丢弃，string 对象中并没有换行符
while (std::getline(std::cin, line))
    std::cout << line << std::endl;

std::string line;
while (std::getline(std::cin, line))
    if (!line.empty())
        std::cout << line << std::endl;

// size 方法返回的是 std::string::size_type 类型，实际上就是 std::size_t
while (std::getline(cin, line))
    if (line.size() > 80)
        std::cout << line << std::endl;
```

> **使用 C++ 版本的 C 标准库头文件**
>
> C++ 标准库中除了定义 C++ 语言特有的功能外，也兼容了 C 语言的标准库。C 语言的头文件形如 name.h，C++ 则将这些文件命名为 cname。即去掉 .h 后缀，在文件名前添加字母 c，这里的 c 表示这是一个属于 C 语言标准库的头文件。
>
> 因此，例如 cctype 头文件和 ctype.h 头文件内容是一样的，只不过从命名规范上来讲前者更符合 C++ 语言的要求。特别的，在名为 cname 的头文件中定义的名字从属于命名空间 std，而定义在名为 .h 的头文件中的名字则不然。

range for 也适用于 std::string。

```c++
std::string s("hello world!");
for (auto c : s)
	std::cout << c;

// 如果想要修改 string 的内容
for (auto &c : s)
	c = std::toupper(c);
cout << s << endl;
```



看到 P84.

## 沧海遗珠

- nullptr：nullptr 实现与机制。
- constexpr：编译期对象构造、constexpr 修饰的指针和引用。
- decltype：左值的计算结果为该左值的引用类型
- std::string：如何实现 `"a literal string" + std::string("a std string object")`。`operator +` 重载为成员方法和普通函数的区别。
- SFINAE

>  **完整的重载匹配顺序**（SFINAE 下）
>
> 1. 找到候选函数，去掉其中会引发编译错误的。
> 2. 完全匹配 > 提升转换 > 标准转换 > 用户定义的转换。
>     - 完全匹配：
>         1. 值 ↔ 引用
>         2. `[]` → `*`（对函数签名来说只有 `*` 而没有 `[]` 这种类型）
>         3. `type(argument-list)` → `(type *)(argument-list)`（函数指针）
>         4. `type` → `const` / `volatile` `type`
>         5. `type *` → `const type *`
>         6. `type *` → `volatile type *`
>     - 提升转换：`char` / `short` → `int` / `float` → `double`。
>     - 标准转换：`int` → `char`，`long` → `double`。
>     - 用户定义的转换：类中的构造函数，类型转换函数等。
> 3. 非模板函数优先于模板函数。
> 4. 寻找“最佳匹配”，我自己也不是很了解，可以参见 《C++ Primer Plus（第五版）》8.5.4 或上网搜索。
>
> 若经过以上过程仍有多个候选函数，则会引发二义性错误。

