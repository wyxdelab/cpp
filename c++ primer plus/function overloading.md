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