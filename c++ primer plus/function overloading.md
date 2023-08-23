# 局部对象
局部变量的生命周期依赖于定义的方式。
## 自动对象
定义：创建于变量定义，销毁于定义所在块末尾。
例如：函数的形参。
创建分为两种情况：
1. 定义变量并初始化。
2. 定义变量未初始化，执行默认初始化，内置类型未初始化的局部变量将会被默认初始化为未定义值。
```c++
int a; //a将被初始化为任意值。
```
## 局部静态变量
定义：创建于变量定义，销毁于程序终止。
创建分为两种情况，同上，但是第二种情况下默认初始化为0。
```c++
size_t count_calls(){
    static size_t ctr =0;
    return ++ctr;
}
int main(){
    for(size_t i =0;i!=10; ++i){
        cout<<cout_calls()<<endl;
    }
    return 0;
}
```
其中 变量ctr定义为静态局部变量，因此，在每次调用结束后，ctr仍然有效，就可以实现逐步增加。

# 分离式编译
## 函数的声明
将函数的声明放在头文件中，但是函数的定义不能放在头文件中。
因为如果把定义放在头文件中，当有多个带有头文件的.cc程序预处理时，会打开头文件中的内容，会出现函数被多次定义的行为，导致报错。
## 分离式编译
如上所述，函数的声明放在头文件，为了保证main函数所在.cc文件的整洁性，将函数的定义在不同.cc文件中，那么在程序编译时，需要分别编译多个.cc文件，将编译后的.o文件链接起来生成可执行程序文件(.exe)
```c++
$ g++ add.cc main.cc  //编译、链接后产生可执行程序文件 a.out
$ g++ add.cc main.cc -o main //编译、链接后产生可执行程序文件 main
```
完整的编译链接过程
```c++
$ g++ -c add.cc //编译生成add.o
$ g++ -c main.cc //编译生成main.o
$ g++ add.o main.o -o main //链接生成main可执行程序文件。
```
# 参数传递
## const形参和实参
当用实参初始化形参时会忽略掉顶层的const。
```c++
void fcn(const int i){}; //在fcn内对i只读不可写。但是实参传入时，const和非const均可。
```
同时，因为忽略顶层的const，那么函数形参const和非const是被视为相同的形参列表。
```c++
void fcn(const int i){};
void fcn(int i){};
//上面两个函数的定义是一样的，因此第二个函数会定义失败(重定义)。
```
常量引用对象既可以用常量初始化，也可以用非常量初始化。
但是，非常量引用对象只能用非常量初始化。
```c++
int i=0;
const int &j=i; //正确
int &k=j; //错误
void func(const int &i){};
func(i); //正确
void func2(int &i){};
func2(j); //错误
```
## 数组参数的传递
当数组作为函数的形参时，一般来说，在传递实参给函数时，形参要是指针的表示形式。除此以外，还可以是引用的形式。
```c++
void f(int *arr){}; //形参是指针，指向传入的数组的第一个元素。
void f(int (&arr)[10]); //形参是数组的引用，将数组名绑定在传入的数组名上，本质上还是令arr指向传入数组的第一个元素。
```
## main函数的参数传递
```c++
int main(int argc,char *argv[]){}; //argc表示命令行中不含空格的字符串的个数。
//argv是一个二维数组，保存命令行的内容，每一行表示一个字符串。
```
## 含有可变形参的函数
### initializer_list形参
**使用前提：实参数量未知，类型相同。**
```c++
initializer_list<int> lst={1,2,3,4,5};
initializer_list<int> lst2=lst;
cout<<lst.begin()<<endl;    //0x5571f055a2b0
cout<<lst2.begin()<<endl;   //0x5571f055a2b0
```
如上所示：虽然执行拷贝操作，但是不会拷贝列表中的元素，且元素类型是const。
## 列表初始化返回值
返回值必须是一种标准库类型的容器，如vector
```c++
vector<int> print(int a,int b){
    return {a,b};
}
```
使用 echo $?打印main函数的返回值到屏幕。

# 函数重载
参数列表、函数名相同，返回类型不同，不构成重载。
## 顶层const和底层const
const的顶层与底层是针对所修饰的对象而言的
```c++
int *const p;   //const修饰指针p本身，也就是指针指向的位置不变，这里是顶层const。
const int p;    //顶层const。
const int *p;   //const修饰指针p所指的对象，也就是指针所指对象的内容不变，这里是底层const。
```
顶层const可以修饰任意数据类型，而底层const修饰是有限制的，如指针和引用。
简单来说，一般数据类型(int、char...)只有顶层const的修饰形式，而指针和引用类型有顶层和底层const两种修饰形式。
## 重载和const形参
对于重载来说，在函数的形参列表中，当使用顶层const修饰变量时，与无const修饰的变量不构成重载；而当使用底层const修饰变量时，与无const修饰的变量构成重载。
```c++
int func(const string&);    //底层const
int func(string & const);   //顶层const
```
# 默认实参
函数参数列表中，默认实参的声明之后，右侧所有形参必须带有默认值，因此要倾向于把经常使用默认值的形参放在右侧，把不使用默认值的形参放在左侧。
在函数调用时，传入实参的顺序是从左侧到右的，所以要求把不使用默认值的形参放在左侧。

