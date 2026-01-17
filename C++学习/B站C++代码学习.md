# 基础语法

#### 引用
引用的主要用法旨在不同的位置使用相同的变量
```
int i = 10;
int &niubi = i;
niubi = 20;//i = 20
```


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

#### 类
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
        cout << "我要打篮球" << age << endl;
    }
}
int main() {
    Stu xu;
    strcpy(xu.name, "niu");
    xu.setage(20);
    xu.playniu();
}
```

#### 面向对象和编程原则
类，对象，继承，多态，封装
单一职责


#### 指针
指针的地址都是4个字节 
```C++
const int *p =&a;//指向可以改，值不可以改
int * const p =&b;//指向不能改，值可以改
//函数传递在普通变量的时候要传递&
//已经是地址了，数组都不需要

```