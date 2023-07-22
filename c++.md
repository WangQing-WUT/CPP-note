## 字符串

```c++
#include <string.h>
const char* str1; // 静态存储区
char str2[];      // 堆
string str3;
char* str4 = new char[strlen(str1) + 1]; // strlen(str)字符串长度

strcpy_s(str4, strlen(str1) + 1, str1);  // strcpy_s(char*, int, const char*)字符串拷贝
strcpy(str4, str2);                      // strcpy(char*, const char*)字符串拷贝
str4 = s.c_str();                        // string => char*
strcmp(str1,str2); // strcmp(const char*, const char*)字符串比较，大于返回1，相等返回0，小于返回-1
                   // 虽然参数类型是const char*，实际情况下char *和char []都是可以作为参数的
                   // string类型可以直接使用==或！=
```



## cin对象

`cin`读取会忽略空格及换行符

如果想读取空格及换行符可使用`cin.get(char)`

`cin`读取到文件尾`EOF`时会将`eofbit`和`failbit`设置为1（默认为0），`cin`将失效

```c++
cin.eof();
cin.fail();  // 常使用fail()成员函数判断文件尾
cin.clear(); // 清除eofbit和failbit标记
cin.get(char); // 不会读取到EOF
int ch = cin.get(); // 会读到EOF(-1)
```



## 字符函数库cctype

返回值为`int`类型，如果是返回非0值，如果不是返回0。

| **函数名称** |                           **功能**                           |
| :----------: | :----------------------------------------------------------: |
|  isalnum()   |                          字母或数字                          |
|  isalpha()   |                             字母                             |
|  iscntrl()   |                           控制字符                           |
|  isdigit()   |                             数字                             |
|  isgraph()   |                所有可以打印的字符（除了空格）                |
|  islower()   |                           小写字母                           |
|  isprint()   | 所有可以打印的字符（包括空格，大于0x1f (US)，小于0x7f (DEL)） |
|  ispunct()   |                           标点符号                           |
|  isspace()   | 空白字符（空格、换行符\n、制表符\t、垂直制表符\v、换页符\f、回车\r） |
|  isupper()   |                           大写字母                           |
|  isxdigit()  |                十六进制数字（0-9，a-f，A-F）                 |
|  isblank()   |                             空格                             |



| **函数名称** |                     **返回值**                     |
| :----------: | :------------------------------------------------: |
|  tolower()   | 如果参数是大写字母，则返回其小写，否则返回该参数值 |
|  toupper()   | 如果参数是小写字母，则返回其大写，否则返回该参数值 |



## 文件输入/输出

```c++
#include <fstream>
#include <iostream>
using namespace std;

int main() {
	ofstream fout;
	fout.open("test.txt");
	if (!fout.is_open()) {
		exit(EXIT_FAILURE);
	}

	for (int i = 0; i < 20; i++) {
		fout << i << endl;
	}

	fout.close();

	ifstream fin;
	fin.open("test.txt");
	if (!fin.is_open()) {
		exit(EXIT_FAILURE);
	}

	int num;
	while (fin >> num) {
		cout << num << endl;
	}
	fin.close();

	return 0;

}
```

如果读取到文件尾`EOF`，`eof()`可返回`true`；

如果读取类型异常或到文件尾`EOF`，`fail()`可返回`true`；

如果文件受损或硬件异常，`bad()`可返回`true`；

`good()`可在没有任何错误的情况下返回`true`。



## 函数

### const

`const`限定符定义的常量不可以用普通指针指向，只能使用`const`指针指向。const 常量只能定义时初始化，不能进行赋值。

应尽可能地使用`const`修饰只读量。

```c++
int age = 18;
int sage = 20;

const int* pt1 = &age;  // 不可以使用*pt1修改age值，但是pt1值可以修改
pt1 = 30;               // not allowed
pt1 = &sage;            // allowed，但依旧不能修改sage的值
pt1 = 30;               // not allowed


int* const pt2 = &age; // 不可以修改pt2值，但是可以通过*pt2修改age值
*pt2 = 30;              // allowed
pt2 = &sage;            // not allowed

const int* const pt3 = &age; // pt3的值和通过*pt3修改age值均不允许
pt3 = &sage;                  // not allowed
*pt3 = 30;                    // not allowed
```

### 形参数组

函数参数为数组，建议使用`int arr[]`作为参数定义，`int *p`更强调是指针。

二维数组作为参数传递，列数需要确定，行数通过参数传递，`sum1`和`sum2`两种方式均可，`sum2`方式可读性更高。

```c++
int data[3][4] = {{1,2,3,4}, {5,6,7,8}, {9,10,11,12}};
int sum1(int (*ar2)[4], int size);
int sum2(int ar2[][4], int size);
ar2[r][c] = *(*(ar2 + r) + c); // l
```

### 函数指针

函数指针，指向指针的指针。

```c++
const double* f1(int a[], int b);
const double* f2(int [], int b);
const double* f3(int* a, int b); // 函数f1, f2, f3等价

// 函数指针p1, p2
// 函数指针数组pa
// 指向函数指针数组pa第一个元素的指针pb
// 指向包含三个函数指针数组的指针pc
const double* (*p1)(int*, int) = f1;
typedef const double* (*p_func)(int*, int);
p_func p2 = f2;              // 等价于const double* (*p2)(int*, int) = f2;
p_func pa[3] = {f1, f2, f3}; // 等价于const double* (*pa[3])(int*, int) = {f1, f2, f3};
p_func* pb = pa;             // 等价于const double* (**pb)(int*, int) = pa;
p_func (*pc)[3] = &pa;       // 等价于const double* (*(*pc)[3])(int*, int) = &pa;
```

### 内联函数

内联函数编译时，将会使用内联函数源代码替换内联函数调用。

函数调用有开销，内联函数去掉了该开销，使用时通常简单的代码块用内联函数替代，计算量大或代码较多的函数不适合用内联函数替代。

内联和宏的区别在于，内联是拿函数原型去替代，而宏会拿同名的参数去替代。

```c++
#define SQUARE(X) X*X
inline square(int x) { return x * x; }

int num = 2;
a = SQUARE(4);     // a = 4 * 4;
b = SQUARE(2 + 3); // b = 2 + 3 * 2 + 3;
c = SQUARE(num++); // c = num++ * num++; num最终等于4
d = square(2 + 3); // d = 5 * 5;
e = square(num++); // e = num * num; num++; num最终等于3
```

### 引用变量

`c++11`特性，`c语言`不支持，通常用于结构，引用必须在定义的时候初始化。

```c++
int a = 100;
int k = 20;
int &b = a; // b是a的一个别名
b++;        // 等价于a++;
b = k;      // 等价于a = k;
int* pt = &a;
int &c = *pt; // 实际等于int &c = a;
pt = &k;      // pt指向了k，但不会改变c是a的引用
```

引用参数常见的是左值引用`&`，必须传递左值，左值为赋值表达式左边可以出现的元素，包括变量、数组元素、结构成员、引用和解除引用的指针，非左值（右值）包括表达式和常量。

`c++11`新增右值引用`&&`，可以传递右值。

函数形参应尽可能使用`const`引用和指针。

### 函数模板

`c++98`1前格式为`template <class T>`，也可以写作`template <typename T>`，模板通常位于头文件中。

```c++
template <typename T>
void Swap(T &a, T &b) {
    T temp;
    temp = a;
    a = b;
    b = temp; 
}
```

优先级：非函数模板>显示具体化（显式实例化）>函数模板（隐式实例化）。

```c++
void Swap(job &a, job &b); //非函数模板

template <typename T> //函数模板
void Swap(T &a, T &b);

template <> void Swap(job &a, job &b); // 显式具体化，两种格式均可
template <> void Swap<job>(job &a, job &b);

template void Swap<job> (job &a, job &b); // 显式实例化，直接告诉编译器创建对应函数

int main() {
    int a = 1;
    int b = 2;
    Swap(a, b); //隐式实例化
    template void Swap<char> (char &a, char &b); // 显式实例化
    char x, y;
    Swap(x, y);
}
```

函数重载及函数模板选择优先级：完全匹配>提升转换>标准转换>用户定义的转换。



## 内存模型

|       **声明**       | **作用域**  | **链接性** | **持续性** | **存储位置** |
| :------------------: | :---------: | :--------: | :--------: | :----------: |
|        int x;        | 代码块/文件 |  无/外部   | 自动/静态  |  栈/全局区   |
|   register int x;    | 代码块/文件 |  无/外部   |    静态    |    寄存器    |
|    static int x;     | 代码块/文件 |  无/内部   |    静态    |    全局区    |
| namespace { int x; } | 代码块/文件 |  无/内部   |    静态    |    全局区    |
|     const int x;     | 代码块/文件 |  无/内部   |    静态    |    全局区    |
| extern const int x;  |    文件     |    外部    |    静态    |    全局区    |
|     function();      |    文件     |    外部    |     ——     |      ——      |
|  static function();  |    文件     |    内部    |     ——     |      ——      |

`const`常量被外部引用时必须声明和引用都要加`extern`关键字，且只有一处初始化。

### new 与定位 new

`new`运算在堆上分配空间，常用初始化如下：

```c++
int *p = new int(6);               // *p = 6
int *ar = new int[4] {2, 4, 6, 8}; // *ar = [2, 4, 6, 8]
```

定位`new`运算符，使用指定位置分配空间，不能使用`delete`回收。

```c++
char buffer[500];
int *p1 = new int[20];          // 堆上寻找新内存分配给p1
int *p2 = new (buffer) int[20]; // 把buffer到buffer + 20 * sizeof(int)分配给p
```

## 对象和类

`private`数据隐藏，`public`抽象接口。

定义位于类声明中的函数自动成为内联函数。

构造函数可重载，无返回值，不可主动调用。构造函数未声明时，系统会默认添加一个无参数无处理的默认构造函数；构造函数声明时，系统不再生成默认构造函数，应注意编写该默认构造函数，在声明未初始化的类对象时可以初始化所有私有数据，否则编译失败。

析构函数原型唯一，有且仅有一个，不定义时默认创建空构造函数，例`~Stock() {};`，对象过期时自动调用，也可以显式调用，常用来执行清理操作，如内存回收等。

初始化方式设置类对象的值比赋值方式效率更高。

```c++
Stock stock1("Test", 23, 3.5);
// 调用构造函数初始化

Stock stock2 = Stock("Test", 24, 3.5); 
// 情况1: 同上
// 情况2: 有些编译器会创建临时对象，然后复制到stock2中，然后销毁临时对象（时间不定），此时会调用析构函数

stock1 = Stock("Test", 25, 3.5);
// 同情况2

stock1 = stock2; 
// 将stock2中数据成员复制到stock1中

Stock stock3 = {"Test", 26, 3.5};
Stock stock3 {"Test", 26, 3.5};
// c++11支持列表初始化

void show() const;
void Stock::show() const;
// 类成员函数不修改类数据成员时，通常后面加上const关键字，声明为const成员函数，否则下面的语句将编译失败
const Stock stock4 = {"Test", 27, 3.5};
stock4.show(); // 编译失败
```

类中私有变量定义常量。

```c++
class Test
{
private:
	const int Months = 12;
    double a[Months];
    ......
}
// 无效定义

class Test
{
private:
	static const int Months = 12;
    double a[Months];
    ......
}
// 有效定义，同其他静态变量一样存储在全局区

class Test
{
private:
	enum {Months = 12};
    double a[Months];
    ......
}
// 有效定义，使用枚举

class Test
{
private:
	enum {Months = 12};
    double a[Months];
    ......
}
// 有效定义，使用枚举

enum egg {Small, Medium, Large, Jumbo};
enum t_shirt {Small, Medium, Large, Xlarge};
int a = Small;
// 编译失败

enum class egg {Small, Medium, Large, Jumbo};
enum class t_shirt {Small, Medium, Large, Xlarge};
int a = egg::Small;
// 编译成功，可用struct代替class

enum egg {Small, Medium, Large, Jumbo};
enum class t_shirt {Small, Medium, Large, Xlarge};
int a = Small;                  // 允许
if (Small < Jumbo) {};          // 允许
int b = t_shirt::Small;         // 不允许
if (Small < t_shirt::Small) {}; // 不允许
int c = int(t_shirt::Small);    // 允许
// 作用域内枚举不能隐式转换，可以显式转换

enum class : short egg {Small, Medium, Large, Jumbo};
// c++11可以指m定底层类型，默认为int
```

## 使用类

### 运算符重载

```c++
T operator[op](const T& t) const;
```

[op]可以是以下运算符，其中=、()、[]、->只能通过类成员函数重载，其他的既可以通过成员函数重载，也可以通过非成员函数重载。

|  +   |  -   |  *   |   /    |   %   |    ^     |
| :--: | :--: | :--: | :----: | :---: | :------: |
|  &   |  \|  |  ~=  |   !    |   =   |    <     |
|  >   |  +=  |  -=  |   *=   |  /=   |    %=    |
|  ^=  |  &=  | \|=  |   <<   |  >>   |   >>=    |
| <<=  |  ==  |  !=  |   <=   |  >=   |    &&    |
| \|\| |  ++  |  --  |   ,    |  ->*  |    ->    |
|  ()  |  []  | new  | delete | new[] | delete[] |



```c++
class Time {
private:
    int hours;
    int minutes;
public:
    Time( );
    Time(int h, int m = 0);
    ......
    Time operator+(const Time& t) const;
    void Show ( ) const;
};

Time Time::operator+(const Time & t) const{
    Time sum;
    sum.minutes = minutes + t.minutes;
    sum.hours = hours + t.hours + sum.minutes / 60 ;
    sum.minutes %= 60 ;
    return sum;
}

Time coding, fixing, total;
total = coding + fixing; // 等价于total = coding.operator+(fixing);

Time t1, t2, t3, t4;
t4 = t1 + t2 + t3; // 等价于t4 = t1.operator+(t2.operator+(t3));
```



### 友元函数

在类中声明，加上`friend`关键字，定义时不加，不是成员函数，不能加`const`，不能通过成员运算符调用，与成员函数有相同的访问权限。

`operator`重载二元运算符时，作为类成员函数时，类对象this指针作为第一个参数，此时重载的`operator`只有一个参数，类对象本身作为左边的值，参数为右边的值；作为友元函数时，第一个参数是左边的值，第二个参数为右边的值。

```c++
class Time {
private:
    int hours;
    int minutes;
public:
    Time( );
    Time(int h, int m = 0);
    ......
    Time operator+(const Time& t) const;
    void Show ( ) const;
    friend Time operator*(double m, const Time& t);
};

Time operator*(double m, const Time& t)
{
    ......
}
```

重载`<<`运算符

```c++
class Time {
private:
    int hours;
    int minutes;
public:
    Time( );
    Time(int h, int m = 0);
    ......
    Time operator+(const Time& t) const;
    void Show ( ) const;
    friend Time operator*(double m, const Time& t);
    friend void operator<<(ostream& os, const Time& t);
};

void operator<<(ostream& os, const Time& t) {
    os << t.hours << " hours, " << t.minutes << " minutes";
}

Time time;
cout << time; // 正确
cout << "Time: " << time; // 错误
```

```c++
class Time {
private:
    int hours;
    int minutes;
public:
    Time( );
    Time(int h, int m = 0);
    ......
    Time operator+(const Time& t) const;
    void Show ( ) const;
    friend Time operator*(double m, const Time& t);
    friend ostream& operator<<(ostream& os, const Time& t);
};

ostream& operator<<(ostream& os, const Time& t) {
    os << t.hours << " hours, " << t.minutes << " minutes";
    return os;
}

Time time;
cout << "Time: " << time; // 正确
```



### 类的自动转换和强制类型转换

只接受一个参数或除第一个参数外其他参数都有缺省值的构造函数可作为转换构造函数，自动完成隐式类型转换，关键字`explicit`可以关闭隐式转换特性，但仍然可以显式转换。

```c++
class Stonewt {
private:
	enum { Lbs_per_stn = 14 };
	int stone;
	double pds_left;
	double pounds;
public:
	Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	~Stonewt();
	void show_lbs()const;
	void show_stn() const;
};

// 隐式转换
// 1.类对象初始化为double值
Stonewt myCat = 19.6;
// 2.double值赋给类对象
Stonewt myCat;
myCat = 19.6;
// 3.返回值被声明为Stonewt的函数返回double值
Stonewt func(Stonewt &s) {
    ......
    return 19.6;
}
// 4.double值传递给接受Stonewt参数的函数
Stonewt st = func(13.9);
// 5.前4种情况下，使用可以转换为double的内置类型
Stonewt myCat = 19;
```

```c++
class Stonewt {
private:
	enum { Lbs_per_stn = 14 };
	int stone;
	double pds_left;
	double pounds;
public:
	explicit Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	~Stonewt();
	void show_lbs()const;
	void show_stn() const;
};

// 显式转换
Stonewt myCat;
myCat = 19.6; // 不允许
myCat = Stonewt(19.6); // 允许
myCat = (Stonewt)19.6; // 允许
```

构造函数：用于某种类型到类类型的转换。

转换函数：用于类类型到某种类型（typeName）的转换。

转换函数必须是类方法，不能指定返回类型，不能有参数。同样，转换函数可以被`explicit`修饰，禁止隐式转换。

```c++
operator typeName();
```

```c++
class Stonewt {
private:
	enum { Lbs_per_stn = 14 };
	int stone;
	double pds_left;
	double pounds;
public:
	explicit Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	~Stonewt();
	void show_lbs()const;
	void show_stn() const;
    operator int() const;
    operator double() const;
};

Stonewt::operator int() const
{
    return int(pounds + 0.5);
}

Stonewt::operator double() const
{
    return pounds;
}

Stonewt poppins = Stonewt(13.9)
double db = poppins; // 允许
cout << int(poppins); // 允许
cout << poppins; // 不允许，定义了两种类型转换存在二义性，如果只定义一种可以隐式转换
```



## 类和动态内存分配

`String`类底层实现如下。

类所有对象共享静态类成员`num_strings`，不能在类声明时初始化静态类成员，在类方法文件中使用作用域运算符`::`对类的`static`变量初始化。静态类成员为整型或枚举型的`const`时，可以在类声明中初始化。

mystring.h

```c++
#pragma once
#include <iostream>
using std::ostream;
#ifndef MYSTRING_H_
#define MYSTRING_H_

class String {
private:
	char* str;
	int len;
	static int num_strings;
public:
	String(const char* s);
	String();
	~String();
	friend ostream& operator<<(ostream& os, const String& st);
};
#endif // !MYSTRING_H_
```

mystring.cpp

```c++
#include "mystring.h"
#include <iostream>
#include <cstring>

using std::ostream;
using std::cout;
using std::endl;

int String::num_strings = 0;

String::String(const char* s)
{
	len = std::strlen(s);
	str = new char[len + 1];
	strcpy_s(str, len + 1, s);
	num_strings++;
	cout << num_strings << ": \"" << str << "\" object created." << endl;
}

String::String()
{
	len = 4;
	str = new char[4];
	strcpy_s(str, 4, "C++");
	num_strings++;
	cout << num_strings << ": \"" << str << "\" default object created." << endl;
}

String::~String()
{
	cout << "\"" << str << "\" object deleted." << endl;
	--num_strings;
	cout << num_strings << " left." << endl;
	delete[] str;
}

ostream& operator<<(ostream& os, const String& st)
{
	os << st.str;
	return os;
}
```

