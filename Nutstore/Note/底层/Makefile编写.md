# Linux下的makefile编写

## 前言
本人记笔记习惯使用OneNote，在学习LinuxC++过程中发现deepin上没有大佬开发或者移植，本人技术也不精，所以决定写博客记笔记（只是习惯问题，并没有对写博客有什么偏见或者不满）。

学习书籍原地址[《跟我一起写Makefile》](https://blog.csdn.net/haoel/article/details/2886)，感谢原作者的付出。

## 环境&配置
操作系统：deepin v20 64位社区版 
GNU Make版本： 4.2.1


## 一、编译和链接
**1.编译（Compile）**

 - 无论是c还是c++，首先要把源文件编译成中间代码文件，即object file，Windows下为.obj后缀文件，Linux下为.o后缀文件。
 - 编译时，编译器需要的是语法正确，函数与变量的声明的正确。**一般来说每个源文件都应该对应于一个中间目标文件（Object File）。**
 - 若函数或变量未被声明，编译器会发出一个警告，但是依然可以生成中间目标文件。

	

**2.链接（Link）**
	

 - 把编译好的所有object file合成可执行文件的过程。
 - 链接时主要是链接函数和全局变量，所以我们可以使用中间目标文件来链接我们的应用程序。链接器不需要知道函数或全局变量所在的源文件，只需要知道所在的中间目标文件。
 - 在源文件过多的情况下，生成的中间目标文件很多，这会使链接操作变得麻烦，这时候可以**将这些中间目标文件打包成“库文件”（Library File），文件后缀为.lib，当然这是Windows下的称呼，Linux下为Archive File，文件后缀为.a**。
 - 若函数或变量未被声明，链接器找不到声明，则会报链接错误代码（Linker Error），在VC下这种错误一般是：Link 2001错误
## 二、MakeFile的语法及规则
### 1.Makefile的规则
 - 假设我们有多个.cpp文件，多个.h文件，我们要写一个Makefile来告诉make命令如何编译和链接这几个文件。规则是：
	1.如果这个工程没有被编译过，那么我们的所有c文件都要编译并被链接。
	2.如果这个工程的某几个.cpp文件被修改那么我们只编译被修改的cpp文件，并链接目标程序。
	3.如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。
### 2.MakeFile语法
	target	...	:	prerequisites	...
	[TAB]command
			 ...

target是一个目标文件，可以是中间目标文件，也可以是可执行文件，还可以是一个标签（Label）。
prerequisites是生成target所需要的文件或者目标。
例如用a.cpp和b.h生成a.o 则target是a.out，prerequisites是a.cpp和b.h，写成：

```bash
	a.out	:	a.cpp b.h
		g++ -c a.cpp
```
command是make需要执行的命令。（任意的Shell命令）

这是一个文件依赖关系，一个或多个target依赖于prerequisite中的文件，其生成规则定义在command中。
**如果prerequisite中的一个以上的文件比target文件更**（第四声）**新或者不存在target文件，那么command将会被执行。**




#### 一个示例
假如我们有三个头文件和四个cpp文件，我们为了完成前面描述的规则，Makefile应该这样编写：

```bash
edit : a.o b.o c.o d.o
        g++ -o edit a.o b.o c.o d.o

a.o : a.cpp a1.h
        g++ -c a.cpp

b.o : b.cpp a2.h
        g++ -c b.cpp

c.o : c.cpp a3.h a1.h
        g++ -c c.cpp

d.o : d.cpp a1.h a2.h a3.h
        g++ -c d.cpp

clean :
        rm edit a.o b.o c.o d.o
```
（这里原文提到了“\”续行符，它的作用是把第二行从开始第一个字符，包括空格，连接到前一行上面，可以增加Makefile的易读性）

编写完成后，直接在该目录下输入make，只要没有错误就可以生成可执行文件edit，如果要删除可执行文件和中间目标文件，执行make clean就可以了。
在以上Makefile文件中：
* target包括edit，a.o，b.o，c.o，d.o
* prerequisite文件包括冒号后面那些.cpp和.h文件
* command则是 g++命令和rm命令

每一个.o文件都有一组依赖文件（prerequisite），而这些.o文件又是edit的以依赖文件。**依赖文件实质上就是说明了这些文件是由哪些文件生成/更新的。**

定义好依赖关系后，后续的command行定义了如何生成目标文件的操作系统命令（shell命令）。
**注意！！！command语句行一定要以TAB开头，当然vim的智能缩进已经帮助我们解决了这个问题。**

注意最后的clean，它不是一个文件，只不过是一个动作名字，其冒号后什么都没有，这样make也不会去寻找它的依赖，也就不会执行它的命令。要执行此命令。就要在make后明确指出这个label的名字（make clean）。我们也可以定义一些其他的label，定义不同的命令，实现一些其他的功能。

##  三、make的工作方式
当我们输入make命令，make将会做以下几件事：
1. make会在当前目录下找名字叫“Makefile”或者 “makefile”的文件。
2. 如果找到这个文件，那么它会开始找文件中第一个目标文件（target），并把这个文件作为最终的目标文件。
3. 如果目标文件不存在或旧于它的依赖文件，那么make就会执行后面定义的command来生成该文件。
4. 如果目标文件所依赖的中间目标文件（object file，.o）也存在,那么make会在当前文件中查找该中间目标文件的依赖性，如果找到则同3。

make会一层层地寻找依赖关系，直到最终编译出第一个目标文件为止。在寻找的过程中若是出现错误（找不到依赖文件，shell语法错误，makefile语法错误等），make会直接退出并报错。

## 四、在Makefile中使用变量
	objects = a.o b.o c.o d.o
	edit : $(objects)
	    g++ -o edit $(objects)
	
	a.o : a.cpp a1.h
	    g++ -c a.cpp
	
	b.o : b.cpp a2.h
	    g++ -c b.cpp
	
	c.o : c.cpp a3.h a1.h
	    g++ -c c.cpp
	
	d.o : d.cpp a1.h a2.h a3.h
	    g++ -c d.cpp
	
	clean :
	    rm edit $(objects)

如果Makefile变得复杂，使用变量可以让修改变得轻松，减少了需要修改的位置和次数，只需要修改变量内的内容以及相应的依赖就可以了。

## 五、让make自动推导
make会自动识别并自己推导命令，没必要在每一个中间目标文件后都写上.cpp命令，例如：

```bash
	objects = a.o b.o c.o d.o
	edit : $(objects)
        g++ -o edit $(objects)

	a.o :  a1.h
        g++ -c a.cpp

	b.o :  a2.h
        g++ -c b.cpp

	c.o :  a3.h a1.h
        g++ -c c.cpp

	d.o :  a1.h a2.h a3.h
        g++ -c d.cpp

	clean :
        rm edit $(objects)
```

这种方法是make的“隐晦规则”
上面的clean可以写成

```bash
.PHONY : clean
clean :
	-rm edit $(objects)
```
.PHONY表示clean是一个伪目标文件。-rm表示将后面的文件无条件删除。
## 六、Makefile的另类写法
利用make的自动推导文件的功能，可以将Makefile写成：

```bash
objects = a.o b.o c.o d.o
	edit : $(objects)
        g++ -o edit $(objects)

    a.o c.o d.o : a1.h
    b.o d.o : a2.h
    c.o d.o : a3.h

.PHONY : clean
clean :
	-rm edit $(objects)
```
这样写的缺点是依赖关系凌乱，且如果文件增多，很难理清楚依赖关系。
## 七、清空目标文件的规则
**每个Makefile中都应该写一个清空目标文件的规则**，这不仅便于重编译，㛑很利于保持文件的清洁。
**clean文件不要放在Makefile文件最前面，会让make以为这是最终目标，clean永远放在Makefile文件的最后**



==学习书籍原地址[《跟我一起写Makefile》](https://blog.csdn.net/haoel/article/details/2886)，感谢原作者的付出！==

# Makefile总述
## 一、Makefile里有什么
主要包含  ：**显示规则、隐晦规则、变量定义、文件指示和注释。**
1. **显示规则**
	显示规则说明如何生成一个或多个目标文件，写Makefile的时候都是在写显示规则。
2. **隐晦规则**
	因为make的自动推导功能，可以将Makefile简化。
3. **变量的定义**
	变量一般都为字符串，类似于c的宏，调用时使用$(变量名)格式调用。
4. **文件指示**
	包括三个部分：
	1） 在一个Makefile中引用另一个Makefile，就像include；
	2）根据某些情况指定Makefile中的有效部分，就像#if；
	3）定义一个多行命令。
5. **注释**
	**Make中只有行注释**，用“#”字符进行注释。
## 二、Makefile的文件名
默认情况下，make会在目录下寻找名字为“GNUmakefile”、“makefile”和“Makefile”的文件。
**最好使用“Makefile”，首字母大写，有种醒目的感觉；最好不要使用“GNUmakefile”，只有GNU的make对这个文件名敏感；有些make只对“makefile”敏感。**

若要自定义文件名，可以在make后加上“-f”或者“--file”参数来指定你的自定义Makefile。

```bash
make -f myMake.Linux
```
或者

```bash
make --file myMake.Linux
```

## 三、引用其他的Makefile
语法：

```bash
include <filename>
```
filename可以使当前操作系统Shell的文件模式（可以包含路径和通配符）
在include前可以有一些空字符，但不能是[TAB]键开始；include可以同时包含几个Makefile，和变量差不多。例如：

1. 目录下有多个.mk后缀的Makefile文件（a.mk b.mk c.mk）：

	```bash
	include *.mk
	```
	等价于

	```bash
	include a.mk b.mk c.mk 
	```
2. 满足1的基础上，定义一个变量$(makefiles)，里面包含了d.mk和e.mk：

	```bash
	#定义变量
	makefiles = d.mk e.mk
	```
	则要包含所有的Makefile文件，可以这样写：
	```bash
	#包含所有makefile
	include *.mk $(makefiles)
	```
	等价于
	```bash
	include a.mk b.mk c.mk d.mk e.mk
	```

当make执行时，会将include所指出的其他Makefile的内容暂时安置在当前位置，就像c/c++的#include。如果文件都没有指定绝对路径或是相对路径的话，make会在当前目录下首先寻找，如果当前目录下没有找到，那么：
1. 如果make后有“-I”或者“--include-dir”参数，make会在参数指定的目录下寻找。
4. 如果目录<prefix>/include（一般是/usr/local/bin或者/usr/include） 存在的话，make也会去找。
5. 如果文件没有找到，make会生成一条警告信息，然后继续载入其他文件，完成Makefile的读取后，make会再次重新查找没有找到的文件，如果还是没有，那么make会生成致命信息，退出运行。可以在include前加一个“-”号让make忽略那些找不到的文件，继续执行下去：

	```bash
	-include <filename>
	```
	**“-”在之前的clean命令中使用过，它的意思是：无论在命令执行过程中出现什么错误，都不要报错并继续执行。**
	-include和其他版本的make兼容的相关命令是sininclude，作用相同。
## 四、环境变量MAKEFILES
MAKEFILES变量作用和include类似，其值是其他的Makefile，空格分隔。与include不同的地方在于**用这个方法引入的Makefile里的“target”不会起作用，如果MAKEFILES中定义的文件有错误，make也不会报错。**
建议不要使用MAKEFILES，因为当你使用这个变量，并且make时，所有的Makefile都会被影响。如果有时候Makefile出现的异常，可以看看当前环境中有没有这个变量。
## 五、make的工作方式
GNU make（其他的make类似）：
* 第一阶段
	1. 读取所有的Makefile；
	2. 读入被include的其它Makefile；
	3. 初始化文件中的变量；
	4. 推导隐晦规则，并分析所有规则；
	5. 为所有的目标文件创建依赖关系链。
	
* 第二阶段
	6. 根据依赖关系，决定哪些目标要重新生成；
	7. 执行生成命令。

在第一个阶段中，make会将定义的变量在当前位置展开，但make并不会完全马上展开，**如果变量出现在依赖关系的规则中，那么仅当这条依赖被决定要使用了，变量才会在其内部展开。**





==学习书籍原地址[《跟我一起写Makefile》](https://blog.csdn.net/haoel/article/details/2886)，感谢原作者的付出！==

# 书写规则
规则包含两个部分，**依赖关系**和**生成目标的方法**。

Makefile中应只有一个最终目标，也就是**第一条规则中的第一个目标**。

## 一、规则的语法
```bash
	targets	...	:	prerequisites	...
	[TAB] command
			 ...
```
或：
```bash
	targets	...	:	prerequisites ; command
	[TAB] command
			 ...
```
* targets是文件名，以空格分开，可以使用通配符，可以是多个文件。

* command是命令行，如果单独作为一行，必须以[TAB]键开头，如果在prerequisite后用逗号分隔。
* prerequisite是依赖文件或目标。

如果命令太长，可以使用续行符“\”，在一行内make没有字符上限。
一般来说，make会以UNIX的标准Shell，也就是/bin/sh来执行命令。

## 二、在规则中使用通配符
make支持“*”，“？”，“[...]”三种通配符这和UNIX的B-Shell相同。


波浪号“～”可以表示：
3. 当前用户的home目录；
4. 某用户的宿主目录，如～yzk/Document表示用户yzk的宿主目录下的Document文件夹。

make也支持“～”。而在Windows或是MS-DOS下，用户没有宿主目录，则波浪号所指的位置随环境变量“HOME”的改变而改变。

**注意：在变量中使用*时，如果是这样的写法**：

```bash
objects = *.o
```
**\*.o不会被展开！！！，objects的值就是\*.o**
如果要包含所有.o文件，可以这样写：

```bash
objects := $(wildcard *.o)
```
:=的意思是覆盖之前的变量

wildcard关键字的作用就是扩展通配符。它是Makefile中的关键字。

## 四、文件搜寻
可以将源文件分类并存放在不同的目录中，然后在Makefile中文件前加上路径，告诉make该去哪里找。
Makefile文件中有个特殊变量“VPATH”，它可以指定文件存放的位置，如果没有指明这个变量，make只会在当前目录下寻找。

```bash
VPATH = src:../headers
```
路径之间用冒号分隔。
**当然，如果make在当前目录下找不到，才会去VPATH中指定的目录寻找**

另一种方法是使用“vpath”关键字（全小写），它可以指定不同的文件在不同的目录中搜索。它的使用方法有三种：
1. `vpath <pattern> <dierctories>`
	为符合模式\<pattern>的文件指定搜索目录\<directories>
2. `vpath <pattern>`
	清除符合模式\<pattern>的文件的搜索目录
3. `vpath` 
	清除所有已被设置好了的文件搜索目录

vpath 使用方法中的\<pattern>可以包含“%”字符，它代表匹配零或若干字符，如“%.h”表示所有以“.h”结尾的文件。（**如果某文件在当前目录没有找到的话**）。
可以连续使用vpath关键字，指定不同的搜索策略，make会按照vpath的先后顺序来执行搜索。

>  vpath %.c foo 
>  vpath % blish 
>  vpath %.c bar 
>  其表示“.c”结尾的文件，先在“foo”目录，然后是“blish”，最后是“bar”目录。
>  vpath %.c foo:bar 
>  vpath % blish 
>  而上面的语句则表示“.c”结尾的文件，先在“foo”目录，然后是“bar”目录，最后才是“blish”目录。 
>  ---该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 四、文件搜寻

VPATH和vpath的区别在于，VPATH是寻找目录下的所有文件，而vpath增加了限制条件，只在一些文件中进行寻找。相同点在于，make都会优先寻找当前目录。

**注意：vpath不会自动寻找文件依赖，需要在命令行加上 -I（是大写的i不是L）+依赖所在文件夹，中间不空格**


## 五、伪目标
之前的“clean”目标就是一个“伪目标”。

```bash
clean ：
	rm *.o edit
```
使用make clean可以执行这个伪目标的命令，但是不会生成目标文件，因为其没有依赖。伪目标取名不能和文件名重名。
为了避免和文件重名的情况，我们可以使用一个特殊的标记“.PHONY”来显式指明这个目标是伪目标，让make知道不管有没有这个文件，这个目标就是伪目标。

```bash
.PHONY : clean
```
只要有这个声明，不管有没有叫“clean”的文件，只有使用“make clean”才能运行“clean”目标。

```bash
.PHONY : clean
clean:
	rm *.o edit
```
伪目标同样可以作为默认目标/最终目标，只要放在第一个。且伪目标可以有依赖文件。
如果Makefile需要生成多个可执行文件，又只想敲一个make了事，且所有的目标文件都写在一个Makefile中，那么可以使用伪目标：

```bash
all : test1 test2 test3
.PHONY : all

test1 : test1.o
        g++ -o test1 test1.o

test1.o : test1.cpp
        g++ -c test1.cpp

test2 : test2.o
        g++ -o test2 test2.o

test2.o : test2.cpp
        g++ -c test2.cpp

test3 : test3.o
        g++ -o test3  test3.o

test3.o : test3.cpp
        g++ -c test3.cpp

```
执行make则会生成 test1，test2，test3三个可执行文件。
伪目标总是被执行，所以其依赖总是不如伪目标新，所以每次都会被生成。
伪目标亦可以成为其他目标的依赖。

```bash
.PHONY: cleanall cleanobj cleandiff 
 cleanall : cleanobj cleandiff 
 	rm program 
 cleanobj : 
 	rm *.o 
 cleandiff : 
	 rm *.diff 
#该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 五、伪目标
```

“cleanobj”和“cleandiff”这两个伪目标有点像“子程序”的意思。我们可以输入“make cleanall”和“make cleanobj”和“make cleandiff”命令来达到清除不同种类文件的目的。

## 六、多目标
Makefile的规则支持多目标，使用自动化变量“$@”表示目前规则中所有的目标的集合。

```bash
 bigoutput littleoutput : text.g 
 	generate text.g -$(subst output,,$@) > $@ 
```
   上述规则等价于：
 ```bash
 bigoutput : text.g 
 	generate text.g -big > bigoutput 
 littleoutput : text.g  generate text.g -little > littleoutput 
 #该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 六、多目标
 ```
subst是一个Makefile函数，作用是截取字符串，“\$@”表示目标的集合，类似数组，从"\$@"中依次取出目标并在命令中使用。

## 七、静态模式
静态模式可以更加容易地定义多目标的规则，可以让规则变得更加的有弹性和灵活。
语法：
```
<targets ...>: <target-pattern>: <prereq-patterns ...>
	<commands>
	...
```
targets是目标的一个集合，可以有通配符；
target-pattern指明targets的模式，也就是目标集模式；
prereq--patterns是目标的依赖模式，它对target-pattern形成的模式在进行依次依赖目标的定义。

如果<target-pattern>定义成“%.o”，意思就是在<targets>中都是以".o"结尾的文件；
如果<prereq--patterns>定义成"%.c"，意思就是对<target-pattern>所形成的目标集进行二次定义，计算方法是，取<target-pattern>中的“%”（即舍去后缀），并加上<prereq--patterns>中的后缀，行成新的集合（%.c）。
例如：

```bash
objects = foo.o bar.o 
all: $(objects) 
$(objects): %.o: %.c 
	$(CC) -c $(CFLAGS) $< -o $@ 
#该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 七、静态模式
```
上面的例子指明目标从\$(object)中获取	“\%.o”表明要所有以“.o”结尾的目标，也就是“foo.o,bar.o”，也就是变量\$object 集合的模式，而依赖模式“%.c”则取模式“%.o”的“%”，也就是“foo bar”，并为其加下“.c”的后缀，于是，我们的依赖目标就是“foo.c bar.c”。
而命令中的“\$<”和“\$@”则是自动化变量，“\$<”表示所有的依赖目标集（也就是“foo.c bar.c”），“\$@”表示目标集（也就是“foo.o bar.o”）。于是，上面的规则展开后等价于下面的规则：

```bash
foo.o : foo.c 
 $(CC) -c $(CFLAGS) foo.c -o foo.o 
 bar.o : bar.c 
 $(CC) -c $(CFLAGS) bar.c -o bar.o 
 #该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 七、静态模式
```

试想，如果我们的“%.o”有几百个，那种我们只要用这种很简单的“静态模式规则”就可
以写完一堆规则，实在是太有效率了。“静态模式规则”的用法很灵活，如果用得好，那会一
个很强大的功能。再看一个例子：

```bash
  files = foo.elc bar.o lose.o 
  $(filter %.o,$(files)): %.o: %.c 
  	$(CC) -c $(CFLAGS) $< -o $@ 
  $(filter %.elc,$(files)): %.elc: %.el 
 	 emacs -f batch-byte-compile $< 
 #该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 七、静态模式
```


\$(filter \%.o,\$(files))表示调用 Makefile 的 filter 函数，过滤“$filter”集，只要其中模式为“%.o”的内容。这个例字展示了 Makefil中更大的弹性。
（第七节没怎么懂，学到后面再回来看看）
## 八、自动生成依赖性
在Makefile中，依赖关系可能会包含一系列的头文件，依赖关系多了，维护就很麻烦。为了避免这种情况发生我们可以使用C/C++编译的一个功能。大多数的C/C++编译器都支持一个“-M”的选项，可以自动寻找源文件包含的头文件并生成依赖关系。
如果是GNU的C/C++编译器，建议使用“-MM”，不然会把标准库头文件也包含进来。

GNU组织建议把编译器为每一个源文件的自动生成的依赖关系放到一个文件中，为每一个“.c”文件都生成一个“.d”的Makefile文件，用于存放.c文件的依赖关系。
这里给出了一个模式规则来产生“.d”文件：

```bash
%.d: %.c 
	@set -e; rm -f $@; \ 
	$(CC) -M $(CPPFLAGS) $< > $@.$$$$; \ 
	sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \ 
	rm -f $@.$$$$ 
	#该模式规则摘自 陈皓《跟我一起写Makefile》第五章 书写规则 八、自动生成依赖性
```

所有的".d"文件依赖于".c"文件，“rm -f $@”的意思是删除所有的目标，也就是".d"文件；
第二行为每个依赖文件“\$<”，也就是“.c”文件生成依赖文件，“$@”表示模式“%.d”文件，如果又一个文件是name.c，那么“%”就是“name”。“\$$$$”表示一个随机符号，第二行生成的文件有可能是“name.d.12345”；
第三行使用sed命令做了一个替换。（查了也没懂这个语法，之后回来再看看）

总而言之，这个模式要做的事就是在编译器生成的依赖关系中加入".d"文件的依赖，即把依赖关系

```bash
main.o : main.c defs.h
```
转成

```bash
main.o main.d : main.c defs.h
```
于是“.d”文件也会自动更新并自动生成了，也可以向“.d”文件加入命令，使其包含完整的依赖规则。接下来，我们要把这些规则放到主Makefile中。可以使用“include”命令引入别的Makefile文件，例如：

```bash
sources = foo.c bar.c
include $(sources:.c=.d)
#该示例摘自 陈皓《跟我一起写Makefile》第五章 书写规则 八、自动生成依赖性
```
“.c=.d”的意思是做一个替换，把变量中所有的“.c”都换成".d"。**最先载入的".d"文件中的目标会成为默认目标。**