在函数调用之前，如果改变默认实参的值，则函数调用中的默认实参就改变，但是如果是在内层作用域中定义一个与默认实参名字相同的变量并赋值，不改变函数调用中的默认实参的值。
```c++
int i=0;
char c=',';
void func(int j=i,char x=c);    //声明。
func();     //调用func(0,',');
void f2(){
    c='#';
    int i=1;
    func();     //调用func(0,'#'); 因为此作用域中的i和函数列表中实参i不是同一个变量，所以函数调用时，i不变。
}
```
# 内联函数和constexpr函数
## 内联函数
与普通函数的区别，在返回值之前添加inline。
内联函数的优点：
将函数体替代函数调用从而插入调用函数的位置，节省函数调用的时间开销。
```c++
inline const string & shorterString(const string &s1, const string &s2){
    return s1.size()<=s2.size()?s1:s2;
}
//此例中是将return 后的语句插入到调用函数的位置。
```
## constexpr函数
当使用constexpr修饰函数调用的返回值时，有两种情况：
1. 当变量名置为常量时，那么在编译时便可以直接得到得到函数调用的值，不需要等到函数运行时再得出结果。
2. 当变量名是变量时，那么编译时不能得到函数调用的值，需要等到函数运行时得出结果。
```c++
constexpr int func(int i){
    return i;
}
int main(){
    int i=2;
    int arr[func(i)];   //constexpr没有起到作用，和普通函数一样。
    int arr[func(2)];   //func()在编译时就解析出结果。
}
```
相比于普通函数重定义报错的问题，内联函数和constexpr函数可以进行重定义而不会报错，同时为了在函数预处理头文件时直接解析出函数体，可以将函数的定义在头文件中。如果只将内联函数的声明放在头文件中，那么内联就不起作用了。
需要注意的是，
内联函数和constexpr函数的定义必须分别相同。
## assert 预处理宏
定义在cassert头文件中
```c++
assert(expr);   //expr表达式为真，什么都不做。expr表达式为假时，报错终止程序运行。
```
NDEBUG是预处理变量
如果在main文件开头定义了NDEBUG
```c++
#define NDEBUG
```
则再下列情况中assert会有不同的执行情况。
```c++
int main(){
    #ifndef NDEBUG
    assert(0);  //assert不执行，程序不会中断运行。
    #endif  
}
```
当main文件中没有定义NDEBUG时，上述程序就会再assert(0)处停止运行。
# 函数指针
```c++
int (*p)(int i,int j);
p=q;    //p和q的返回值类型和参数类型数量必须相同。
p=&q;   //与上一行等价。
cout<<(*p)(1,2)<<endl;
cout<<p(1,2)<<endl; //上一行与此行执行效果一致。
```
## 重载函数的指针
```c++
void ff(int *);
void ff(int);
void (*pf)(int)=ff; //pf指向ff(int)
void (*pf)(int *)=ff;   //pf指向ff(int *)
void (*pf)(char)=ff;    //报错，没有与ff的参数列表匹配
int (*pf)(int)=ff;  //报错，没有与ff的返回值匹配
```
## 函数指针作为形参
```c++
void useBigger(const string &s1,const string &s2,bool pf(const string &,const string &));   //函数名pf被自动转换为函数指针
void useBigger(const string &s1,const string &s2,bool (*pf)(const string &,const string &));    //显式定义函数指针
```
## 类型别名简化函数
```c++
using F=int(int,int);   //F是函数名
using FF=int(*)(int,int);   //FF是函数指针
```
一般简化的函数是已经声明和定义过的函数，从而方便函数指针的初始化
```c++
int q(int i,int j){
    return i+j;
}
using F=int(int,int);
F *f=q; //此处必须加*，因为f返回函数指针，F是函数类型，不是函数指针类型

using F=int(*)(int,int);
F f=q;  //此处不加*，因为F是函数指针类型，f也是返回函数指针
```
除了使用using简化函数，还可以使用decltype
```c++
int ufn(int,int);
decltype(ufn) *f;   //decltype(ufn)表示ufn这个函数类型，这里是定义一个函数指针f，指向ufn的函数类型的函数。
```