main.cpp

```c++
#include <iostream>
#include "mystring.h"

using std::ostream;
using std::cout;
using std::endl;

void callme1(String&);
void callme2(String);

int main() {
	{
		String s1("s1");
		String s2("s2");
		String s3("s3");
		cout << "s1: " << s1 << endl;
		cout << "s2: " << s2 << endl;
		cout << "s3: " << s3 << endl;

		callme1(s1);
		cout << "s1: " << s1 << endl;
		callme1(s2);
		cout << "s2: " << s2 << endl;

		String s4 = s3;
		cout << "s4: " << s4 << endl;
		String s5;
		s5 = s1;
		cout << "s5: " << s5 << endl;
	}
	return 0;
}

void callme1(String& rs) {
	cout << "String passed by reference: \"" << rs << "\"." << endl;
}

void callme2(String vs) {
	cout << "String passed by value: \"" << vs << "\"." << endl;
}
```

输出

```shell
1: "s1" object created.
2: "s2" object created.
3: "s3" object created.
s1: s1
s2: s2
s3: s3
String passed by reference: "s1".
s1: s1
String passed by reference: "s2".
s2: s2
s4: s3
4: "C++" default object created.
s5: s1
"s1" object deleted.
3 left.
"s3" object deleted.
2 left.
"葺葺葺葺D蠹? object deleted.
1 left.
```

delete顺序：s5->s4->s3->s2->s1。

`String s4 = s3;`等同于`String s4 = String(s3);`,用对象初始化对象将会调用复制构造函数，同时使用默认的赋值运算符。

自动提供的特殊类成员函数：

- 默认构造函数（默认什么都不做）
- 默认析构函数（默认什么都不做）
- 复制构造函数
- 赋值运算符
- 地址运算符（隐式地址运算符返回`this`指针）

### 复制构造函数

用于将一个对象复制到新创建的对象中，用于初始化过程（包括按值传递参数），函数原型如下：

```c++
Class_name(const Class_name &);
```

以下四种声明都将调用复制构造函数：

```
String ditto(motto);
String metoo = motto;
String also = String(motto);
String* pString = new String(motto);
```

默认复制构造函数逐个复制非静态成员的值（浅复制），若使用了复制构造函数初始化，析构函数中使用了释放内存操作，可能会导致程序运行异常，当成员变量含有使用 new 初始化的指针成员，应自定义复制构造函数进行深复制。

```c++
String::String(const String& st)
{
	num_strings++;
	len = st.len;
	str = new char[len + 1];
	strcpy_s(str, len + 1, st.str);
	cout << num_strings << ": \"" << str << "\" object created\n";
}
```

### 赋值运算符

赋值运算符原型如下：

```c++
Class_name& Class_name::operator=(const Class_name&);
```

当将现有对象赋值给另一个对象时，将使用重载的赋值运算符，初始化对象不使用赋值运算符，使用复制构造函数。默认赋值构造函数与复制构造函数类似，采用浅复制。

```c++
String& String::operator=(const String& st)
{
	if (this == &st) {
		return *this;
	}
	delete[] str;
	len = st.len;
	str = new char[len + 1];
	strcpy_s(str, len + 1, st.str);
	return *this;
}
```

考虑以下代码：

```c++
String name;
name[40];
cin.getline(temp, 40);
name = temp;
```

最后一句赋值操作将执行以下步骤：

1. 使用 String(const char*) 创建临时 String 对象（一个参数的构造函数为转换函数）
2. 使用 String& String::operator=(const String&) 将临时对象信息复制到 name 对象中
3. 调用析构函数删除临时对象

这种方式并不高效，可以直接重载赋值运算符，不需要创建和删除临时对象。

```c++
String& String::operator=(const char* s)
{
	delete[] str;
	len = std::strlen(s);
	str = new char[len + 1];
	strcpy_s(str, len + 1, st.st);
	return *this;
}
```



### 空指针

c++98中可以使用0，(void*)0和NULL表示空指针，c++11在此基础上引入nullptr关键字表示空指针。

### 比较成员函数

使用标准成员函数 strcmp(st1, st2) 比较两个字符串大小，若按照字符顺序，st1 位于 st2 前，该函数返回一个负值；若两个字符串相同，则返回0；若 st1 位于 st2 后，该函数返回一个正值。

```c++
bool operator<(const String& st1, const String& st2)
{
	return (std::strcmp(st1.str, st2.str) < 0);
}

bool operator>(const String& st1, const String& st2)
{
	return st2 < st1;
}

bool operator==(const String& st1, const String& st2)
{
	return false; return (std::strcmp(st1.str, st2.str) == 0);
}
```



### 使用中括号访问字符

一个用于普通String对象中括号访问字符，可以修改字符；一个用于const类型String对象访问字符，不可修改字符。

```c++
char& String::operator[](int i)
{
	return str[i];
}

const char& String::operator[](int i) const
{
	return str[i];
}
```



### 静态类成员函数

成员函数声明添加 static 关键字即为静态类成员函数，不能通过类对象调用。静态类成员函数不能使用 this 指针，只能访问静态数据成员。公有声明的静态类成员函数可以它通过类名和 :: 运算符来调用。



### 再谈定位 new

定位 new 创建的对象无法隐式调用析构函数释放内存，需要显式调用析构函数，先按照创建顺序相反的顺序释放对象，再释放缓冲区。

```c++
char* buffer = new char[512];
String *p1, *p2;
p1 = new (buffer) String;
p2 = new (buffer + sizeof(String)) String("Test");
p3 -> ~String();
p1 -> ~String();
delete[] buffer;
```

### 队列

myqueue.h

```c++
class Customer
{
private:
	long arrive;
	int processtime;
public:
	Customer() { arrive = processtime = 0; }
	void set(long when);
	long when() const { return arrive; }
	int ptime() const { return processtime; }
};

typedef Customer Item;

class Queue {
private:
	struct Node
	{
		Item item;
		struct Node* next;
	};
	enum { Q_SIZE = 10 };
	Node* front;
	Node* rear;
	int items;
	const int qsize;
	Queue(const Queue& q) : qsize(q.qsize), front(q.front), rear(q.rear), items(q.items) { }
	Queue& operator=(const Queue& q) { return *this; }

public:
	Queue(int qs = Q_SIZE);
	~Queue();
	bool isempty() const;
	bool isfull() const;
	int queueCount() const;
	bool enqueue(const Item& item);
	bool dequeue(Item& item);
};
```

myqueue.cpp

```c++
#include <cstdlib>
#include "myqueue.h"

Queue::Queue(int qs) : qsize(qs), front(nullptr), rear(nullptr), items(0)
{
}

Queue::~Queue()
{
	Node* temp;
	while (front != nullptr) {
		temp = front;
		front = front->next;
		delete temp;
	}
}

bool Queue::isempty() const
{
	return items == 0;
}

bool Queue::isfull() const
{
	return items == qsize;
}

int Queue::queueCount() const
{
	return items;
}

bool Queue::enqueue(const Item& item)
{
	if (isfull()) {
		return false;
	}
	Node* add = new Node;
	add->item = item;
	add->next = nullptr;
	items++;
	if (front == nullptr) {
		front = add;
	}
	else {
		rear->next = add;
	}
	rear = add;
	return true;
}

bool Queue::dequeue(Item& item)
{
	if (front == nullptr) {
		return false;
	}
	item = front->item;
	items--;
	Node* temp = front;
	front = front->next;
	delete temp;
	if (items == 0) {
		rear = nullptr;
	}
	return true;
}

void Customer::set(long when)
{
	processtime = rand() % 3 + 1;
}

```

成员初始化列表，跟在类构造函数参数列表后（前面带上冒号），由逗号分隔的初始化列表，可以初始化类成员变量（其中 const 数据成员和引用数据成员必须在这里初始化，他们都有只能在创建时初始化的特性），初始化初值可以是常量，也可以是参数列表中的参数。该过程在对象创建时完成，还没有执行构造函数代码块，初始化顺序由数据成员在类声明中顺序决定，与成员初始化列表顺序无关。

```c++
Queue::Queue(int qs) : qsize(qs), front(NULL), rear(NULL), items(0)
{
}
```

若希望对象不能被复制，可以将复制构造函数和赋值运算符重载定义为私有方法。

```c++
class Queue
{
private:
	Queue(const Queue& q) : qsize(0) { }
	Queue& operator=(const Queue& q) { return *this;}
}
```



## 类继承

### 基类与派生类

派生类构造函数要使用成员初始化列表调用基类构造函数，若没有显式调用，编译器默认调用基类默认构造函数。

派生类可以使用基类非私有方法。

基类指针可以指向派生类对象（反向无效），基类引用可以引用派生类对象（反向无效），但基类指针或引用只能调用基类方法。利用此特性，可以使用派生类对象初始化基类对象（调用基类复制构造函数），也可以使用派生类对象给基类对象赋值（调用基类重载的赋值运算符）。

创建派生类对象过程如下：

1. 调用基类构造函数（若没显式调用，将调用默认构造函数）

2. 调用派生类构造函数

派生类对象销毁过程如下：

1. 调用派生类析构函数
2. 调用基类析构函数

tabtenn.h

```c++
#pragma once
#ifndef TABTENN_H_
#define TABTENN_H_
#include <string>
using std::string;

class TableTennisPlayer {
private:
	string firstname;
	string lastname;
	bool hasTable;
public:
	TableTennisPlayer(const string& fn = "none", const string& ln = "none", bool ht = false);
	void Name() const;
	bool HasTable() const { return hasTable; }
	void ResetTable(bool ht) { hasTable = ht; }
};

class RatedPlayer : public TableTennisPlayer {
private:
	unsigned int rating;
public:
	RatedPlayer(unsigned int r = 0, const string& fn = "none", const string& ln = "none", bool ht = false);
	RatedPlayer(unsigned int r, const TableTennisPlayer& tp);
	unsigned int Rating() const { return rating; }
	void ResetRating(unsigned int r) { rating = r; }
};

#endif // !TABTENN_H
```



tabtenn.cpp

```c++
#include "tabtenn.h"
#include <iostream>

TableTennisPlayer::TableTennisPlayer(const string& fn,
	const string& ln, bool ht) : firstname(fn), lastname(ln), hasTable(ht)
{
}

void TableTennisPlayer::Name() const
{
	std::cout << lastname << ", " << firstname;
}

RatedPlayer::RatedPlayer(unsigned int r, const string& fn,
	const string& ln, bool ht) : rating(r), TableTennisPlayer(fn, ln, ht)
{
}

RatedPlayer::RatedPlayer(unsigned int r, 
	const TableTennisPlayer& tp) : rating(r), TableTennisPlayer(tp)
{
}
```





### 虚方法

如果需要在派生类中重新定义基类的方法，使用关键字 virtual 将方法声明为虚方法（定义中不需要该关键字）。

基类声明了虚方法，派生类中该方法自动成为虚方法，派生类可加可不加 virtual（通常也加）。

brass.h

```c++
#pragma once
#ifndef BRASS_H_
#define BRASS_H_
#include <string>
using std::string;

class Brass {
private:
	string fullName;
	long acctNum;
	double balance;
public:
	Brass(const string& s = "Nullbody", long an = -1, double bal = 0.0);
	void Deposit(double amt);
	virtual void Withdraw(double amt);
	double Balance() const;
	virtual void ViewAcct() const;
	virtual ~Brass() {}
};

class BrassPlus :public Brass {
private:
	double maxLoan;
	double rate;
	double owesBank;
public:
	BrassPlus(const string& s = "Nullbody", long an = -1, double bal = 0.0, double ml = 500, double r = 0.11125);
	BrassPlus(const Brass& ba, double ml = 500, double r = 0.11125);
	virtual void ViewAcct() const;
	virtual void Withdraw(double amt);
	void ResetMax(double m) { maxLoan = m; }
	void ResetRate(double r) { rate = r; }
	void ResetOwes() { owesBank = 0; }
};

#endif // !BRASS_H_
```



brass.cpp

```c++
#include <iostream>
#include "brass.h"
using std::cout;
using std::endl;
using std::string;

typedef std::ios_base::fmtflags format;
typedef std::streamsize precis;
format setFormat();
void restore(format f, precis p);

Brass::Brass(const string& s, long an, double bal)
{
	fullName = s;
	acctNum = an;
	balance = bal;
}

void Brass::Deposit(double amt)
{
	if (amt < 0) {
		cout << "Negative deposit not allowed; deposit is cancelled." << endl;
	}
	else {
		balance += amt;
	}
}

void Brass::Withdraw(double amt)
{
	format initialState = setFormat();
	precis prec = cout.precision(2);
	if (amt < 0) {
		cout << "Withdrawal amount must be positive; withdrawal canceled." << endl;
	}
	else if (amt <= balance) {
		balance -= amt;
	}
	else {
		cout << "Withdrawal amount of $" << amt << " exceeds your balance." << endl;
		cout << "Withdrawal canceled." << endl;
	}
	restore(initialState, prec);
}

double Brass::Balance() const
{
	return balance;
}

void Brass::ViewAcct() const
{
	format initialState = setFormat();
	precis prec = cout.precision(2);
	cout << "Client: " << fullName << endl;
	cout << "Account Number: " << acctNum << endl;
	cout << "Balance: " << balance << endl;
	restore(initialState, prec);
}

BrassPlus::BrassPlus(const string& s, long an, double bal, double ml, double r) :Brass(s, an, bal)
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

BrassPlus::BrassPlus(const Brass& ba, double ml, double r) :Brass(ba)
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

void BrassPlus::ViewAcct() const
{
	format initialState = setFormat();
	precis prec = cout.precision(2);
	Brass::ViewAcct();
	cout << "Maximum loan: $" << maxLoan << endl;
	cout << "Owed to bank: " << owesBank << endl;
	cout.precision(3);
	cout << "Loan Rate: " << 100 * rate << "%" << endl;
	restore(initialState, prec);
}

void BrassPlus::Withdraw(double amt)
{
	format initialState = setFormat();
	precis prec = cout.precision(2);
	double bal = Balance();
	if (amt <= bal) {
		Brass::Withdraw(amt);
	}
	else if (amt <= bal + maxLoan - owesBank) {
		double advance = amt - bal;
		owesBank += advance * (1.0 + rate);
		cout << "Bank advance: $" << advance << endl;
		cout << "Finance charge: $" << advance * rate << endl;
		Deposit(advance);
		Brass::Withdraw(amt);
	}
	else {
		cout << "Credit limit exceeded. Transaction canceled." << endl;
	}
	restore(initialState, prec);
}

format setFormat()
{
	return cout.setf(std::ios_base::fixed, std::ios_base::floatfield);
}

void restore(format f, precis p)
{
	cout.setf(f, std::ios_base::floatfield);
	cout.precision(p);
}
```



声明为虚方法后，将对类对象的指针或引用产生影响：

1. 若未声明为虚方法，指针或引用调用该方法取决于指针或引用的类型；
2. 若声明为虚方法，指针或引用调用该方法取决于被指向的或被引用的对象类型。

```c++
Brass dom("Dominic Banker", 11224, 4183.45);
BrassPlus dot("Dorothy Banker", 12118, 2592.00);
Brass& b1_ref = dom;
Brass& b2_ref = dot;
//若为非虚方法
b1_ref.ViewAcct(); // Brass::ViewAcct()
b2_ref.ViewAcct(); // Brass::ViewAcct()
// 若为虚方法
b1_ref.ViewAcct(); // Brass::ViewAcct()
b2_ref.ViewAcct(); // BrassPlus::ViewAcct()

// 若为虚方法，强制类型转换不影响虚函数表，所以还是调用派生类成员函数
Brass* b3_ref = &dot;
Brass* b4_ref = (Brass *)&dot;
b3_ref.ViewAcct(); // BrassPlus::ViewAcct()
b4_ref.ViewAcct(); // BrassPlus::ViewAcct()
```

由于存在上述问题，因此基类的析构函数应定义为虚析构函数（即使不做任何操作，也应该定义一个虚析构函数），保证派生类对象销毁时先调用的派生类析构函数，然后调用基类析构函数。

编译器对虚方法采用动态联编，编译器为每个类提供了一个隐藏的虚函数表，运行时才能确定对象类型，决定调用基类还是派生类的虚方法，然后通过虚函数表查找到要执行的虚函数的入口地址。



### 抽象基类（ABC）

纯虚函数，函数声明结尾加上 = 0，可以定义，也可以不定义。

类声明中包含至少一个纯虚函数时，该类为抽象基类，包含纯虚函数的类只用作基类，不可创建对象。



### 继承与动态内存分配

若基类含有new，派生类不含有new，派生类构造函数使用成员初始化列表初始化基类成员，不需要处理动态内存分配（不需要自定义复制构造函数，赋值运算符），派生类默认析构函数会自动调用基类析构函数。

若基类和派生类都含有new：

1. 派生类构造函数使用成员初始化列表初始化基类成员，然后在初始化自己需要new的部分；
2. 派生类复制构造函数使用成员初始化列表复制基类成员，基类指针可以指向派生类对象，然后再复制自己需要new的部分；
3. 派生类赋值运算符显式调用基类赋值运算符完成基类赋值（BaseClass::operate=(subobject)或*this = subobject），然后再完成自己需要new部分的赋值；

派生类友元函数<<运算符重载显式强制类型转换，把派生类对象引用转为基类对象引用，调用基类<<友元运算符。

### 各继承方式对比

is-a关系使用公有继承，has-a关系使用私有继承

|       **特征**       |     **公有继承**     |      **保护继承**      |     **私有继承**     |
| :------------------: | :------------------: | :--------------------: | :------------------: |
|   **公有成员变成**   |   派生类的公有成员   |    派生类的保护成员    |   派生类的私有成员   |
|   **保护成员变成**   |   派生类的保护成员   |    派生类的保护成员    |   派生类的私有成员   |
|   **私有成员变成**   | 只能通过基类接口访问 |  只能通过基类接口访问  | 只能通过基类接口访问 |
| **能否隐式向上转换** |          是          | 是（当只能在派生类中） |          否          |



## c++中的代码重用

### valarray类

用于数值处理的数组模板类

```c++
#include <valarray>
using std::valarray;

int a[5] = { 1,2,3,4,5 };
valarray<int> v1;       // 长度为0的空数组  
valarray<int> v2(6);    // 长度为6的空数组，默认值都是0
valarray<int> v3(2, 8); // 长度为8的数组，每个元素都等于2
valarray<int> v4(a, 4); // 长度为4的数组，取数组a前4个元素初始化v4
v4.operator[](2);       // 可以使用下标访问数组元素，等价于v4[2]
v4.sum();               // 求和
v4.size();              // 数组长度
v4.max();               // 最大值
v4.min();               // 最小值
```

### 多重继承（multiple inheritance，MI）与虚基类

使用多个基类的继承被称为多重继承，公有MI会存在一些问题。

```c++
class Student : private std::string, private std::valarray<double>
{
public:
	...
}
```

继承类初始化方式

```
Student::Student(const char* str, const double* pd, int n)
	: std::string(str), std::valarray<double>(pd, n) {}
```

有如下类声明：

