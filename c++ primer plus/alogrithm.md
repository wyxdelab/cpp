# 只读算法
accumulate(求和)、find(查找)、count(计数)、equal(判断两个序列是否有连续相同的元素)
# 写算法
fill(对给定区间序列初始化)、fill_n(给定起始点和写入长度来初始化)、copy(将一个序列的元素复制到另一个序列)
以上的写算法不会关心写的范围是否超出限制，因为上述写算法起作用的前提是容器有空间，当没有空间时，继续写入会报错，而不会扩充空间。
**因此，这里引入尾部插入函数，back_inserter()**
```c++
vector<int> vec;
auto it=back_inserter(vec); //it是vec的插入迭代器
*it=2;  //这里将元素2插入到vec中，实际上是调用了push_beck()，此时it就自动指向了下一位
*it=3;  
```
可将back_inserter()用于上述写算法的起始位置，此时写算法便可以自动扩充空间。

# 重排算法
sort()，使得元素有序，当有相同元素时，相同元素相邻，此时，使用unique()使得元素唯一，但实际上多余的元素没有删除，而unique()返回有序元素的尾部的后一位，也就是多余元素的第一位，再用容器的成员函数erase()删除多余元素

# 向算法传递函数，来修改函数的功能

sort()和stable_sort()都是排序算法，但是sort()会有可能两个排序条件相同的元素在排序前后的顺序颠倒，不稳定，stable_sort()就是解决了这个问题。
如：序列整体按照长度排序，相同长度的元素再按照字典顺序排序，此时，需要先整体按字典排序，然后再整体按长度排序。
但是使用sort()，有可能导致在按长度排序时，字典序列的颠倒
使用stable_sort可以解决这个问题

上述问题中，两个算法默认是按照字典顺序排序，那么需要在参数列表中添加一个谓词，实际上是一个函数，用来修改算法按照长度排序。
```c++
bool isshorter(const string &s1,const string &s2){
    return s1.size()<s2.size();
}
void sort_string_by_size(){
    int val=0;
    vector<string> vec;
    elimDups(vec);
    stable_sort(vec.begin(),vec.end(),isshorter);
    for(auto c:vec){
        cout<<c<<" ";
    }
    cout<<endl;
}//isshorter就是二元谓词(二元就是接收两个参数)
```

## 分割算法
partition、stable_partition
区别在于stable_partition不会改变分割后一侧的元素的原始的顺序

# lambda表达式
因为谓词的只有一元和二元，且有些标准库算法只支持一元谓词，那么当需要传入多个参数时就出现问题
lambda表达式用来解决这个问题，虽然其参数列表有和谓词的参数列表同样的限制，但是其捕获列表没有限制，因此可将多余的参数加入捕获列表
```c++
[capture list](parameter list) ->return type {function body};    //这就是lambda表达式
auto f=[](const string &s1,const string &s2){
    return s1.size()<s2.size();
};  这里定义了lambda表达式
f("hi","hello");    //调用了lambda
stable_sort(vec.begin(),vec.end(),f);   //将上面的算法中的谓词用lambda表达式代替，同样的效果
```

其中捕获列表用来传入lambda表达式所处函数中的局部变量，
```c++
string::size_type sz=5;
auto f=[sz](const string &s1){
    return s1.size()>=sz;
};  
vector<string> vec{"hi","hello","yes","liming"};
auto wc=find_if(vec.begin(),vec.end(),f)    
```
find_if()函数的参数列表中的谓词只能接收一个参数，所以需要捕获列表来获取其余参数。
一般来说，参数列表中是容器中的元素类型，捕获列表中的参数类型是带有lambda表达式的标准库算法所在函数中的局部变量类型

## 隐式捕获
实际就是捕获列表中没有显式指出参数名称，如果要值捕获，传入=，如果要引用捕获，传入&。

## 混合捕获
捕获列表中，隐式捕获放在前，显式捕获在后
```c++
[]
[names]
[&]
[=]
[&,list]    list中不能有引用捕获
[=,list]    list中不能有值捕获

```
对于值捕获来说，lambda表达式内不能改变捕获的参数，因为是右值，如果在参数列表后加上mutable，就可以改变值捕获的参数，因为mutable本身具有的特性是能够在元素不可修改的情况下，用mutable声明后可以修改。
```c++
int v1=42;
auto f=[v1]()mutable{return ++v1;};
v1=0;
cout<<f()<<endl;    //打印43，而不打印1，是因为lambda表达式捕获了可以修改的v1，但依旧是值捕获，相当于v1的拷贝，因此即使v1=0，也不会影响lambda表达是的输出

int v1=42;
auto f=[&v1](){return ++v1;};
v1=0;
cout<<f()<<endl;    //打印1，而不是打印43，是因为lambda捕获了v1的引用，属于引用捕获，因此当v1=0，会改变lambda表达式的输出
```

# for_each 算法表达式
```c++
vector<int> vec{1,2,3,4};
auto f=[](int i){cout<<i<<" ";};    //lambda表达式
void f1(int i){cout<<i<<" ";}       //函数的定义
for_each(vec.begin(),vec.end(),f);  //将f替换成f1有同样的小效果
```

