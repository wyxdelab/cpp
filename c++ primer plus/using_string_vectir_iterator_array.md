# 命名空间的using声明

using namespace::name;
namespace:命名空间
name:对象名
eg:
```c++
using std::cout; //此时只能使用std中的cout对象
```
需要使用一个命名空间中的多个函数名时，可只声明命名空间。
eg:
```c++
using namespace std; //此时可以使用std中的所有对象。
```

如果没有在main函数之前声明命名空间，则在main中调用函数时，需要再加上命名空间。
eg:
```c++
std::cout<<"hello"<<std::endl;
```
# 标准库类型 string
string 定义在命名空间std中，且string类型需包含string头文件。
```c++
#include <string>
using std::string; //此形式只包含std中的string
using namespace std; //此形式包含整个std命名空间
```
## 定义和初始化
初始化的几种常见类型
```c++
string s1; //s1是一个空字符串,s1.length()=0。直接初始化。
string s2 = s1; //s2是s1的副本。拷贝初始化。
string s3 = "hiya"; //s3是字符串字面值的副本。拷贝初始化。实际上是先初始化一个"hiya"的临时对象，再将其拷贝给s3。
string s4(10, 'c'); //s4的内容是10个c。直接初始化。
```
## string对象的操作
### 读写
```c++
int main(){
    string s1;
    cin >> s1;
    cout << s1 << endl;
    //输入hello world，输出hello，因为在读取操作时，string对象会忽略开头的空格，读取到空格为止。
    
    string s2="hello world";
    cout << s2 << endl;
    //输出hello world，这里之所以没有忽略world，是因为字符串在最开始拷贝给s2，没有读取操作。

    string line;
    while (getline(cin,line)){
        cout << line << endl;
    }
    //按行读取，直到遇见换行符为止（换行符被读取），赋给line（line不保存换行符）。
}
```
## string::size_type 类型
string标准库定义的类型，体现与机器无关的特性。
```c++
string::size_type len=line.size();
```
尽量避免使用int类型来定义变量。
## string对象间的操作
比较：s1 < s2，按字符串中每个字母的顺序比较，靠前的字母小。
相加：s1=s1+s2
```c++
string s2=s1+","+"world"; //正确
string s4=","+"world"+s2; //错误，因为两个字符串常量不能直接相加。
```
### 对string对象中的字符处理
1. 直接使用下标运算符
```c++
string s1="world";
cout << s1[0] <<endl;
//输出w
```
2. 处理每一个字符
```c++
for (auto c : line){
    cout << c << endl;
}
//每一次循环输出一个字符，c取line中的字符。
```
如果改变字符串中的字符：需要加引用
```c++
for (auto &c : line){
    c=toupper(c)
}
//将每个字符变为大写。
```