```c++
class Worker{
	...
};

class Singer : public Worker{
	...
};

class Waiter : public Worker{
	...
};

class SingingWaiter : public Singer, public Waiter{
	...
};
```

以下语句将存在二义性：

```c++
SingingWaiter ed;
Worker* pw = &ed;
```

因为ed中包含两个Worker对象，分别来自于Singer和Waiter，可以使用强制类型转换来明确是哪个Worker：

```c++
Worker* pw1 = (Singer*) & ed;
Worker* pw2 = (Waiter*) & ed;
```

但通常情况下，应该只有一个基类，虚基类使得从多个类派生出的对象只继承一个基类，virtual和public次序无关紧要。

```c++
class Singer : virtual public Worker{
	...
};

class Waiter : public virtual Worker{
	...
};

class SingingWaiter : public Singer, public Waiter{
	...
};
```

使用虚基类的多重继承的构造函数与普通MI的构造函数不同，它的所有基类都只有一个（若有相同基类将会共享一个），所以虚基类禁止信息通过中间类自动传递给基类，下面的例子在SingingWaiter初始化时，会自动初始化Singer和Waiter的成员变量，但是不会传递信息给Worker，如果没有显示调用Worker的构造函数，将会使用Worker类的默认构造函数初始化Worker一次。

```c++
// 仅初始化Waiter和Singer的成员变量，wk不会被传递到Worker，调用Worker默认构造函数初始化Worker对象一次
SingingWaiter(const Worker &wk, int p =0, int v = Singer::other)
    : Waiter(wk, p), Singer(wk, v) {}

// 分别初始化Waiter、Singer和Worker对象自己的成员变量
SingingWaiter(const Worker &wk, int p =0, int v = Singer::other)
    : Worker(wk), Waiter(wk, p), Singer(wk, v) {}
```

同时多重继承也会导致函数调用的二义性，若Waiter、Singer和Worker类均定义了Show()函数，没有在SingingWaiter类中定义Show()函数，则需要在调用时指明是使用的哪个Show()。

```c++
SingingWaiter sw;
sw.Show();         // 错误
sw.Singer::Show(); // 调用Singer对象的Show()函数
```

虚基类可以避免一些函数调用的二义性，同名函数，即使定义为派生类的私有函数（此时无法访问），派生类中的优先级高于基类。

### 类模板

类模板使用`template <class T>`或`template <typename T>`开头，T为变量类型名，使用类模板时将会用实际类型替换它。定义类模板成员函数时，类限定符`ClassName::`改为`ClassName<T>::`,通常和类定义放在同一个头文件中（c++以前的标准曾使用export关键字，可以将类模板的成员函数定义在另一个文件中，但c++11已不再这样使用）。

template_stack.h

```c++
template <typename T>
class Stack
{
private:
	enum { MAX = 10 };
	T items[MAX];
	int top;
public:
	Stack();
	bool isempty();
	bool isfull();
	bool push(const T& item);
	bool pop(T& item);
};

template <class T>
Stack<T>::Stack() {
	top = 0;
}

template <class T>
bool Stack<T>::isempty() {
	return top == 0;
}

template <class T>
bool Stack<T>::isfull() {
	return top == MAX;
}

template <class T>
bool Stack<T>::push(const T& item) {
	if (top < MAX) {
		items[top++] = item;
		return true;
	}
	else {
		return false;
	}
}

template <class T>
bool Stack<T>::pop(T& item) {
	if (top > 0) {
		item = items[--top];
		return true;
	}
	else {
		return false;
	}
}
```

使用模板类时需要实例化，使用具体类型替换模板类的泛型名。

```c++
Stack<int> st1;
Stack<std::string> st2;
```

类模板的非类型参数（表达式参数）可以是整型（包括布尔）、枚举、指针或引用，模板代码不能修改参数的值，也不能使用参数的地址，实例化模板的时候，表达式参数的值必须是常量表达式，使用引用或指针时，在每个版本c++中要求不一样。

```c++
template <char const* name>
class MyClass
{
    ...
};

extern char const s03[] = "hi"; // 外部链接
char const s11[] = "hi";        // 内部链接，全局变量

int main()
{
    Message<s03> m03; // OK(all versions)
    Message<s11> m11; // OK since c++11;

    static char const s17[] = "hi"; // 静态变量
    Message<s17> m17;               // OK, since C++17
}
```

类模板可以继承、递归、包含多个类型参数、提供默认类型模板参数。

```c++
// 继承
template <typename T>
class Array
{
    ...
};

template <typename T>
class GrowArray : public Array<T>
{
    ...
};

// 递归
Array< Array<int> > aa;

// 多类型参数
template <typename T1, typename T2>
class Pair
{
	...
}

// 默认类型模板参数
template <typename T1, typename T2 = int>
class Topo
{
	...
}

Topo<double, double> m1;
Topo<double> m2;
```

隐式实例化

```c++
Pair<int, double> *pt;
pt = new Pair<int, double>;
```

显式实例化，声明需位于模板定义所在的名称空间中，即使未创建类对象，编译器也会生成声明的类

```c++
template class Pair<int, double>;
```

显式具体化，定义某种类型专属模板，编译时优先使用具体化的类

```c++
template <> class SortedArray<const char*>
{
	...
}
```

部分具体化，提供部分模板类型参数，如果编译时有多个模板可供选择，优先使用具体化程度最高的模板

```c++
// 通用模板
template <class T1, class T2> class Pair {...};
// 部分具体化
template <class T1> class Pair<T1, int> {...};
// 显式具体化
template <> class Pair<int, int> {...};

Pair<double, double> p1; // 使用通用模板
Pair<double, int> p2;    // 使用部分具体化
Pair<int, int> p3;       // 使用显式具体化

// 为指针类型提供特殊版本类模板

template<class T> // 通用模板
class Feeb {...};
template<class T*> // 为指针类型部分具体化模板
class Feeb {...};

Feeb<char> fb1;  // 使用通用模板
Feeb<char*> fb1; // 使用为指针类型部分具体化模板

// 其他部分具体化例子
template <class T1, class T2, class T3> class Trio {...};
template <class T1, class T2> class Trio<T1, T2, T2> {...};
template <class T1> class Trio<T1, T1*, T1*> {...};

Trio<int, short, double> t1;
Trio<int, double, double> t2;
Trio<short, short*, short*> t3;
```

模板类外定义模板成员（模板类，模板函数）

```c++
template <typename T>
class beta
{
private:
	template <typename V>
	class hold;
	hold<T> q;
	hold<int> n;
public:
	beta(T t, int i) : q(t), n(i) {}
	template <typename U>
	U blab(U u, T t);
	void Show() const { q.show(); n.show(); }
};

template <typename T>
template <typename V>
class beta<T>::hold
{
private:
	V val;
public:
	hold(V v = 0) :val(v) {}
	void show() const { cout << val << endl; }
	V Value() const { return val; }
};

template <typename T>
template <typename U>
U beta<T>::blab(U u, T t) 
{
	return (n.Value() + q.Value()) * u / t;
}
```

使用模板作为模板类参数

```c++
template <template <typename T> class Thing>
class Crab
{
private:
    Thing<int> s1;
    Thing<double> s2;
public:
    Crab() {};
    bool push(int a, double x) { return s1.push(a) && s2.push(x); }
    bool pop(int& a, double& x) { return s1.pop(a) && s2.pop(x); }
}
Crab<Stack> nebula;

template <template <typename T> class Thing, typename U, typename V>
class Crab
{
private:
    Thing<U> s1;
    Thing<V> s2;
public:
    Crab() {};
    bool push(int a, double x) { return s1.push(a) && s2.push(x); }
    bool pop(int& a, double& x) { return s1.pop(a) && s2.pop(x); }
}
Crab<Stack, int, double> nebula;
```

模板类的友元分为三类：非模板友元，约束模板友元，非约束模板友元

```c++
// 非模板友元函数, 显式具体化定义每个所需的友元函数
template <typename T>
class HasFriend
{
private:
	T item;
	static int ct;
public:
	HasFriend(const T& t) : item(t) { ct++; }
	~HasFriend() { ct--; }
	friend void counts();
	friend void reports(HasFriend<T>&);
};

template <typename T>
int HasFriend<T>::ct = 0;

void counts()
{
	cout << "int count: " << HasFriend<int>::ct << ";";
	cout << "double count: " << HasFriend<double>::ct << endl;
}

void reports(HasFriend<int>& hf)
{
	cout << "HasFriend<int>: " << hf.item << endl;
}

void reports(HasFriend<double>& hf)
{
	cout << "HasFriend<double>: " << hf.item << endl;
}
```



```c++
// 约束模板友元函数，类外部声明友元函数模板
template <typename T> void counts();
template <typename T> void reports(T&);

template <typename T>
class HasFriend
{
private:
	T item;
	static int ct;
public:
	HasFriend(const T& t) : item(t) { ct++; }
	~HasFriend() { ct--; }
	friend void counts<T>();
	friend void reports<>(T&); // friend void reports<T>(T&); <>中模板参数可省略，可以从函数参数推断出模板类型
};

template <typename T>
int HasFriend<T>::ct = 0;

template <typename T>
void counts()
{
	cout << "template size: " << sizeof(HasFriend<T>) << "; ";
	cout << "template counts(): " << HasFriend<T>::ct << endl;
}

template <typename T>
void reports(T& hf)
{
	cout << hf.item << endl;
}


int main() {
	counts<int>();
    // 可以隐式实例化，也可以显式实例化reports(T& hf)
	HasFriend<int> hf1(10);
	HasFriend<int> hf2(20);
	HasFriend<double> hf3(3.3);
	reports(hf1);
	reports<HasFriend<int>>(hf2);
	reports(hf3);
	counts<int>();
	counts<double>();
	return 0;
}

output:
template size: 4; template counts(): 0
10
20
3.3
template size: 4; template counts(): 2
template size: 8; template counts(): 1
```



```c++
// 非约束模板友元函数，类内部声明友元函数模板
template <typename T>
class ManyFriend
{
private:
	T item;
public:
	ManyFriend(const T& t) : item(t) {}
	template <typename C, typename D> friend void show2(C&, D&);
};

template <typename C, typename D> void show2(C& c, D& d)
{
	cout << c.item << ", " << d.item << endl;
}


int main() {
	ManyFriend<int> hf1(10);
	ManyFriend<int> hf2(20);
	ManyFriend<double> hf3(3.3);
	show2(hf1, hf3);
	return 0;
}

output:
10, 3.3
```

模板别名

```c++
// typedef方式
typedef std::array<double, 12> arrd;
typedef std::array<int, 12> arri;
typedef std::array<std::string, 12> arrst;


// using = 用于模板别名
template <typename T>
using arrtype = std::array<T, 12>;

arrtype<double> a1;      // std::array<double, 12>
arrtype<int> a2;         // std::array<int, 12>
arrtype<std::string> a3; // std::array<std::string, 12>

// using = 用于非模板别名，等价于typedef
typedef const char* pc1;
using pc2 = const char*;
typedef const int* (*pa1)[10];
using pa2 = const int* (*)[10];
```



## 友元、异常和其他

### 友元类

两个类有关联，但非is-a和has-a关系，可以定义为友元类，使得一个类可以访问另一个类所有成员。

```c++
class Tv
{
private:
	int state;
	int volume;
	int maxchannel;
	int channel;
	int mode;
	int input;
public:
	friend class Remote;
	enum { OFF, ON };
	enum { MinVal, MaxVal = 20 };
	enum { Antenna, Cable };
	enum { TV, DVD };
	Tv(int s = OFF, int mc = 125) :state(s), volume(5), maxchannel(mc), channel(2), mode(Cable), input(TV) {}
	void onoff() { state ^= 1; }
	bool ison() const { return state == ON; }
	bool volup();
	bool voldown();
	void chanup();
	void chandown();
	void set_mode() { mode ^= 1; }
	void set_input() { input ^= 1; }
	void settings() const;
};

bool Tv::volup() {
	if (volume < MaxVal) {
		volume++;
		return true;
	}
	else {
		return false;
	}
}

bool Tv::voldown() {
	if (volume > MinVal) {
		volume--;
		return true;
	}
	else {
		return false;
	}
}

void Tv::chanup() {
	if (channel < maxchannel) {
		channel++;
	}
	else {
		channel = 1;
	}
}

void Tv::chandown() {
	if (channel > 1) {
		channel--;
	}
	else {
		channel = maxchannel;
	}
}

void Tv::settings() const {
	cout << "TV is " << (state == OFF ? "OFF" : "ON") << endl;
	if (state == ON) {
		cout << "Volume setting = " << volume << endl;
		cout << "Channel setting = " << channel << endl;
		cout << "Mode = " << (mode == Antenna ? "Antenna" : "Cable") << endl;
		cout << "Input = " << (input == TV ? "TV" : "DVD") << endl;
	}
}

class Remote
{
private:
	int mode;
public:
	Remote(int m = Tv::TV) :mode(m) {}
	bool volup(Tv& t) { return t.volup(); }
	bool voldown(Tv& t) { return t.voldown(); }
	void onoff(Tv& t) { t.onoff(); }
	void chanup(Tv& t) { t.chanup(); }
	void chandown(Tv& t) { t.chandown(); }
	void set_mode(Tv& t) { t.set_mode(); }
	void set_input(Tv& t) { t.set_input(); }
	void set_chan(Tv& t, int c) { t.channel = c; }
};
```





### 友元类成员函数

遵循声明在前使用在后的原则，合理组织类及类成员函数声明和实现的顺序。内联函数的t链接性是内部的，类外定义内联函数需要和类声明在同一文件。

```c++
class Tv;

class Remote
{
private:
	int mode;
	enum { TV, DVD };
public:
	Remote(int m = TV) :mode(m) {}
	bool volup(Tv& t);
	bool voldown(Tv& t);
	void onoff(Tv& t);
	void chanup(Tv& t);
	void chandown(Tv& t);
	void set_mode(Tv& t);
	void set_input(Tv& t);
	void set_chan(Tv& t, int c);
};

class Tv
{
private:
	int state;
	int volume;
	int maxchannel;
	int channel;
	int mode;
	int input;
public:
	friend void Remote::set_chan(Tv&, int);
	enum { OFF, ON };
	enum { MinVal, MaxVal = 20 };
	enum { Antenna, Cable };
	enum { TV, DVD };
	Tv(int s = OFF, int mc = 125) :state(s), volume(5), maxchannel(mc), channel(2), mode(Cable), input(TV) {}
	void onoff() { state ^= 1; }
	bool ison() const { return state == ON; }
	bool volup();
	bool voldown();
	void chanup();
	void chandown();
	void set_mode() { mode ^= 1; }
	void set_input() { input ^= 1; }
	void settings() const;
};

bool Tv::volup() {
	if (volume < MaxVal) {
		volume++;
		return true;
	}
	else {
		return false;
	}
}

bool Tv::voldown() {
	if (volume > MinVal) {
		volume--;
		return true;
	}
	else {
		return false;
	}
}

void Tv::chanup() {
	if (channel < maxchannel) {
		channel++;
	}
	else {
		channel = 1;
	}
}

void Tv::chandown() {
	if (channel > 1) {
		channel--;
	}
	else {
		channel = maxchannel;
	}
}

void Tv::settings() const {
	cout << "TV is " << (state == OFF ? "OFF" : "ON") << endl;
	if (state == ON) {
		cout << "Volume setting = " << volume << endl;
		cout << "Channel setting = " << channel << endl;
		cout << "Mode = " << (mode == Antenna ? "Antenna" : "Cable") << endl;
		cout << "Input = " << (input == TV ? "TV" : "DVD") << endl;
	}
}

inline bool Remote::volup(Tv& t) { return t.volup(); }
inline bool Remote::voldown(Tv& t) { return t.voldown(); }
inline void Remote::onoff(Tv& t) { t.onoff(); }
inline void Remote::chanup(Tv& t) { t.chanup(); }
inline void Remote::chandown(Tv& t) { t.chandown(); }
inline void Remote::set_mode(Tv& t) { t.set_mode(); }
inline void Remote::set_input(Tv& t) { t.set_input(); }
inline void Remote::set_chan(Tv& t, int c) { t.channel = c; }

int main() {
	Tv s42;
	Remote r42;
	s42.onoff();
	s42.settings();
	r42.volup(s42);
	s42.settings();
	return 0;
}
```

除了上述的单向友元关系外，还存在双向友元关系，注意使用友元类的成员函数的声明与定义的先后顺序。

```c++
class Tv
{
public:
	friend class Remote;
    void buzz(Remote& r);
    ...
}

class Remote
{
public:
    friend class Tv;
    void bool volup(Tv& t) { t.volup(); }
    ...
}

inline void Tv::buzz(Remote& r)
{
	...
}
```

共同友元函数，有函数需要访问两个类的私有数据，例如同步操作。

```c++
class Analyzer;

class Probe
{
    friend void sync(Analyzer& a, const Probe& b);
    friend void sync(Probe& a, const Analyzer& b);
    ...
}

class Analyzer
{
    friend void sync(Analyzer& a, const Probe& p);
    friend void sync(Probe& p, const Analyzer& a);
    ...
}

inline void sync(Analyzer& a, const Probe& p)
{
    ...
}

inline void sync(Probe& p, const Analyzer& a)
{
    ...
}
```



### 嵌套类

c++11允许在类中声明另一个类，称之为嵌套类，一定程度上避免类名称冲突，根据声明位置不同（共有、保护、私有），继承类和类外对嵌套类有不同的访问权限，权限规则同普通的类成员函数一致。嵌套类在模板类中也是适用的。



### 异常类

默认情况下，异常发生会调用异常终止abort()函数，用于直接终止程序，位于头文件stdlib.h中，也可以使用头文件cstdlib。

让函数返回错误码，使程序员对异常进一步处理。

异常处理机制throw-try-catch，throw用于异常抛出，程序沿函数调用栈后退，直至找到try块，根据抛出异常类型找到对应异常捕获catch块，处理相应异常，通常情况下catch的是类类型，也可以是字符串等基本c++类型。throw抛出异常的过程涉及栈解退，不断回退调用函数，释放栈中自动变量（包括类对象的析构调用），直到找到第一个位于try块中的返回地址。

```c++
#include <iostream>
#include <cstdlib>
using namespace std;

double hmean(double a, double b) {
	if (a == -b) {
		throw "bad hmean() arguments: a = -b not allowed.";
	}
	return 2.0 * a * b / (a + b);
}

int main() {
	double x, y, z;
	while (cin >> x >> y) {
		try {
			z = hmean(x, y);
		}
		catch (const char* s) {
			cout << s << endl;
			continue;
		}
		cout << "Harmonic mean of " << x << " and " << y << " is " << z << endl;
	}
	return 0;
}
```

异常规范格式如下，但c++11已将其摒弃，不建议使用，关键字noexcept指出函数不会引发异常。

```c++
int func(int a) throw(bad_thing);
int func(int a) noexcept;
```

引发异常时，编译器会创建一个临时拷贝，即使catch中指定的是引用，因为跳出当前函数后，局部变量会销毁。

```c++
class problem { };

void super() throw (problem) {
	...
    if (...) {
        problem oops;
        throw oops;
    }
	...
}

try {
	super();
}
catch (problem& p) {
	...
}
```

通常catch中使用引用，因为基类的引用可以匹配派生类对象，便于异常分级。

