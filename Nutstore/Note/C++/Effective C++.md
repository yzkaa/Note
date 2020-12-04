# 第0章 导读

## 术语

1. 声明式（declaration）是告诉编译器某个东西的名称和类型（type），但略去细节。

```C++
extern int x;							//对象（object）声明式
std::szie_t numDigits(int number);		//函数（function）声明式
class Widget;							//类（class）声明式

template<typename T>					//模板（template）
class GraphNode;						//“typename”的使用
```

​		std命名空间是几乎所有C++标准程序库元素的栖身处。C89标准程序库也适用于C++而继承自C的符号（size_t等）可能存在于全局（global）作用域或std内，或两者兼具取决于哪个头文件被包含（#include）。

​		size_t只是一个typedef，是C++计算个数时用的某种不带正负号（unsigned）类型。

2. 每个函数的声明揭示其签名式（signature），即参数和返回类型。numDigits的函数签名是std::size_t(int)，意为“函数获得一个int并返回一个std::size_t”。官方定义签名并没有包括返回类型。

3. 定义式（definition）的任务是提供编译器一些声明式所遗漏的细节。对对象而言，定义式是编译器为此对象拨发内存的地点。对函数（function）或函数模板（function template）而言定义式提供了代码本体。对类（class）和模板类（class template）而言，定义式列出他们的成员。

```c++
   int x;											//对象定义式
   std::size_t numDigits(int number){				//函数定义式
   	std::size_t digitsSoFar = 1;				//此函数返回其参数的数字个数
   	while((number/=10)!=0) ++digitsSoFar;		//例如十位数返回2，百位数返回3
   	return digitsSoFar;
   }
   
   class Widget{								//class定义式
   	public:
   		Widget();
   		~Widget();
   		...
   };
   template<typename T>							//template定义式
   classGraphNode{
   public:
   	GraphNode();
   	~GraphNode();
   	...
   }
```

4. 初始化（initialization）是“基于对象初值”的过程。对用户自定义类型的对象而言，初始化由构造函数执行。默认（default）构造函数是一个可被调用而不带任何实参的函数。这样的构造函数要不没有参数，要不就是每个参数都有缺省值：

```c++
class A{
public A();									//default构造函数
};

class B{
public:
	explicit B(int x=0,bool b=true);		//default构造函数
};

class C{
public:
    explicit C(int x);						//不是default构造函数
};
```

5. explicit用来阻止构造函数被执行隐式转换（implicit type conversions），禁止编译器执行非预期的类型转换，但仍可以进行显式转换（explicit type conversions）：

```c++
void doSomething(B bObject);

B bObj1;
doSomething(bObj1);					
B bObj2(28); 								

doSomething(28);					//隐式转换，报错
doSomething(B(28));					//显式转换


```

6. 拷贝（copy）构造被用来“以同型对象初始化自我对象”，copy assignment操作符被用来“从另一个同型对象中拷贝其值到自我对象”：

   

```c++
class Widget{
public:
    Widget();								//default构造函数
    Widget(const Widget& rhs);				//copy构造函数
    Widget& operator=(const Widget& rhs);	//copy assignment操作符
    ...
};
Widget w1;									//调用default构造函数
Widget w2(w1);								//调用copy构造函数
w1=w2;										//调用copy assignment操作符
Widget w3 = w2;								//调用copy构造函数
```

​		赋值符号也可以用来调用copy构造函数。copy构造和copy赋值有所区别，如果一个新对象被定义（w3），一定会有个构造函数被调用，不可能调用赋值操作。如果没有新对象被定义，就不会有构造函数被调用，则会调用copy赋值。

​		copy构造函数定义了一个对象如何以值传递（passed by value）。

```c++
bool hasAcceptableQuality(Widget w);
...
Widget aWidget;
if(hasAcceptableQuality(aWidget)) ...
```

​		参数w是以值（by value）方式传递给hasAcceptableQuality，所以在上述调用中aWiget被复制到w内。这个复制动作由Widget的copy构造函数完成。

​		Pass-by-value意味着“调用copy构造函数”。尽量使用以常量引用传递（Pass-by-reference-to-const）来代替以值传递。

7. STL是标准模板库（Standard Template Library），是C++标准库程序的一部分，致力于容器、迭代器、算法及相关机能。许多相关机能以函数对象（function objects）实现，那是“行为像函数”的对象。这样的对象来自于重载operator() （function call 操作符）的classes。

8. 不明确行为/未定义行为（undefined behavior）由于各种因素，某些C++构件的行为没有定义：无法预估运行期会发生什么事。

   ```c++
   int *p=0;			//p是一个null指针
   std::cout<<*p;		//对一个null指针取值（dereferencing）会导致不明确行为
   
   char name[] = "Darla"; //长度为6的字符数组
   char c = name[10];	   //指向一个无效的数组索引导致不明确行为。
   ```

9. C++没有提供接口为语言元素，接口（interface）一般为函数的签名（signature）或class的可访问元素（public接口，protectd接口，private接口），或是针对某template类型参数需为有效的一个表达式。是只一般性的设计观念。