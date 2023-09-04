# 类类型
this是一个指向类类型的常量指针
```c++
class dictionary{
public:
    std::string isbn() const
    {
        return this->bookno;
    }   //此处的this的类型是const dictionary *const
    double avg_price() const;
private:
    string bookno;
};
//一般来说，dictionary类型的this指针类型为dictionary *const
```
## 在类的外部定义成员函数
```c++
double dictionary::avg_price() const{
    
}
```
上述函数虽然声明在类的内部，定义在类的外部，但可以调用类的成员。
# 构造函数
特殊的成员函数控制对象的初始化过程
特点：
1. 函数名就是类名
2. 无返回类型
## 默认构造函数
1. 只有当类没有声明任何构造函数时，编译器才会自动生成默认构造函数。
而当类内声明了至少一种构造函数时，就不会再自动生成默认构造函数。
2. 一般来说，当类内含有内置类型(int)或复合类型(数组)的成员变量时，应该定义一个新的构造函数，来将这些变量初始化。否则，当运行自动的默认初始化时，成员变量将得到未定义的值。
3. 当类中含有其他类类型的成员且此成员无默认构造函数时，编译器无法自动默认初始化，必须定义一个新的构造函数。
## 类的定义
**使用class和或struct关键字定义类类型。**
二者的区别：
两者的默认访问权限不同，struct的默认访问权限是public，class的默认访问权限是private。
即：在不显式写明访问说明符时，struct类中所有的成员的访问权限均是public(类外可以直接访问类内的成员)，而class类中的访问权限均是private(类外无法访问类内的成员)。
## 封装性的益处
1. 确保用户代码不会无意间破坏封装对象的状态。
2. 被封装的类的具体实现细节可以随时改变，无需调整用户级代码。
   
## 友元
对于需要访问class类内private成员的非类成员函数，由于class的封装性，直接访问一定是无法实现。此时，需要将非类成员函数转化为类的友元函数，就可以访问类内的private成员。
直接在函数声明前加上friend，且函数的声明必须在类内，但是具体位置不限，不受访问说明符的约束。
```c++
class Person{
friend istream &read(istream &is);
public:
    Person(){}
private:
    string name;
    string address;
};
istream &read(istream &is){
    is>>name>>address;
    return is;
}
```
在类内部定义友元函数，则该函数被隐式定义为inline函数
友元的弊端：破坏了封装性。
## 可变数据成员
当成员函数是const类型时，函数体内不可以修改普通的private数据成员，但是可以修改可变(mutable)的private数据成员。
```c++
class Screen{
public:
    void some_member() const{
        ++access_ctr;   //此处不会报错。
        ++access;   //此处会报错。
    }
private:
    mutable size_t access_ctr;
    size_t access;
};
```
管理动态内存的类不能依赖拷贝和赋值操作的默认版本，因此，一般要使用string和vector来避免动态管理内存的复杂性。

# const与函数重载
普通成员函数和const成员函数构成重载。

const对象只能引用const类型成员函数。
而非const对象既可以引用const类型成员函数，也可以引用const类型成员函数，但是优先引用非const类型。
```c++
Screen &display(ostream &os){
        os<<contents;
        cout<<"Screen &display(ostream &os)"<<endl;
    }
Screen &display(ostream &os) const{
    os<<contents;
    cout<<"const Screen &display(ostream &os) const"<<endl;
}
Screen myscreen(5,3,'c');
const Screen creen(5,3,'n');
myscreen.display(cout); //调用非const类型的display，当类内没有非const类型的display时，不会报错，会调用const类型的display。
creen.display(cout);    //调用const类型的display，当类内没有声明const类型的display时，会报错。
```
## 类的声明与定义
类可以只声明不定义，在这种情况下，类的类型是一种不完全类型，可以在声明之后定义类的指针和引用，但不能定义类的对象。

```c++
class person{
    person p;   //报错，因为此时类还没有定义完全，编译器不知道为对象p分配多少储存空间。
    person &q;  //不报错，因为此时类已经声明过了，可以定义引用和指针。
    person *r;  //同上。
};
```
友元函数定义在类的内部，如果类中的成员函数想要调用友元函数，必须先声明。
```c++
class person{
    friend void f(){
        cout<<"f()"<<endl;
    }
public:
    void g(){
        f();    //报错，因为没有声明f()，无论是在类定义之前还是定义之后声明友元函数f()，都可以不报错。
    }
};
```
## 用于类成员声明的名字查找
对类的成员进行声明时
1. 对于成员函数，要查找函数的返回类型和参数列表，先在类的作用域内、函数声明之前这一个范围内查找，如果没有匹配，则到类之前的外层作用域中查找，查找到后进行匹配。
2. 对于数据成员，要查找成员的类型，查找方法同上。
   
其中，成员函数体内的内容会在整个类的定义都通过编译之后，再去编译，因此会出现以下情况。
```c++
typedef double Money;
string bal;
class Account{
public:
    Money balance(){
        return bal;     //这里的bal是Money类型，因为这个语句是在整个类定义完后最后编译的，即在Money bal之后编译。
    }
private:
    Money bal;  //因为bal在内层作用域，与外层域的string bal无关，且此时Money已经经过编译，因此bal的类型是Money。
};
```
## explicit关键字
加了explicit的默认构造函数不能进行参数的隐式转换
且如果在类的外部定义默认构造函数，那么加了explicit会报错。
```c++
class A{
public:
    A(string &s):bookno(s){}
    explicit A(string &s):bookno(s){}   //这里加explicit后，combine(string("hhh"))和combine(A("hhh"))都会报错。只有combine(A(string("hhh")))不会i报错。
private:
    string bookno;
}
void combine(A a){}
combine("hhh"); //报错，因为隐式转换只能经过一步，这里有两步隐式转换。
combine(string("hhh")); //只有一步隐式转换
combine(A("hhh"));  //只有一步隐式转换。
explicit A(string &s):bookno(s){}   //报错
```
## 类的静态成员
类的静态成员不属于类声明的对象，属于类本身。
因此当类声明多个对象时，每个对象拥有各自的成员，但不包含静态成员。
```c++
class A{
public:
    void display();
    static void add();
}
```
上面的代码中add()属于静态成员，因此当声明了两个对象时，每个对象都有display，但是add只有一个。

静态成员函数的调用和普通成员函数相同，并且多出一种调用方式：直接使用类名::函数名()调用。

静态成员函数的定义，类的外部和内部均可，但是在外部定义时不能带着static。