不知道异常类型的时候，可以使用`catch(...)`捕获任何异常，但无法获取异常信息。

异常类exception有个虚方法what()，可以在派生类中重写该方法。

```c++
class bad_hmean :public std::exception {
public:
	const char* what() { return "error"; }
};
```

异常分类，基类exception，派生类均有一个接收string参数的构造函数和一个返回const char*类型错误信息的what()函数。

|     基类      |      派生类      |                             描述                             |
| :-----------: | :--------------: | :----------------------------------------------------------: |
|  logic_error  |   domain_error   |  数学函数中定义域越界，例如传递不在[-1, 1]的值给反正弦函数   |
|               | invalid_argument |                 无效参数异常，参数不符合预期                 |
|               |   length_error   | 内存空间不足以执行操作，例如string的append()操作超出string处理的最大长度 |
|               |  out_of_bounds   |                无效索引错误，例如数组操作越界                |
| runtime_error |   range_error    |                    计算结果不在范围内错误                    |
|               |  overflow_error  |                 上溢错误，例如超出整型最大值                 |
|               | underflow_error  |          下溢错误，例如小于浮点型能表示的最小非0值           |

new分配内存失败时会抛出bad_alloc异常，也可以使用返回空指针的new，内存分配失败不抛出异常，返回空指针，此时new出来的对象虽然是空指针，但也需要delete或delete[]释放。

```c++
struct MyStruct
{
	double a[20000];
};

try {
    MyStruct* pb = new MyStruct[2000000];
}
catch (bad_alloc& ba) {
    cout << ba.what() << endl;
    exit(0);
}

MyStruct* pb = new(std::nothrow) MyStruct[2000000];
if (pb == nullptr) {
    cout << "bad_alloc" << endl;
}
delete pb;
```



### 运行阶段类型识别（RTTI）

RTTI只适用于包含虚函数的类，因为只有包含虚函数的类，才应该将派生对象赋给基类指针。

使用RTTI的原因：

- 派生对象可能包含不是继承来的方法，想知道类类型，并调用合适的方法
- 出于调试目的，想跟踪对象类型

RTTI的三个元素：

- dynamic_cast运算符
- typeid运算符
- type_info类

dynamic_cast运算符用于类型是否能安全转换，当可以安全转换时，返回强制转换后的类型，不可安全转换时，指针类型返回空指针，引用类型抛出bad_cast异常。

```c++
#include <iostream>
using namespace std;

class Grand {
private:
	int hold;
public:
	Grand(int h = 0) :hold(h) {}
	virtual void Speak() const { cout << "I am a grand class!" << endl; }
	virtual int Value() const { return hold; }
};

class Superb :public Grand {
public:
	Superb(int h = 0) :Grand(h) {}
	virtual void Speak() const { cout << "I am a superb class!" << endl; }
	virtual void Say() const { cout << "I hold the superb value of " << Value() << "!" << endl; }
};

class Magnificent :public Superb {
private:
	char ch;
public:
	Magnificent(int h = 0, char c = 'A') :Superb(h), ch(c) {}
	virtual void Speak() const { cout << "I am a magnificent class!" << endl; }
	virtual void Say() const { cout << "I hold the character " << ch << " and the integer " << Value() << "!" << endl; }
};

Grand* GetOne() {
	Grand* p{};
	switch (rand() % 3)
	{
	case 0:
		p = new Grand(rand() % 100);
		break;
	case 1:
		p = new Superb(rand() % 100);
		break;
	case 2:
		p = new Magnificent(rand() % 100, 'A' + rand() % 26);
		break;
	default:
		break;
	}
	return p;
}

int main() {
	srand(time(0));
	Grand* pg;
	Superb* ps;
	for (int i = 0; i < 10; i++) {
		pg = GetOne();
		pg->Speak();
		if (ps = dynamic_cast<Superb*>(pg)) {
			ps->Say();
		}
	}
	return 0;
}

output:
I am a grand class!
I am a superb class!
I hold the superb value of 78!
I am a magnificent class!
I hold the character F and the integer 19!
I am a superb class!
I hold the superb value of 69!
I am a magnificent class!
I hold the character J and the integer 40!
I am a superb class!
I hold the superb value of 52!
I am a superb class!
I hold the superb value of 23!
I am a grand class!
I am a magnificent class!
I hold the character O and the integer 73!
I am a magnificent class!
I hold the character Y and the integer 43!
```



```c++
Grand& GetOne() {
	Grand* p{};
	switch (rand() % 3)
	{
	case 0:
		p = new Grand(rand() % 100);
		break;
	case 1:
		p = new Superb(rand() % 100);
		break;
	case 2:
		p = new Magnificent(rand() % 100, 'A' + rand() % 26);
		break;
	default:
		break;
	}
	return *p;
}

int main() {
	srand(time(0));
	for (int i = 0; i < 10; i++) {
		Grand& pg = GetOne();
		pg.Speak();
		try {
			Superb& ps = dynamic_cast<Superb&>(pg);
			ps.Say();
		}
		catch (bad_cast& bc) {
			cout << bc.what() << endl;
		}
	}
	return 0;
}

output:
I am a superb class!
I hold the superb value of 35!
I am a magnificent class!
I hold the character T and the integer 62!
I am a magnificent class!
I hold the character S and the integer 81!
I am a grand class!
Bad dynamic_cast!
I am a grand class!
Bad dynamic_cast!
I am a magnificent class!
I hold the character X and the integer 87!
I am a magnificent class!
I hold the character D and the integer 80!
I am a superb class!
I hold the superb value of 79!
I am a superb class!
I hold the superb value of 87!
I am a magnificent class!
I hold the character J and the integer 14!
```

typeid运算符与type_info类，typeid的参数可以是类名或结果为类对象的表达式，返回一个type_info对象的引用，type_info类重载了==和!=，可以用来类型比较。typeid()若参数为空指针，程序将引发bad_typeid异常。type_info类实现随厂商而异，但都包含一个name()成员，该函数返回一个字符串（通常为类名）。

```c++
Grand& GetOne() {
	Grand* p{};
	switch (rand() % 4)
	{
	case 0:
		p = new Grand(rand() % 100);
		break;
	case 1:
		p = new Superb(rand() % 100);
		break;
	case 2:
		p = new Magnificent(rand() % 100, 'A' + rand() % 26);
		break;
	case 3:
		p = nullptr;
		break;
	default:
		break;
	}
	return *p;
}

int main() {
	srand(time(0));
	for (int i = 0; i < 10; i++) {
		Grand& pg = GetOne();
		try {
			cout << typeid(pg).name() << endl;
			if (typeid(Magnificent) == typeid(pg)) {
				cout << "type = Magnificent" << endl;
			}
			pg.Speak();
			Superb& ps = dynamic_cast<Superb&>(pg);
			ps.Say();
		}
		catch (bad_cast& bc) {
			cout << bc.what() << endl;
		}
		catch (bad_typeid& bt) {
			cout << bt.what() << endl;
		}
	}
	return 0;
}

output:
I am a superb class!
I hold the superb value of 54!
class Magnificent
type = Magnificent
I am a magnificent class!
I hold the character P and the integer 43!
Attempted a typeid of nullptr pointer!
class Superb
I am a superb class!
I hold the superb value of 86!
class Grand
I am a grand class!
Bad dynamic_cast!
class Grand
I am a grand class!
Bad dynamic_cast!
class Grand
I am a grand class!
Bad dynamic_cast!
class Magnificent
type = Magnificent
I am a magnificent class!
I hold the character I and the integer 68!
class Superb
I am a superb class!
I hold the superb value of 25!
```



### 类型转换运算符

- dynamic_cast
- const_cast
- static_cast
- reinterpret_cast

dynamic_cast，允许类层次结构可以向上转换，在一定情况下可以向下转换（例如基类指针指针或引用的实际值是派生类对象，此时满足运行时检查，可以转换为该派生类指针），如果 dynamic_cast 转换失败，则会返回空指针（对于指针类型）或抛出 std::bad_cast`异常（对于引用类型）。

const_cast，用于去除指针或引用的 const 或 volatile 限定符，可以修改它们所指向的变量的值（只要该变量本身不是 const）。

static_cast，用于基本类型之间的转换、隐式类型转换、类层次结构中公有派生类和基类之间的相互转换等。

reinterpret_cast，主要用于进行低级类型转换，它会将一种类型的指针或引用转换为另一种类型的指针或引用，即得到一个完全不同的地址、类型和值。



## string类和标准模板库

### 标准c++ string类

string类构造函数，前七个是string类基本构造函数，后两个是c++11新增构造函数，size_type是一个整型，字符串最大长度定义为string::npos（通常是unsigned int或unsigned  long最大值）。注意[begin, end)，包含begin位置字符，不包含end位置字符。

|                           构造函数                           |                           描述                            |
| :----------------------------------------------------------: | :-------------------------------------------------------: |
|                    string(const char* s)                     |                使用字符串s初始化string对象                |
|                 string(size_type n, char c)                  |               使用n个字符c初始化string对象                |
|                  string(const string& str)                   |                       复制构造函数                        |
|                           string()                           |                   默认构造函数，长度为0                   |
|              string(const char* s, size_type n)              |     字符串s的前n个字符初始化string对象，即使超出s结尾     |
|     template \<class Iter> string(Iter begin, Iter end)      |      使用指针[begin, end)区间内字符初始化string对象       |
| string(const string& str, size_type pos = 0, size_type n = npos) | 使用str从位置pos开始到结尾的字符或n个字符初始化string对象 |
|                string(string&& str) noexcept                 |           移动构造函数（复制过程中可能修改str）           |
|              string(initializer_list\<char> il)              |         使用初始化列表li中的字符初始化string对象          |

```c++
string one("test");
string two(20, 'A');
string three(one);
string four;
four = two + three;
char alls[] = "All's well that ends well";
string five(alls, 20);
string six(alls + 6, alls + 10);
string six1(&five[6], &five[10]);
string seven(four, 7, 16);
```

移动构造函数功能同复制构造函数，但不保证将str视为const，在某些情况下，编译器使用移动构造函数替代复制构造函数，以提高性能。初始化列表语法应用于string类可以使以下声明合法：

```c++
string comp_lang = { 'L', 'i', 's', 'p' };
```

传统字符串输入方式：

```c++
char info[100];
cin >> info; // 遇到空格、制表符或换行符终止，超出info长度将溢出，程序出错
cin.getline(info, 100); // 读取一行字符，可以读取空格，最多读取99个字符
cint.get(info, 100);    // 读取99个字符，把换行符留在缓冲区，下次使用>>或getline时将会读取到换行符
```

string输入方式：

```c++
string stuff;
cin >> stuff;  // 遇到空格、制表符或换行符终止，字符串最大长度string::nops，超出程序出错
getline(cin, stuff); // 读取一行字符，读取到分界字符停止，并将分界字符丢弃，建议使用前调用cin.ignore()清除输入流中缓存
getline(cin, stuff, ':'); // 指定':'字符为输入边界，默认为\n
```

cin读取string遇到空白字符（空格、制表符或换行符，使isspace()方法返回true的字符）结束，会将空白字符留在输入缓冲区。

getline读取string情况：

- 遇到文件尾，存储string，设置eofbit寄存器，fail()和eof()方法都将返回true
- 遇到分界字符（默认为\n），存储string，把分界字符从输入流中删除
- 超出string::nops或可用内存大小，读取失败，设置failbit寄存器，fail()方法返回true
- 读取正常，设置goodbit寄存器
- 系统故障（如硬盘故障），读取失败，设置badbit寄存器

string类重载了全部六个关系运算符（>、<、），也可以和字符串进行比较。

string类的find()方法的四个重载原型：

| 方法原型                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| size_type find(const string& str, size_type pos = 0) const   | 从pos位置开始，查找子串str。<br />若找到，返回子串首次出现时其首字符索引；<br />否则，返回string::npos。 |
| size_type find(const char* s, size_type pos = 0) const       | 从pos位置开始，查找子串s。<br />若找到，返回子串首次出现时其首字符索引；<br />否则，返回string::npos。 |
| size_type find(const string& str, size_type pos = 0, size_type n) | 从pos位置开始，查找s的前n个字符组成的字串。<br />若找到，返回子串首次出现时其首字符索引；<br />否则，返回string::npos。 |
| size_type find(char ch, size_type pos = 0) const             | 从pos位置开始，查找字符ch。<br />若找到，返回子串首次出现时其首字符索引；<br />否则，返回string::npos。 |

string类相关方法还包括：rfind()、find_first_of()、find_last_of()、find_first_not_of()和find_last_not_of()，他们的重载特征标都与find()方法相同。

| 方法                | 描述                                               |
| :------------------ | -------------------------------------------------- |
| rfind()             | 查找字符串或字符在string对象中最后一次出现位置     |
| find_first_of()     | 查找参数中任何字符在string对象中第一次出现的位置   |
| find_last_of()      | 查找参数中任何字符在string对象中最后一次出现的位置 |
| find_first_not_of() | 查找第一个不在参数中的字符                         |
| find_last_not_of()  | 查找最后一个不在参数中的字符                       |

string对象的其他方法：

- capacity()方法返回当前分配的内存块大小，为避免扩大字符串大小时频繁的拷贝操作，通常大于size大小，具体大小随实现而异

- reserve(size_t n)方法为预分配内存大小
- c_str()方法返回一个C-风格字符串，例如文件操作的open()方法只接受C-风格字符串

string具体化，编码方式及字符集不同：

- typedef basic_string\<char> string，ASCII编码
- typedef basic_string\<wchar_t> wstring，UTF-16编码
- typedef basic_string\<char16_t> u16string，UTF-16编码
- typedef basic_string\<char32_t> u32string，UTF-32编码



### 智能指针模板类

指针过期后自动释放内存，禁止使用非堆内存初始化智能指针。

添加头文件\<memory>以使用。

- auto_ptr

  - c++98解决方案，c++11已摒弃
  - 适用于new-delete
  - 建立所有权概念，只能有一个智能指针拥有该对象，赋值或复制操作会转让所有权，赋值或复制后原指针变为空指针，将无法访问该对象
  - 多指针指向同一块内存时，指针过期将导致多次释放该内存，将会出现运行时错误

  ```c++
  auto_ptr<double> pd(new double);
  auto_ptr<double> pd1;
  pd1 = pd; // 编译通过，运行出错
  ```

  

- unique_ptr

  - c++11解决方案
  - 适用于new-delete和new[]-delete[]
  - 建立所有权概念，只能有一个智能指针拥有该对象，赋值或复制操作会转让所有权，编译阶段不让通过
  - 无运行时危险的赋值或复制操作可以编译通过，例如临时变量
  - 可以使用std::move函数进行合法的赋值操作
  - 不要调用将一个unique_ptr复制或赋值给另一个的方法，例如sort()
  - Boost库提供的scoped_ptr可替代编译器自带的unique_ptr

  ```c++
  unique_ptr<string> p2(new string("auto"));
  unique_ptr<string> p3；
  p3 = p2; // 编译失败
  
  unique_ptr<string> demo(const char* s) {
      unique_ptr<string> temp(new string(s));
      return temp;
  }
  
  unique_ptr<string> ps1;
  ps1 = demo("unique"); // 合法操作
  unique_ptr<string> ps2;
  ps2 = unique_ptr<string>(new string); // 合法操作
  
  unique_ptr<string> ps3, ps4;
  ps3 = demo("unique"); 
  ps4 = move(ps3); // 合法操作，但注意ps3此时为空指针，没有再次赋值前不要对其操作
  ps3 = demo("unique again"); 
  
  ```

  

- shared_ptr

  - c++11解决方案
  - 适用于new-delete
  - 引用计数，直至最后一个引用过期时才销毁对象



### 标准模板库（STL）

提供一组表示容器、迭代器、函数对象和算法的模板。

STL容器基本方法：

- size()

  容器中元素数目

- swap()

  交换两个容器内容

- begin()

  返回一个指向容器中第一个元素的迭代器

- end()

  返回一个表示超过容器尾的迭代器，最后一个元素后面的那个元素

迭代器是一种广义指针，可以执行解除引用（*p）和递增（p++）操作，每个容器类都有自己的迭代器。

```c++
vector<double> scores;
vector<double>::iterator pd;
for(pd = scores.begin(); pd != scores.end(); pd++)
	cout<< *pd << endl;
