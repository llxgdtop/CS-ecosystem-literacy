# GoLang学习



## 包、变量与函数

### 包

每个 Go 程序都由包构成。

程序从 `main` 包开始运行。

本程序通过导入路径 `"fmt"` 和 `"math/rand"` 来使用这两个包。

按照约定，包名与导入路径的最后一个元素一致。例如，`"math/rand"` 包中的源码均以 `package rand` 语句开始。

```go
package main

import (
	"fmt"
	"math/rand"
    //记得是圆括号，这是“分组”形式导入语句
)

func main() {
	fmt.Println("我最喜欢的数字是 ", rand.Intn(10))
    //Println中隔一个,就会加一个空格，故这里有两个空格
}
```



### 导出名

在 Go 中，如果一个名字以大写字母开头，那么它就是已导出的。例如，`Pizza` 就是个已导出名，`Pi` 也同样，它导出自 `math` 包。

`pizza` 和 `pi` 并未以大写字母开头，所以它们是未导出的。

在导入一个包时，你只能引用其中已导出的名字。 任何「未导出」的名字在该包外均无法访问。

```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Println(math.pi)
}
//错误，因为pi(π)是math里面的名字，要引用这个名字，要把pi改成Pi，像上一个代码块，就用了rand.Intn(),包不用大写，但包中的名字要大写
```



### 函数

注意类型在变量名的 **后面**。调用方法和cpp一样。

（参考这篇关于 [Go 声明语法](http://blog.go-zh.org/gos-declaration-syntax) 的文章，了解为何使用这种类型声明的形式。）

```go
func add(x int, y int) int {
	return x + y;
}
//其中，由于xy都是int，所以可以写成x,y int
```

函数可以返回任意数量的返回值。(与cpp不同)

```go
func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")//由于在此之前并没有声明变量如var a，b string,所以此处用:=有初始化并赋值的意思，具体见变量
	fmt.Println(a, b)
}
```



没有参数的 `return` 语句会直接返回已命名的返回值，也就是「裸」返回值。

裸返回语句应当仅用在下面这样的短函数中。在长的函数中它们会影响代码的可读性。

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
//7 10 
```



### 变量

`var` 语句用于声明一系列变量。和函数的参数列表一样，类型在最后。

如例中所示，`var` 语句可以出现在包或函数的层级。反正出现了类型就要配合var或者下面的const。

```go
var c, python, java bool

func main() {
    var h int
	var i int = 1
	fmt.Println(h, i, c, python, java)//0 1 false false false
}
```

变量声明可以包含初始值，每个变量对应一个。

如果提供了初始值，则类型可以省略；变量会从初始值中推断出类型。

```go
var c, python, java = true, false, "no!"//此时不用写bool或者string
```

在函数中，短赋值语句 `:=` 可在隐式确定类型的 `var` 声明中使用。

函数外的每个语句都 **必须** 以关键字开始（`var`、`func` 等），因此 `:=` 结构不能在函数外使用。

```go
package main

// 正确示例：在函数外部使用显式的var声明
var x = 10
var z int =20
//错误示例
//x := 10

func main() {
    // 在函数内部使用短变量声明
    y := 20
    i := y//当y的类型确定时，可以用:=传给新变量来确定新变量的类型
    println(x, y, i)
}
```



### 常量

常量的声明与变量类似，只不过使用 `const` 关键字。

常量可以是字符、字符串、布尔值或数值。

**常量不能用 `:=` 语法声明。**



### 基本类型

Go 的基本类型有

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
//int 类型可以存储最大 64 位的整数，根据平台不同有时会更小。
byte // uint8 的别名

rune // int32 的别名
     // 表示一个 Unicode 码位

float32 float64

complex64 complex128//复数
```

当你需要一个整数值时应使用 `int` 类型， 除非你有特殊的理由使用固定大小或无符号的整数类型。

和导入语句（import）一样，变量声明也可以「分组」成一个代码块。

```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1//此处不能省略uint64，因为超出了int的限制
	z      complex128 = cmplx.Sqrt(-5 + 12i)//此处如果想表达i，不能单独用i，否则会被判断成一个变量，要用1i
)

func main() {
	fmt.Printf("类型：%T 值：%v\n", ToBe, ToBe)
	fmt.Printf("类型：%T 值：%v\n", MaxInt, MaxInt)
	fmt.Printf("类型：%T 值：%v\n", z, z)
}
//类型：bool 值：false
//类型：uint64 值：18446744073709551615
//类型：complex128 值：(2+3i)
```

没有明确初始化的变量声明会被赋予对应类型的 **零值**。

零值是：

- 数值类型为 `0`，
- 布尔类型为 `false`，
- 字符串为 `""`（空字符串）。



表达式 `T(v)` 将值 `v` 转换为类型 `T`。%T和%v用于占位符，表示类型与值。

一些数值类型的转换：

```
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

或者，更加简短的形式（当然这要在函数里面使用）：

```
i := 42
f := float64(i)
u := uint(f)
```

与 C 不同的是，Go 在不同类型的项之间赋值时需要显式转换。





## 流程控制语句:for、if、else、switch和 defer

