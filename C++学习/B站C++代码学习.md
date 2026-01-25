# 基础语法
### 部分用法
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

### 引用
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

### 数组

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

### 指针
指针的地址都是4个字节 
```C++
const int *p =&a;//指向可以改，值不可以改
int * const p =&b;//指向不能改，值可以改
//函数传递在普通变量的时候要传递&
//已经是地址了，数组都不需要

```

### 结构体
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


### 面向对象和编程原则
###### 内容
类，对象，继承，多态，封装
###### 变量相关
- 全局变量，全局常量，静态变量 static，字符串常量--地址接近；局部变量，局部常量地址接近
- 不要返回局部变量的地址，该内存已经不属于你
### 堆区
new返回的是地址
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
### 函数用法
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


### 命名空间
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

### 类和对象
封装，继承，多态
#### 封装
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
##### 构造函数进行初始化
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
###### 分类：有参无参，普通拷贝
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
person p5 = p4;
```
==有参无默认，有拷贝==
==有拷贝其他都无==
###### 深拷贝浅拷贝
深拷贝和浅拷贝的本质区别是
深拷贝通过自己写一个拷贝函数，并在其中使用新的newint来存储一个被拷贝对象的指针指向大小来返回一个地址
```C++
class person {
    //......
    person(int age, int height) {
            m_age = age;
            m_height = new int(height);
        }

    ~person() {
        if (m_meight != NULL) {
            delete m_meight;
            m_height = NULL;
        }
    }

    int m_age;
    int* m_height;
}
```
###### 初始化列表
```C++
Person(int a, int b, int c) :m_A(a), m_B(b), m_C(c) {}
	void PrintPerson() {
		cout << "mA:" << m_A << endl;
		cout << "mB:" << m_B << endl;
		cout << "mC:" << m_C << endl;
	}
```
###### 类对象对类成员
先调用类成员的构造，再来大的
###### 类中的静态变量和静态成员函数
- 使用静态变量静态成员，每一个对象共享一份内容
- 需要在外部声明int，里面用static
- 可以通过对象访问，也可以通过类名访问
- 静态函数只能访问静态成员变量
```C++
static int A;//函数里面
int person::A =10;
static void func()
person p;
p.func();//对象
person::func();//类名

```
##### C++对象模型和this指针
###### 对象模型
成员变量和成员函数分开存储
基础都是1，static不占，int有以后就是4，1消失
###### this指针
1.解决名称冲突
this指针指向被调用的成员函数 所属的对象，防止名字一样
```C++
person(int age) {
    this -> age = age
}
int age;
```
2.返回对象本身*this*
```C++
class Person {
    public:
        Person(int age) {
            //1、当形参和成员变量同名时，可用this指针来区分
            this - > age = age;
        }
    Person& PersonAddPerson(Person p) {//返回是值，创建新的对象
        this - > age += p.age;
        //返回对象本身
        return *this;
    }
    int age;
};
void test01() {
    Person p1(10);
    cout << "p1.age = " << p1.age << endl;
    Person p2(10);
    p2.PersonAddPerson(p1).PersonAddPerson(p1).PersonAddPerson(p1);
    cout << "p2.age = " << p2.age << endl;
}
int main() {
    test01();
    system("pause");
    return 0;
}
```
###### 常函数和常对象
**常函数**
this的本质是一个person * const this
指向是不可以变化的，如果希望得到一个值也不可以改的常函数，则在{前面写一个const，变成常函数
如果还有想修改的值，则使用mutable int来定义值
**常对象**
const person p;
p.B =100;如果B是mutable则可以修改
常对象只能调用常函数
##### 友元
###### 全局函数做友元
 某个全局函数，在类里面friend一下定义，可以访问私有内容
```C++
#include <iostream> 
#include <string> 
using namespace std;
class Building { // 声明goodGay为友元函数，参数匹配引用类型 
friend void goodGay(Building& building);
}
void goodGay(Building& building) { // 引用用 . 访问成员，而非指针的 -> 
cout << "好基友访问：" << building.m_SittingRoom << endl; cout << "好基友访问：" << building.m_BedRoom << endl; }