```

vector容器常用方法：

- push_back()

  容器末尾添加元素

  ```c++
  vector<double> scores;
  double a;
  scores.push_back(a);
  ```

  

- erase()

  两个迭代器参数，删除容器指定区间[p1, p2)内元素

  ```c++
  vector<double> scores;
  // 删除begin()和begin() + 1指向的元素
  scores.erase(scores.begin(), scores.begin() + 2);
  ```

  

- insert()

  三个迭代器参数，第一个为新元素插入位置p1，第二个和第三个为插入区间[p2, p3]，将[p2, p3]插入到p1前面

  ```c++
  vector<int> old_v;
  vector<int> new_v;
  // 将new_v除第一个元素外的其他元素，插入到old_v的第一个元素前面
  old_v.insert(old_v.begin(), new_v.begin() + 1, new_v.end());
  // 将new_v除第一个元素外的其他元素，插入到old_v的最后一个元素后面
  old_v.insert(old_v.end(), new_v.begin() + 1, new_v.end());
  ```



其他STL方法：

- for_each()

  可用于任何容器类循环，三个参数，前两个为迭代器，第三个参数为函数对象（例如函数指针，该函数不可修改容器元素值）

- random_shuffle()

  要求容器类允许随机访问，随机排列两个迭代器参数区间中的元素

- sort()

  - 两参数版本

    两个迭代器参数，若容器类是用户自定义的，需要重载operator<()函数，默认按照升序排列

  - 三参数版本

    前两个参数为迭代器参数，第三个参数为函数对象（返回值为bool）

基于范围的for循环，c++11特性，用于遍历容器，可以使用引用修改容器内容。

```c++
void add(int& a) { a += 2;}
vector<int> scores = { 1,2,3 };
for (auto& x : scores) add(x);
for (auto& i : scores) cout << i << endl;
```



### 泛型编程

迭代器类型：输入迭代器、输出迭代器、正向迭代器、双向迭代器、随机访问迭代器。

例如vector迭代器为随机访问迭代器，list迭代器为双向迭代器。

|    迭代器功能    | 输入迭代器 | 输出迭代器 | 正向迭代器 | 双向迭代器 | 随机访问迭代器 |
| :--------------: | :--------: | :--------: | :--------: | :--------: | :------------: |
|   解除引用读取   |     1      |     0      |     1      |     1      |       1        |
|   解除引用写入   |     0      |     1      |     1      |     1      |       1        |
| 固定和可重复排序 |     0      |     0      |     1      |     1      |       1        |
|     ++p、p++     |     1      |     1      |     1      |     1      |       1        |
|     --p、p--     |     0      |     0      |     0      |     1      |       1        |
|       p[n]       |     0      |     0      |     0      |     0      |       1        |
|      p + n       |     0      |     0      |     0      |     0      |       1        |
|      p - n       |     0      |     0      |     0      |     0      |       1        |
|      p += n      |     0      |     0      |     0      |     0      |       1        |
|      p -= n      |     0      |     0      |     0      |     0      |       1        |

copy()方法，可以实现一种容器到另一种容器的复制，也可以用于数组，因为可以将指向数组的指针用作迭代器，三个迭代器参数，前两个为复制范围[p1, p2)（输入迭代器），第三个参数为复制到的起始位置（输出迭代器）。

```c++
int casts[5] = { 6,7,8,9,10 };
vector<int> dice(5);
copy(casts, casts + 5, dice.begin());
```



为容器类定义迭代器++操作有前缀和后缀两种，无参数的是p++，有参数int的是++p，--操作类似。

```c++
operator++();
operator++(int);
```



头文件\<iterator>提供了一些迭代器：

- 输入迭代器istream_iterator

  模板两个重要参数，第一个是读取数据类型，第二个是字符类型，默认是char。

  istream_iterator对象通常使用cin等输入流初始化，默认构造函数表示输入流结束，通常是遇到文件尾EOF、类型不匹配或其他输入故障。

  ```c++
  #include <iostream>
  #include <iterator>
  #include <vector>
  
  using namespace std;
  
  int main() {
      vector<int> data;
      vector<int> dice(5);
      istream_iterator<int> input(cin);
      istream_iterator<int> end_input;
      while (input != end_input) {
          data.push_back(*input);
          ++input;
      }
      for (auto x : data) {
          cout << x << " ";
      }
      cout << endl;
      cin.clear();
      cin.ignore(numeric_limits<streamsize>::max(), '\n');
  
      copy(istream_iterator<int>(cin), istream_iterator<int>(), dice.begin());
      for (auto x : dice) {
          cout << x << " ";
      }
      cout << endl;
  
      return 0;
  }
  ```

  

- 输出迭代器ostream_iterator

  模板两个重要参数，第一个是读取数据类型，第二个是字符类型，默认是char。

  ```c++
  #include <iostream>
  #include <iterator>
  #include <vector>
  
  using namespace std;
  
  int main() {
  	vector<int> data{ 1,2,3,4 };
      // 容器中内容写入到标准输出流，并以逗号分隔
  	copy(data.begin(), data.end(), ostream_iterator<int, char>(cout, ", "));
  	cout << endl;
  	return 0;
  }
  ```

  

- 反向迭代器reserve_iterator

  容器类有rbegin()和rend()成员函数，他们分别返回指向超尾和指向第一个元素的迭代器，迭代器类型为反向迭代器，反向迭代器的解除引用内部实现是先递减再解除引用。

  ```c++
  #include <iostream>
  #include <iterator>
  #include <vector>
  
  using namespace std;
  
  int main() {
      vector<int> vec{ 1, 2, 3, 4, 5 };
  
      // 使用正向迭代器遍历容器
      for (auto it = vec.begin(); it != vec.end(); ++it) {
          cout << *it << " ";
      }
      cout << endl;
  
      // 使用反向迭代器遍历容器
      for (auto rit = vec.rbegin(); rit != vec.rend(); ++rit) {
          cout << *rit << " ";
      }
      cout << endl;
  
      return 0;
  }
  ```

  

- 头插迭代器front_insert_iterator

  适用范畴：deque、list等。

- 尾插迭代器back_insert_iterator

  适用范畴：可使用push_back()方法的容器类，例如vector、deque、list等。

  创建格式：容器类型作为模板参数，使用需要插入元素的容器对象作为初始化参数。

  ```c++
  vector<int> dice;
  back_insert_iterator<vector<int>> back_iter(dice);
  ```

  

- 插入迭代器insert_iterator

  插入迭代器构造函数有两个参数，第一个是容器对象，第二个是插入目标位置，插入时会插入在目标位置前。

  ```c++
  #include <iostream>
  #include <iterator>
  #include <vector>
  #include <deque>
  
  using namespace std;
  
  int main() {
      vector<int> vec(5);
      deque<int> dq;
      vector<string> words(4);
      string s1[4] = { "a","b","c","d" };
      string s2[2] = { "x","y" };
  
      // 使用 back_insert_iterator 将元素插入到 vector 中
      auto b_it = back_insert_iterator<vector<int>>(vec);
      *b_it++ = 1;
      *b_it++ = 2;
      *b_it++ = 3;
  
      // 使用 front_insert_iterator 将元素插入到 deque 中
      auto f_it = front_insert_iterator<deque<int>>(dq);
      *f_it++ = 1;
      *f_it++ = 2;
      *f_it++ = 3;
  
      copy(s1, s1 + 4, words.begin());
      copy(s2, s2 + 2, insert_iterator<vector<string>>(words, words.begin()));
  
      for (auto x : vec) {
          cout << x << " ";
      }
      cout << endl;
  
      for (auto x : dq) {
          cout << x << " ";
      }
      cout << endl;
  
      for (auto x : words) {
          cout << x << " ";
      }
      cout << endl;
  
      return 0;
  }
  
  outputs:
  0 0 0 0 0 1 2 3
  3 2 1
  x y a b c d
  ```

  

copy()方法功能总结：

- 复制一种容器内容到另一种容器（包括数组）
- 复制容器内容到输出流
- 复制输入流内容到容器
- 将信息插入到容器中



### STL容器

序列容器：deque、forward_list、list、queue、priority_queue、stack和vector，线性顺序结构。

- vector

  动态数组，头文件\<vector>，任意位置均可增删元素，尾部增删元素时间复杂度O(1)，头部及中间增删元素时间复杂度为O(n)，可反转容器，可使用rbegin()和rend()逆序遍历容器

- deque

  双端队列，头文件\<deque>，首尾都可以增删元素，时间复杂度O(1)，序列中增删元素时间复杂度O(n)，类似于vector可随机访问，但vector的随机访问和序列中插入效率更高

- forward_list

  单向链表，序列头部进行增删操作，时间复杂度O(1)

- list

  双向链表，头文件\<list>，任意位置进行增删操作，时间复杂度O(1)，可反转容器，list专用成员函数如下表所示，std::sort()不能应用于list，因为它要求容器能随机访问

  |                        函数                         |                             功能                             |
  | :-------------------------------------------------: | :----------------------------------------------------------: |
  |               void merge(list\<T>& x)               | 合并两个有序list，合并后稳定排序，合并结果保存在调用list中，x置为空 |
  |              void remove(const T& val)              |      从list中删除所有与值val相等的元素，时间复杂度O(n)       |
  |                     void sort()                     |      使用<运算符对链表进行升序排序，时间复杂度O(nlogn)       |
  |       void splice(iterator pos, list\<T>& x)        |       将list x中元素插入到调用list的pos位置前，x置为空       |
  | void splice(iterator pos, list\<T>& x, iterator it) | 将list x中it位置元素插入到调用list的pos位置前，x中it指向的元素删除 |
  | void splice(iterator pos, list\<T>& x, first, lsat) | 将list x中[first, last)元素插入到调用list的pos位置前，x中该区间元素删除 |
  |                    void unique()                    |            删除list中相邻的重复元素，只保留第一个            |

  

- queue

  队列，先进先出，队尾插入，队首删除，默认底层类为deque

  |          函数           |                 功能                 |
  | :---------------------: | :----------------------------------: |
  |   bool empty() const    | 如果queue为空返回true，否则返回false |
  | size_type size() const  |         返回queue中元素数目          |
  |       T& front()        |        返回指向队首元素的引用        |
  |        T& back()        |        返回指向队尾元素的引用        |
  | void push(const T& val) |            在队尾插入val             |
  |       void pop()        |             删除队首元素             |

  

- priority_queue

  优先队列，最大元素将会被移动到队首，默认底层类为vector，底层实现是堆，默认情况下是最大堆，其迭代器是随机访问迭代器，但由于未提供相应迭代器接口，其迭代器也不支持++和--操作，所以不支持遍历，只能访问根节点，即队首

- stack

  栈，后进先出，仅允许在栈顶插入和删除元素，默认底层类为vector，成员函数与queue类似

  |          函数           |                 功能                 |
  | :---------------------: | :----------------------------------: |
  |   bool empty() const    | 如果stack为空返回true，否则返回false |
  | size_type size() const  |         返回stack中元素数目          |
  |        T& top()         |        返回指向栈顶元素的引用        |
  | void push(const T& val) |            在栈顶插入val             |
  |       void pop()        |             删除栈顶元素             |



X: 序列类型

a: X类型的一个对象

n: 整数

t: 序列存储类型的一个值

i, j, p, q: 迭代器

|      表达式       | 返回类型 |                          含义                           |      适用容器       |
| :---------------: | :------: | :-----------------------------------------------------: | :-----------------: |
|     X a(n, t)     |          |            声明一个名为a的由n个t值组成的序列            |         all         |
|      X(n, t)      |          |             创建一个由n个t值组成的匿名序列              |         all         |
|     X a(i, j)     |          |    声明一个名为a的序列，将其初始化为区间[i, j)的内容    |         all         |
|      X(i, j)      |          |     创建一个匿名序列，将其初始化为区间[i, j)的内容      |         all         |
|  a.insert(p, t)   |  迭代器  |                     将t插入到p前面                      |         all         |
| a.insert(p, n, t) |   void   |                    将n个t插入到p前面                    |         all         |
| a.insert(p, i, j) |   void   |              将区间[i, j)中元素插入到p前面              |         all         |
|    a.erase(p)     |  迭代器  |                     删除p指向的元素                     |         all         |
|   a.erase(p, q)   |  迭代器  |                 删除区间[p, q)中的元素                  |         all         |
|     a.clear()     |   void   |     清空序列元素，等价于a.erase(a.begin(), a.end())     |         all         |
|     a.front()     |    T&    |                       *a.begin()                        | vector、list、deque |
|     a.back()      |    T&    |                       *--a.end()                        | vector、list、deque |
|  a.push_front(t)  |   void   |                 a.insert(a.begin(), t)                  |     list、deque     |
|  a.push_back(t)   |   void   |                  a.insert(a.end(), t)                   | vector、list、deque |
|  a.pop_front(t)   |   void   |                   a.erase(a.begin())                    |     list、deque     |
|   a.pop_back(t)   |   void   |                   a.erase(--a.end())                    | vector、list、deque |
|       a[n]        |    T&    |                    *(a.begin() + n)                     |    vector、deque    |
|      a.at(n)      |    T&    | 同a[n]，与其区别是a.at(n)会在越界时引发out_of_range异常 |    vector、deque    |



有序关联容器：set、multiset、map、multimap，底层实现均为红黑树

无序关联容器：unordered_set、unordered_multiset、unordered_map、unordered_multimap，底层实现为哈希表

- set

  有序集合，不可重复，可反转，集合操作函数定义在头文件\<algorithm>中

  i, j, p, q: 迭代器

  inserter: 插入迭代器

  |                  函数                  |                    功能                    |
  | :------------------------------------: | :----------------------------------------: |
  |    set_union(i, j, p, q, inserter)     |                  集合并集                  |
  | set_intersection(i, j, p, q, inserter) |                  集合交集                  |
  |  set_difference(i, j, p, q, inserter)  | 集合差集，从第一个集合中删除两个集合的交集 |
  |         lower_bound(i, j, val)         |   有序序列中查找第一个不小于目标值的位置   |
  |         upper_bound(i, j, val)         |    有序序列中查找第一个大于目标值的位置    |

  ```c++
  #include <iostream>
  #include <set>
  #include <vector>
  #include <algorithm>
  
  using namespace std;
  
  int main() {
  	set<int> set1 = { 1, 2, 3 };
  	set<int> set2 = { 3, 4, 5 };
  
  	set<int> result1;
      // 此处inserter函数等价于insert_iterator<set<int>>()
  	set_union(set1.begin(), set1.end(), set2.begin(), set2.end(), inserter(result1, result1.begin()));
  	for (auto x : result1) {
  		cout << x << " ";
  	}
  	cout << endl;
  
  	set<int> result2;
  	set_intersection(set1.begin(), set1.end(), set2.begin(), set2.end(), inserter(result2, result2.begin()));
  	for (auto x : result2) {
  		cout << x << " ";
  	}
  	cout << endl;
  
  	set<int> result3;
  	set_difference(set1.begin(), set1.end(), set2.begin(), set2.end(), inserter(result3, result3.begin()));
  	for (auto x : result3) {
  		cout << x << " ";
  	}
  	cout << endl;
  
  	vector<int> nums = { 1, 2, 3, 4, 5 };
  	auto it = lower_bound(nums.begin(), nums.end(), 3);
  	cout << "The first position of 3 is: " << it - nums.begin() << endl;
  
  	it = upper_bound(nums.begin(), nums.end(), 3);
  	cout << "The first position of number greater than 3 is: " << it - nums.begin() << endl;
  
  	return 0;
  }
  
  outputs:
  1 2 3 4 5
  3
  1 2
  The first position of 3 is: 2
  The first position of number greater than 3 is: 3
  ```

  

- multiset

  同set，但元素可以重复

- map

  有序key-value键值对集合（按key排序），key不可重复

- multimap

  同map，但key可以重复



### 函数对象

使用方式同普通函数的可调用对象，也叫函数符，常用于算法、容器、线程等方面。

例如for_each()函数第三个参数使用的就是函数符，其函数原型如下：

```c++
template <typename InputIt, typename UnaryFunction>
UnaryFunction for_each(InputIt first, InputIt last, UnaryFunction f);
```



- 函数指针

  ```c++
  int foo(int x) {
    return x * x;
  }
  
  int (*func_ptr)(int) = foo;
  int result = func_ptr(5);
  ```

  

- 类运算符()重载

  ```c++
  class Times {
  public:
      Times(int n): m(n) {}
      int operator()(int x) const {
          return x * m;
      }
  private:
      int m;
  };
  
  Times times3(3);
  int result = times3(5); // result = 15
  
  ```

  

- 匿名函数

  ```c++
  #include <iostream>
  #include <vector>
  #include <algorithm>
  
  void print(int i) {
      std::cout << i << ' ';
  }
  
  int main() {
      std::vector<int> v = {1, 2, 3, 4, 5};
      // 使用Lambda表达式作为操作，将每个元素乘以2
      std::for_each(v.begin(), v.end(), [](int& i){ i *= 2; });
      // 输出 2 4 6 8 10
      std::for_each(v.begin(), v.end(), print);
      return 0;
  }
  ```

  

|   类别   |         定义         |               用例                |
| :------: | :------------------: | :-------------------------------: |
|  生成器  |      无参函数符      |                                   |
| 一元函数 |   一个参数的函数符   |       for_each()第三个参数        |
| 二元函数 |   两个参数的函数符   |                                   |
|   谓词   | 返回bool值的一元函数 | list成员函数remove_if()第三个参数 |
| 二元谓词 | 返回bool值的二元函数 |         sort()第三个参数          |



STL内置函数符，定义在头文件\<functional>中

| 运算符 |    函数符     |
| :----: | :-----------: |
|   +    |     plus      |
|   -    |     minus     |
|   *    |  multiplies   |
|   /    |    divides    |
|   %    |    modulus    |
|   -    |    negate     |
|   ==   |   equal_to    |
|   !=   | not_equal_to  |
|   >    |    greater    |
|   <    |     less      |
|   >=   | greater_equal |
|   <=   |  less_equal   |
|   &&   |  logical_and  |
|  \|\|  |  logical_or   |
|   !    |  logical_not  |

函数适配器

```c++
// 二元函数转换为一元函数
// f2()二元函数
// f1()一元函数
// binder1st，使用val替代f2第一个参数，因此f1(x)等价于f2(val, x)
// binder1st，使用val替代f2第二个参数，因此f1(x)等价于f2(x, val)
binder1st(f2, val) f1;
binder2nd(f2, val) f1;

#include <iostream>
#include <vector>
#include <algorithm>
#include <iterator>
#include <functional>
using namespace std;

void Show(double);
const int LIM = 6;

int main() {
	double arr1[6] = { 2,3,4,5,6,7 };
	double arr2[6] = { 1,1,1,1,1,1 };
	vector<double> vd1(arr1, arr1 + 6);
	vector<double> vd2(arr2, arr2 + 6);
	vector<double> sum(6);
	
    // vd1和vd2相加，结果保存在sum中
	transform(vd1.begin(), vd1.end(),vd2.begin(), sum.begin(), plus<double>());
	for_each(sum.begin(), sum.end(), Show);
	cout << endl;
	// vd1中元素乘2.5，结果保存在sumz
	transform(vd1.begin(), vd1.end(), sum.begin(), bind1st(multiplies<double>(), 2.5));
	for_each(sum.begin(), sum.end(), Show);
	return 0;
}

void Show(double d)
{
	cout << d << ' ';
}

outputs:
3 4 5 6 7 8
5 7.5 10 12.5 15 17.5
```



### STL算法

- 非修改式序列操作\<algorithm>
  - `std::find`：在序列中查找指定值的元素。
  - `std::count`：计算序列中指定值的元素个数。
  - `std::all_of`：判断序列中的所有元素是否都满足给定的条件。
  - `std::any_of`：判断序列中是否存在至少一个元素满足给定的条件。
  - `std::none_of`：判断序列中是否不存在满足给定条件的元素。
- 修改式序列操作\<algorithm>
  - `std::copy`：将一个序列的元素复制到另一个序列中。
  - `std::fill`：将指定的值赋给序列中的元素。
  - `std::transform`：对序列中的每个元素应用给定的操作，并将结果存储在另一个序列中。
  - `std::replace`：将序列中的所有指定值替换为另一个值。
  - `std::remove`：从序列中删除指定值的所有元素。
- 排序和相关操作\<algorithm>
  - `std::sort`：对序列进行升序排序。
  - `std::partial_sort`：对序列进行部分排序，保证指定位置前的元素是有序的。
  - `std::binary_search`：在有序序列中进行二分查找。
  - `std::merge`：合并两个有序序列为一个有序序列。
  - `std::reverse`：反转序列中的元素的顺序。
- 通用数字运算\<numeric>
  - `std::accumulate`：计算序列中元素的累加和。
  - `std::inner_product`：计算两个序列的内积。
  - `std::partial_sum`：计算部分和序列。
  - `std::iota`：生成按递增顺序填充序列的值。



- 就地算法

  在原始数据上直接进行操作，修改原始数据，不需要额外的缓冲区或副本。

  例如sort()

- 复制算法

  将原始数据复制到另一个位置，生成一个新的副本。复制算法不会改变原始数据，而是创建一个新的数据序列。

  例如copy()

有些算法两个版本都有，复制版本的名称以_copy结尾，例如replace_copy()。有些算法将函数应用于容器元素来得到执行结果，该版本通常以\_if结尾，例如replace_if()。

```c++
std::vector<int> numbers = {1, 2, 2, 3, 4, 2};
std::replace(numbers.begin(), numbers.end(), 2, 5);
// 现在 numbers 变为 {1, 5, 5, 3, 4, 5}

std::vector<int> numbers = {1, 2, 3, 4, 5};
std::replace_if(numbers.begin(), numbers.end(), [](int num) {
    return num % 2 == 0; // 判断偶数
}, 0);
// 现在 numbers 变为 {1, 0, 3, 0, 5}

std::vector<int> source = {1, 2, 3, 4, 5};
std::vector<int> destination(source.size());
std::replace_copy(source.begin(), source.end(), destination.begin(), 2, 0);
// 现在 destination 变为 {1, 0, 3, 4, 5}

