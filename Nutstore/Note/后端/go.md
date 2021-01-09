# go安装时的坑

如果是使用apt进行安装的话，那么GOROOT应该在/usr/lib/go-XXX，而不是~/go

# go语言的优势

可直接编译成机器码，不依赖其他库，glibc的版本有一定要求，部署就是扔一个文件上去就完成了



# 基础语法

```go
//保存后会自动格式化，很方便
package main

import (
	"fmt"	//包导入后必须要使用，不然会报错
)

func main() {	//左括号必须和函数同一行
	//调用函数大部分要导入包
	fmt.Println("Hello World!")
	
}
```

定义了的东西都要使用

没有进行初始化的变量默认为0。

```go
var a,b int
```

可以通过初始化自动推导类型

```go
package main

import (
	"fmt"
)

func main() { //左括号必须和函数同一行
	//自动推导，自动定义并赋值，同一个变量只能用一次
	c := 30
	//%T打印变量类型
	fmt.Printf("c type is:%T", c)
}

```

println自动换行

printf格式化输出

```go
package main

import "fmt"

func main() { //左括号必须和函数同一行
	//多重赋值
	i, j := 10, 20
	i, j = j, i
	fmt.Println(i, j)
	//匿名变量_，丢弃数据不处理，配合函数返回值使用，可以返回多个值
	tmp, _ := i, j
	fmt.Println(tmp)
}
```

```go
	//常量
	const a int = 10
	const b = 20//常量自动推导不需要:
```

```go
	//多个类型变量声明
	var (
		c int     = 1
		d float32 = 2
		e float64 = 3
	)
```

```go
	//枚举
	const (
		g = iota
		h,k,l = iota
		i = iota
	)
	//值都一样，可以只写一个
	const (
		g1 = iota
		h1 
		i1
	)

```

