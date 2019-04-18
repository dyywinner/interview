# 互联网技术岗位关于C＋＋的知识点归纳为以下五个部分：

- C＋＋基础知识
- 面向过程的特性
- 面向对象的特性
- 泛型编程的特性
- 标准模板库和算法

必须要说明的是，C＋＋知识繁杂磅礴，面试题中可被问到的很多，如果想要成功拿到心仪的offer，除了掌握C＋＋这个工具外，需要结合一些其它领域知识如: 

- 基础数据结构和基本的算法
- 网络编程
- 多线程编程
- (Linux）Shell脚本（awk, sed）
- 编译

# 关键字
## static
static修饰的变量和函数存储在内存的静态文本区，与全局变量存储的位置一致。而静态文本区的字节默认都是0x00

- 修饰局部变量
- 修饰全局变量
- 修饰函数
## const与宏定义
### const vs define
C++中定义常量的时候不再采用define,因为define只做简单的宏替换，并不提供类型检查.

### const
const关键字可以修饰变量，引用，函数，对象等：

- 需要注意的概念其实是“常量指针”和“指针常量”，也就是const修饰一个指针变量的时候产生的两种差异。我们知道，一个指针变量，使用的时候需要考虑该指针本身和被它所指的对象，看如下例子：
```C++
char *const pc; 	//到char的const指针
char const *pc1; 	//到const char的指针
const char *pc2; 	//到const char的指针（后两个声明是等同的）
```
从右向左读的记忆方式：

- 1 pc is a const pointer to char. 故pc不能指向别的字符串，但可以修改其指向的字符串的内容。pc是一个指向字符类型的常指针，pc的值不可变，但是pc值（也就是pc指向的地址）所代表的内存空间的内容是可以变的，所以pc是一个指针常量。
- 2 pc2 is a pointer to const char. 故pc2的内容不可以改变，但pc2可以指向别的字符串。也就是说pc2是指向一个不可变内容空间（常量）的指针，也就是常量指针。pc2＋＋可行，但pc2 = “hello world”不可行。当然，只是说不能通过pc2去修改那段内容，别的方式是可以的。
- 其实，const只对它左边的东西起作用，唯一的例外就是const本身就是最左边的修饰符，那么它才会对右边的东西起作用。
# 类型转换(4种)

- static_cast
	+ 基础类型转换
	+ static_cast 转换时并不进行运行时安全检查，所以是非安全的，很容易出问题。因此 C++ 引入 dynamic_cast 来处理安全转型。
- dynamic_cast
	+ dynamic_cast 主要用来在继承体系中的安全向下转型。它能安全地将指向基类的指针转型为指向子类的指针或引用，并获知转型动作成功是否。如果转型失败会返回null（转型对象为指针时）或抛出异常（转型对象为引用时）。dynamic_cast 会动用运行时信息（RTTI）来进行类型安全检查，因此 dynamic_cast 存在一定的效率损失。
	+ **Note:** 因为 dynamic_cast 只有在基类带有虚函数的情况下才允许将基类转换为子类。当然，允许转换也不代表可以转换成功。
```C++
	class CBase {
		virtual void dummy() {}
	};
	class CDerived: public CBase{
		int a;
	};
	int main (){
		CBase * pba = new CDerived;
		CBase * pbb = new CBase;
		CDerived * pd1, * pd2;
		pd1 = dynamic_cast<CDerived*>(pba);
		pd2 = dynamic_cast<CDerived*>(pbb);
		return 0;
	}
```
- const_cast
	+  前面提到 const_cast 可去除对象的常量性（const），它还可以去除对象的易变性（volatile）。const_cast 的唯一职责就在于此，若将 const_cast 用于其他转型将会报错。
- reinterpret_cast
	+ reinterpret_cast 用来执行低级转型，如将执行一个 int 的指针强转为 int。其转换结果与编译平台息息相关.总之，reinterpret_cast 只用于底层代码，一般我们都用不到它，如果你的代码中使用到这种转型，务必明白自己在干什么。

- **Conclude**: 需要类型转换的时候，优先使用C＋＋的这种风格进行类型转换，
	+ 基础类型转换的时候，使用static_cast, 
	+ 子类与父类之间进行转换的时候，尤其是基类向子类转换的时候，使用dynamic_cast。
	+ 其它情况，如转换为void指针，使用dynamic_cast, 
	+ int型指针到int，以及函数指针之间的转换使用reinterpret_cast, const_cast只用于去除对象的常量性（const）和易变性（volatile）
# 重载与重写与隐藏
##  重载与重写
- 重载（Overload）
同一可访问区内被声明的几个具有不同参数列（参数的类型，个数，顺序不同）的同名函数，根据参数列表确定调用哪个函数，重载不关心函数返回类型。成员函数被重载的特征：

	+ 1 相同的范围（在同一个类中）；
	+ 2 函数名字相同；
	+ 3 参数不同；
	+ 4 virtual关键字可有可无。

- 重写（Override），也叫覆盖（Overwrite）
重写是指派生类函数重写基类函数，是C++的多态的表现，特征是：
	+ 1 不同的范围（分别位于派生类与基类）；
	+ 2 函数名字相同；
	+ 3 参数相同；
	+ 4 基类函数必须有virtual关键字。

- 说到底，这两个概念其实并没有太大的关联
	+ 重载是**编译多态**的一种实现；
	+ 重写与虚函数相关，用于实现动态绑定，属于**编译时多态**的实现。

### 重写与隐藏
- “隐藏”是指派生类的函数屏蔽了与其同名的基类函数，规则如下：
	+ 1 如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual关键字，基类的函数将被隐藏。
	+ 2 如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual关键字。此时，基类的函数被隐藏。如果有virtual关键字，函数同名，参数相同，就是“重写”了
# 指针和引用的区别
## 指针与引用概念
- 指针从本质上讲就是存放变量地址的一个变量，在逻辑上是独立的，它可以被改变，包括其所指向的地址的改变和其指向的地址中所存放的数据的改变。
- 引用是一个别名，它在逻辑上不是独立的，它的存在具有依附性，所以引用必须在一开始就被初始化，而且其引用的对象在其整个生命周期中是不能被改变的（自始至终只能依附于同一个变量）。
## 指针传递参数和引用传递参数的区别
- 指针传递参数本质上是值传递的方式，它所传递的是一个地址值。
- 引用传递过程中，被调函数的形式参数虽然也作为局部变量在栈中开辟了内存空间，但是这时存放的是由主调函数放进来的实参变量的地址。
- 总结一下指针和引用的相同点和不同点：
	+ 相同点：
 		- 都是地址的概念；
		-指针指向一块内存，它的内容是所指内存的地址；而引用则是某块内存的别名。
	+ 不同点:
		- 指针是一个实体，而引用仅是个别名；
		- 引用只能在定义时被初始化一次，之后不可变；指针可变；引用“从一而终”，指针可以“见异思迁”；
		- 引用没有const
# 内存对齐

## 智能指针(4种)
智能指针产生的原因在于解决常规指针可能产生的**内存泄漏**问题(会自动调用析构函数)，
将基本类型指针封装为**类对象指针**（这个类肯定是个模板，以适应不同基本类型的需求），并在析构函数里编写delete语句删除指针指向的内存空间.

- auto_ptr(C++98)： 不支持复制（拷贝构造函数）和赋值（operator =），但复制或赋值的时候不会提示出错。因为不能被复制，所以不能被放入容器中。
```C++98
# include<memory>
using namespace std;
class A{
public:
    A(int a){
        cout<<"A()..."<<endl;
        this->a = a;
    }
    void func(){
        cout<<"a = "<<this->a<<endl;
    }
    ~A(){
        cout<<"~A()..."<<endl;
    }
private:
    int a;
};
void test1(){
    A *ap = new A(10);
    ap->func();
    delete ap;
};

void test2(){
    auto_ptr<A> ptr(new A(10));
    ptr->func();
    //不需要手动delete,因为auto_ptr自动回收！
}
```
- unique_ptr(C++11): 
	+ 通过资源独占的方式来实现防止内存泄露的,当不用该对象时,会自动调用析构函数进行内存释放.他的拷贝构造还有赋值函数都被设置为私有属性,则你调用他的拷贝和赋值会报错,这个是与auto_ptr不同的地方.
	+ 不支持复制和赋值，但比auto_ptr好，直接赋值会编译出错。如果非要赋值的话可以通过使用move()函数std::move() 当调用这个时会将原本的对象析构掉,所以还是唯一的.
```C++11
	std::unique_ptr<int> p1(new int(5));
	std::unique_ptr<int> p2 = p1;			 // 编译会出错
	std::unique_ptr<int> p3 = std::move(p1); // 转移所有权, 现在那块内存归p3所有, p1成为无效的指针.
```
- shared_ptr
	+ 原理 ：shared_ptr维护了一个指向control block的指针对象，来记录引用个数。其中有use_count被引用的个数这个属性,这个智能指针在构造的时候,将引用计数置为1, 在reset函数中将引用计数减一,每当share_ptr离开其作用范围,即被销毁时引用计数减一,在拷贝构造和赋值时将引用计数增加1,每当计数消减为0时就调用析构函数将内存释放掉
- weak_ptr
	+ 原理 ：weak_ptr用于避免shared_ptr相互指向产生的环形结构，造成的内存泄漏。weak_ptr count是弱引用个数；弱引用个数不影响shared count和对象本身，shared count为0时则直接销毁。
	+ 如何判断weak_ptr的对象是否失效？ 
		- 1、 expired()：检查被引用的对象是否已删除。 
		- 2、 lock()会返回shared指针，判断该指针是否为空。 
		- 3、 use_count()也可以得到shared引用的个数，但速度较慢。
- shared 和 unique区别 ：
	+ unique具有唯一性，对指向的对象值存在唯一的unique_ptr。unique_ptr不可复制，赋值，但是move()可以转换对象的所有权，局部变量的返回值除外。与shared_ptr相比，若自定义删除器，需要在声明处指定删除器类型，而shared不需要，shared自定义删除器只需要指定删除器对象即可，在赋值时，可以随意赋值，删除器对象也会被赋值给新的对象。
	+ 原因：unique的实现中，删除器对象是作为unique_ptr的一部分;而shared_ptr，删除器对象保存在control_block中。
## explicit
规避可被单参调用的构造函数引起的隐式类型转换
所有的智能指针类都有一个explicit构造函数，以指针作为参数.因此不能自动将指针转换为智能指针对象，必须显式调用
## 内存管理
### new/delete 与 malloc/free关系
- delete会调用对象的析构函数,和new调用构造函数对应; free只会释放内存与malloc开辟内存对应。
- malloc与free是C++/C语言的标准库函数，new/delete是C++的运算符。它们都可用于申请动态内存和释放内存。对于非内部数据类型的对象而言，光用maloc/free无法满足动态对象的要求。对象在创建的同时要自动执行构造函数，对象在消亡之前要自动执行析构函数。
- 由于**malloc/free是库函数**而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于malloc/free。因此C++语言需要一个能完成动态内存分配和初始化工作的运算符new，以及一个能完成清理与释放内存工作的运算符delete。**注意new/delete不是库函数。**

### delete与 delete []区别
- delete只会调用一次析构函数，而delete[]会调用每一个成员的析构函数。在More Effective C++中有更为详细的解释：“当delete操作符用于数组时，它为每个数组元素调用析构函数，然后调用operator delete来释放内存。”delete与new配套，delete []与new []配套
```C++
	MemTest *mTest1=new MemTest[10];

	MemTest *mTest2=new MemTest;

	Int *pInt1=new int [10];

	Int *pInt2=new int;

	delete[]pInt1; //-1-

	delete[]pInt2; //-2-

	delete[]mTest1;//-3-

	delete[]mTest2;//-4-   在-4-处报错。
```
- 这就说明：对于内建简单数据类型，delete和delete[]功能是相同的。对于自定义的复杂数据类型，delete和delete[]不能互用。delete[]删除一个数组，delete删除一个指针。简单来说，用new分配的内存用delete删除；用new[]分配的内存用delete[]删除。delete[]会调用数组元素的析构函数。内部数据类型没有析构函数，所以问题不大。如果你在用delete时没用括号，delete就会认为指向的是单个对象，否则，它就会认为指向的是一个数组。
# 面向对象
## 对象大小
当没有成员变量，只有成员函数（空类）时，大小默认为1（在g++和VS编译环境下）

- 空类大小为什么是1？
定义三个不同的对象，若是空类的大小为0，则这三个对象都在地址0处，与三个不同的对象冲突。
## 为什么要继承？多与单继承
- C++为什么要有继承
我们都知道很多类都有自己的数据成员以及函数，在编写程序时，会有很多类的拥有相同的数据成员和函数，为了节省时间以及代码量，我们把这些公共的数据和函数封装成一个类，后面的类只要继承这个类即可。
- 什么是继承
继承是面向对象复用的重要手段。继承是类型之间的关系建模，共享公有的东西，实现各自本质不同的东西。当创建一个类时，您不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。这个已有的类称为基类，新建的类称为派生类。
## 类成员变量初始化顺序

## 析构函数与构造函数执行顺序
常见对象－>构造函数（缺省构造函数，有参构造函数，复制构造函数）
销毁对象－>析构函数

对象是由“底层向上”开始构造的，当建立一个对象时，首先调用基类的构造函数，然后调用下一个派生类的构造函数，依次类推，直至到达派生类次数最多的派生次数最多的类的构造函数为止。因为，构造函数一开始构造时，总是要调用它的基类的构造函数，然后才开始执行其构造函数体，调用直接基类构造函数时，如果无专门说明，就调用直接基类的默认构造函数。在对象析构时，其顺序正好相反。

C++构造函数初始化按下列顺序被调用：

- 首先，任何虚基类的构造函数按照它们被继承的顺序构造；
- 其次，任何非虚基类的构造函数按照它们被继承的顺序构造；
- 最后，任何成员对象的构造函数按照它们声明的顺序调用；

```C++
#include <iostream>
using namespace std;
//基类
class CPerson
{
    char *name;        //姓名
    int age;            //年龄
    char *add;        //地址
public:
    CPerson(){cout<<"constructor - CPerson! "<<endl;}
    ~CPerson(){cout<<"deconstructor - CPerson! "<<endl;}
};

//派生类(学生类)
class CStudent : public CPerson
{
    char *depart;    //学生所在的系
    int grade;        //年级
public:
    CStudent(){cout<<"constructor - CStudent! "<<endl;}
    ~CStudent(){cout<<"deconstructor - CStudent! "<<endl;}
};

//派生类(教师类)
//class CTeacher : public CPerson//继承CPerson类，两层结构
class CTeacher : public CStudent//继承CStudent类，三层结构
{
    char *major;    //教师专业
    float salary;    //教师的工资
public:
    CTeacher(){cout<<"constructor - CTeacher! "<<endl;}
    ~CTeacher(){cout<<"deconstructor - CTeacher! "<<endl;}
};

//实验主程序
int main()
{
    CPerson person;
    CStudent student;
    CTeacher teacher;
}
/* output:
constructor - CPerson! 
constructor - CPerson! 
constructor - CStudent! 
constructor - CPerson! 
constructor - CStudent! 
constructor - CTeacher! 
deconstructor - CTeacher! 
deconstructor - CStudent! 
deconstructor - CPerson! 
deconstructor - CStudent! 
deconstructor - CPerson! 
deconstructor - CPerson! 
*/
```
## 为什么拷贝构造函数的参数一定是引用
避免拷贝构造函数不限制的递归复制下去。
## 虚函数与运行时多态

- 多态：是对于不同对象接收相同消息时产生不同的动作。C++的多态性具体体现在运行和编译两个方面：在程序运行时的多态性通过继承和虚函数来体现；在程序编译时多态性体现在函数和运算符的重载上；

- 虚函数：在基类中冠以关键字 virtual 的成员函数。 它提供了一种接口界面。允许在派生类中对基类的虚函数重新定义。

- 纯虚函数的作用：在基类中为其派生类保留一个函数的名字，以便派生类根据需要对它进行定义。作为接口而存在 纯虚函数不具备函数的功能，一般不能直接被调用;从基类继承来的纯虚函数，在派生类中仍是虚函数。如果一个类中至少有一个纯虚函数，那么这个类被称为抽象类（abstract class）。

- 抽象类中不仅包括纯虚函数，也可包括虚函数。抽象类必须用作派生其他类的基类，而不能用于直接创建对象实例。但仍可使用指向抽象类的指针支持运行时多态性。

- 虚函数列表: 包含虚函数的类才会有虚函数表， 同属于一个类的对象共享虚函数表， 但是有各自的_vptr;虚函数表实质是一个指针数组，里面存的是虚函数的函数指针。

## 函数重载与编译时多态
见 上文 重载与重写
## 有元函数
- 破换函数封装性
- 类的友元函数是定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。
- 友元可以是一个函数，该函数被称为友元函数；友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。

如果要声明函数为一个类的友元，需要在类定义中该函数原型前使用关键字 friend，如下所示：
```C++
class Box{
   double width;
public:
   double length;
   friend void printWidth( Box box );
   void setWidth( double wid );
};
```
## 设计模式
### SOLID五大原则
- SRP The Single Responsibility Principle 单一责任原则
	+ 一个类只做一件事情，只有一个引起它的变化
- OCP The Open Closed Principle 开放封闭原则
	+ 对扩展开放，对修改封闭
- LSP The Liskov Substitution Principle 里氏替换原则
	+ 子类必须能替换基类
- DIP The Dependency Inversion Principle 依赖倒置原则
	+ 依赖于抽象；而不依赖细节
- ISP The Interface Segregation Principle 接口分离原则
	+  核心思想是：使用多个小的专门的接口，而不要使用一个大的总接口。   

### 观察者模式
- 动机Motivation
	+ 1 构建软件时，需要为对象建立一种通知“依赖关系”——一个对象状态发生改变，所有依赖对象都将得到通知。如果这种依赖太过于紧密，软件就不能很好的抵御变化。
	+ 2 使用OOP技术，可以将这种依赖弱化，并形成一种稳定的依赖关系。从而实现软件体系结构**松耦合**。
## 泛型编程

# 标准模板库（Standard Template Library）

容器
迭代器
适配器
算法
函数对象
空间适配器
vector
map

# Lambda表达式
lambda表达式的完整声明如下：

- capture list:捕获列表
	+ [var]:以值的形式捕获
	+ [&]:引用方式捕获
	+ [=]:值捕获
- params list:参数列表
- mutable： 捕获列表可以修改
- exception：异常处理
- return type：返回值类型
```C++
[capture list](params list) mutable exception->return type{function body}
```
- 1.若按值传递，表达式内部复制了一份捕获的变量；不论被捕获的变量如何改变（如++a），其已被捕获的值，在表达式内部保持不变；
- 2.若按引用传递，表达式内部的值，使用的就是被捕捉的变量的值；所以，被捕获的变量改变时，其内部的值也会改变；
- 3.按值传递，若想修改变量的值，则需要加mutable关键字；因为lambda默认是常函数；在一个常函数内部，只能修改static类型变量的值，不能修改非static类型变量的值。
# 模版
用模板的目的就是要让这程序的实现与类型无关：

- 模板是一种对类型进行参数化的工具；
- 通常有两种形式：
	+ 函数模板：函数模板针对仅参数类型不同的函数；
	+ 类模板：类模板针对仅数据成员和成员函数类型不同的类
- **注意**：模板的声明或定义只能在全局，命名空间或类范围内进行。即不能在局部范围，函数内进行，比如不能在main函数中声明或定义一个模板。
```C++
//函数模版
template <class 形参名，class 形参名，......> 返回类型 函数名(参数列表)
//类模版
template<class  形参名，class 形参名，…>   class 类名
```