std::vector<int> source = {1, 2, 3, 4, 5};
std::vector<int> destination(source.size());
std::replace_if_copy(source.begin(), source.end(), destination.begin(), [](int num) {
    return num % 2 == 0;
}, 0);
// 现在 destination 变为 {1, 0, 3, 0, 5}
```



应用于string类的算法next_permutation()，两个参数[begin(), end())，将给定范围的元素重新排列为下一个字典序更大的排列，若没达到最后一个排列，该算法返回true，序列中有重复元素时不会生成重复序列；否则返回false，并重置为第一个排列（即完全升序排列）。

```c++
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
	string str;
	cout << "Enter the string (quit to quit): ";
	while (cin >> str && str != "quit") {
		cout << "Permutation of " << str << endl;
		while (next_permutation(str.begin(), str.end())) {
			cout << str << endl;
		}
        cout << str << endl;
		cout << "Enter next string (quit to quit): ";
	}
	cout << "Done." << endl;
	return 0;
}

outputs:
Enter the string (quit to quit): acb
Permutation of acb
bac
bca
cab
cba
abc
Enter next string (quit to quit): abb
Permutation of abb
bab
bba
abb
Enter next string (quit to quit): cba
Permutation of cba
abc
Enter next string (quit to quit): quit
Done.
```



容器成员函数可以调整容器长度，而STL函数不能修改容器长度，实现相同功能时，两种函数的结果可能并不相同。例如list的remove()函数可以删除指定值元素，并修改list长度，但STL函数std::remove()，仅仅将不等于指定值的元素复制到list前面，返回等于执行了list.remove()后的list.end()的迭代器，标识容器新的超尾。

```c++
#include <iostream>
#include <list>
#include <algorithm>

using namespace std;

void Show(int);

int main() {
	list<int> la = { 1,4,3,4,5,7,8,4 };
	list<int> lb = { 1,4,3,4,5,7,8,4 };

	la.remove(4);
	auto last = remove(lb.begin(), lb.end(), 4);

	cout << "la_size: " << la.size() << endl;
	for_each(la.begin(), la.end(), Show);
	cout << endl;
	cout << "lb_size: " << lb.size() << endl;
	for_each(lb.begin(), lb.end(), Show);

	lb.erase(last, lb.end());
	cout << endl;
	cout << "lb_size: " << lb.size() << endl;
	for_each(lb.begin(), lb.end(), Show);

	return 0;
}

void Show(int x)
{
	cout << x << " ";
}


outputs:
la_size: 5
1 3 5 7 8
lb_size: 8
1 3 5 7 8 7 8 4
lb_size: 5
1 3 5 7 8
```



### 数组模板

- vector

  STL的一部分，动态扩容，随机访问，支持面向容器的操作：排序、插入、搜索、数据转移等

- valarray

  面向数值计算的数据，提供了各种数学函数和运算符重载

  ```c++
  valarray<double> vad1(10), vad2(10), vad3(10);
  vad3 = vad2 + vad1; // 重载基本运算符
  vad3 = vad2 * vad1;
  vad3 = vad3 * 2.5;
  vad3 *= 2.5;
  vad3 = log(vad1); // 重载了对数函数、三角函数等数学函数
  vad3 = sin(vad1);
  vad3 = 10.0 * ((vad1 + vad2) / 2.0 + vadl * cos(vad2));
  valarray<bool> vbool = vad1 > 9; // 创建一个bool数组，vad1[i] > 9时，vbool[i]置为true，否则为false
  sort(vad1, vad1 + 10);        // 非法操作
  sort(&ad1[0], &vad1[10]);     // 编译通过，运行数组越界
  sort(begin(vad1), end(vad1)); // c++11提供的合法操作，由valarray类提供
  ```

  \<valarray>头文件中还定义了一个用于切片操作的函数slice(a, b, c)，三个整数a、b、c分别表示起始索引、取值个数、跨距，例如slice(0, 4, 3)表示取下标为0、3、6、9的元素，利用该函数，一维数组valarray可以表示二维数据。

  ```c++
  valarray<double> va(8);
  va[slice(1,3,3)] = 10;  // 1、4、7下标元素值置为10，{ 0,10,0,0,10,0,0,10 }
  
  using vint = valarray<int>;
  vint val = { 1,2,3,4,5,6,7,8,9,10,11,12 }; // 二维数组4*3
  val[slice(0, 4, 3)] = vint(val[slice(1, 4, 3)]) + vint(val[slice(2, 4, 3)]); // 第二列加上第三列元的和赋给第一列
  ```

  

- array

  固定大小数组，相较于c数组更安全和方便



### 模板initializer_list

定义在<initializer_list>头文件中，用于使用列表初始化数组或容器，列表中元素类型要相同或可以隐式转换为目标类型，initializer_list对象只读，不可修改。

```c++
// 以下几种方式等价
valarray<int> al = { 1, 2, 3 };
valarray<int> al{ 1, 2, 3 };
valarray<int> al({ 1, 2, 3 });

shared_ptr<double> pd{ new double }; // 等价于shared_ptr<double> pd(new double);
vector<int> vi{10}; // 等价于vector<int> vi({10});

#include <iostream>
#include <initializer_list>

void printValues(std::initializer_list<int> values) {
    for (auto value : values) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
}

int main() {
    std::initializer_list<int> numbers = { 1, 2, 3, 4, 5 };
    printValues(numbers);

    return 0;
}
```



## 输入、输出和文件

### 流、缓冲区和iostream

- streambuf

  基类，用于管理输入输出流的内部缓冲区，它定义了操作缓冲区（填充、访问、刷新、管理等）的接口

- ios_base

  基类，用于设置和获取与流相关的一般属性，其他流类继承自它，如 `std::ios`、`std::ostream` 和 `std::istream`

- ios

  `std::ios_base` 的派生类，用于处理格式、状态和错误等流的控制特性，包含了一个streambuf对象指针

- ostream

  用于输出的流类，通过该类可以进行输出操作，比如将数据写入到标准输出设备或文件中

- istream

  用于输入的流类，通过该类可以进行输入操作，比如从标准输入设备或文件中读取数据

- iostream

  同时支持输入和输出的流类，它继承自 `std::istream` 和 `std::ostream`，可用于双向数据传输，包含\<iostream>头文件将自动创建8个流对象，即cin、cout、cerr和clog的窄字符流（char）和wcin、wcout、wcerr和wclog的宽字符流（wchar_t）

- cin

  标准输入流对象，用于从控制台或终端读取用户输入的数据

- cout

  标准输出流对象，用于将数据输出到控制台或终端

- cerr

  标准错误流对象，用于将错误消息输出到控制台或终端

- clog

  标准日志流对象，用于将日志消息输出到控制台或终端

`cerr` 和 `clog` 相对于 `cout` 使用不同的错误/日志缓冲区，`cerr` 和 `clog` 的输出通常比 `cout` 更及时，输出会直接发送到标准错误设备，不受输出缓冲区和标准输出重定向的影响。默认情况下，`cin` 和 `cerr` 是无缓冲的，而 `cout` 和 `clog` 是行缓冲的。这意味着 `cout` 和 `clog` 在遇到换行符时会自动刷新缓冲区，而 `cin` 和 `cerr` 则没有缓冲区的延迟。

重定向输入流使用`<`运算符，重定向输出流使用`>`运算符，通常在操作系统命令行使用，例如`counter <ifile >ofile`，可执行程序`counter`，重定向输入输出为文件而不是控制台，使用ifile文件作为输入，ofile文件作为输出。标准错误流（`cerr`和`clog`）不受重定向影响，依然会打印在控制台，当前操作系统使用运算符`2>`重定向标准错误流。



### cout

通常结和`<<`运算符（插入运算符）一起使用，能识别输出c++中所有基本类型：

- unsigned char
- signed char
- char
- short
- unsigned short
- int
- unsigned int
- long
- unsigned long
- long long 
- unsigned long long 
- float
- double 
- long double

指针类型，包括c风格字符串和指针地址：

- const char*
- const unsigned char*
- const signed char*
- void*

```c++
char* amount = "hello world";
cout << amount; // 打印amount字符串
cout << (void*)amount; // 打印amount地址
```

ostream一些方法的原型：

```c++
ostream& operator<<(type); // <<运算符
ostream& put(char); // 用于字符显示
basic_ostream<charT, traits>& write(const char_type* s, streamsize n); // 用于字符串显示，cout调用返回ostream&


cout.put('A');  // 打印字符A
cout.put(65);   // 打印字符A，int转换char
cout.put(66.3); // 打印字符B，double转换为char
cout.put('I').put('t');  // 打印字符It

const char* str = "hello";
cout.write(str, strlen(str)) << endl; // 打印hello并换行
```

- flush

  刷新输出缓冲区，立即将缓冲区内容发送到输出设备

  ```c++
  cout << "hello";
  // 三种方式均可
  cout.flush();
  cout << flush;
  flush(cout);
  ```

  

- endl

  输出流中插入一个换行符，并刷新输出缓冲区

格式化输出：

- 修改计数基数

  十进制dec（默认）、十六进制hex和八进制oct，修改持续生效，直至设置为其他基数

  ```c++
  // 两种方式均可
  hex(cout);
  cout << hex;
  ```

  

- 调整字段宽度

  cout的width()成员函数，只影响下一个cout的内容，默认字宽为0，输出内容长度大于设置的宽度时，不会截断，会完整显示内容，填充字符默认是空格，默认右对齐

  ```c++
  int width();  // 返回当前设置的字宽
  int width(int i); // 设置字宽为i，返回上一个字宽值
  
  cout << cout.width() << endl;
  cout.width(8);
  int width = cout.width();
  cout << "aaf" << "abc" << endl;
  
  outputs:
  0
       aafabc
  8
  ```

  

- 填充字符

  cout的fill()成员函数，用来改变填充字符，该修改一直有效，直到更改为止

  ```c++
  cout.fill('*');
  cout << cout.fill() << endl; // fill()返回当前填充字符
  cout.width(8);
  cout << "3900" << endl;
  cout.width(8);
  cout << "258" << endl;
  
  outputs:
  *
  ****3900
  *****258
  ```

  

- 浮点数显示精度

  - 默认输出模式下，指显示的总位数，默认精度为6位，末尾0不显示
  - 定点模式和科学模式下，指小数点后有效数字位数
  - 精度设置一直有效，直到更改它为止

  ```c++
  cout.precision(2); // 浮点数显示精度设置为2
  ```

  

- 打印数字末尾0和小数点

  ```c++
  cout.setf(ios_base::showpoint);
  
  #include <iostream>
  using namespace std;
  
  int main() {
  	float a = 20.04;
  
  	cout.setf(ios_base::showpoint);  // 打印数字末尾0和小数点
  	cout << "a = " << a << endl;
  
  	cout.unsetf(ios_base::showpoint); // 取消setf设置
      cout << "a = " << a << endl;
      
  	cout.precision(2);
  	cout << "a = " << a << endl;
  
  	cout.setf(ios_base::showpoint);
  	cout << "a = " << a << endl;
  	
  	return 0;
  }
  
  outputs:
  a = 20.0400
  a = 20.04
  a = 20
  a = 20.
  ```



- setf()

  ios_base有个成员变量控制输出格式，可以用setf()函数修改格式标志位，返回上一次格式标志位的值，setf()修改一直生效，函数原型如下：

  ```c++
  fmtflags setf(fmtflags flags);  // setf()传递进去的整数将会与格式标志位进行按位或操作，即原来的1保留，flags中1新增
  ```

  

  |        常量         |                    功能                    |
  | :-----------------: | :----------------------------------------: |
  | ios_base::boolalpha |      可使用true和false输入输出bool值       |
  | ios_base::showbase  | 显示基数前缀，dec(无前缀)、hex(0x)、oct(0) |
  | ios_base::showpoint |            显示末尾的0和小数点             |
  | ios_base::uppercase |      十六进制输出时，采用大写字母表示      |
  |  ios_base::showpos  |         正数前面加上+，仅对dec有效         |

  ```c++
  cout.setf(ios_base::showbase);
  cout << 10 << endl;
  // 使用setf设置基数时，注意要把当前基数unsetf掉，设置多个基数位时将会以十进制输出
  cout.unsetf(ios_base::dec);
  cout.setf(ios_base::hex);
  cout << 10 << endl;
  
  cout.setf(ios_base::uppercase);
  cout << 10 << endl;
  
  cout.unsetf(ios_base::hex);
  cout.setf(ios_base::oct);
  cout << 10 << endl;
  
  cout.setf(ios_base::showpos);
  cout.unsetf(ios_base::oct);
  cout.setf(ios_base::dec);
  cout << 10 << endl;
  
  bool flag = 1;
  cout.setf(ios_base::boolalpha);
  cout << flag << endl;
  
  outputs:
  10
  0xa
  0XA
  012
  +10
  true
  ```

  ```c++
  // setf()另一个原型，清除格式标志位mask中的1，添加flag中的1（且在mask中也是1）
  fmtflags setf(fmtflags flags, fmtflags mask); 
  ```

  |        flags         |         mask          |                 功能                 |
  | :------------------: | :-------------------: | :----------------------------------: |
  |    ios_base::dec     |  ios_base::basefield  |              十进制显示              |
  |    ios_base::hex     |  ios_base::basefield  |             十六进制显示             |
  |    ios_base::oct     |  ios_base::basefield  |              八进制显示              |
  |   ios_base::fixed    | ios_base::floatfield  | 定点计数法，精度代表小数点后有效数字 |
  | ios_base::scientific | ios_base::floatfield  | 科学计数法，精度代表小数点后有效数字 |
  |    ios_base::left    | ios_base::adjustfield |                左对齐                |
  |   ios_base::right    | ios_base::adjustfield |                右对齐                |
  |  ios_base::internal  | ios_base::adjustfield |    符号或基数前缀左对齐，值右对齐    |

  ```c++
  cout.setf(ios_base::showbase);
  cout << 10 << endl;
  
  cout.setf(ios_base::hex, ios_base::basefield);
  cout << 10 << endl;
  
  cout.setf(ios_base::uppercase);
  cout << 10 << endl;
  
  cout.setf(ios_base::oct, ios_base::basefield);
  cout << 10 << endl;
  
  cout.setf(ios_base::showpos);
  cout.setf(ios_base::dec, ios_base::basefield);
  cout << 10 << endl;
  
  bool flag = 1;
  cout.setf(ios_base::boolalpha);
  cout << flag << endl;
  
  outputs:
  10
  0xa
  0XA
  012
  +10
  true
      
      
  ios_base::fmtflags old = cout.setf(ios_base::left, ios_base::adjustfield); // 设置输出左对齐
  cout.setf(old, ios_base::adjustfield); // 还原对齐设置
  
  // 浮点格式两个标记位（定点，科学），仅当有一位为1时才生效，两个位都为0或1时，浮点格式为默认模式
  // 没有直接切换默认浮点格式的标记，可使用以下任意方式恢复默认浮点格式
  cout.setf(0, ios_base::floatfield);
  cout.unsetf(ios_base::floatfield);  // 已知当前模式时，可直接unsetf当前模式
  ```

  

- 标准控制符

  c++提供了对用户更友好的方法来调用setf()，例如：

  ```c++
  cout<< hex << uppercase << fixed; // 十六进制基数，大写十六进制字母，浮点定点模式
  ```

  |   控制符    |                     调用函数                     |
  | :---------: | :----------------------------------------------: |
  |  boolalpha  |            setf(ios_base::boolalpha)             |
  | noboolalpha |           unsetf(ios_base::boolalpha)            |
  |  showbase   |             setf(ios_base::showbase)             |
  | noshowbase  |            unsetf(ios_base::showbase)            |
  |  showpoint  |            setf(ios_base::showpoint)             |
  |  showpoint  |           unsetf(ios_base::showpoint)            |
  |   showpos   |             setf(ios_base::showpos)              |
  |  noshowpos  |            unsetf(ios_base::showpos)             |
  |  uppercase  |            setf(ios_base::uppercase)             |
  | nouppercase |           unsetf(ios_base::uppercase)            |
  |  internal   | setf(ios_base::internal, ios_base::adjustfield)  |
  |    left     |   setf(ios_base::left, ios_base::adjustfield)    |
  |    right    |   setf(ios_base::right, ios_base::adjustfield)   |
  |     dec     |     setf(ios_base::dec, ios_base::basefield)     |
  |     hex     |     setf(ios_base::hex, ios_base::basefield)     |
  |     oct     |     setf(ios_base::oct, ios_base::basefield)     |
  |    fixed    |   setf(ios_base::fixed, ios_base::floatfield)    |
  | scientific  | setf(ios_base::scientific, ios_base::floatfield) |

  

- 头文件\<iomanip>

  提供了一些可以与cout连用的控制符

  - setprecision()设置浮点精度，持续有效
  - setfill()设置填充字符，持续有效
  - setw()设置字段宽度，仅下一个输出有效

  ```c++
  #include <iostream>
  #include <iomanip>
  using namespace std;
  
  int main() {
  	cout << fixed << right;
  	cout << setw(6) << "N" 
  		<< setw(13) << "square root" 
  		<< setw(13) << "fourth root" << endl;;
  	double root;
  	for (int n = 10; n <= 100; n += 10) {
  		root = sqrt(double(n));
  		cout << setw(6) << setfill('=') << n
  			<< setfill(' ') << setw(10) << setprecision(3) << root
  			<< setw(13) << setprecision(4) << sqrt(root)
  			<< endl;
  	}
  	return 0;
  }
  
  outputs:
       N  square root  fourth root
  ====10     3.162       1.7783
  ====20     4.472       2.1147
  ====30     5.477       2.3403
  ====40     6.325       2.5149
  ====50     7.071       2.6591
  ====60     7.746       2.7832
  ====70     8.367       2.8925
  ====80     8.944       2.9907
  ====90     9.487       3.0801
  ===100    10.000       3.1623
  ```

  

### cin

通常结和`>>`运算符（抽取运算符）一起使用，能识别输出c++中所有基本类型，读取从非空白字符（空格、换行和字符表）开始，到与目标类型不匹配的第一个字符之间的全部内容：

- unsigned char&
- signed char&
- char&
- short&
- unsigned short&
- int&
- unsigned int&
- long&
- unsigned long&
- long long&
- unsigned long long&
- float&
- double&
- long double&

指针类型，读取输入的一个单词，添加一个控制字符\0，组合成一个字符串：

- char*
- unsigned char*
- signed char*



|          成员          |                             描述                             |
| :--------------------: | :----------------------------------------------------------: |
|         eofbit         |                   如果到达文件尾，设置为1                    |
|         badbit         |           如果流被破坏，设置为1，例如文件读取错误            |
|        failbit         |        如果输入或输出没能读取或写入预期字符，设置为1         |
|        goodbit         |                    常数0，表示流操作成功                     |
|         good()         |      如果流操作成功，流状态为0，返回true，否则返回false      |
|         eof()          |                   如果eofbit为1，返回true                    |
|         bad()          |                   如果badbit为1，返回true                    |
|         fail()         |               如果failbit或badbit为1，返回true               |
|       rdstate()        |                          返回流状态                          |
|      exceptions()      |    获取流发生错误时哪些错误抛出std::ios_base::failure异常    |
| exceptions(iostate ex) |    设置流发生错误时哪些错误抛出std::ios_base::failure异常    |
|    clear(iostate s)    | 无参数版本用于清除流状态（置0），一个参数版本先清除当前状态再设置流状态 |
|  setstate(iostate s)   |                        用于设置流状态                        |

- `std::ios_base::goodbit`：表示不抛出任何异常
- `std::ios_base::badbit`：表示在发生底层数据错误时抛出 `std::ios_base::failure` 异常
- `std::ios_base::failbit`：表示在失败的输入/输出操作被执行时抛出 `std::ios_base::failure` 异常
- `std::ios_base::eofbit`：表示在尝试读取文件结束标记（EOF）时抛出 `std::ios_base::failure` 异常

```c++
// eofbit、failbit、badbit分别是流状态的低三位，goodbit代表0
static constexpr _Iostate goodbit = static_cast<_Iostate>(0x0);
static constexpr _Iostate eofbit  = static_cast<_Iostate>(0x1);
static constexpr _Iostate failbit = static_cast<_Iostate>(0x2);
static constexpr _Iostate badbit  = static_cast<_Iostate>(0x4);