# 标准库类型 vector
vector 定义在命名空间std中，且vector类型需包含vector头文件。
```c++
#include <vector>
using std::vector; //此形式只包含std中的vector
using namespace std; //此形式包含整个std命名空间
```
vector是类模板。
## 定义和初始化
```c++
vector<int> v1; //vector对象中的元素是int类型，v1是空的，v1.size()=0
vector<int> v2(v1); //v2是v1的副本
vector<int> v3(10,1); //v3初始化为10个1
vector<int> v3(10); //v3初始化为10个0
vector<int> v4{10,1}; //v4初始化为10和1
```
## 添加元素
利用push_back添加元素，不需要考虑vector的大小，vector的大小会随着元素增多动态增加，且增加的方式是每次多一倍(vector.capacity())
```c++
vector<int> v2;
v2.push_back(1); //向v2中添加元素1。
```
不能用下标运算符添加元素(但编译器不会报错)，但可以使用下标运算符访问和修改元素。
# 迭代器 iterator
所有标准库容器都支持迭代器，比如vector。
string虽然不是容器，但是也支持迭代器。
迭代器类似于指针类型，指向某一个元素或者尾元素的下一个位置。
string和标准库容器有一类成员函数，返回迭代器类型。
```c++
vector<int> v={1,2,3,4};
*(v.begin()); //元素1，因为返回值类似于指针，所以获取值就需要解引用。
v.end(); //指向4后面一个位置
```
## 迭代器类型
对于vector<int> v来说
其迭代器类型为
```c++
vector<int>::iterator it; //it的类型就是v的迭代器的类型
vector<int>::const_iterator it; //it只能读元素，不能写元素
v.cbegin(); //返回const_iterator it
v.cend();
```
## 迭代器的失效问题
vector的push_back操作会导致原来的迭代器失效。
```C++
vector<int> v1={1,2,3,4,5,6};
auto bg=v1.begin();
auto ed=v1.end();
v1.push_back(1);
for (auto iter=bg;iter!=ed;iter++){
    cout<<(*iter);
}
cout<<endl;
//输出的结果是没有意义的数字。
```
在**范围for循环**中向vector对象添加元素也会导致迭代器失效，因为在范围for循环中用到迭代器，采用**push_back**添加元素会导致迭代器失效，进而导致for循环出现问题。
## 迭代器运算
可以进行加减运算，自增自减运算。
# 数组
数组的大小是固定的。
```c++
int arr1[5]; //数组长度为5，初始化值不为0，随机。
int arr1[5]={1,2}; //此时初始化后三位为0。
int n=5;
int arr2[n]; //和int arr1[5];效果一样
```
定义数组时不能使用auto
```c++
auto arr[10]={1,2}; //错误
```
可以忽略数组下标中的值不写。
```c++
int arr[]={1,2,3}
```
## 字符数组的特性
```c++
char arr[5]="hello"; //错误的，因为hello后面还要保存一个空字符\0，共6个字符。
```
## 数组不允许拷贝和赋值
```c++
int a[]={1,2,3};
int a2[]=a; //错误
a2=a; //错误
```
## 数组的复杂声明
```c++
int *pstr[10]; //含有10个整型指针的数组pstr。
int (*pstr)[10]; //pstr作为指针指向含有10个整数的数组。
int (&pstr)[10]; //pstr作为引用，引用一个含有10个整数的数组。
```
## begin,end函数
两个函数定义在iterator头文件中。
```c++
#include<iterator>
int arr[]={1,2,3,4};
int *be=begin(arr); //指向1
int *ed=end(arr); //指向4后面一个位置。
```
## string对象和c风格字符串的转换
可以将string对象转换为c风格字符串
```c++
string s("hello");
char *str=s.c_str(); //c_str返回一个指针变量
s="world"; //当string对象改变时，返回的指针指向的值也会改变。
```
## 使用数组初始化vector
```c++
int arr[]={1,2,3,4,5};
vector<int> v(begin(arr),end(arr)); //可用于将数组拷贝至vector
```
## 类型别名简化数组
```c++
using int_array int[4]; //sizeof(int_array)=16,int_array代表整个数组
int arr[]={1,2};  //sizeof(arr)=8，arr代表一个指针
```
# 左值和右值
**左值：对象的值(内容)**
**右值：对象的身份(在内存中的位置)**
**左值可以代替右值，右值不可以代替左值。**
凡是涉及到内存的操作，均需要用到左值。
如：取地址符、下标运算符、解引用符、迭代器递增/减。
## decltype关键字对于左值和右值的不同
```c++
int *p;
int q;
decltype(*p); //返回值为int&，*p可以看作左值。
decltype(q); //返回值为int，q可以看作右值。
```
# 强制类型转换
**强制类型转换符号后的表达式一定要带括号。**
## static_cast
用于非const类型的强制转换，一般是大类型向小类型转换。
```c++
double slope = static_cast<double> (j)/i;
```
## const_cast
用于const类型的强制转换。const->非const
```c++
const char *pc;
char *p = const_cast<char *> (pc); //正确
const_cast<string> (pc); //错误，因为const_cast只能改变const，而不能改变类型。
```
# try语句块和异常处理
## throw表达式
```c++
throw runtime_error("Data must refer to same ISBN"); //抛出runtime_error异常。暂停程序运行。
```
## try语句块
```c++
try{
    program-statment
    throw runtime_error()  //程序代码执行失败，throw抛出异常。
}   catch(runtime_error error //异常类型的声明){
    //对异常情况进行处理。
}
```
