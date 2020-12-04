# LearnKotlin.kt

```kotlin
package com.example.helloworld

import java.lang.StrictMath.max

fun main(){
    val a =10           //不可变变量
    var b =11        	//可变变量
    println("a="+a+b)   //输出
    println("a=$a$b")   //输出
    //var c               //变量必须初始化才能进行推导
    val c :Int =10         //显示声明变量类型
    b = largerNumber(a,c)
//    var d = Person() //声明类对象，实例化，不需要写new
//    d.name = "yzk"
//    d.age = 20
//    d.eat();

    val s = Student1("123",1,"yzk",20)

    val cellphone1 = Cellphone("xiaomi",1299.99)
    val cellphone2 = Cellphone("xiaomi",1599.99)
    println(cellphone1)
    println("cellphone1 equals cellphone2"+(cellphone1==cellphone2))

    Singleton.singletonTest()//自动创建了Singleton实例

    /***********集合的创建与遍历************/
    //listof创建一个不可变的集合
    val list = listOf("apple","banana","orange","pear","grape")
    for (fruit in list){
        println(fruit)
    }
    //mutableListOf创建一个可变的集合
    val list1  = mutableListOf("apple","banana","orange","pear","grape")
    list1.add("123")

    //set
    val set = setOf("apple","banana","orange","pear","grape")
    val set1 = mutableSetOf("apple","banana","orange","pear","grape")

    //map
    val map =  mapOf("apple" to 1,"orange" to 2)
    //map遍历
    for ((fruit,number) in map){
        println("fruit is " + fruit+", number is " + number)
    }

    /****集合式函数API****/
    //寻找集合中最长的元素
    val list2 = listOf("apple","banana","orange","pear","grape")
    val maxLengthFruit = list2.maxBy { it.length }
    //maxBy：接收一个lambda，将每次遍历集合的值传递给lambda，maxBy按照我们传入的条件来进行遍历，寻找该条件下的最大值。
    println("max length fruit is "+maxLengthFruit)
    /*Lambda：是一小段可以作为参数传递的代码
    * {参数名1：参数类型，参数名2：参数类型->函数体}
    * 函数体最后一行代码会自动作为lambda表达式的返回值*/
    val lambda = {fruit:String->fruit.length}
    val maxLengthFruit1 = list2.maxBy(lambda)

    //简化lambda表达式
    //可以直接将lambda表达式传入maxBy
    val maxLengthFruit2 = list2.maxBy({fruit:String->fruit.length})
    //当lambda参数是函数的最后一个参数时，可以将lambda表达式移到函数括号的外面
    val maxLengthFruit3 = list2.maxBy() {fruit:String->fruit.length}
    //如果lambda参数是函数唯一一个参数的话，还可以将函数的括号省略
    val maxLengthFruit4 = list2.maxBy {fruit:String->fruit.length}
    //运用类型推导机制
    val maxLengthFruit5 = list2.maxBy {fruit->fruit.length}
    //当lambda表达式的参数列表中只有一个参数时，也不必声明参数名，而是可以使用it关键字代替
    val maxLengthFruit6 = list2.maxBy {it.length}


    //map函数，最常用的一种API，用于将集合中的每个元素都映射成另一个元素，映射规则在lambda中指定。
    val list3 = listOf("Apple","Banana","Orange")
    val newList = list.map{it.toUpperCase()}
    for (fruit in newList){
        println(fruit)
    }

    //filter,比较常用的函数式API，用于过滤集合中的数据
    //filter和map的顺序可以调换，但是效率会低很多
    val newList1 = list.filter{it.length<=5}
                        .map{it.toUpperCase()}


    //any和all，比较常用的函数式API，any用于判断集合中是否存在一个满足制定条件的元素，all用于判断
    //集合中所有元素是否都满足规则
    val anyResult = list.any{it.length<=5}
    val allResult = list.all{it.length<=5}

    //如果调用一个Java方法，并且该方法接收一个Java单抽象方法接口参数，就可以使用函数式API。
    //Java单抽象方法接口指的是接口中只有一个带实现方法，如果接口中有多个待实现方法，这无法使用函数式
    //或API，如Runnable接口
    //线程,直接由java翻译过来
    Thread(object : Runnable{
        override fun run(){
            println("Thread is running")
        }
    }).start()
    //函数式API简化
    Thread(Runnable {
      println("Thread is running")  //隐式重写run
    }).start()

    //如果一个Java方法的参数列表中有且仅有一个Java单抽象方法接口参数，我还可以将接口名进行省略。
    Thread( {
        println("Thread is running")  //隐式重写run
    }).start()

    //如果lambda参数是函数唯一一个参数的话，还可以将函数的括号省略
    Thread {
        println("Thread is running")  //隐式重写run
    }.start()

}
/*
fun methodName(param1:Int,param2:Int):Int{  //声明函数 函数名（参数名：参数类型）：返回值类型{函数体}
    return 0
}
*/

fun largerNumber(num1:Int,num2:Int):Int{
    return max(num1,num2)
}

/*上面的代码可以简化为*/
fun largerNumber1(num1: Int,num2: Int):Int = max(num1,num2)//当函数体只有一句时可以这样写

/*使用推导机制继续简化*/
fun largerNumber2(num1: Int,num2: Int) = max(num1,num2)



/******************************************顺序结构******************************************************/

/*if可以有返回值*/
fun largerNumber3(num1:Int,num2:Int):Int{
    return if(num1>num2){
        num1
    }else{
        num2
    }
}
/*精简*/
fun largerNumber4(num1:Int,num2: Int)=if (num2<num1){
    num1
}else{
    num2
}
/*再精简*/
fun largerNumber5(num1: Int,num2: Int)=if(num2<num1) num1 else num2

/*when语句,也可以有返回值*/
/*精确匹配*/
fun getScore(name:String) = when(name){
    "Tom" -> 86
    "Jim" -> 77
    "Jack" -> 95
    "Lily" -> 100
    else -> 0
}
/*不带参数版本*/
fun getScore1(name:String) = when{
    name.startsWith("Tom") -> 86//name开头为Tom的都会响应
    name=="Jim" -> 77
    name=="Jack" -> 95
    name=="Lily" -> 100
    else -> 0
}
/*类型匹配*/
fun checkNumber(num:Number){
    when(num){
        is Int -> println("number is Int")
        is Double -> println("number is Double")
        else -> println("number not support")
    }
}

/******************************************循环语句******************************************************/
/*while循环没有区别*/
/*for*/
fun a(){
    var i = 0..10//区间表示,端点可取值
    i = 0 until 10//左闭右开区间
    var a = 10 downTo 0//降序左右闭合区间
    for (i in 0..10 step 2){//使用step调整步长

    }
}

/******************************************面向对象******************************************************/

//类,所有的非抽象类默认不可被继承
open class Person(val name : String,val age : Int){//open 关键字，让类可以继承
    fun eat(){
        println(name+"is eating.He is"+ age + "years old")
    }
}
//继承
//class Student1(val sno:String,val grade:Int) : Person(){//调用父类的无参构造函数
//    init{                                               //init结构体，可以将主构造函数逻辑写在里面
//        println("sno is "+sno)
//        println("grade is "+grade)
//    }
//}

class Student1(val sno:String,val grade:Int,name : String,age : Int) ://后面两个参数是继承自父类，所以不需要定义类型，以免冲突
    Person(name,age){//调用有参构造函数
    fun func(){
        println(sno+grade+name+age)
    }
}

/*一个类只能有一个主构造函数，但是可以有多个次构造函数*/
/*当一个类既有主构造函数，又有次构造函数，次构造函数必须调用主构造函数*/
class Student2(val sno:String,val grade:Int,name : String,age : Int) ://后面两个参数是继承自父类，所以不需要定义类型，以免冲突
    Person(name,age),Study{//继承接口
        override fun readBooks() {  //调用接口
        println(name + "is reading")
    }

    override fun doHomework() {
        println(name + "is doing homework")
    }

    constructor(name: String,age: Int) : this("",0,name,age){//使用constructor定义次构造函数，并调用主构造函数

    }
    constructor() : this("",0) {

    }

}

/*没有主构造，只有次构造函数时*/
class Student3 : Person{
    constructor(name: String,age: Int) : super(name,age){

    }
}


/********可空类型系统*********/
//kotlin将空指针的检查提前到了编译期，如果参数为空，则报错。
fun doStudy(study:Study){
    study.readBooks()
    study.doHomework()
}

//kotlin准备了一套可空类型系统，在类名后面加上一个？则表示这个类可空。

//判空辅助工具
//"?."操作符，当对象为空时什么都不做

fun doStudy1(study:Study){
    study?.readBooks()
    study?.doHomework()
}

//"?:"操作符，左右两边都接受一个表达式，如果左表达式不为空就返回左边表达式的值，否则返回右边的值。
fun getTextLength(text : String?) = text?.length ?:0
//空指针检查机制不够智能，在进行判空检查后，之后调用的函数不会直到已经进行过判空检查，仍然会认为
//存在空指针风险，编译不会通过。可以使用不保险的"!!"非空断言工具
var content: String?="hello"
fun printUpperCase(){
    val  upperCase = content!!.toUpperCase()
    println(upperCase)
}

//let辅助工具，是一个函数，提供了函数式API的编程接口，并将原始调用对象作为函数值传递到lambda表达式中
//obj.let{ obj2->
//    编写具体的业务逻辑
//}
//obj和obj2其实是一个对象
fun doStudy3(study:Study?){
    study?.let{stu ->
        stu.readBooks()
        stu.doHomework()
    }

}
fun doStudy4(study:Study?){
    study?.let{
        it.readBooks()
        it.doHomework()
    }
}

//if变量不能处理全局变量的判空问题，因为全局变量可能会被其他线程所修改。而使用let可以处理。

//字符串内嵌表达式
fun ab(){
    val a1 = "yzk"
    val a2 = "yzkaa"
    println("my name is $a1 , and $a2")
}

//函数参数默认值，定义函数形参时给一个默认值，调用时就不需要进行值传递。
//可以通过键值对的方式来传递的参数，这样可以不按顺序进行传参
//完全可以通过只编写一个主构造函数，然后给参数设定默认值的方式来实现次构造函数。所以次构造函数很少用到。


```









# Study

```kotlin
package com.example.helloworld
/*接口*/
interface Study {//接口必须全部实现
    fun readBooks()
    fun doHomework(){
        println("do homework default implementation")//接口进行默认实现
    }
}

```









# Singleton

```kotlin
package com.example.helloworld

/*单例类：构造函数私有化，禁止其他类调用，然后给出一个构造方法，如果没有实现就创建一个实现，否则直接返回已经有的实现*/
object Singleton{ //object关键字
    fun singletonTest(){
        println("singletonTest ois called.")
    }
}
```

# Cellphone

```kotlin
package com.example.helloworld
/*数据类*/
data class Cellphone(val brand:String,val price:Double)//当一个类中没有代码时，可以省略大括号
```