// 将异常处理方式设置为在发生底层数据错误或失败的输入/输出操作时抛出异常
// 默认情况下cin、cout错误时是不会抛出异常的
std::cout.exceptions(std::ios_base::badbit | std::ios_base::failbit);

// 都可以设置流状态，区别在于clear先把流状态置0
// 抛出std::ios_base::failure异常是在clear函数里操作的，setstate和exceptions都会调用clear函数
clear(std::ios_base::badbit | std::ios_base::failbit);
setstate(std::ios_base::badbit | std::ios_base::failbit);
```



输入失败时，cin返回0，可用于while和if的判断条件，来结束输入。流状态设置后，后面的输入输出将关闭，所以希望后续输入输出能正常工作，注意使用clear()重置流状态，若输入非预期，此时不匹配的输入仍留在输入队列，可使用一些方式跳过这些字符。

```c++
int a;
while(cin >> a) {
	...
}

if(cin.fail() && !cin.eof()) { // 排除遇到文件尾部情况
    cin.clear();
    while(!isspace(cin.get())) // 读取到空白字符为止
		continue;
    while(cin.get() != '\n')   // 读取到换行字符为止
		continue;
}
```

- 单字符输入

  - get(char& ch)

    - 读取一个任意字符，包括空白字符
    - 返回istream对象的引用，这意味着可以连续调用
    - 若到达文件尾（模拟文件尾，DOS和Windows下命令行是Ctrl+Z；UNIX下是行首按下Ctrl+D），get(ch)赋值失败，返回值转换为false

    ```c++
    char ch;
    while(cin.get(ch)) {
    	char c1, c2, c3;
    	cin.get(c1).get(c2) >> c3; // c3赋值为c2后第一个非空白字符，>>运算符会跳过空白字符
    }
    ```

    

  - get(void)

    - 读取一个任意字符，包括空白字符
    - 返回读取到的字符编码（int类型）
    - 若到达文件尾，返回EOF（在头文件\<iostream>中定义，Windows下是-1）

    ```c++
    char ch;
    while((ch = cin.get()) != EOF) {
    	char c1;
    	cin.get(c1).get(); // 可以在魔王使用，因为get(ch)返回cin对象的引用
    }
    ```

    

- 字符串输入

  ```c++
  // 参数一：字符串存储地址
  // 参数二：比最大读取字符数大1（额外一个字符用于保存\0）
  // 参数三：分界字符，默认为换行符\n
  // 函数将在遇到分界符或到达最大读取字符数减一个字符后停止输入
  // get()会将分界字符留在输入流中，getline()会将分界字符丢弃
  // 遇到EOF时将设置eofbit和failbit，遇到流破坏将会设置badbit
  // 空行，即输入流中只有分界字符时，get()会设置failbit；getline()会读取一个空值，不会设置failbit
  // 输入字符数大于接收字符数时，getline()会设置failbit，get()不会设置failbit
  istream& get(char*, int, char);
  istream& get(char*, int);
  istream& getline(char*, int, char);
  istream& getline(char*, int);
  
  // 丢弃输入流中n个字符，或到达第一个ch字符
  // n默认值为1，ch默认值为EOF
  // ignore()函数返回调用对象，可以拼接使用
  istream& ignore(int n = 1, int ch = EOF);
  ```

  

其他istream方法：

- istream& read(char* str, int n)

  从输入流读取n个字符保存到str，不会在结尾追加\0，通常和ostream的write()方法操作文件输入输出，返回值是istream对象引用，因此可以拼接使用

- int peek()

  返回输入流中下一个字符，但不抽取该字符

- long long gcount()

  - 返回最后一个非格式化抽取方法读取的字符数
  - 抽取运算符（>>）读取的会格式化数据与特定类型匹配，非格式化抽取指get()、getline()、ignore()和read()
  - 格式化抽取可以使用strlen()计算字符数，该方法比gcount()速度快

- istream& putback(char ch)

  把字符ch插入到上次从输入流读取的位置之前



### 文件输入和输出

- 创建简单的文件I/O流，可以使用\<fstream>头文件
- ifstream对象管理输入流
- ofstream对象管理输出流
- 每个对象都拥有自己的缓冲区
- 文件流关闭只是断开流到文件的连接，流管理和缓冲区仍然存在

```c++
ofstream fout;
fout.open("a.txt");
ofstream fout("a.txt"); // 默认文件若不存在则创建
fout << "Hello World";  // 可以使用<<运算符输出到文件
fout.close(); // 关闭输出文件流

ifstream fin;
fin.open("b.txt");
ifstream fin("b.txt"); // 默认文件不存在则打开失败，设置failbit为1
// 四种判断输入流是否成功打开的方式
// c++建议使用is_open()，
if(fin.fail()) {
    exit(0);
}
if(!fin.good()) {
    exit(0);
}
if(!fin) {
    exit(0);
}
if(!fin.is_open()) {
    exit(0);
}
char ch;
fin >> ch;
fin.clear(); // 重置流状态，不是必须的，但建议这么做
fin.close(); // 关闭输入文件流
```

命令行参数读取。

```c++
// 假定生成的程序名为counter
// 命令行输入counter a.txt b.txt c.txt
// argc指命令行中参数个数，包括程序名
// argv[0]值为counter，以此类推
int main(int argc, char *argv[]) {
	...
}
```

open()方法原型如下，openmode为打开模式，_Prot为文件打开权限，默认权限因操作系统而异

```c++
// ifstream成员函数
// 默认打开模式为ios_base::in，文件可读
void open(
	const char* _Filename, 
	ios_base::openmode _Mode = ios_base::in, 
	int _Prot = ios_base::_Default_open_prot
);

// ofstream成员函数
// 默认打开模式为ios_base::out，文件可写且文件内容被截断，其调用std::basic_filebuf的open()函数来打开文件
// 若输出模式为ios_base::out并且没有指定其他附加模式，则文件会以截断模式打开
// 可以使用追加模式或读写模式避免文件截断，有些实现允许省略ios_base::out
// ofstream fout;
// fout.open("example.txt", ios_base::app | ios_base::out);
// fout.open("example.txt", ios_base::in | ios_base::out);
void open(
	const char* _Filename, 
	ios_base::openmode _Mode = ios_base::out, 
	int _Prot = ios_base::_Default_open_prot
);
```



- 文件打开模式

|       常量       |                  功能                  |
| :--------------: | :------------------------------------: |
|   ios_base::in   |           打开文件，文件可读           |
|  ios_base::out   |           打开文件，文件可写           |
|  ios_base::ate   |       打开文件，移动指针到文件尾       |
|  ios_base::app   |             文件尾追加内容             |
| ios_base::trunc  | 如果文件存在，则截断文件，删除所有内容 |
| ios_base::binary |             打开二进制文件             |

- C++和C的文件打开模式，C语言使用fopen(filename, cmode)函数打开文件

|                     C++模式                     |     C模式      |                           功能                           |
| :---------------------------------------------: | :------------: | :------------------------------------------------------: |
|                  ios_base::in                   |      "r"       |             打开以读取，文件不存在则打开失败             |
|                  ios_base::out                  |      "w"       |          等价于ios_base::out \| ios_base::trunc          |
|        ios_base::out \| ios_base::trunc         |      "w"       |   打开以写入，若文件存在，则截断文件；文件不存在则创建   |
|         ios_base::out \| ios_base::app          |      "a"       |           打开以写入，只追加，文件不存在则创建           |
|          ios_base::out \| ios_base::in          |      "r+"      |  打开以读写，在文件允许的位置读写，文件不存在则打开失败  |
| ios_base::out \| ios_base::in \|ios_base::trunc |      "w+"      |   打开以读写，若文件存在，则截断文件；文件不存在则创建   |
|           c++mode \| ios_base::binary           | "rb"/"ab"/"wb" |                 二进制只读/写入/追加模式                 |
|            c++mode \| ios_base::ate             |       -        | 以指定模式打开，并移动到文件尾，C使用fseek()达到同样效果 |

- 二进制文件模式

  - 字符输出与文本模式无异，数字或结构体输出与文本模式不同
  - 通常和write()函数配合写入
  - 文本模式的换行随系统而异，Windows文件使用回车符+换行符表示换行，UNIX和LInux文件使用换行符表示换行，Macintosh文件使用回车表示换行，为增加可移植性，c++在不同系统上读取文件会将换行统一换成\n，写入文件时随系统而异
  - 二进制文件不可以文本模式读取，反之亦然
  - UNIX系统不区分文本模式和二进制模式

  ```c++
  #include <fstream>
  using namespace std;
  
  const int LIM = 20;
  struct planet
  {
      char name[LIM];
      double population;
      double g;
  };
  
  int main() {
      planet pl = { "Earth", 7.73e09, 0.9e-02 };
      ofstream fout("planets.dat", ios_base::out | ios_base::app);
      fout << pl.name << " " << pl.population << " " << pl.g << endl;
      // 文本编辑打开文件，文件内容：Earth 7.73e+09 0.009
      fout.close();
  	return 0;
  }
  
  int main() {
      planet pl = { "Earth", 7.73e09, 0.9e-02 };
      // 二进制追加模式，文本编辑器打开显示乱码
      ofstream fout("planets.dat", ios_base::out | ios_base::app | ios_base::binary);
      fout.write((char*)&pl, sizeof(pl));
      fout.close();
  
      planet p;
      // 二进制读取模式，正常打印每条数据
      ifstream fin("planets.dat", ios_base::in | ios_base::binary);
      while (fin.read((char*)&p, sizeof(pl))) {
          cout << p.name << " " << p.population << " " << p.g << endl;
      }
      fin.close();
  	return 0;
  }
  ```
  



文件随机存取方法，通常操作二进制文件，文本文件操作结果将不确定，fstream对象包含一个ifstream对象和一个ofstream对象，所以同时包含以下所有方法。

- seekg()

  - 用于输入流ifstream对象，移动输入流指针位置
  - 移动的位置超出文件头部或文件尾部，failbit将被设置为1

- tellg()

  - 用于输入流ifstream对象，返回当前文件指针相对于文件开头的字节数
  - 第一个字节编号为0

- seekp()

  - 用于输出流ofstream对象，移动输出流指针位置

  - 输出流使用追加模式ios_base::app时，该函数不会生效，追加模式只能在文件尾部写入，其他部分是只读的

  - 移动的位置超出文件头部，failbit将被设置为1；移动的位置超出文件尾部，会自动拓展文件大小至此位置，中间部分被填充无效值或空字节

  - 函数原型

    ```c++
    // pos指相对于文件开头位置的偏移量
    std::istream& seekg(std::streampos pos);
    std::ostream& seekp(std::streampos pos);
    
    // off指相对于dir位置的偏移量
    // dir参数是一个枚举类型，有三个值ios_base::beg、ios_base::cur和ios_base::end
    // 分别表示文件开头、当前位置和文件结尾
    std::istream& seekp(std::streamoff off, std::ios_base::seekdir dir);
    std::ostream& seekp(std::streamoff off, std::ios_base::seekdir dir);
    ```

    

- tellp()
  - 用于输出流ofstream对象，返回当前文件指针相对于文件开头的字节数
  - 对于fstream对象，tellg()和tellp()返回的值相同

c++使用临时文件，创建与删除与常规文件无异，但需要处理文件不重名问题，头文件\<cstdio>中提供的tmpnam()函数可以生成临时文件名

```c++
// 函数原型，pszName为临时文件名存储对象，返回值为操作结果，返回值0表示操作成功
// 现已使用tmpname_s()替代了
// 能生成最大文件名长度为L_tmpnam
char* tmpnam(char* pszName);
```



```c++
#include <iostream>
#include <cstdio>

using namespace std;

int main() {
	char pszName[L_tmpnam] = { '\0' };
	for (int i = 0; i < 10; i++) {
		tmpnam_s(pszName);
		cout << pszName << endl;
	}
	return 0;
}

outputs:
C:\Users\QWang\AppData\Local\Temp\ujl8.0
C:\Users\QWang\AppData\Local\Temp\ujl8.1
C:\Users\QWang\AppData\Local\Temp\ujl8.2
C:\Users\QWang\AppData\Local\Temp\ujl8.3
C:\Users\QWang\AppData\Local\Temp\ujl8.4
C:\Users\QWang\AppData\Local\Temp\ujl8.5
C:\Users\QWang\AppData\Local\Temp\ujl8.6
C:\Users\QWang\AppData\Local\Temp\ujl8.7
C:\Users\QWang\AppData\Local\Temp\ujl8.8
C:\Users\QWang\AppData\Local\Temp\ujl8.9
```



### 内核格式化

头文件\<sstream>提供了对string对象之间的I/O，可以将格式化信息写入string对象，或从string对象读取。

```c++
ostringstream outstr;
double price = 3.6;
char* ps = " for a copy of the ISO/EIC C++ standard!"
outstr.precision(2);
outstr << fixed;
outstr << "Pay only CHF " << price << ps <<endl;
// str()成员函数可以将ostringstream对象转为string对象
string mesg = outstr.str();

// istringstream可以使用string对象初始化
string facts = "Hello world";
istringstream instr(facts);
string word;
while(instr >> word) {
    cout << word << endl;
}
```



## C++11新标准

- 新增long long、unsigned long long、char16_t、char32_t类型

- 可使用初始化列表初始化列表和类（可以加=，也可以不加），若类有initializer_list作为参数的构造函数，则初始化列表将只能用于该构造函数

- auto关键字自动类型推断

- decltype关键字，将变量类型声明为表达式类型

  ```c++
  decltype(x) y; // 声明一个类型为表达式x类型的变量y
  
  double x;
  int n;
  decltype(x * n) y; // y为double类型变量
  decltype(&x) y;    // y为·double*类型变量
  
  // 应用于模板类型
  template<typename T, typename U>
  void ef(T t, U u)
  {
      decltype(t * u) tu;
  }
  
  int j = 3;
  int& k = j;
  const int& n = j;
  decltype(n) i1 = 8;   // const int&
  decltype(j) i2;       // int
  decltype((j)) i3 = j; // int&，非常量引用需要使用左值初始化，左值指可寻址的元素，右值指临时元素
  decltype(k + 1) i4;   //
  ```

  

- 返回类型后置，函数声明返回类型放在最后

  ```c++
  double f1(double, int);  // 常规函数声明
  auto f2(double, int) -> double; // 返回类型后置
  
  template<typename T, typename U>
  auto f2(T t, U u)->decltype(t* u); // 在t和u的作用域内使用它们
  ```

  

- 模板别名using =，替代typedef

  ```c++
  typrdef std::vector<std::string>::iterator itType;
  using itType = std::vector<std::string>::iterator;
  
  template<typename T>
  using arr12 = std::array<T, 12>; // using可用于模板部分具体化，typedef不行
  arr12<double> a1;
  ```

  

- 空指针nullptr，0也可以表示空指针，因此表达式nullptr == 0为true，但是0是整型常量，nullptr是指针常量，使用nullptr更安全

- 摒弃智能指针auto_ptr，新增智能指针unique_ptr、share_ptr和weak_ptr

- 摒弃异常规范throw()，添加关键字noexcept表示函数不会引发异常

- 提供新的作用域内枚举方式，可以使用class和struct解决名称冲突问题

  ```c++
  enum Old { yes, no, maybe };                         // 传统格式，名称不能与其他枚举重复
  enum class New1 { never, sometimes, often, always }; // 新格式
  enum struct New2 { never, lever, sever };            // 新格式
  // 新的枚举方式要求进行显式限定进行引用，New1::never，New2::never
  ```

  

- 类显式转换关键字explicit，禁止单参数构造函数导致的自动隐式转换，反向转换函数也同样适用

  ```c++
  class Plebe
  {
      Plebe(int);             // 允许自动从int类型转换为Plebe类型
      explicit Plebe(double); // 禁止自动从double类型转换为Plebe类型
      ...
      operator int() const;               // 允许自动从Plebe类型转换为int类型
      explicit operator double() const;   // 禁止自动从Plebe类型转换为double类型
  }
  
  Plabe a, b;
  a = 5;          // 允许
  b = 0.5;        // 禁止
  b = Plebe(0.5); // 允许
  
  int n = a;     // 允许
  double x; 
  x = b;         // 禁止 
  x = double(b); // 允许
  ```

  

- 早期C++不支持在类定义中进行类成员初始化，类成员初始化必须在构造函数中完成，C++11允许在类定义中进行类成员初始化，即类成员默认值，可以使用赋值或初始化列表方式，构造函数若在成员初始化列表提供了这些成员的值，默认值将被覆盖

- 模板及STL方面修改

  - 对于内置数组和支持迭代器的类和STL容器，可以使用基于范围的for循环简化代码

  - C++11新增STL容器forward_list、unordered_map、unordered_multimap、unordered_set和unordered_multiset，新增模板array，可指定元素类型和元素数

  - 新的STL方法cbegin()和cend()，返回一个const版本迭代器，于此类似，也有crbegin()和crend()

  - valarray升级，添加函数begin()和end()接收valarray作为参数，返回迭代器，使之能够使用STL算法

  - 摒弃关键字export使模板声明和模板定义可以在不同文件中的功能

  - 早期C++声明嵌套模板时需要使用空格将尖括号分开，避免与运算符>>混淆，C++11不在这样要求

    ```c++
    std::vector<std::list<int> > v1; // C++98
    std::vector<std::list<int>> v1;  // C++11
    ```

    


- 右值引用

  左值指可获取地址的数据，可出现在赋值表达式左边，例如变量名或解除引用的指针；右值指不能对其应用地址运算符的值，可出现在赋值表达式的右边，例如字面常量（字符串除外，它表示地址）、x+y表达式或返回值的函数（返回的非引用）。

  ```c++
  int x = 10;
  int y = 23;
  int&& r1 = 13;
  // r2关联的是x+y的结果23，及时后续修改了x或y的值，也不会影响r2的值
  int&& r2 = x + y;
  double&& r3 = std::sqrt(2.0);
  ```

  

### 移动语义与右值引用

移动语义，指返回临时变量时，通常需要复制构造函数将临时变量的信息复制过来，这样做工作量很大，可以将临时变量的所有权直接转让而省去复制操作，这种方法称为移动语义。

常规复制构造函数使用const类型的左值引用作为参数，可以关联到左值实参。

移动构造函数可以通过右值引用完成移动语义，它使用右值引用作为参数，可以使用函数返回的临时变量直接把其所有权转让给新对象，这个过程中可能修改其实参，所以右值引用参数不使用const。

```c++
#include <iostream>
using namespace std;

