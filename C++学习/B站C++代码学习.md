# 基础语法
#### 部分用法
###### 随机数：
```C++
#include <ctime>
srand((unsigned int)time(NULL));
```
###### 清屏退出
```C++
system("pause");
system("cls");
```

#### 引用
引用的主要用法旨在不同的位置使用相同的变量
```
int i = 10;
int &niubi = i;
niubi = 20;//i = 20
```
- 引用完以后不会改变，再次等于是赋值
- 应用必须初始化
- 子程序引用的时候，形参会修饰实参
```C++
#include <iostream>
struct Player {
    int level = 1;
};
// 注意这里的 &，表示返回的是引用
int& getLevel(Player& p) {
    return p.level; 
}
int main() {
    Player myHero;
    // 震撼的操作来了：函数调用居然在等号左边！
    getLevel(myHero) = 50; 
    std::cout << "英雄等级变成了: " << myHero.level; // 输出 50
    return 0;
}
```
- 如果函数的返回是一个引用，可以作为一个左值

#### 数组

```C++
int arr[10] = {1,2,3,4,5,6};//其他的是0
int arr1[] = {1,2,3};//自动推导数组长度
char arr2[3] = "hel";//错误，\0占一个位置
int arr4[5] = {0};
int arr5[3][2] = {1,2,3,4,5,6};
//parell
int arr6[3][2] = {{1,2},{3,4},{5,6}};
int arr7[][2]  = {1,2,3,4,5,6};
cout<<"二维数组首地址"<<(int)arr<<endl;
cout<<"二维数组第一个数字首地址"<<(int)&arr[0][0]<<endl;
```

#### 命名空间
 命名空间旨在出现多个同名函数的情况下，有自己独占的位置来进行避免重复
```C++
 namespace Outer {
     int x = 10;
     int y = 20;
     namespace Inner {
         int x = 100; // 遮蔽了外层的 
         x void print() { // 1. 直接访问：优先找当前空间 
             int a = x; // a 是 100 
             // 2. 访问外层：如果内层没有同名变量，直接用 
             int b = y; // b 是 20 (直接拿到了 Outer::y) 
             // 3. 显式访问：如果被遮蔽了，用 :: 强制指定 
             int c = Outer::x; // c 是 10 
         }
     }
 }
```

#### 类和对象
封装，继承，多态
##### 封装
设置一个典型的类，来进行封装，对外部方便调用
```C++
class Stu {
    private: 
    int age;
    int id;
    public: 
    char name[20];
    //对于private变量，会使用get和set来进行数据的定义
    void setage(int a) {
        age = a;
    }
    void playniu() {
        cout << "我要打篮球" << age << name << endl;
    }
}
int main() {
    Stu xu;//对象（实例化）
    strcpy(xu.name, "niu");
    xu.setage(20);
    xu.playniu("xuhansong");
}
```
###### 对象的初始化和清理
1.构造函数进行初始化
```C++
class person {
    public: person() {
        cout << "gouzaohanshu diaoyong" << endl;
    }
}
```
- 函数名和类名相同
- 没有返回值，不写void
- 构造函数可以有参数，可以发生重载
- 创建对象，构造函数会自动调用
- 实例化自动运行
**分类：有参无参，普通拷贝
```C++
class person {
    public: int age;
    person(int a) { //有参
        age = a;
        cout << "gouzaohanshu diaoyong" << endl;
    }
    person(const person & p) {//拷贝
        age = p.age
    }
}
```
调用：括号，显示，隐式转换**
括号法：
```C++
person p1;//不要小括号
person p2(10);//有参构造
person p3(p2);//拷贝构造
```
显示法：
```C++
person p2 = person(10);
person p3 = person(p2);
person(10);//匿名对象，会直接结束不要用拷贝构造
```
隐式转换：
```C++
person p4 = 10;
person p5 =p4;
```

2.析构函数进行清理 
```C++
class person {
    public: 
    person() {
            cout << "gouzaohanshu diaoyong" << endl;
        }
        ~person() {
            cout << "xigou" << endl
        }
}
```
- 不能有参
- 自动执行
- 在子程序结束以后执行，主程序一直不动
#### 指针
指针的地址都是4个字节 
```C++
const int *p =&a;//指向可以改，值不可以改
int * const p =&b;//指向不能改，值可以改
//函数传递在普通变量的时候要传递&
//已经是地址了，数组都不需要

```

#### 结构体
```C++
struct Student {}; //stu3
struct Studnet s1 = {
    A,
    B,
    C
};
struct Student arry1[2] = {
    {A1,A2,A3},
    {2},
    {3}
}
struct * s2 = & s1;
cout << "A" << s2 - > A << "B" << s2 - > B << endl;

void print(Student *p){
p->A =B;
cout << "A" << p - > A << "B" << p - > B << endl;
}
```


#### 面向对象和编程原则
###### 内容
类，对象，继承，多态，封装
###### 变量相关
- 全局变量，全局常量，静态变量 static，字符串常量--地址接近；局部变量，局部常量地址接近
- 不要返回局部变量的地址，该内存已经不属于你
#### 堆区
```C++
int * func(){
int *a =new int(10);//new返回该数据类型的指针
return a;
}
void test(){
int *p = func();
cout << *p <<endl;
}
//delete a 来释放
int *arr = new int [10];
//释放数组 delete[] arr


```

#### 函数用法
```C++
int func(int a = 12, int b = 15) {}
```
- 可以直接用，也可以再给值
- 但是一个有默认参数，其他都必须有
- 声明和实现只能一个有
```C++
int func(int a = 12, int = 15) {}
```
- 也有占位参数这个东西
- 后续调用必须填补
##### 函数重载
- 同一个作用域下
- 函数名称相同
- 函数参数类型不同，个数不同，顺序不同
- 返回值不可以重载
###### 注意事项
- 函数重载碰到默认参数会出现二义性
- 函数重载可以使用&，分为有const和没有，调用的时候也是




