Building building; 
// 调用函数：直接传对象名，不用加 & 取地址 
goodGay(building);
```
###### 类做友元
```C++
class goodGay {
    public: goodGay();
    void visit(); //只让visit函数作为Building的好朋友，可以发访问Building中私有内容
    void visit2();
    private: Building * building;
};
class Building {
    //告诉编译器 goodGay类是Building类的好朋友，可以访问到Building类中私有内容
    friend class goodGay;
    public:
        Building();
    public:
        string m_SittingRoom; //客厅
    private:
        string m_BedRoom; //卧室
};
Building::Building() {
    this - > m_SittingRoom = "客厅";
    this - > m_BedRoom = "卧室";
}
goodGay::goodGay() {
    building = new Building;
}
void goodGay::visit() {
    cout << "好基友正在访问" << building -> m_SittingRoom << endl;
    cout << "好基友正在访问" << building -> m_BedRoom << endl;
}
void test01() {
    goodGay gg;
    gg.visit();
}
```
###### 成员函数做友元
friend void Goodgay::visit();
##### 运算符重载
对已有的运算符进行重新定义，赋予新的功能对自定义变量操作
###### 加法
成员函数，写在类里面
```C++
person operator + (person & p) {
    person temp;
    temp.m_A = this - > m_A + p.m_A;
    temp.m_B = this - > m_B + p.m_B;
    return temp
}
//可以直接使用 
person p3 = p1.operator+(p2);
person p3 = p1+p2;
```
全局函数，写在外面
```C++
person operator + (person &p1, person &p2) {
    person temp;
    temp.m_A = p1.m_A + p2.m_A;
    temp.m_B = p1.m_B + p2.m_B;
    return temp
}
//可以直接使用 
person p3 = operator+(p1, p2);
person p3 = p1 + p2;
```
###### 左移运算
目的是直接输出
只能利用全局函数
```C++
ostream &operator <<(ostream &cout,person &p){
cout <<p.m_A<<p.m_B;
return cout;
}//记得配合友元
```
###### ++前后运算
```C++
//private
person & operator++() {
    m_mun++;
    return * this
} //avance
person operator++(int) { //important int不要引用
    person temp = * this
    m_mun++;
    return temp
}

//全局的
Counter& operator++(Counter& c) { 
c.value++; // 直接修改传入的对象 
return c; // 返回引用 
}

Counter operator++(Counter & c, int) {
    Counter temp = c; // 1. 拷贝一份原有的状态 
    c.value++; // 2. 修改原对象
    return temp; // 3. 返回修改前的副本 
}
```
###### 赋值运算符
因为在直接等于的情况下，出现完全复制，导致堆区的释放出现问题。两个会同时释放一个位置，不合理，所以要用深复制。
```C++
class Person {
    public:
        Person(int age) {
            //将年龄数据开辟到堆区
            m_Age = new int(age);
        }
    //重载赋值运算符 
    Person & operator = (Person & p) {
        if (m_Age != NULL) {
            delete m_Age;
            m_Age = NULL;
        }
        //编译器提供的代码是浅拷贝
        //m_Age = p.m_Age;
        //提供深拷贝 解决浅拷贝的问题
        m_Age = new int( * p.m_Age);
        //返回自身
        return * this;
    }
    ~Person() {
        if (m_Age != NULL) {
            delete m_Age;
            m_Age = NULL;
        }
    }
    //年龄的指针
    int * m_Age;
};
void test01() {
    Person p1(18);
    Person p2(20);
    Person p3(30);
    p3 = p2 = p1; //赋值操作
    cout << "p1的年龄为：" << * p1.m_Age << endl;
    cout << "p2的年龄为：" << * p2.m_Age << endl;
    cout << "p3的年龄为：" << * p3.m_Age << endl;
}
int main() {
    test01();
    system("pause");

    return 0;
}
```
###### 关系运算符
```C++
bool operator==(Person & p)
	{
		if (this->m_Name == p.m_Name && this->m_Age == p.m_Age)
		{
			return true;
		}
		else
		{
			return false;
		}
	}

```
###### 调用运算符
仿函数
```C++
class MyAdd
{
public:
	int operator()(int v1, int v2)
	{
		return v1 + v2;
	}
};
void test02()
{
	MyAdd add;
	int ret = add(10, 10);
	cout << "ret = " << ret << endl;
	//匿名对象调用  
	cout << "MyAdd()(100,100) = " << MyAdd()(100, 100) << endl;
}
```

##### 析构函数进行清理 
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
#### 继承
##### 基础语法
```C++
class son :jichengfangshi father
//使用的时候，直接调用函数就行
```
##### 继承方式
- 父的私有，都不能使用
- 父的其他，如果是public，照旧，如果其他，都变化
##### 对象模型
利用开发人员命令提示工具查看对象模型
跳转盘符 F:
跳转文件路径 cd 具体路径下
查看命令
cl /dl reportSingleClassLayout类名 文件名
##### 继承中构造和析构的顺序
父 - 子 - 子结束 - 父结束
##### 同名成员处理
```C++
s.A //son
s.Base::A//father
```
如果子类有和父类相同的，会全部隐藏
例如重载的时候，不能直接给数
##### 继承中的静态同名