class Useless {
private:
	int n;
	char* pc;
	static int ct;
	void ShowObject() const;
public:
	Useless();
	explicit Useless(int k);
	Useless(int k, char ch);
	Useless(const Useless& f); // 复制构造函数，const类型左值引用
	Useless(Useless&& f);      // 移动构造函数，右值引用
	~Useless();
	Useless operator+(const Useless& f) const;
	void ShowData() const;
};

int Useless::ct = 0;

void Useless::ShowObject() const
{
	cout << "Number of elements: " << n;
	cout << "; Data address: " << (void*)pc << endl;
}

Useless::Useless()
{
	++ct;
	n = 0;
	pc = nullptr;
	cout << "default constructor called; number of objects: " << ct << endl;
	ShowObject();
}

Useless::Useless(int k) : n(k)
{
	++ct;
	cout<<"int constructor called; number of objects: " << ct << endl;
	pc = new char[n];
	ShowObject();
}

Useless::Useless(int k, char ch) : n(k)
{
	++ct;
	cout << "int, char constructor called; number of objects: " << ct << endl;
	pc = new char[n];
	for (int i = 0; i < n; i++) {
		pc[i] = ch;
	}
	ShowObject();
}

Useless::Useless(const Useless& f) : n(f.n)
{
	++ct;
	cout << "copy constructor called; number of objects: " << ct << endl;
	pc = new char[n];
	for (int i = 0; i < n; i++) {
		pc[i] = f.pc[i];
	}
	ShowObject();
}

Useless::Useless(Useless&& f) : n(f.n)
{
	++ct;
	cout << "move constructor called; number of objects: " << ct << endl;
	pc = f.pc;      // 窃取所有权
	f.pc = nullptr; // 修改f对象，移动构造函数不能使用const，空指针可以执行delete[]，避免析构函数多次释放同一空间
	f.n = 0;        // 无关紧要语句
	ShowObject();
}

Useless::~Useless()
{
	cout << "destructor called; objects left: " << --ct << endl;
	cout << "delete objects:" << endl;
	ShowObject();
	delete[] pc;
}

Useless Useless::operator+(const Useless& f) const
{
	cout << "Entering operator+()" << endl;
	Useless temp = Useless(n + f.n);
	for (int i = 0; i < n; i++) {
		temp.pc[i] = pc[i];
	}
	for (int i = n; i < temp.n; i++) {
		temp.pc[i] = f.pc[i - n];
	}
	cout << "temp object:" << endl;
	cout << "Leaving operator+()" << endl;
	return temp;
}

void Useless::ShowData() const
{
	if (n == 0) {
		cout << "object empty";
	}
	else {
		for (int i = 0; i < n; i++) {
			cout << pc[i];
		}
	}
	cout << endl;
}

int main() {
	{
		Useless one(10, '#');
		Useless two = one;
		Useless three(20, '$');
		Useless four(one + three);
		cout << "object one: ";
		one.ShowData();
		cout << "object two: ";
		two.ShowData();
		cout << "object three: ";
		three.ShowData();
		cout << "object four: ";
		four.ShowData();
	}
	return 0;
}

outputs:
int, char constructor called; number of objects: 1
Number of elements: 10; Data address: 000002CBFA5B55A0
copy constructor called; number of objects: 2
Number of elements: 10; Data address: 000002CBFA5B5640
int, char constructor called; number of objects: 3
Number of elements: 20; Data address: 000002CBFA5B5280
Entering operator+()
int constructor called; number of objects: 4
Number of elements: 30; Data address: 000002CBFA5B0570
temp object:
Leaving operator+()
move constructor called; number of objects: 5
Number of elements: 30; Data address: 000002CBFA5B0570
destructor called; objects left: 4
delete objects:
Number of elements: 0; Data address: 0000000000000000
object one: ##########
object two: ##########
object three: $$$$$$$$$$$$$$$$$$$$
object four: ##########$$$$$$$$$$$$$$$$$$$$
destructor called; objects left: 3
delete objects:
Number of elements: 30; Data address: 000002CBFA5B0570
destructor called; objects left: 2
delete objects:
Number of elements: 20; Data address: 000002CBFA5B5280
destructor called; objects left: 1
delete objects:
Number of elements: 10; Data address: 000002CBFA5B5640
destructor called; objects left: 0
delete objects:
Number of elements: 10; Data address: 000002CBFA5B55A0
```

在C++98中，没有右值引用，下面的语句将调用复制构造函数：

```c++
// 复制构造函数参数是左值引用，左值引用不能指向右值；若实参是右值，const引用形参将指向一个临时变量
// 下面的语句首先执行operator+()有个局部变量temp
// 调用复制构造函数返回一个临时变量对象，然后删除temp
// 创建对象four，调用复制构造函数使用临时对象给four赋值
// 最后删除临时对象
// 而移动构造函数可以直接使用temp变量，省去临时变量对象创建与销毁
// 甚至一些编译器优化，可以直接将temp转到four名下，省去fourd
Useless four(one + three);
```

复制赋值运算符

```c++
Useless& Useless::operator=(const Useless& f)
{
	if (this == &f) {
		return *this;
	}
	delete[] pc;
	n = f.n;
	pc = new char[n];
	for (int i = 0; i < n; i++) {
		pc[i] = f.pc[i];
	}
	return *this;
}
```

移动赋值运算符

```c++
Useless& Useless::operator=(Useless&& f)
{
	if (this == &f) {
		return *this;
	}
	delete[] pc;
	n = f.n;
	pc = f.pc;
	f.n = 0;
	f.pc = nullptr;
	return *this;
}
```

可以使用头文件\<utility>中的std::move()函数强制将一个左值转换为右值，使得可以使用移动语义。

```c++
Useless one(10, '@');
Useless two = one;              // 调用复制构造函数
Useless three = std::move(one); // 调用移动构造函数，调用结束后one将失效
```



### 新的类功能

- C++11新增移动构造函数和移动赋值运算符
  - 没有提供构造函数时，编译器提供默认构造函数，对于内置类型。默认构造函数不进行初始化，类成员对象调用其构造函数
  - 若没有提供复制构造函数或移动构造函数，编译器在代码需要时提供默认复制构造函数和默认移动构造函数
  - 默认复制构造函数，逐成员复制，指针是浅复制，这意味着资源共享，若类具有多态，还会复制虚函数表
  - 默认移动构造函数，逐成员复制，指针是浅复制，并把源对象指针成员值设置为有效但未定义的状态，避免资源重复释放，类成员对象调用其移动构造函数，源对象失效
  - 若提供了析构函数、复制构造函数或复制赋值运算符，编译器不会提供默认移动构造函数和默认移动赋值运算符
  - 若提供了移动构造函数或移动赋值运算符，编译器将不会提供默认复制构造函数和默认赋值运算符

- 默认的方法，适用于类的6个特殊成员函数：默认构造函数、析构函数、复制构造函数、移动构造函数、复制赋值运算符和移动赋值运算符，告诉编译器生成默认的这些成员函数，这些函数仅声明，不能自定义实现

  ```c++
  Useless() = default;
  ~Useless() = default;
  Useless(Uselesee&) = default;
  Useless(Uselesee&&) = default; // 实际测试，默认移动构造函数并不能正确处理指针转让，会多次释放该内存导致错误
  Useless& operator=(const Uselesee&) = default;
  Useless& operator=(Uselesee&&) = default;
  ```

  

- 禁用的方法，适用于类的所有方法，禁用标记的方法，可以将类方法放进private达到同样的效果

  ```c++
  Useless& operator=(const Uselesee&) = delete; // 禁止复制赋值运算符
  ```

  

- 委托构造函数，复用其他构造函数

  ```c++
  Notes::Notes(int kk, double xx, std::string stt) : k(kk), x(xx), st(tt) { ... }
  Notes::Notes() : Notes(0, 0.01, "Oh") { ... } // 此时成员初始化列表其他成员变量初始化不会被执行
  ```

  

- 继承构造函数，派生类可以直接使用基类构造函数来初始化派生类对象，但是使用基类构造函数只能初始化基类成员，派生类成员变量无法初始化

  ```c++
  class Base {
  public:
  	Base();
      Base(int a);
      Base(double b);
      Base(int a, double b);
  };
  
  class Derived : public Base {
  public:
      Derived();
      Derived(int a);
      Derived(double b);
  };
  
  
  Derived d1;          // 调用Derived();
  Derived d2(19.4);    // 调用Derived(double b);
  Derived d3(10, 1.4); // 调用Base(int a, double b);
  ```

  

- 虚方法管理标识符override和final，override用在派生类虚函数参数列表后面，指出该虚函数要覆盖基类的方法；final放在虚函数参数列表后面，指出这是该虚函数最终版本，派生类不能覆盖该虚方法

  ```c++
  class Action
  {
      int a;
  public:
      Action(int i = 0) : a(i) {}
      int val() const { return a; }
      virtual void f(char ch) const { cont << val() << ch << endl; }
  }
  
  class Bingo : public Action
  {
  public:
      Bingo(int i = 0) : Action(i) {}
      virtual void f(char* ch) const { cont << val() << ch << endl; }
  }
  
  Bingo b(10);
  b.f('a'); // 编译出错，因为基类与派生类f()函数特征标并不相同，所以基类f()函数将被隐藏
  
  class Bingo : public Action
  {
  public:
      Bingo(int i = 0) : Action(i) {}
      // 编译出错，因为基类没有与之相同特征标的要覆盖的虚函数
      virtual void f(char* ch) const override { cont << val() << ch << endl; }
  }
  
  class Action
  {
      int a;
  public:
      Action(int i = 0) : a(i) {}
      int val() const { return a; }
      // 派生类将不能重新定义函数f(char ch)
      virtual void f(char ch) const final { cont << val() << ch << endl; }
  }
  ```

  

### Lambda函数

匿名函数，形如：

```c++
[](int x) { return x % 3 == 0; };
```

- [z]将按值访问变量z

- [&z]将按引用访问变量z

- [&, z]将按值访问变量z，按引用访问作用域内其他所有动态变量

- [=, &z]将按引用访问变量z，按值访问作用域内其他所有动态变量

- 可以像函数一样复用

  ```c++
  auto mod3 = [](int x) { return x % 3 == 0; };
  count = std::count_if(vec.begin(), vec.end(), mod3);
  bool result = mod3(x); // 也可以像函数一样直接使用
  ```

  

- 通常情况下，lambda函数语句较简单时，编译器可自动推断出返回值类型

- 当lambda函数较为复杂，返回类型不一时，需要使用返回类型后置声明返回类型

  ```c++
  auto mod3 = [](int x)->int { 
  			x = x + 1; 
  			if (x > 6) {
  				return 5;
  			}
  			else {
  				return x % 3 == 0;
  			}
  		};
  ```

  

### 包装器

也叫适配器adapter，用于给其他编程接口提供更一致或更合适的接口。

```c++
#include <iostream>
using namespace std;

template<typename T,typename F>
T use_f(T v, F f) {
	static int count = 0;
	count++;
	cout << " use_f count = " << count << ", &count = " << &count << endl;
	return f(v);
}

class Fp
{
private:
	double z_;
public:
	Fp(double z = 1.0) :z_(z) {}
	double operator()(double p) { return z_ * p; }
};

class Fq
{
private:
	double z_;
public:
	Fq(double z = 1.0) :z_(z) {}
	double operator()(double q) { return z_ + q; }
};

double dub(double x) { return 2.0 * x; }
double square(double x) { return x * x; }

int main() {
	double y = 1.21;
	cout << "Function pointer dub:\n";
	cout << "  " << use_f(y, dub) << endl;
	cout << "Function pointer square:\n";
	cout << "  " << use_f(y, square) << endl;
	cout << "Function object Fp:\n";
	cout << "  " << use_f(y, Fp(5.0)) << endl;
	cout << "Function object Fq:\n";
	cout << "  " << use_f(y, Fq(5.0)) << endl;
	cout << "Lambda expression 1:\n";
	cout << "  " << use_f(y, [](double u) {return u * u; }) << endl;
	cout << "Lambda expression 2:\n";
	cout << "  " << use_f(y, [](double u) {return u * u / 2.0; }) << endl;
	return 0;
}

// 从count地址可以看出，六次调用共生成五个模板实例化
outputs:
Function pointer dub:
 use_f count = 1, &count = 00007FF7BDD369B8
  2.42
Function pointer square:
 use_f count = 2, &count = 00007FF7BDD369B8
  1.4641
Function object Fp:
 use_f count = 1, &count = 00007FF7BDD369BC
  6.05
Function object Fq:
 use_f count = 1, &count = 00007FF7BDD369C0
  6.21
Lambda expression 1:
 use_f count = 1, &count = 00007FF7BDD369C4
  1.4641
Lambda expression 2:
 use_f count = 1, &count = 00007FF7BDD369C8
  0.73205
```

鉴于上述问题，模板被多次实例化，降低了模板效率，实际上这六次调用有个共同点就是参数列表和返回类型相同，即调用特征标相同。头文件\<functional>中提供了模板function，可用于包装调用特征标相同的函数指针、函数对象和lambda表达式，让模板只实例化一次。

```c++
#include <iostream>
#include <functional>
using namespace std;

template<typename T,typename F>
T use_f(T v, F f) {
	static int count = 0;
	count++;
	cout << " use_f count = " << count << ", &count = " << &count << endl;
	return f(v);
}

class Fp
{
private:
	double z_;
public:
	Fp(double z = 1.0) :z_(z) {}
	double operator()(double p) { return z_ * p; }
};

class Fq
{
private:
	double z_;
public:
	Fq(double z = 1.0) :z_(z) {}
	double operator()(double q) { return z_ + q; }
};

double dub(double x) { return 2.0 * x; }
double square(double x) { return x * x; }

int main() {
	double y = 1.21;
	function<double(double)> ef1 = dub;
	function<double(double)> ef2 = square;
	function<double(double)> ef3 = Fp(5.0);
	function<double(double)> ef4 = Fq(5.0);
	function<double(double)> ef5 = [](double u) {return u * u; };
	function<double(double)> ef6 = [](double u) {return u * u / 2.0; };
	cout << "Function pointer dub:\n";
	cout << "  " << use_f(y, ef1) << endl;
	cout << "Function pointer square:\n";
	cout << "  " << use_f(y, ef2) << endl;
	cout << "Function object Fp:\n";
	cout << "  " << use_f(y, ef3) << endl;
	cout << "Function object Fq:\n";
	cout << "  " << use_f(y, ef4) << endl;
	cout << "Lambda expression 1:\n";
	cout << "  " << use_f(y, ef5) << endl;
	cout << "Lambda expression 2:\n";
	cout << "  " << use_f(y, ef6) << endl;
	return 0;
}

outputs:
Function pointer dub:
 use_f count = 1, &count = 00007FF77500CC68
  2.42
Function pointer square:
 use_f count = 2, &count = 00007FF77500CC68
  1.4641
Function object Fp:
 use_f count = 3, &count = 00007FF77500CC68
  6.05
Function object Fq:
 use_f count = 4, &count = 00007FF77500CC68
  6.21
Lambda expression 1:
 use_f count = 5, &count = 00007FF77500CC68
  1.4641
Lambda expression 2:
 use_f count = 6, &count = 00007FF77500CC68
  0.73205
```

- 可以不用多次声明同一个function，可以使用一个function对象

  ```c++
  // typedef function<double(double)> ef;
  using ef = function<double(double)>;
  
  cout << "Function pointer dub:\n";
  cout << "  " << use_f(y, ef(dub)) << endl;
  cout << "Function pointer square:\n";
  cout << "  " << use_f(y, ef(square)) << endl;
  cout << "Function object Fp:\n";
  cout << "  " << use_f(y, ef(Fp(5.0))) << endl;
  cout << "Function object Fq:\n";
  cout << "  " << use_f(y, ef(Fq(5.0))) << endl;
  cout << "Lambda expression 1:\n";
  cout << "  " << use_f(y, ef([](double u) {return u * u; })) << endl;
  cout << "Lambda expression 2:\n";
  cout << "  " << use_f(y, ef([](double u) {return u * u / 2.0; })) << endl;
  ```

  

- 可以修改模板参数为function包装器对象达到同样效果

  ```c++
  #include <iostream>
  #include <functional>
  using namespace std;
  
  template<typename T>
  T use_f(T v, function<T(T)> f) {
  	static int count = 0;
  	count++;
  	cout << " use_f count = " << count << ", &count = " << &count << endl;
  	return f(v);
  }
  
  class Fp
  {
  private:
  	double z_;
  public:
  	Fp(double z = 1.0) :z_(z) {}
  	double operator()(double p) { return z_ * p; }
  };
  
  class Fq
  {
  private:
  	double z_;
  public:
  	Fq(double z = 1.0) :z_(z) {}
  	double operator()(double q) { return z_ + q; }
  };
  
  double dub(double x) { return 2.0 * x; }
  double square(double x) { return x * x; }
  
  // 参数dub、Fp(5.0)等本身类型并不是function<double(double)>，所以需要指出use_f的具体化类型
  // dub类型为double(double x)
  // Fp(5.0)类型为Fp
  // [](double u) {return u * u; }类型为lambda [](double u)->double
  int main() {
  	double y = 1.21;
  	cout << "Function pointer dub:\n";
  	cout << "  " << use_f<double>(y, dub) << endl;
  	cout << "Function pointer square:\n";
  	cout << "  " << use_f<double>(y, square) << endl;
  	cout << "Function object Fp:\n";
  	cout << "  " << use_f<double>(y, Fp(5.0)) << endl;
  	cout << "Function object Fq:\n";
  	cout << "  " << use_f<double>(y, Fq(5.0)) << endl;
  	cout << "Lambda expression 1:\n";
  	cout << "  " << use_f<double>(y, [](double u) {return u * u; }) << endl;
  	cout << "Lambda expression 2:\n";
  	cout << "  " << use_f<double>(y, [](double u) {return u * u / 2.0; }) << endl;
  	return 0;
  }
  ```

  

### 可变参数模板

能创建接受可变数量参数的模板函数和模板类，具体做到以下几点：

- 模板参数包
- 函数参数包
- 展开参数包
- 递归

```c++
#include <iostream>
using namespace std;

void show_list() {}

template<typename T>
void show_list(const T& value) {
	cout << value << '\n';
}

template<typename T, typename... Args>
void show_list(const T& value, const Args&... args) {
	cout << value << ", ";
	show_list(args...);
}

int main() {
	int n = 10;
	double x = 3.14;
	string str = "hello world";
	show_list(n, x);
	show_list(x * x, '!', 8, str);
	return 0;
}

outputs:
10, 3.14
9.8596, !, 8, hello world
```

C++17可以使用折叠表达式处理参数包

```c++
template<typename... Args>
void show_list_all(const Args&... args) {
	(cout << ... << args) << endl; // 右折叠表达式
}

int sum = (args + ... + 0); // 左折叠表达式
```



## 备注

- c++ 赋值运算符 = 返回左值的引用

- 逗号运算符返回最后一个表达式的结果
- const左值引用参数可以接受左值和右值（指向右值的临时拷贝），非const左值引用参数只接受左值，右值引用参数只接受右值



