# C++

编译器：Visual Studio  2022

[【1】【Cherno C++】【中字】欢迎来到C++_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1uy4y167h2/?spm_id_from=333.788&vd_source=e5aa67477135266ae5c66fe7c8b108f3)

[最好的C++学习教程(上篇)——The Cherno CppSeries - 知乎](https://zhuanlan.zhihu.com/p/553387258)

[最好的C++学习教程(下篇)——The Cherno CppSeries - 知乎](https://zhuanlan.zhihu.com/p/553405837)

## 5. C++如何工作

带有#符号的是预处理命令，在编译前做处理。#include就是在编译前做复制粘贴工作。

常说的编译（build）包括编译（complie）和链接（link）。编译（build）的工作是将单个或多个cpp文件分别单独编译（complie）成obj文件再进行链接（link）成可执行文件exe。

主函数main返回值类型为`int`，但比较特殊，可以不返回，不返回默认返回0。

vs中Debug/Release，x86/x64分别指编译配置与目标平台，均可自行配置。默认配置中Release模式会比Debug模式更快，原因是Debug下没有做任何优化而Release会。

## 6. 编译器如何工作

预处理，将.cpp转换成.obj。

## 7. 链接器如何工作

输出日志（output）的报错提示以C开头的一般是编译环节错误，LNK开头一般是链接环节错误。

程序执行的入口点一般是main函数，但不一定必须是main函数。

编译过程（compile）仅考虑每个单独的.cpp文件语法有没有问题，写在不同文件函数在被其他文件调用是链接过程解决的问题。

`static`关键字可以使函数仅在这个文件被声明。

有时候会不经意出现定义了完全相同的两个或多个函数体，此时可以新建一个.h的头文件，将函数定义写在头文件内，然后在每个cpp文件中`#include`，哈哈，这个时候会出现新的问题，#include是将头文件内容复制粘贴过来，这样导致了有多个cpp文件含有头文件内函数的定义。有多个解决方案：

一、头文件内函数用`static`修饰，这样被复制过去的函数体就只是单独在此处cpp有用，是此cpp的内部函数。

二、用`inline`关键字修饰头文件的函数，它会把实际的函数体实现替换调用处的语句。

三、最好的，将函数体写在单独的编译单元（cpp文件），在头文件写该函数的声明，哪个文件需要调用此函数只需要包含这个头文件即可。

链接器的工作是将编译器产生的目标文件（.obj）链接在一起。

## 8. 变量

在c++中不同数据类型的唯一区别在于占用的大小。

## 9. 函数

函数的主要目的是防止代码重复。

## 10. 头文件

`#pragma once`预处理命令使此文件只被包含一次。

也可以使用

```c++
#ifndef _LOG_H
#define _LOG_H
...
#endif
```

使同一个头文件不被重复包含。

`#include <>`与`#include ""`的区别在于引号通常用于包含相对于当前文件的文件且优先查找当前文件（很可能是main.cpp）相对路径下的，例如Log.cpp与Log.h在同一级路径，包含Log.h可以`#include "../Log.h"`这样写，尖括号就是只要在指定路径下或者说是包含目录下就行。引号也可以指定编译器包含目录的相对路径里的文件，也可以说“尖括号是引号的子集”。

## 12. if语句

````
else if(ptr == "Hello")
	Log("ptr is Hello");
````

其实是

```
else
{
	if(ptr == "Hello")
		Log("ptr is Hello");
}
```

过多的使用if语句会使程序变慢，原因是可能会跳到不同的函数体。函数在编译后存储在内存的某处，在汇编中需要call指令，需要耗费更多时间。

## 13. VS中的配置

VS生成文件的默认配置非常亏贼，很没有逻辑，建议修改为：

输出文件目录：\$(SolutionDir)bin\\\$(Platform)\\$(Configuration)\

中间文件目录：\$(SolutionDir)bin\\intermediates\\\$(Platform)\\$(Configuration)\

## 16. 指针

指针是一个**整数（`int`）**，一种存储内存地址的数字，类型毫无意义，这是指针的实质。

最简单的指针`void* ptr = nullptr;`

```
int var = 8;
int* ptr = &var; //引用，读取变量地址。
*ptr = 10; //逆向引用，读取此地址的数据。
```

注意，虽然说指针的类型毫无意义，但是在解引用也就是逆向引用时，编译器需要知道你操作的值究竟是什么类型，比如上例的通过解引用将var改成10，但编译器不知道这个10它是short、int、long long啥的，所以在创建指针时指针的类型，就是我们要告诉指针这个操作值究竟是什么。

在堆分配一些内存的例子

```
char* buffer = new char[8];
memset(buffer, 0, 8); //用指定数据填充内存块，用数据0填充以buffer开头的8个字节。
delete[] buffer; //因为分配的是数组，所以delete[]。记得删除分配内存喵。
```

在32位的应用程序中，指针占四个字节，也就是32位。

双指针`char** ptr = &buffer;`，指向指针的指针。

## 17.引用

```
int a = 8;
int& ref = a;
```

引用不是一个真正的变量，上例仅创建了a一个变量。 

引用是不能改变指向的指针。

上例可以理解为给a起了ref的别名。

```
void Increment(int* value)
{
	(*value)++;
}
int a = 8;
Increment(&a);
```

与

```
void Increment(int& value)
{
	value++;
}
int a = 8;
Increment(a);
```

是完全一样的，引用让指针看起来更简洁。

## 18. 类

一个类的例子

```
class Player
{
public:
	int x, y;
	int speed;
	
	void Move(int xa, int ya)
	{
		x += xa * speed;
		y += ya * speed;
	}
};

Player player;
```

类内成员默认为private。

类内函数被称为方法。

类主要是可以用来组织我们的代码，使它更容易维护，和引用作用一样。

## 19. 类与结构体的区别

技术上，只有成员默认属性也就是可见性的不同，结构体成员默认为public

上述类也可以变成结构体

```
struct Player
{
	int x, y;
	int speed;
	
	void Move(int xa, int ya)
	{
		x += xa * speed;
		y += ya * speed;
	}
};
```

所以，用类还是结构体，完全看你想怎么写。

## 21、22、23. 静态（static）

分为两种，一种为在类和结构体外的static，一种为在类和结构体内的static。

对于类外的static，静态变量或函数意味着，当需要将这些函数和变量与实际定义的符号链接时，链接器不会在这个翻译单元的作用域外，寻找那个符号定义。

`extern int s_Varianle;`

`extern`关键字意味着会在外部翻译单元（编译单元）中寻找此变量。

对于类内的static，静态变量意味着在类的所有实例中，这个变量只有一个实例，也就是所有实例（对象）共享这一个变量，所以通过类实例来引用静态变量是无意义的。静态方法无法访问类的实例，静态方法可以被调用而不需要通过类的实例。

静态变量不属于某个实例，需要定义才能使用。

```
struct Entity
{
	static int x, y;
	
	void Print()
	{
		std::cout << x << "," << y << std::endl;
	}
};

int Entity::x;
int Entity::y; //定义静态变量。
```

静态方法不能引用非静态的成员变量，原因是静态方法没有类实例。**类的幕后工作方式其实是，每个非静态方法总是获得当前类的一个实例作为参数**，但静态方法不属于某个实例。

在类内写静态方法和在类外写静态函数其实是一样的

```
//以下为错误案例，静态方法调用了非静态成员变量
struct Entity
{
	int x, y;
	
	static void Print()
	{
		std::cout << x << "," << y << std::endl;
	}
	void Print2()
	{
		std::cout << x << "," << y << std::endl;
	}
};

Entity::Print(); //调用静态方法。

void Print2(Entity e)
{
	std::cout << e.x << "," << e.y << std::endl;
} //这本质上是非静态类方法在编译时的真实样子。
static void Print()
{
	std::cout << e.x << "," << e.y << std::endl;
} //去掉参数Entity e正是static做的事情，明显是有错误的，它不知道你要访问哪个实例。
```

静态局部（Local Static）变量，它的生存期基本相当于整程序的生存期，但作用域会被限制在函数内（和函数其实没关系，可以在任意作用域声明它，只是它是局部的，函数是一个例子）。

与静态全局变量对比，因为作用域的不同，静态局部变量仅在函数（或者别的作用域）内可以访问。

```
void Function()
{
	static int i = 0;
	i++;
	std::cout << i << std::endl;
} //函数外没法访问i。
```

静态局部变量是让你的代码更干净的方法之一，类与引用也是如此。

## 24. 枚举

枚举的本质是，它只是给特定的值命名的一种方式，这样你就不必在各种地方，处理各种**整数**

一个创建枚举的例子

```
enum Example ：unsigned char //为枚举指定赋值的整数类型
{
	A = 5, B, C
};

Example value = B;
```

枚举本质上让我们生活更容易，让代码更干净，类似的还有静态局部变量，类以及引用。

## 25. 构造函数

构造函数是一种特殊类型发方法，这是一种你每次构造一个对象时都会调用的方法。它没有返回类型，并且它的名称必须与类的名称相同

```
class Entity
{
public:
	float X, Y;
	
	Entity()
	{
		X = 0.0f;
		Y = 0.0f;
	}
};
```

就算不写构造函数，也会有默认构造函数，它只是什么都没做而已。

构造函数和一般函数一样可以重载，比如说

```
class Entity
{
public:
	float X, Y;
	
	Entity()
	{
		X = 0.0f;
		Y = 0.0f;
	}
	Entity(float x, float y)
	{
		X = x;
		Y = y;
	}
};
Entity e(10.0f, 5.0f);//可以这样创建一个实例
```

如果我写了个类只想让人使用静态方法而不希望创造实例，可以这么做：

```
class Log
{
private:
	Log() {}//这样在创建实例的时候就无法访问构造函数也就不能创建实例了。
public:
	static void Write()
	{
	
	}
};
//或者↓
class Log
{

public:
	Log() = delete;//默认构造函数被删掉了。
	
	static void Write()
	{
	
	}
};
```

## 26. 析构函数

析构函数在销毁对象时运行。长这样↓

```
class Entity
{
public:
	float X, Y;
	
	Entity()
	{
		X = 0.0f;
		Y = 0.0f;
	}
	~Entity()
	{
	
	}//析构函数
};
```

##  27. 继承

继承的主要效用是可以帮助我们避免代码重复，它只是我们扩展现有类并为基类提供新功能的一种方式。子类会拥有父类拥有的一切，就好像我们复制粘贴了所有东西。

这是例子↓

```
class Entity
{
public:
	float X, Y;
	
	void Move(float xa, float ya)
	{
		X += xa;
		Y += ya;
	}
};

class Player : public Entity
{
public:
	const char* Name;
	void PrintName()
	{
		std::cout << Name << std::endl;
	}
};

Player player;
player.Move();//调用Entity类内方法
```

## 28. 虚函数

虚函数允许我们在子类中重写方法，例如B是A的子类，如果在A类中创建一个方法，标记为virtual，我们可以选择在B类中重写那个方法，让它做其他事情。

此处需要引入初始化列表的概念：

### 初始化列表

与其他函数不同，构造函数除了有名字，参数列表和函数体之外，还可以有初始化列表，初始化列表以冒号开头，后跟一系列以逗号分隔的初始化字段。

```
class foo
{
public:
foo(string s, int i) : name(s), id(i) {}; // 初始化列表
private:
string name; int id;
};
```

初始化类的成员有两种方式，一是使用初始化列表，二是在构造函数体内进行赋值操作。

使用初始化列表主要是性能问题，对于内置类型，如int, float等，使用初始化类表和在构造函数体内初始化差别不是很大，但是对于类类型来说，最好使用初始化列表，原因见后文初始化列表。

---

### 关于new

new一个构造函数就会创建一个新对象，比如说`Entity`是一个类，你可以↓

```
Entity* e = new Entity();
```

---

回到虚函数，你需要知道的是，如果你想覆写基类（父类）的方法，必须将基类中的函数标记为虚函数。

```
class Entity
{
public:
	std::string GetName() { return "Entity"; }
};

class Player : public Entity
{
private:
	std::string m_Name;/*一般来说类成员变量加m_前缀是为了防止成员函数的参数列表中出现同样的变量名称造成的混乱*/
public:
	Player(const std::string& name)
		: m_Name(name) {}
		
	std::string GetName() { return m_Name; }
};

void PrintName(Entity* entity)
{
	std::cout << entity->GetName() << std::endl;
}

int main()
{
	Entity* e = new Entity();
	PrintName(e);
	
	Player* p = new Player("Cherno");
	PrintName(p);//就算形参列表里是父类实例，子类依旧可以传。
	
	std::cin.get();
}
```

上面例子的问题是`PrintName(p);`打印出的是Entity，可我们希望的是Cherno，出现这个错误的原因是，在我们声明函数时，我们的方法通常在类内部起作用，当调用方法的时候，会调用属于该类型的方法，比如形参列表里是Entity类型，它会跑到Entity类中找这个叫GetName的函数。正确的写法是：

```
class Entity
{
public:
	virtual std::string GetName() { return "Entity"; }
};
```

更好的习惯是，将覆写的函数加上`override`关键字：

```
class Entity
{
public:
	virtual std::string GetName() { return "Entity"; }
};

class Player : public Entity
{
private:
	std::string m_Name;
public:
	Player(const std::string& name)
		: m_Name(name) {}
		
	std::string GetName() override { return m_Name; } //override在这里
};
```

这样可以让它更具可读性，预防bug的发生，因为带上`override`关键字，函数名必须是已经存在的函数，这样可以防止拼写错误。

写虚函数会有额外的性能开销，它需要额外内存储存v表（虚函数表），以至它可以分配到正确的函数，包括基类需要一个成员指针指向v表，其次，调用虚函数时，它需要遍历整个表来确定要映射到哪个函数。

## 29. 纯虚函数

创建一个类，只由未实现的方法组成，然后强制子类去实际实现它们，这被称为接口（interface），这个接口类实际上不包含方法实现，实际上不可能实例化那个类，见如下例子↓

```
class Entity
{
public:
	virtual std::string GetName() = 0; //这就是纯虚函数，也就是那个未实现的方法。
};
```

纯虚函数意味着，它必须在子类中实现，否则你无法实例化这个子类。

纯虚函数，或者说这个接口类确保了它的子类都有一个特定的方法，那么你就可以将这个类（抽象基类）作为参数（类型）放入一个通用的函数中，然后调用这个方法或者做其他事情。例子如下↓

```
void Print(Entity* obj)
{
	std::cout << obj->GetName() << std::endl;
}
```

因为Entity类有纯虚函数，是一个接口类，作为Entity的子类们必须实现这个`GetName()`函数，这些子类对象都可以传入这个`Print()`函数来使用这个功能。

## 30. 可见性

C++有三个可见性修饰字`private`、`protected`、`public`可见性递增，`private`意味着仅类内和友元可访问，`protected`表示它的子类们也可以访问，`public`公开，都可以访问。

可见性是帮助你我大家的东西，有时候类作者写private不让你访问是怕破坏什么等等，总之，不要忽视可见性，就算是个人写代码，它也能提升你读自己代码时的可读性。

## 31. 数组

数组名只是个指针。

```
int example[5];
int* ptr = example;

for(int i = 0; i < 5; i++)
	example[i] = 2;
```

通过索引（index）或者指针可以给数组元素赋值：

```
example[2] = 5;
*(ptr + 2) = 6; //此处加2并非偏移两个字节，而是根据指针类型，偏移两个元素，所以是2*4个字节的偏移。
```

如果就想一个字节一个字节地偏移，可以这样操作：

``` 
*(int*)((char*)ptr + 8) = 6; //转换成占一个字节的类型的指针再加，然后转回int*。加多少取决于类型。
```

要是你想new一个数组：

```
int* another = new int[5];
for (int i = 0; i < 5; i++)
	another[i] = 2;

delete[] another;
```

C++11有新数组std::array，它有着边界检查等一些安全性优势

```
#include <array>

std::array<int, 5> another;
int size = another.size(); //新数组可以这样知道大小，传统的原始数组如果不在栈上就比较难知道大小了
```

比如说：

```
int a[5];
int size = sizeof(a);
int count = sizeof(a) / sizeof(int); //在栈上就比较方便

int* b = new int[5];
int count1 = sizeof(b) / sizeof(int); //此时sizeof(b)就只是一个int*的大小了，在堆上的就比较危险了
```

## 32.字符串

字符串实际上给你返回的是地址，你可以C风格地定义一个字符串，然后这样打印：

```
const char* name = "Cherno";
std::cout << name << std::endl;
```

name只是一个指针知道字符串什么时候开始，但字符串也需要知道什么时候结束，所以就有了空终止符，当指针跑到0，它就知道这个字符串结束了，所以字符串的末尾会多一个终止符。

```
char name[7] = { 'C', 'h', 'e', 'r', 'n', 'o', '\0' };
//或者
char name[7] = { 'C', 'h', 'e', 'r', 'n', 'o', 0 };
```

用C++风格定义一个字符串：

```
#include <string>

std::string name = "Cherno"; //"Cherno"这里本质上是一个const char[]
//注意，string类重载了运算符，所以追加字符串是可行的，比如：
name += "hello!";

//但是const char[]字符数组不可以这么做
std::string name = "Cherno" + "hello!"; //这是错误的

//所以我们可以这么做：
std::string name = std::string("Cherno") + "hello!"; /*相当于调用了string的构造函数，相当于创建了字符串*/
```

还有一件事，string类作为参数传给函数是建议引用，不然copy一个类是很需要花销的，最好是让这个string作为一个常量，不要改变它。

```
void PrintString(const std::string&	string)
{
	std::cout << string << std::endl;
}
```

## 33. 字符串字面量

```
"Cherno";
```

以上语句将创建一个存储在**常量区**的const char[]，所以当你使用指针去指向这个内存地址它显然是**只读**的，但你可以在栈上写一个数组去接收这个字面量：

```
char* name = "Cherno";
name[2] = 'a'; //这是不可行的

char name[7] = "Cherno";
name[2] = 'a'; //bingo!
```

这也是为什么建议你要`const char* name = "Cherno";`的原因。

 ## 34. const

const甚至可以被称为伪关键字，类似类和结构体的可见性，这是一个机制，让我们的代码更加干净，并对开发人员写代码强制特定的规则，不会改变生成的东西。

再说指针：

```
const int* a = new int;
// 和
int const* a = new int;
//其实是一样的，本质上在于const在不在*（pointer，指针符号）的前面
//上面两个是指针指向的数据你没法重新赋值，但你可以改变指针的指向。

int* const a = new int;
//这个是反过来，它不可以改变指针的指向，但可以改变指针指向的值。

/*怎么去思考区别这两个呢，可以理解为一个是"const的int"的指针，一个是int型的指针，它是const的。*/

//你还可以写两个const
const int* const a = new int;
//什么也不能变。
```

再说类内const：

```
class Entity
{
private:
	int m_X, m_Y;
public:
	int GetX() const //此const意味着这个方法不会修改任何实际的类，不可以修改类成员变量
	{
		m_X = 2; //不可行
		return m_X;
	}
};
```

最爽的是：

```
class Entity
{
private:
	int* m_X, m_Y; /*这里要注意下，此时只有m_X是指针，如果想要m_Y也是指针，请这样写int* m_X, *m_Y;*/
public:
	const int* const GetX() const /*返回一个不可被修改的指针，且这个方法不可以修改实际的类*/
	{
		return m_X;
	}
};
```

还有一点要提的就是，当你写了一个形参列表，里面是const的类：

```
class Entity
{
private:
	int m_X, m_Y;
public:
	int GetX() const
	{
		return m_X;
	}
	int GetX()
	{
		return m_X;
	}
};

void PrintEntity(const Entity& e) 
/*因为这个const限制了e不允许被改变，所以函数体内的所有方法都不可以是非const的，如果类内只有int GetX()而没有int GetX() const则会报错*/
{
	std::cout << e.GetX() << std::endl;
}
```

当你想让类内方法const但又想改变一些变量怎么办，太变态了还可以这样操作，使用`mutable`：

```
class Entity
{
private:
	int m_X, m_Y;
	mutable int var;
public:
	int GetX() const
	{
		var = 2;
		return m_X;
	}
}
```

`mutable`允许函数是常量方法，但可以修改变量。

## 36 . 成员初始化列表

要确保做初始化列表时，要与成员变量声明时的顺序一致，因为你无论按什么顺序初始化，它大都会按照定义类成员的顺序，进行初始化，如果打破这个顺序，就会导致各种各样的依赖性问题：

```
class Entity
{
private:
	int m_Score;
	std::string m_Name;
public:
	Entity()
		: m_Score(0), m_Name("Unknown") {}
};
```

除了代码风格上好看，性能上也有差别：

```
class Example
{
public:
	Example()
	{
		std::cout << "Created Entity!" << std::endl;
	}
	
	Example(int x)
	{
		std::cout << "Created Entity with " << x << "!" << std::endl;
	}
};
class Entity
{
private:
	Example m_Example; //创建Entity实例的时候这里会创建一个Example类
public:
	//1
	Entity()
	{
		m_Example = Example(8); /*这里Example(8)又会多创建一个Example类需要多调用一次默认构造函数*/
	}
	//2
	Entity()
		: m_Example(Example(8)) /*或者直接m_Example(8),这里不需要多调用一次默认构造*/
};
```

所以建议使用初始化列表，如果不用，就会浪费性能。

## 37. 三元操作符

实际上是if语句的语法糖，利于代码整洁性与易读性。

```
static int s_Level = 1;
static int s_Speed = 2;

if (s_Level > 5)
	s_Speed = 10;
else
	s_Speed = 5;
//等价于
s_Speed = s_Level > 5 ? 10 : 5;
```

还可以三元操作符嵌套

```
s_Speed = s_Level > 5 ? s_Level > 10 ? 15 : 10 : 5;
//等价于
if (s_Level > 5)
{
	if(s_Level > 10)
		s_Speed = 15;
	else
		s_Speed = 10;
}
else 
{
	s_Speed = 5
}
```

但是嵌套得多了就太过分了，不建议。

## 38. 创建并初始化对象

```
class Entity
{
private:
	std::string m_Name;
public:
	Entity() : m_Name("Unknown") {}
	Entity(const std::string& name) : m_Name(name) {}
	
	const std::string& GetName() const { return m_Name; }
};

int main()
{
	Entity e1;
	Entity e2("Cherno");
	Entity e3 = Entity("Cherno"); //三种创建对象的方法
}
```

作用域甚至可以你写个空的，只有花括号的：

```
int main()
{
	{
		Entity entity("Cherno");
	} //这个entity会在这个花括号后迎来它的末日
}
```

如果你要在堆上创建对象：

```
Entity* entity = new Entity("Cherno");
```

## 39. new

```
Entity* e = new Entity;
//或者
Entity* e = new Entity();

Entity* e = new Entity[50];

//在c中，可能会这样开辟内存：
Entity* e = (Entity*)malloc(sizeof(Entity));
//这两个的区别在于new它不仅开辟了空间，它还会调用构造函数，而c风格的只是开辟。
```

`new`和`malloc`都会返回空指针（void*），c风格它需要转换类型，new的话，我也不知道为什么，就这样写着吧。

还有，记得delete

```
delete e;
delete[] e;
```

`new`和`delete`其实是运算符重载。

`delete`会调用c中`free()`函数释放内存，一般`free()`用来释放`malloc()`开辟的内存。

## 40. 隐式转换与explicit关键字

```
class Entity
{
private:
	std::string m_Name;
	int m_Age;
public:
	Entity(const std::string& name)
		:m_Name(name), m_Age(-1) {}
	Entity(int age)
		: m_Name("Unknown"), m_Age(age) {}
};

void PrintEntity(const Entity& entity)
{
	//Printing
}

int main()
{
	Entity a = 22;
	Entity b = "Cherno"; //通过隐式转换你可以这样创建对象
	
	PrintEntity(22);
	PrinEntity("Cherno"); /*这样是不行的，因为这个字面量是个const char[],而隐式转换只能做一次，这里从const char[]转std::string转Entity*/
	PrintEntity(std::string("Cherno")); //这样子，帮它转一次
}
```

它确实大大简化了你代码，但是太变态了，尽量避免用它。

对于`explicit`关键字：

```
	explicit Entity(const std::string& name)
		:m_Name(name), m_Age(-1) {}
	explicit Entity(int age)
		: m_Name("Unknown"), m_Age(age) {}
```

会禁止你使用隐式地调用构造函数，只允许显式调用。

## 41. 运算符重载

直接上例子：

```
class Vector2
{
private:
	float m_X, m_Y;
public:
	Vector2(float x, float y)
		: m_X(x), m_Y(y) {}
	Vector2 operater+(const Vector2& other) const
	{
		return Vector2(m_X + other.m_X, m_Y + other.m_Y);
	}
};
```

你甚至可以理解为operater+是函数名，`operater+(other);`是可行的。

类外也可以写重载，只是你得多写一个自身对象实例的参数而已。

## 42. this关键字

在一个属于一个类的函数或方法内，我们可以使用this，this是一个指向当前对象实例的指针，该方法属于这个对象实例。

类内调用类外函数用this也是可以的：
```
void PrintEntity(const Entity& e)
{
	// Printing
}

class Enity()
{
public:
	Entity()
	{
		PrintEntity(*this);
	}
}；
```

## 43. 栈作用域生存期

基于栈的变量，在我们一出作用域就被释放了。

要是我就是想在堆上创建但又想它自动释放怎么办，写一个特殊的指针类，叫unique_ptr，作用域指针：

```
class ScopedPtr
{
private:
	Entity* m_Ptr;
public:
	ScopedPtr(Entity* ptr)
		: m_Ptr(ptr) {}
	~ScopedPtr()
	{
		delete m_Ptr;
	}
};

int main()
{
	Entity* e = new Entity(); // 原先堆开辟的方法
	
	ScopedPtr* e(new Entity());
	
    // 或者为了和原先的样子更像，使用隐式转换
    ScopedPtr* e = new Entity();
    /* 很明显，e是开辟在栈，作用域结束就会被销毁，调用析构函数delete了new的那块空间，实现全自动 */
}
```

## 44. 智能指针

首先，**unique_ptr**：
```
#include <memory> // 记得包含我！

class Entity
{
public:
	void Print() {}
};

int main()
{
	{
		// std::unique_ptr<Entity> entity(new Entity());
        /* 注意，unique_ptr不可以隐式构造，源码带explicit的 */
		/* 这不是好的方式，出于异常安全，建议使用↓ */
		std::unique_ptr<Entity> entity = std::make_unique<Entity>();
		//它就和原始指针一样使用
		entity->Print();
	}
	std::cin.get();
}
```

这个指针的效用是自动化的帮你销毁你new的东西，在离开作用域后，不用自己delete，非常有用且低开销。

```
std::unique_ptr<Entity> entity = std::make_unique<Entity>();
std::unique_ptr<Entity> e0 = entity; 
/* 
这是不被允许的，unique_ptr的拷贝构造和=重载是被delete的，原因是一个unique_ptr指向的内存被释放，其他的unique_ptr的指向就是一个已经被释放的地方了。
*/
```

如果你就想要复制这个智能指针让多个指针都指向同一个地方，可以使用**shared_ptr**，它的工作方式是引用计数，它可以跟踪你的指针有多少个引用，当引用计数到0，这个指向的地方就会被删除,比如，你创建了一个shared_ptr，又创建了一个shared_ptr来复制它，这时引用计数就是2，当第一个shared_ptr死的时候，引用计数器会减少1，当减到0时，内存被释放。

```
std::shared_ptr<Entity> sharedEntity = std::make_shared<Entity>();
// 这里使用make_shared是它会帮你分配一块控制块内存存储引用计数。
std::share_ptr<Entity> sharedEntity(new Entity());
// 这样子创建就还需要你自己分配控制块，不推荐。

// shared_ptr可以被复制，是正常工作的,它们会在你所有引用销毁后自动释放内存。
std::shared_ptr<Entity> e0 = sharedEntity;
```

最后一个，**weak_ptr**

```
std::weak_ptr<Entity> weakEntity = sharedEntity;
```

weak_ptr一样可以复制shared_ptr，但与shared_ptr复制shared_ptr的区别是，后者会增加引用计数，前者不会。

shared_ptr由于控制块需要计数，所以它有一定开销。

当你要声明一个堆分配的对象，并且你不希望自己来清理，不想显式地调用delete，或者显式地管理内存时，使用智能指针，尽量使用unique_ptr，优先级unique > shared。

## 45. 复制与拷贝构造

假如有一个类：

```
class String
{
private:
	char* m_Buffer;
public:
	String()
	{
		m_Buffer = new char;
	}
	~String()
	{
		delete m_Buffer;
	}
}

int main() 
{
	String string1；
	String string2 = string1;
	
	std::cin.get();
} // 当运行到此处也就是作用域外的时候，程序崩溃了。
```

崩溃的原因是，当使用`String string2 = string1;`时，C++只是简单地把string1的成员变量m_Buffer复制给string2的m_Buffer，导致它们指向了同一个内存空间，当离开作用域，string1和string2被释放，string1释放时已经把它指向的内存空间delete了，但释放string2时再次调用析构函数再次delete同一个地方，导致程序崩溃。

这种就是浅拷贝了。

C++会默认提供一个拷贝构造函数，长这样：

```
String(const String& other)
	: m_Buffer(other.m_Buffer), m_Size(other.m_Size) {}
```

如果我们决定不需要拷贝构造函数，不允许复制，我们可以将拷贝构造函数声明为delete

```
String(const String& other) = delete;
```

但是为了防止以上情况导致程序崩溃，我们需要自己写一个拷贝构造函数，进行深拷贝：

```
String(const String& other)
{
	m_Buffer = new char;
	*m_Buffer = *(other.m_Buffer);
}
```

用值传递的函数实际上在传递的时候也需要copy，也就是会调用拷贝构造函数，不过你可以使用引用来传递对象，这样最好了，更好的是再来个const，毕竟你没有理由到处复制，他们会拖慢你的程序：`void PrintString(const String& string)`，简称const引用。

## 46. 箭头操作符

一般用法：

```
class Entity
{
public:
	void Print() const {}
}
int main
{
    Entity e;
    Entity* ptr = &e;
    
    (*ptr).Print();
    //或者
    ptr->Print();
}
```

在你定义了智能指针的时候，->作为运算符，你可以重载它：

```
class ScopedPtr
{
private:
	Entity* m_Ptr;
public:
	ScopedPtr(Entity* ptr)
		: m_Ptr(ptr) {}
	~ScopedPtr()
	{
		delete m_Ptr;
	}
	Entity* operator->() //->重载
	{
		return m_Ptr;
	}
};
int main()
{
	ScopedPtr entity = new Entity(); /* 我希望能和普通的指针一样使用智能指针，就需要重载-> */
	entity->Print(); //自定义的指针也可以使用->
}
```

## 47、48. 动态数组（std::vector）及优化

std::vector的工作方式是当它超过你指定的容量大小时，它会在内存中创建一个比第一个大的新数组，把所有东西复制到这里，然后删除旧的那一个。你可以像这样不断地向它添加东西，它就是一个大小可以改变的数组。

vector一般不能得到最佳性能，设计这个类的人优先考虑的不是快。

下面是例子：

```
#include <vector> //注意包含

struct Vertex
{
	float x, y, z;
	
	Vertex(float x, float y, float z)
		: x(x), y(y), z(z) {}
};
std::ostream& operator<<(std::ostream& stream, const Vertex& vertex) // 这只是重载<<方便打印
{
	stream << vertex.x << ", " << vertex.y << ", " << vertex.z;
	return stream;
}
int main
{
	std::vector<Vertex> vertices; 
	/* 对于动态数组，数组存对象还是指针要视情况而定，技术上最优是存储对象，这样存储的对象是在一段连续的内存里的 */
	
	// 往动态数组添加东西
	vertices.push_back({ 1, 2, 3 }); // 后面{1， 2， 3}类似给结构体赋值的样子
	vertices.push_back({ 4, 5, 6 });
	for (int i = 0; i < vertices.size(); i++)
		std::cout << vertices[i] << std::endl;
		
	// 对于数组的遍历，可以使用↓
	for (Vertex& v : vertices) // 用引用会避免复制数组，造成开销
		std::cout << v << std::endl;
		
	// 如果想删除某个元素
	vertices.erase(vertices.begin() + 1) // 这会删去第二个元素
}
```

传递数组给函数时尽量确保通过引用传递，值传递会复制整个数组到这个函数，造成不必要的开销。

因为vector调整大小需要将数组复制到新的地方，这就是代码拖慢的原因，优化的思路就是要避免复制对象。

```
vertices.push_back(Vertex(1, 2, 3));
/* push第一个元素时是将在main函数的栈帧中创建的一个Vertex复制到vector中,如果我们可以在适当的位置构造Vertex就可以优化这一步复制 */

vertices.push_back(Vertex(4, 5, 6));
/* 因为vector默认大小为1，push第二个元素它需要扩容，前面提到vector扩容需要把整个数组copy到新的内存段，又因为此时vector有两个元素，便复制了两次 */

vertices.push_back(Vertex(7, 8, 9));
/* 此处会复制三次，三次push一共调用了六次拷贝构造，如果我们可以给定它的大小就可以避免因为扩容而复制，这是一个优化策略 */
```

根据第二个优化策略，也就是要避免因为扩容导致的复制，可以使用：

```
std::vector<Vertex> vertices; 
vertices.reserve(3); // 告诉vector我需要3个Vertex的内存大小
vertices.push_back(Vertex(1, 2, 3));
vertices.push_back(Vertex(4, 5, 6));
vertices.push_back(Vertex(7, 8, 9));
```

给定大小后会从原先的6次copy变为3次。

根据第一个优化策略，也就是将在main函数栈帧创建的对象直接在vector对应位置创建，避免这一次复制：

```
std::vector<Vertex> vertices; 
vertices.reserve(3);
vertices.emplace_back(1, 2, 3); // 使用emplace只需要传之前构造函数的参数列表
vertices.emplace_back(4, 5, 6);
vertices.emplace_back(7, 8, 9);
```

经两次优化从原先的六次copy变为0次。

当我们做优化的时候，需要知道实际发生了什么，并且了解环境，知道如何使用可用工具来优化它。

## 49、50. 使用库（静态库与动态库）

Cherno讨厌使用包管理器链接到非本地的远程仓库或者什么什么，理想情况是库应该在本地甚至是项目文件内，以便可以直接编译运行项目的应用程序，不需要考虑包管理去下载需要的库，我也非常认同，就算是在本地但不在项目文件内我也不喜欢。

如果有源代码可以在本地编译为静态库或者动态库链接，没有源码直接链接二进制文件也可以。

### 静态库

此处先讲二进制文件，当你下载了某个库的二进制文件，一般包含有includes和library。

include目录是一堆我们需要使用的头文件，这样我们就可以实际使用预构建的二进制文件中的函数。

library目录有那些预先构建的二进制文件，一般包含动态库和静态库，静态链接意味着这个库会被放到你的可执行文件中，而动态链接库是在运行时被链接的。

静态链接在技术上可以产生更快的应用程序，通常静态链接是最好的选择。

Cherno习惯在解决方案目录下创建Dependencies文件夹，分类库目录并在对应目录下将而include文件夹以及lib文件夹copy过来。

windows下.dll是动态库，.lib后缀是静态库。

然后在项目属性内设置二进制文件路径，如果使用绝对路径别人clone你的仓库时路径就会有问题，所以使用相对路径，比如↓

$(SolutionDir)Dependencies\GLFW\include

库路径同理。

然后在cpp文件中包含`#include "GLFW\glfw3.h"`之类的。

（此文件实际目录为$(SolutionDir)Dependencies\GLFW\include\GLFW\glfw3.h）

库文件除了路径，还需要将库文件名在linker的input项中设置。

Cherno的习惯是只要是在解决方案内的都用引号，如果是完全的外部依赖，不在编译器中和解决方案一起编译的用尖括号。

### 动态库

对于GLFW，动态库有两个文件，一个是.dll结尾，一个是glfw3dll.lib，后者就是一堆.dll文件的指向（意思应该是指向了函数定义的位置），有了它就不用在运行时去检索所有东西的位置。

然后在linker的input项设置添加glfw3dll.lib，并将.dll文件copy至可执行文件（.exe）同一目录下。头文件库文件路径与静态库一致。

.dll文件的搜索位置可以设置，但可执行文件目录下是自动检索的路径，与可执行文件放同级一定没问题。

## 51. 创建并使用库

这一集讲述了如何在一个解决方案创建多个项目，其中一个项目作为库的项目，然后将它链接到可执行文件中。

作为库的项目要确保将配置类型设置为静态库（.lib）而不是可执行文件（.exe），否则生成的会是.exe文件。

在vs中可以自动化完成库的链接只需要在可执行文件的项目设置的add选项中的reference中勾选作为库的项目即可。

这一集直接通过再看一遍复习会来得更快：[【51】【Cherno C++】【中字】C++中创建与使用库（VisualStudio多项目）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Vp4y1s7uD?spm_id_from=333.788.videopod.sections&vd_source=e5aa67477135266ae5c66fe7c8b108f3)

 

## 52. 处理多返回值

如果想让函数返回多个值，可以选择定义一个结构体然后返回这个结构体，结构体内包含你想要的那些值，或者写一个void函数，将你想要返回的值传引用或者传指针写入函数的参数列表，让函数直接处理你要的值，或者返回一个数组，数组和结构体的区别是数组元素都是同类型的值。

简单的例子：

```
struct weekleaf
{
	int m_a;
	int m_b;
};
weekleaf Fuc1()
{
	return { 1, 2 };
}
void Fuc2(int& a, int& b)
{
	a = 1;
	b = 2;
}
void Fuc3(int* a, int* b)
{
	if(a)
		*a = 1; //检查是否空指针
	*b = 2;
}
int* Fuc4()
{
	return new int[]{ 1, 2 };
}
std::array<int, 2> Fuc5() // 要#include <array>
{
	std::array<int, 2> results;
	results[0] = 1;
	results[1] = 2;
	return results;
}
std::vector<int> Fuc6() // 要#include <vector>
{
	std::vector<int> results;
	results[0] = 1;
	results[1] = 2;
	return results;
}
int main()
{
	int a, b;
	Fuc2(a, b);
	Fuc3(nullptr, &b); /* 指针的好处是你可以传个null然后做一个检查,而引用需要你传一个有效的值 */
}
```

讨论一种通用的方法，来返回两个不同类型的变量，它们的类型可能会变化，也可能不会变化，这两种方法分别是`std::tuple`元组和`std::pair`对组，Cherno讲这里的时候好像有点微醺，按关键词去Goolge/Bing检索一下吧。。。

总而言之，还是返回一个结构体可读性强一些。

## 53. 模板(templates)

模板允许你定义一个可以根据你的用途进行编译的模板，基于一套规则，你可以让编译器帮你写代码。

为什么要使用模板，当一些函数的具体实现一致但参数列表内传入值的类型不同，而我们又都需要使用它们，就需要写很多个仅参数列表的变量类型不同的重载函数，而使用模板可以一次解决这个问题

举个例子：

这里是一些仅参数列表的变量类型不同的重载函数↓

```
void Print(int value)
{
	std::cout << value << std::endl;
}
void Print(std::string value)
{
	std::cout << value << std::endl;
}
void Print(float value)
{
	std::cout << value << std::endl;
}
```

使用模板就可以减少重复工作

```
template<typename T>
void Print(T value)
{
	std::cout << value << std::endl;
}
int main()
{
	Print<int>(5); // 显式地指定T的类型
	Print(5); // 隐式地指定T的类型
}
```

其实模板这不是一个真的函数，**只有当我们实际调用它的时候，这些函数才被真的创建**，在调用的时候会基于传递的参数这个函数才被创建出来，并作为源代码被编译。`tempalte<class T>`与`tempalte<typename T>`是一样的。

如果我想创造一个类，类内有个数组成员，我希望在编译的时候才确定它的大小，此时使用模板就会有很好的相性，看我操作：

```
template<int N>
class array
{
private:
	int m_Array[N];
public:
	int GetSize() const { return N; }
};
int main()
{
	Array<5> array;
	std::cin.get();
}
```

还有更牛逼的，我可以在编译时才指定成员数组的类型：

```
template<typename T, int N>
class array
{
private:
	T m_Array[N];
public:
	int GetSize() const { return N; }
};
int main()
{
	Array<int, 5> array;
	std::cin.get();
}
```

不要疯狂地复杂地用模板和滥用模板，这样会让可读性变得很低。

## 54. 堆栈内存比较

堆和栈是内存的两个区域，栈的大小会预定义，堆也是一个预定义默认值的区域，但它的大小可以生长，随着应用程序的进行而改变。

在栈上分配内存只是把东西堆在一起，依次在栈上开辟空间存储变量会发现它们的地址都靠在一起，这也是为什么栈分配非常快，当我创建一个int，大小四个字节，栈指针就会移动四个字节并返回地址。

在堆上分配要做一大堆事情，包括记录以及返回你要的地址，new关键字实际调用了`malloc`，这是一个巨大的函数，做了很多事情。你的程序会维护一个叫空闲列表（free list）的东西，它会跟踪哪些内存块是空闲的以及它们在哪，当你`malloc`的时候它就会浏览free list然后做一大堆事情返回给你一个空闲内存的地址。

反正，在栈上分配内存就像一条cpu指令，非常快，而在堆上分配需要做很多事情。如果可以，尽量在栈上分配。

## 55. 宏

关于这些模板或者宏之类的以及C++新特性，它只是工具，不是非得用，总而言之，大道至简，写好代码就行，别装（doge）。

宏，预处理命令，在编译前做文本编辑工作，举个例子：

```
int main()
{
	std::cin.get();
}
```

与

```
#define WAIT std::cin.get
int main()
{
	WAIT;
}
```

是一个效果，这个宏就是在你编译前把你文本换了。

它不局限于语句，它可以是任何代码，任何文本：

```
#define OPEN_CURLY {
int main()
OPEN_CURLY
	std::cin.get();
}
```

这样很傻逼↑

还可以写的和函数似的，但本质还是文本替换：

```
#define LOG(x) std::cout << x << std::endl
int main()
{
	LOG("Hello"); // std::cout << "Hello" << std::endl;
	std::cin.get();
}
```

另一个用法：

```
#define PR_DEBUG

#ifdef PR_DEBUG
#define LOG(x) std::cout << x << std::endl
#else // 这里写#elif defined(PR_RELEASE)更加明确些
#define LOG(x)
#endif
```

如果定义了PR_DEBUG，则将`LOG(x)`替换成`std::cout << x << std::endl`，如果没有，`LOG(x)`替换成空，用于release版本去掉一些调试代码，减少开销。

上述更好的写法是：

```
#define PR_DEBUG 1

#if PR_DEBUG == 1
#define LOG(x) std::cout << x << std::endl
#else
#define LOG(x)
#endif
```

这样就不需要将`#define PR_DEBUG`注释以去掉调试代码，直接将`#define PR_DEBUG 1`设为`#define PR_DEBUG 0`即可。

折叠代码：

```
#if 0

// code

#endif
```

中间的代码都会被禁用。

写宏不一定要都在一行，可以用反斜杠\新开一行，反斜杠是换行字符的转义：

```
#define MAIN int main() \
{\
	std::cin.get();\
}
```

宏只能在一行，所以不可以直接Enter换行，所以要先用\转义Enter来换行，在文本上看起来确实换行了，但经过\转义实际上没换行，预处理会把这些东西看成一行。还有不要在转义字符\后打空格，这样就是对空格的转义了，Enter没转义实际上就不在一行了。

## 56. auto关键字

写auto编译器会自动推断类型：

```
auto a = 5;
auto b = a;
```

滥用会降低可读性，要是全是auto的话。

当你见到一个非常长非常复杂的类型时：

```
class Device {};
class DeviceManager
{
private:
	std::unordered_map<std::string, std::vector<Device*>> m_Devices;
public:
	const std::unordered_map<std::string, std::vector<Device*>>& GetDevices()
	{
		return m_Devices;
	}
};
int main()
{
	using DeviceMap = std::unordered_map<std::string, std::vector<Device*>>;
	// 或者
	// typedef std::unordered_map<std::string, std::vector<Device*>> DeviceMap;
	
	DeviceManager dm;
	const DeviceMap& devices = dm.GetDevices();
	// 或者
	// const auto& devices = dm.GetDevices();
}
```

使用`using`和`typedef`改名，或者直接`auto`。

## 57. 静态数组（std::array）

一个有五个元素大小的整数数组。

```
std::array<int, 5> data;
data[0] = 2;
data[4] = 1;
```

它和旧的C风格的数组是一样的，分配在栈上，且`std::array`最优化了，它非常快，它相对旧的普通数组来说，它作为一个类，它有很多方法可以使用。

## 58. 函数指针

函数指针是将一个函数赋值给一个变量的方法。大概可以理解为，编译后函数是一堆cpu指令，存在于二进制文件的某个地方，函数指针就是这个函数的位置。

```	
void HelloWorld()
{
	std::cout << "Hello World!" << std::endl;
}
int main()
{
	auto function = HelloWorld; // 直接函数名即可；auto最好用的一集
	// 然后你就可以这样调用它
	function();
}
```

这里的`auto`其实本质上是这样的：`void(*)()`，这也是个类型，`auto function`等同`void(*function)()`，这个东西的位置真的很怪，它起别名甚至是这样的↓

```
typedef void(*)() HelloWorldFunction; // 错误示例
typedef void(*HelloWorldFunction)(); // 正确实例
```

然后你就可以这样用这个类型↓

```
// 原先的样子
void(*function)() = HelloWorld;
// 改名后
HelloWorldFunction function = HelloWorld;
```

对于有参数列表的函数：

```
void HelloWorld(int a)
{
	std::cout << "Hello World! Value: " << a << std::endl;
}
int main()
{
	typedef void(*HelloWorldFunction)(int);
	void(*function)(int) = HelloWorld;
	HelloWorldFunction function = HelloWorld;
	function(8);
}
```

这下爱上`auto`了。

有了函数指针，函数可以作为参数传入函数，举个例子：

```
void PrintValue(int value)
{
	std::cout << "Value: " << value << std::endl;
}
void ForEach(const std::vector<int>& values, void(*func)(int))
{
	for (int value : values)
		func(value);
}
int main()
{
	std::vector<int> values = { 1, 5, 4, 2, 3 }; // 一种初始化列表
	ForEach(values, PrintValue);
}
```

## 59. lambda

lambda本质上是我们定义的一种叫做匿名函数的方式，用这种方式不需要实际创建一个函数，就像一个快速的一次性函数。lambda是我们不需要通过函数定义，就可以定义一个函数的方法。

对于上一节的例子，可以用lambda：

```
ForEach(values, [](int value) { std::cout << "Value: " << value << std::endl; });
```

lambda表达式可以赋值给变量：

```
auto lambda = [](int value) { std::cout << "Value: " << value << std::endl; };
ForEach(values, lambda);
```

lambda比较复杂，建议查cppreference。

## 60. 为什么不使用using namespace std

加`std::`很明显会更好认呐，当你写了多个命名空间，包含有同个名称的函数时，不加前缀可能会导致调用混乱，还有不要在头文件内加`using namespace`，相思了吗这么干，谁知道你的include会包含了什么呢，你把这些命名空间用在了你原本没打算用的地方，编译报错追踪问题会非常困难。

两个原因，我想知道我用的是什么，我想避免将来可能出现的错误。

**如果你就是想用，就在一个足够小的作用域下使用。**

## 61. 名称空间

名称空间的主要目的是避免命名冲突。在C语言中没有命名空间，为了区分同返回值同函数名同参数列表不同函数体的函数改名，将不同特征嵌入函数名，比如`apple_init()`和`orange_init()`。C++中则可以使用命名空间。

```
namespace apple {
	void print(const char* text)
	{
		std::cout << text << std::endl;
	}
}
```

也可以嵌套：

```
namespace apple { namespace functions {
	void print(const char* text)
	{
		std::cout << text << std::endl;
	}
} }
```

这个是Cherno喜欢的写法，它喜欢将namespace写在一行，这样命名空间内的函数就只需要一级缩进，不需要二级、三级等等。

如果想用apple内的函数就只需要写上`apple::`

```
int main()
{
	apple::print("Hello");
	std::cin.get();
}
```

`::`是命名空间的操作符；类本身也是命名空间。

对于作用域，除了指定namespace，也可以单独指定命名空间的某个函数：

```
int main()
{
	using apple::print;
}
```

也可以给命名空间取别名：

```
{
	namespace a = apple;
	a::print("Hello");
}
```

对于嵌套的命名空间：

```
{
	using namespace apple::functions;
	
	// 也可以分开写
	using namespace apple;
	using namespace functions;
	
	// 取别名
	namespace a = apple::funcions;
	
	apple::functions::print("Hello");
	a::print("Hello");
}
```

## 62. 线程

使用多线程需要添加头文件`#include <thread>`

等待一个线程完成它的工作的方法 :`worker.join()`

例子：

```
#include <iostream>
#include <thread>

static bool is_Finished = false;
void DoWork() {
    using namespace std::literals::chrono_literals;
    while (is_Finished) {
        std::cout << "hello" << std::endl;
        std::this_thread::sleep_for(1s); // 等待一秒
    }
}

int main() {
    std::thread worker(DoWork); // 开启多线程操作
    
    std::cin.get(); // 此时工作线程在疯狂循环打印，而主线程此时被cin.get()阻塞
    
    is_Finished = true; // 让worker线程终止的条件，如果按下回车，则会修改该值，间接影响到另一个线程的工作。

    worker.join();  // join:等待工作线程结束后，才会执行接下来的操作(指该语句之后的语句)

    std::cin.get();
}
```

## 63. 计时

建议在需要用计时的时候使用`chrono`库，除非你想要更低的开销更高的精度，可以使用平台特定的库，比如`win32`里的。

举例：

```
#include <iostream>
#include <chrono>
#include <thread>

int main() {
    using namespace std::literals::chrono_literals; //有了这个，才能用下面1s中的's'
    
    auto start = std::chrono::high_resolution_clock::now();
    std::this_thread::sleep_for(1s);
    auto end = std::chrono::high_resolution_clock::now();
    std::chrono::duration<float> duration = end - start;
    std::cout << duration.count() << "s" << std::endl;
    
    std::cin.get();
}
```

通过记录代码块两端的时刻，作差即可得到花费的时间。

可以写一个计时器类，放在函数实现的起始位置，就可以自动化计时函数花费的时间：

```
struct Timer
{
    std::chrono::time_point<std::chrono::steady_clock> start, end;
    std::chrono::duration<float> duration;

    Timer()
    {
        start = std::chrono::steady_clock::now();
    }

    ~Timer()
    {
        end = std::chrono::steady_clock::now();
        duration = end - start;

        float ms = duration.count() * 1000;
        std::cout << "Timer took " << ms << " ms" << std::endl;
    }
};
void Function()
{
    Timer timer;
    for (int i = 0; i < 100; i++)
        std::cout << "Hello\n"; //相比于std::endl更快
}

int main()
{
    Function();
}
```

## 64. 多维数组

二维数组就是存储数组的数组：

```
int *array = new int[5]; // 使用new时其实并没有创建数组，只是开辟一段特定数组大小的内存空间
int **a2d = new int *[5]; // 存储5个int指针的数组
for (int i = 0; i < 5; i++)
    a2d[i] = new int[5]; // allocate the memory
    
for (int y = 0; y < 5; y++)
{
    for (int x = 0; x < 5; x++)
    {
        a2d[y][x] = 2;
    }
}
```

三维数组就是存储存储数组的数组的数组：

```
int ***a3d = new int **[5]; // 存储5个int指针的指针的数组   三维数组
for (int i = 0; i < 5; i++)
{
    a3d[i] = new int *[5];
    for (int j = 0; j < 5; j++)
    {
        // int **ptr = a3d[i];
        // ptr[j] = new int[5];
        a3d[i][j] = new int[5];
        for (int n = 0; n < 5; n++)
        {
        	a3d[i][j][n] = 2;
        }
    }
}
```

释放二维数组时，不能直接`delete[] a2d`，这样会释放掉存储数组位置的指针，实际数据没有释放，导致内存泄漏，正确做法：

```
for (int i = 0; i < 5; i++) // 需要先释放真正的多维数组
	delete[] a2d[i];
delete[] a2d;
```

通过上例知道，二维数组存储的数据并非是连续的一整块，二维数组中每个元素存储着new出来的不同内存块，这些内存块不一定是连续的，当我们遍历访问二维数组时，每遍历5个元素就会跳到另一个内存位置来读写数据，这会导致cache miss（缓存不命中），这意味着我们将**浪费时间**从ram中获取数据，所以，用二维数组遍历会比只分配一个一维数组慢得多，如果数据都在一起，定位数据会有更多的cache hits（缓存命中）以及更少的cache miss（缓存不命中），程序会更快：

```
int *array = new int[5 * 5];  //二维
// for (int i = 0; i < 5 * 5; i++)
// {
//     array[i] = 2;
// }
for (int y = 0; y < 5; y++)   //数组优化，将二维数组转化为一维数组
{
    for (int x = 0; x < 5; x++)
    {
        array[y * 5 + x] = 2;
    }
}
```

## 65. 排序

`std::sort(vec.begin(), vec.end(), 谓语)`，谓语可以设置排序的规则，谓语可以是内置函数，也可以是lambda表达式。默认是从小到大。

```
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
    std::vector<int>  values = {3, 5, 1, 4, 2};
    std::sort(values.begin(), values.end());  
    for (int value : values)
    std::cout << value << std::endl; // 1 2 3 4 5
    std::cin.get();
}
```

谓语使用内置函数`std::greater`则会从大到小：

```
#include <functional>

std::sort(values.begin(), values.end(),std::greater<int>()); 
```

通过lambda灵活排序具体看cppreference。

## 66. 类型双关

一般变量而言，会有类型，int4字节，double8字节，对于c++而言，你可以做一些绕过类型做一些事情，毕竟它们只是存在内存的数据，你可以直接去控制内存。这是一种c语言风格的原始类型转换方法（或者不该叫类型转换）：**取地址，换成对应类型的指针，再解引用**。

这和如下

```
int a = 50;
double value = (double)a;
```

样子的类型转换是有区别的，这里是int类型的a变量存储的数据转换成以double类型的变量存储表示，在memory中a是`32 00 00 00`，四字节，自左往右位数升高，16进制（正常写应该是长00000032这样），而转换后的value在memory中是`00 00 00 00 00 00 49 40`，可以发现内存里数据不一样了。

而这一节想做的事情是，取已经存在的内存（`32 00 00 00`）,想在这段内存上，让这段地址被当作double看待（`32 00 00 00 00 00 00 00`）：

也就是将同一块内存的东西通过不同类型的指针给取出来（把int看成double会多出四字节不属于我们的内存，这很糟糕，只为了演示）：

```
#include <iostream>
int main()
{
    int a = 50;
    double value = *(double*)&a;
    std::cout << value << std::endl;

    std::cin.get();
}
//可以用引用，这样就可以避免拷贝成一个新的变量：（只是演示，这样做很糟糕）
#include <iostream>
int main()
{
    int a = 50;
    double& value = *(double*)&a;
    std::cout << value << std::endl;

    std::cin.get();
}
```

把一个结构体当成数组来看（结构体的成员变量存在于一段连续的内存，和数组一样）：

```
#include <iostream>
struct Entity
{
    int x, y;
};

int main()
{
    Entity e = {5, 8};
    int *position = (int *)&e;
    std::cout << position[0] << ", " << position[1] << std::endl;

    int y = *(int *)((char *)&e + 4); // 也就是这个数组的第二个元素8
    std::cout << y << std::endl;
}
```

由于是`char*`，`char`类型占1字节，char指针+4就是偏4个字节，也就是偏了一个int类型，自然就到了8，和`*((int*)&e + 1)`同理。

## 67. 联合体

通常union是匿名使用的，但是匿名union不能含有成员函数。

union的特点是**共用内存** 。可以像使用结构体或者类一样使用它们，也可以给它添加静态函数或者普通函数、方法等待。然而**不能使用虚方法**，还有其他一些限制。

例子：

```
#include <iostream>
int main() 
{  
    union //匿名使用，不写名字
    {
        float a;
        int b;
    };  
    a = 2.0f;  //共享内存，a被赋值了一个浮点数，整形的b也被复制了一个浮点数
    std::cout << a << '，' << b << std::endl;
    //输出： 2，107165123
    //原因：int b取了组成浮点数的内存，然后把它解释成一个整型（类型双关）
}
```

union里的成员会共享内存，分配的大小是按最大成员的sizeof, 上面例子有两个变量，改变其中一个另外一个里面对应的也会改变。如果是这两个成员是结构体struct{ int a,b} 和 int k , 如果k=2 ; 对应a也=2 ，b不变。

union在这种情况下很好用，就是用不同的结构表示同样的数据 ，那么你可以按照获取和修改他们的方式来定义你的union结构：

```
#include <iostream>
struct Vector2
{
    float m_x, m_y;
};

struct Vector4
{
    union // 不写名称，作为匿名使用
    {
        struct //第一个Union成员
        {
            float x, y, z, w;
        };
        struct // 第二个Union成员，与第一个成员共享内存
        {
            Vector2 a, b; // a和x，y的内存共享，b和z，w的内存共享
        };
    };
};
void PrintVector2(const Vector2 &vector)
{
    std::cout << vector.m_x << ", " << vector.m_y << std::endl;
}

int main()
{
    Vector4 vector = {1.0f, 2.0f, 3.0f, 4.0f}; // 可以把你的union当成四个浮点数
    PrintVector2(vector.a); // 也可以把你的union当成两个vector2结构体
    PrintVector2(vector.b);
    vector.z = 500;
    PrintVector2(vector.a);
    PrintVector2(vector.b);
}
```

因为占用同一段内存，所以在可以使用类型双关的时候，使用union，可读性更强。

## 68. 虚析构函数

先给出例子：

```
#include <iostream>

class Base
{
public:
    Base() { std::cout << "Base Constructor\n"; }
    virtual ~Base() { std::cout << "Base Destructor\n"; }
};

class Derived : public Base
{
public:
    Derived()
    {
        m_Array = new int[5];
        std::cout << "Derived Constructor\n";
    }
    ~Derived()
    {
        delete[] m_Array;
        std::cout << "Derived Destructor\n";
    }

private:
    int *m_Array;
};

int main()
{
    Base *base = new Base();
    delete base;
    std::cout << "-----------------" << std::endl;
    Derived *derived = new Derived();
    delete derived;
    std::cout << "-----------------" << std::endl;
    Base *poly = new Derived();
    delete poly; // 基类析构函数中如果不加virtual，则此处会造成内存泄漏
    
    // 打印结果：
    // Base Constructor
    // Base Destructor
    // -----------------
    // Base Constructor
    // Derived Constructor
    // Derived Destructor
    // Base Destructor
    // -----------------
    // Base Constructor
    // Derived Constructor
    // Derived Destructor //基类析构函数中如果不加virtual，子类的虚构函数不会被调用
    // Base Destructor
}
```

如果用基类指针来引用派生类对象，也就是`Base *poly = new Derived();`，那么基类的析构函数必须是 virtual 的，否则 C++ 只会调用基类的析构函数，不会调用派生类的析构函数。

继承时，要养成的一个好习惯就是，**基类析构函数中，加上virtual。**

若派生类有一个成员int数组在堆上分配东西，在构造函数中分配，在析构函数中删除。运行当前代码发现没有调用那个派生析构函数，但是它调用了派生类的构造函数。我们在构造函数中分配了一些内存，但是永远不会调用派生析构函数delete释放内存，因为析构函数没有被调用，永远不会删除堆分配数组，造成内存泄漏。

## 69. C++的类型转换

c语言风格的类型转换：

```
// 隐式转换
double value = 5.25;
int a = value;

// 显式转换
double value = 5.25;
int a = (int)value; // （）内写想转成的类型，后跟变量
```

c++风格的类型转换（cast）：

cast分为`static_cast` `dynamic_cast` `reinterpret_cast` `const_cast`，它们不做任何c风格类型转换不能做的事情，它们可能做的可能比c风格做的事情多，比如运行时检查转换是否有效等，但最终实际的结果也是一个成功的类型转换而已，它们只是一些语法糖。

`static_cast`，静态类型转换。

`reinterpret_cast`，可以把这段内存重新解释成别的东西，也就是前面章节提到的类型双关，它可以做类型双关章节提到的所有事情。

`const_cast`，移除或添加变量的const限定。

随便举个例子：

```
double value = 5.25;
int a =static_cast<int>(value);
```

## 72. 预编译头文件

预编译（percomplied header）可以解决一个项目中同一个头文件被反复编译的问题。使得写代码时不需要一遍又一遍的去`#include`那些常用的头文件并再次编译，而且能**大大提高编译速度**。

预编译头文件中的内容最好都是**不需要反复更新修改的东西**比如`<vector>`这个头文件一般就不会被修改，如果是反复更新的文件那每次都得重新编译，预编译的作用就起不到了。

预编译头文件的使用会隐藏掉这个cpp文件的依赖。比如用了`#include <vector>`，就清楚的知道这个cpp文件中需要vector的依赖，而如果放到预编译头文件中，就会将该信息隐藏，也就是会不清楚这个cpp都需要用上哪些头文件。

vs中的使用流程：

1. 新建一个pch.h（可以自己命名，改成其他名称也没问题）文件，将需要预编译的头文件写进pch.h，例如：

   ```
   // pch.h文件内容
   #pragma once
   
   #include <iostream>
   #include <memory>
   #include <thread>
   ```

2. 新建一个pch.cpp文件，包含pch.h：

   ```
   #include "pch.h"
   ```

3. 右键pch.cpp，在属性->Percompiled Headers->Percomplied Header中设置为Create。

4. 右键项目，在属性->Percompiled Headers->Percomplied Header中设置为Use，属性->Percompiled Headers->Percompiled Header File中设置为pch.h。

视频讲解的会更详细：[【72】【Cherno C++】【中字】C++的预编译头文件_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1eu411f736?spm_id_from=333.788.videopod.sections&vd_source=e5aa67477135266ae5c66fe7c8b108f3)

## 73. dynamic_cast

dynamic_cast是专门用于沿继承层次结构进行的强制类型转换，并且**dynamic_cast只用于多态类类型**。

### 多态

多态的概念：通俗来说，就是**多种形态**，具体点就是去完成某个行为，当不同的对象去完成时会产生出不同的状态。

多态是在不同继承关系的类对象，去调用同一函数，产生了不同的行为。那么在继承中要构成多态还有两个条件：

1. 必须通过基类的指针或者引用调用虚函数。
2. 被调用的函数必须是虚函数，且派生类必须对基类的虚函数进行重写。

在28节虚函数中例子中的函数形参列表需要的是父类但允许传入子类，父类方法被标记为虚函数且在子类重写，传入子类对象就不会出现调用错误的方法的情况，因为存在虚函数表。

---

回到dynamic_cast，如果我只是想派生类转换为基类，这很简单，因为派生类包含了基类所有的东西，但如果你反过来，想把基类转换为派生类，这就比较复杂了，因为你现在只有一个基类指针，你知道这个指针指向一个基类实例，但是你不知道这个指向的具体是什么，它可以只是基类对象，也可以是某个派生类的对象，比如下面的例子中，它可以是Base类实例，它也可以是Player实例或者是Enemy实例。

如果我这个Base指针指向的实际是一个Enemy实例，我使用强转把它转换为Player类就可能导致程序崩溃，因为Enemy类不一定有着和Player类相同的成员变量和成员函数，自此，我们得使用dynamic_cast。

dynamic_cast会检查基类指针指向的是否是指定派生类实例，如果是，会成功转换，如果不是，会返回NULL指针：

```
#include<iostream>
class Base
{
public:
    virtual void print(){}
};
class Player : public Base
{
};
class Enemy : public Base
{
};
int main()
{
    Player* player = new Player();
    Base* base = new Base();
    Base* actualEnemy = new Enemy();
    Base* actualPlayer = new Player();
    
    Player* p0 = dynamic_cast<Player*>(actualEnemy); 
    // 实际Enemy转Player，返回了NULL
    
    Player* p1 = dynamic_cast<Player*>(actualPlayer);
    // 实际Player转Player，返回了一个有效值
}
```

那dynamic_cast是如何知道它们具体是什么的呢，要做到这点是因为它存储了运行时类型信息（runtime type information，RTTI），所以dynamic_cast实际上是增加开销的，检查类型是否匹配也需要时间。

根据返回NULL的性质，dynamic_cast实际上可以用来做验证，检查是否为这个类型，例如：

```
Player* p0 = dynamic_cast<Player*>(actualEnemy); 
if (p0) // 如果类型不匹配，即p0为NULL，跳过这个action
{
	// action
}
```

## 74. 基准测试

编写一个计时器对代码测试性能，通过计时器测量代码块耗时，确保你测量的东西，实际上是被编译的代码,release模式下编译器会帮我们做代码上的优化，可能会剥离某些代码，甚至完全更改代码。

该计时器实现与63节计时中的几乎一致：

```
#include <iostream>
#include <memory>
#include <chrono>   //计时工具
#include <array>

class Timer {
public:
    Timer() {
        m_StartTimePoint =  std::chrono::high_resolution_clock::now();
    }
    ~Timer() {
        Stop();
    }
    void Stop() {
        auto endTimePoint = std::chrono::high_resolution_clock::now();
        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimePoint).time_since_epoch().count();
        //microseconds 将数据转换为微秒
        //time_since_epoch() 测量自时间起始点到现在的时长
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimePoint).time_since_epoch().count();
        auto duration = end - start;
        double ms = duration * 0.001;  // 转换为毫秒数
        std::cout << duration << "us(" << ms << "ms)\n";
    }
private:
    std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimePoint;
};

int main() 
{
    struct Vector2 {
        float x, y;
    };

    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        Timer timer;
        for (int i = 0; i < sharedPtrs.size(); i++) {
            sharedPtrs[i] = std::make_shared<Vector2>();
        }
    }

    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        Timer timer;
        for (int i = 0; i < sharedPtrs.size(); i++) {
            sharedPtrs[i] = std::shared_ptr<Vector2>(new Vector2());
        }
    }

    {
        Timer timer;
        std::array<std::unique_ptr<Vector2>, 1000> sharedPtrs;
        for (int i = 0; i < sharedPtrs.size(); i++) {
            sharedPtrs[i] = std::make_unique<Vector2>();
        }
    }
}
```

## 75. 结构化绑定

结构化绑定（Structured Bindings）是**C++17**的新特性，能让我们更好地处理多返回值。可以在将函数返回为tuple、pair、struct等结构时且赋值给另外变量的时候，**直接得到成员**，而不是结构。

老方法：

使用返回结构体处理多返回值详见52节处理多返回值。

使用tuple，pair，tie：

```
#include <iostream>
#include <string>
#include <tuple>

// std::pair<std::string,int> CreatPerson() // 只能有两个变量
std::tuple<std::string, int> CreatPerson() // 可以理解为pair的扩展
{
    return {"Cherno", 24};
}

int main()
{
    //元组的数据获取易读性差，还不如像结构体一样直接XXX.age访问更加可读。
    // std::tuple<std::string, int> person = CreatPerson();
     auto person = CreatPerson(); //用auto关键字
     std::string& name = std::get<0>(person);
     int age = std::get<1>(person);

    //tie 可读性好一点
     std::string name;
     int age;
     std::tie(name, age) = CreatPerson();
}
```

C++17新方法：

```
#include <iostream>
#include <string>
#include <tuple>


std::tuple<std::string, int> CreatPerson() 
{
    return {"Cherno", 24};
}

int main()
{
    auto[name, age] = CreatPerson(); //直接用name和age来储存返回值
    std::cout << name;
}
```

新特性使得处理多返回值不需要创建一个只会使用一次的结构体，直接得到成员更加简洁优雅。

编译时确保C++版本切换到C++17。

## 76. 处理optional数据

`std::optional`是C++17的新东西，用来讲述c++如何处理那些可能存在，也可能不存在的数据，或者是一种我们不确定的类型。

举读取文件的例子：

在没有`std::optional`之前，在读取文件内容的时候，往往需要判断读取是否成功，常用的方法是传入一个引用变量或者判断返回的std::string是否为空：

```
#include <iostream>
#include <fstream>
#include <string>

std::string ReadFile(const std::string &fileapath, bool &outSuccess) 
{
    std::ifstream stream(filepath);
    //如果成功读取文件
    if (stream) {
        std::string result;
        // read file
        stream.close();
        outSuccess = true;  //读取成功，修改bool
        return result;
    }
    outSuccess = false; //反之
    return std::string() // 这里是string的构造函数，表示创建一个新string
    // 或者返回空字符串 return ""
}

int main() 
{
    bool flag;
    auto data = ReadFile("data.txt", flag);
    //如果文件有效，则接着操作
    if (flag) 
    {
    }
}
```

使用`std::optional`：

```
#include <iostream>
#include <fstream>
#include <optional>
#include <string>

std::optional<std::string> ReadFileAsString(const std::string& filepath)
{
    std::ifstream stream(filepath);
    if (stream)
    {
        std::string result;
        // read file
        stream.close();
        return result;
    }
    return {};
    //如果文本存在的话，它会返回所有文本的字符串，如果不存在或者不能读取，则返回std::optional {}。
}

int main()
{
     std::optional<std::string> data = ReadFileAsString("data.txt");
    //auto data = ReadFileAsString("data.txt"); //可用auto关键字
    if (data)
    {   
        std::cout << "File read successfully!" << std::endl;      
    }
    else
    {
        std::cout << "File could not be opened!" << std::endl;
    }

    std::cin.get();
}
```

如果文件无法打开，或者文件的特定部分没有被设置或读取，也许我们有一个默认值，这很常见。此时就可以使用value_or()函数。其作用就是：如果数据确实存在于std::optional中，它将返回给我们那个字符串。如果不存在，它会返回我们传入的任何值。

删除data.txt,此时文件不存在打不开，则被设置为默认值：

```
#include <iostream>
#include <fstream>
#include <optional>
#include <string>

std::optional<std::string> ReadFileAsString(const std::string& filepath)
{
    std::ifstream stream(filepath);
    if (stream)
    {
        std::string result;
        //getline(stream, result);
        stream.close();
        return result;
    }

    return {}; //返回空
}

int main()
{
    std::optional<std::string>  data = ReadFileAsString("data.txt");

    std::string value = data.value_or("Not present");
    std::cout << value << std::endl;

    if (data)
    {
        std::cout << "File read successfully!" << std::endl;
    }
    else
    {
        std::cout << "File could not be opened!" << std::endl;
    }
}
//输出
Not present
File could not be opened!
```

## 85. 左值与右值

左值（lvalue）是有某种存储支持的变量，可以理解为有地址的量，右值（rvalue）是临时量。

```
int i = 10;
// i在内存中有实际位置，是左值；10，数字字面量，短暂存在的值，是右值。
```

Cherno的例子：

```
#include <iostream>

int& GetValue()
{ // 左值引用
    static int value = 10;
    return value;
}

void SetValue(int value) {}

void PrintName(std::string& name)
{ // 非const的左值引用只接受左值
    std::cout << "[lvalue]" << name << std::endl;
}

void PrintName(std::string&& name)
{ // 右值引用不能绑定到左值，只接受右值
    std::cout << "[rvalue]" << name << std::endl;
}

int main()
{
    int i = GetValue();
    GetValue() = 5;
    /*
    如果函数为：
    int GetValue()
    {
    	return 10;
    }
    返回的则是个右值，GetValue() = 5;则会报错，因为这个表达式必须是可修改的左值。
    */

	SetValue(i);  // 左值参数调用
    SetValue(10); // 右值参数调用，当函数被调时，这个右值会被用来创建一个左值
    
	/* 
	关于const，const引用可以同时接受左值和右值。
    int& a = 10;会报错，因为你不能用将右值赋给左值引用，但有个特殊的规则，虽然不能用左值来引用右值，但加上const就变得可行了：
    const int& a = 10;
    实际情况是,编译器可能会用你的存储创建一个临时变量，然后把它赋值给那个引用：
    int temp = 10;
    const int& a =temp // const int& a = 10;实际上做的事
    所以它不可避免地创建了一个左值（temp），同时支持了左值与右值。
    */
    
    std::string firstName = "Yan";
    std::string lastName = "Chernikov";
    std::string fullname = firstName + lastName;
    // 表达式firstName + lastName是右值
    PrintName(fullname);             // 接受左值
    PrintName(firstName + lastName); // 接受右值
}
```

关于右值引用（与左值引用不同在于多一个&），也就是上例中`void PrintName(std::string&& name) {}`，它的优势在于优化：

如果我们知道传入的是一个临时对象的话，那么我们就不需要担心它们是否活着，是否完整，是否拷贝。我们可以简单地偷它的资源，给到特定的对象，或者其他地方使用它们。因为我们知道它是暂时的，它不会存在很长时间 而如果如上使用const string& str，虽然可以兼容右值，但是却不能从这个字符串中窃取任何东西！因为这个str可能会在很多函数中使用，不可乱修改！（所以才加了const）。

---

> 部分章节先留坑，以后学了再填。
