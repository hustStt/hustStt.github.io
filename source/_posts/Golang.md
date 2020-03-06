---
title: Golang
date: 2020-03-02 00:00:41
tags:
---
# 顺序编程
## 变量

+ 出现在:= 左侧的变量不应该是已经被声明过的，否则会导致编译错误
```
var i int
i := 2
```

+ 变量初始化和变量赋值是两种不同的概念
+ 支持多重赋值
```
i, j = j, i
```
+ int和int32在Go语言中被认为是两种不同的类型，编译器不会自动类型转换
```
var value2 int32
value1 := 64   //int
value2 = value1
//编译错误
```
### 布尔类型
+ 布尔类型不能接受其他类型的赋值，不支持自动或强制的类型转换。
```
var v1 bool
v1 = 1   // 编译错误
v1 = bool(1) // 编译错误

v1 = (1!=0) //编译正确
```
<!-- more -->
### 浮点型
+ float32 float64 等价于C语言的float类型和double类型
+ 自动推到的类型将被自动设为float64
```
var fvalue1 float32

fvalue = 12
fvalue2 := 12.0
```
+ 浮点数比较不是一种精准的表达方式，不能直接用==来判断两个浮点数是否相等，会不稳定
```
//替代方案

import "math"

// p为用户自定义的比较精度，比如0.0001
func IsEqual(f1, f2, p float64) bool {
    return math.Abs(f1-f2) < p
}
```
### 复数类型
+ 复数complex64  实部（real） 虚部（imag）
```
var value1 complex64
value1 = 3.2 + 12i
value2 := 3.2 + 12i
value3 := complex(3.2, 12)
```
+ 对于一个复数z = complex(x, y)， 可以通过real(z) 获取实部，通过imag(z)获取虚部  math/complx库

### 字符串
+ 字符串的内容可以用类似于数组下标的方式获取，但与数组不同，字符串的内容不能在初始化后被修改。
```
str := "Hello world"
str[0] = 'X' //编译错误
len(str)  //11
```
+ 字符串操作
```
x + y
len(s)
s[i]
```
+ 字符串遍历
  - 字节数组遍历
  ```
  str := "Hello, 世界"
  n := len(str)  // 13
  for i := 0; i < n; i++ {
      ch := str[i] // 依据下标取字符串中的字符 类型为byte
  }
  ```
  - Unicode字符遍历
  ```
  str := "Hello, 世界"
  for i,ch := range str {
      fmt.Println(i, ch) //ch的类型为rune
  }
  ```
### 字符类型
+ 在Go语言中支持两个字符类型，一个是byte(uint8的别名)，代表UTF-8字符串的单个字节的值。另一个是rune，代表单个Unicode字符
+ Go语言的多数API都假设字符串为UTF-8编码，尽管Unicode字符在标准库中有支持，但是实际上较少使用。

### 数组
1. 元素访问
    + 可以使用数组下标访问
    + 可以使用关键字range，便捷的遍历容器中的元素，数组也支持
    ```
    for i, v := range array {
        fmt.Println("Array element[", i, "]=", v)
    }
    ```
2. 值类型
    + Go语言中数组是一个值类型。所有值类型变量在赋值和作为参数传递时都将产生一次复制动作。如果数组作为函数的参数类型，则在函数调用时该参数将发生数据复制。
    + 注意这与C\+\+不一样 C\+\+传递的是指针

### 数组切片
+ 数组切片就像一个指向数组的指针，实际上它有自己的数据结构，数组切片的数据结构可以抽象为以下3个变量。
    + 一个指向原生数组的指针
    + 数组切片中的元素个数
    + 数组切片已分配的存储空间

#### 创建数组切片
```
func main() {
    var myArray [10]int = [10]int{1,2,3,4,5,6,7,8,9,10}
    
    //基于数组创建一个数组切片
    var mySlice []int = myArray[:5]
    
}

```

+ 基于数组生成一个数组切片
```
//基于myArray的所有元素创建数组切片：
mySlice = myArray[:]

//基于myArray的前5个元素创建数组切片：
mySlice = myArray[:5]

//基于从第5个元素开始的所有元素创建数组切片
mySlice = myArray[5:]
```

+ 直接创建
    + 并非一定要实现准备一个数组才能创建数组切片。使用内置函数make()可以用于灵活的创建数组切片。
    + 事实上还会有一个匿名数组被创建出来，只是不需要我们来操心而已。

```
//创建一个初始元素个数为5的数组切片，元素初始值为0：
mySlice1 = make([]int, 5)

//创建一个初始元素个数为5的数组切片，元素初始值为0，并预留10个元素的储存空间:
mySlice2 = make([]int, 5, 10) 

//直接创建并初始化包含5个元素的数组切片
mySlice3 := []int{1, 2, 3, 4, 5}

```

#### 元素遍历
+ 与数组遍历差不多
```
for i := 0;i < len(mySlice);i++{
    fmt.Println("xxxx" , i, "xxxx", mySlice[i])
}

for i, v := rangmySlice {
    fmt.Println("xxxx" , i, "xxxx", v)
}

```

#### 动态增减元素
+ 课动态增减元素是数组切片比数组更为强大的功能。与数组相比，数组切片多了一个存储能力的概念（capacity），即元素个数和分配的空间可以是两个不同的值。
+ 合理地设置存储能力的值，可以大幅降低数组切片内部重新分配内存和搬运内存块的频率。
+ 数组切片支持内置的cap()函数和len()函数，cap函数返回的是数组切片分配的空间大小，而len函数返回的是切片中当前存储的元素个数。
+ 可以使用append()函数在后面继续新增元素
```
mySlice = append(mySlice, 1, 2, 3)

mySlice2 := []int{8,9,10}
// 给mySlice后面添加另一个数组切片
mySlice = append(mySlice, mySlice2...)
```
+ 后面的三个点，相当于吧mySlice2包含的所有元素打散后传入
+ 数组切片会自动处理存储空间不足的问题，如果追加的内容长度超过当前已分配的存储空间，数组切片会自动分配一块足够大的内存。

#### 基于数组切片创建数组切片
```
oldSlice := []int{1,2,3,4,5}
newSlice := oldSlice[:3]
```
+ 选择的oldSlicef元素范围甚至可以超过所包含的元素个数，比如newSlice可以基于oldSlice的前6个元素创建，虽然oldSlice只包含5个元素，只要这个范围不超过oldSlice的存储能力，那么就是合法的，超出的部分会填上0

#### 内容复制
+ 数组切片支持Go语言的另一个内置函数copy()，用于将内容从一个数组切片复制到另一个数组切片。如果两个数组切片不一样大，就会按照其中较小的那个数组切片的元素个数进行复制。
```
slice1 := []int{1,2,3,4,5}
slice2 := []int{5,4,3}

copy(slice2, slice1) //只会复制slice1的前3个元素到slice2中
copy(slice1, slice2) //只会复制slice2的3个元素到slice1的前3个位置
```

### map
+ 1.变量声明
```
//map的声明基本上没有多余的元素
var myMap map[string] PersonInfo

//string是键的类型，persionInfo是其所存放的值类型
```

+ 2.创建
+ 可以使用Go语言的内置函数make()来创建一个新map

```
var myMap map[string] PersonInfo

//创建
myMap = make(map[string] PersonInfo)
myMap = make(map[string] PersonInfo, 1000)

//创建并初始化
myMap = map[string] PersonInfo{
    "1234" : PersonInfo{"1", "Jack", "Room 101,..."}
}

//元素赋值
myMap["1234"] = PersonInfo{"1", "Jack", "Room 101,..."}
```

+ 3. 元素删除
+ Go语言提供了一个内置函数delete()，用于删除容器中的元素。
```
delete(myMap, "1234")
//如果“1234”这个键不存在，那么这个调用将上面都不发生，也不会有什么副作用，但是如果传入map变量的值为nil，该调用将导致程序抛出异常
```
+ 4.元素查找
+ 在Go语言中map查找功能设计得比较精巧
```
value, ok := myMap["1234"]
if ok {//找到了
    //处理找到的value
}
```

### 流程控制
+ 条件跳转
    + 条件语句不需要使用括号将条件包含起来
    + 无论语句体内有几条语句，花括号 { } 都是必须存在的
    + 左花括号 { 必须与 if 或者 else 处于同一行
    + 在if之后，条件语句之前，可以添加遍历初始化语句，使用；分隔
    + 在有返回值的函数中，不允许将“最终的”return语句包含在if...else...结构中，否则会编译失败。
+ 选择语句
```
switch i {
    case 0:
        fmt.Printf("0")
    case 1:
        fmt.Printf("1")
    default:
        fmt.Printf("default")
}

```
+ switch后面的表达式甚至不是必需的，比如
```
switch {
    case 0 <= Num && Num <= 3:
        fmt.Printf("0-3")
    case 4 <= Num && Num <= 6:
        fmt.Printf("4-6")
    case 7 <= Num && Num <=9:
        fmt.Printf("7-9")
}
```
+ 在使用switch结构时，我们需要注意一下几点：
    + 条件表达式不限制为常量或者整数
    + 单个case中，可以出现多个结果选项
    + 与C语言等规则相反，Go语言不需要用break来明确退出一个case
    + 只有在case只能够明确添加fallthrough关键字，才会继续执行紧跟的下一个case
    + 可以不设定switch之后的条件表达式，在此种情况下，整个switch结构与多个if...else...的逻辑作用等同
+ 循环语句
    + Go语言不支持以逗号为间隔的多个赋值语句，必须使用平行赋值的方式来初始化多个变量
    + Go语言的for循环同样支持continue和break来控制循环，但是它提供了一个更高级的break，可以选择终端哪一个循环
    ```
    JLoop:
    for j := 0; j < 5;j++ {
        for i := 0; i < 5;i++ {
            if i > 5 {
                break JLoop
            }
        }
    }
    
    ```
+ 跳转语句 支持goto

### 函数
#### 不定参数
+ 1.不定参数类型
    ```
    func myfunc(args ...int) {
        for _, arg := range args {
            
        }
    }
    ```
    + ...type格式的类型只能作为函数的参数类型存在，并且必须是最后一个参数。它是一个语法糖，从内部实现机理上来说，类型...type本质上是一个数组切片，也就是[]type，这也是为什么上面的参数args可以使用for循环来获得每个传入的参数
+ 2.不定参数的传递
```
func myfunc(args ...int){
    //按原样传递
    myfunc3(args...)
    
    //传递片段，实际上任意的int slice都可以传进去
    myfunc3(args[1:]...)
}
```

+ 3.任意类型的不定参数
    + 之前的例子中将不定参数类型约束为int，如果你希望传任意类型，可以指定类型为interface{}。
    ```
    func Printf(format string, args ...interface{}){
        
    }
    ```
    + 用interface{}传递任意类型数据是Go语言的惯例用法。使用interface{}仍然是类型安全的，这和C/C++不太一样。
+ 4.多返回值
    + 可以给返回值命名，就像函数的输入参数一样，返回值被命名之后，他们的值在函数开始的时候被自动初始化为空，在函数中执行不带任何参数的return 语句时，会返回对应的返回值变量的值。
+ 5.匿名函数与闭包
    + 匿名函数是指不需要定义函数名的一种函数实现方式
    + 函数可以向普通遍历一样被传递或使用，这与C语言的回调函数比较类似。不同的是，Go语言支持随时在代码里定义匿名函数
    + 匿名函数由一个不带函数名的函数声明和函数体组成
    ```
    func(a, b int, z float64) bool{
        return a*b <int(z)
    }
    ```
    + 匿名函数可以直接赋值给一个变量或者直接执行
    ```
    f := func(x, y int) int {
        return x + y
    }
    
    func(ch chan int) {
        ch <- ACK
    } (reply_chan) //花括号后面直接跟参数列表表示函数调用
    ```
    + Go的匿名函数是一个闭包
    + 闭包是可以包含自由变量的代码块，这些变量不在这个代码块内或者任何全局上下文中定义，而是在代码块的环境中定义。要执行的代码块（由于自由变量包含在代码块中，所以这些自由变量以及他们引用的对象没有被释放）为自由变量提供绑定的计算环境（作用）
    +  闭包的价值在于可以作为函数对象或者匿名函数，对于类型系统而言，这意味着不仅要表示数据还要表示代码。支持闭包的多数语言都将函数作为第一级对象，就是说这些函数可以存储到变量中作为参数传递给其他函数，最重要的是能够被函数动态创建和返回。
    + Go语言中的闭包同样也会引用到函数外的变量，闭包的实现确保只要闭包还被使用，那么被闭包引用的变量会一直存在。
    ```
    package main
    
    import (
        "fmt"
    )
    
    func main() {
        var j int = 5
        
        a := func()(func()) {
            var i int = 10
            return func() {
                fmt.Printf("i, j: %d, %d\n", i, j)
            }
        }()
        
        a()
        
        j *= 2
        a()
    }
    
    ```
### 错误处理
+ 漂亮的错误处理规范是Go语言最大的亮点之一

#### error接口
+ Go语言引入了一个关于错误处理的标准模式，即error接口，该接口的定义如下
```
type error interface {
    Error() string
}
```
+ 对于大多数函数，对于要返回错误，大致上都可以定义为如下模式，将error作为多种返回值的最后一个，但这并非是强制要求：
```
func Foo(param int)(n int, err error){
    //...
}

//调用时的代码建议按如下方式处理错误情况
n, err := Foo(0)

if err != nil {
    // 错误处理
} else {
    //使用返回值n
}
```

#### defer
+ 以文件关闭为例，每一个可能抛出异常的地方，每一个return的位置，都需要关掉之前打开的文件句柄。即使你头脑清晰，想明白了每一个分支和可能出错的条件，在该关闭的地方都关闭了，怎么做到后继者也能做到同样的水平？
+ C++另一种解决方案：开发者可以将需要释放的资源变量都声明在函数的开头部分，并在函数的结尾部分统一释放资源。函数需要退出时，就必须使用goto语句跳转到指定位置先完成资源清理工作，而不能调用return语句直接返回。
+ Go语言使用 defer 关键字简简单单地解决了这个问题，就比如一下的例子：
```
func CopyFile(dst, src string) (w int64, err error) {
    srcFile, err := os.Open(src)
    if err != nil {
        return
    }
    
    defer srcFile.Close()
    
    dstFile, err := os.Create(dstName)
    if err != nil {
        return
    }
    
    defer dstFile.Close()
    
    return io.Copy(dstFile, srcFile)
}

//即使其中的Copy()函数抛出异常，Go仍然会保证dstFile和srcFile会被正常关闭。
//如果觉得一句话干不完清理工作，也可以使用在defer后加一个匿名函数的做法：
defer func() {
    // 做你的清理工作
} ()

```
+ defer语句的调用时遵循先进后出的原则，即最后一个defer语句将最先被执行。

#### panic()和recover()

+ Go语言引入了两个内置函数 panic() 和 recover() 以报告和处理运行时错误和程序中的错误场景：
```
func panic(interface{})
func recover() interface{}

```
+ 当一个函数执行过程中调用panic()函数时，正常的函数执行流程将立即终止，但函数中之前==使用defer关键字延迟执行==的函数将正常展开执行，之后该函数将返回到调用函数，并导致逐层向上执行panic流程，直至所属的goroutinue中所有正在执行的函数被终止。错误信息将被报告，包括调用panic()函数时传入的参数，==这个过程称为错误处理流程。==
+ 从panic()的参数类型interface{}我们可以得知，该函数接收任意类型的数据，比如整型、字符型、对象等。调用方法很简单
```
panic(404)
panic("network broken")
panic(Error("file not exists"))
```
+ recover() 函数用于终止错误处理流程。一般情况下，recover()应该在一个使用defer关键字的函数中执行以有效截取错误处理流程。如果没有在发生异常的goroutine中明确调用恢复过程（使用recover关键字），会导致该goroutine所属的进程打印异常信息后直接退出。
+ ==要点：==
+ 1. 当panic发生之后，程序从正常的执行流程跳转到执行panic发生之前通过defer语句注册的defered函数，直到某个defered函数通过recover函数捕获了panic后再恢复正常的执行流程，如果没有recover则当所有的defered函数被执行完成之后结束程序；
    2. defer语句会被编译器翻译成对runtime包中deferproc()函数的调用，该函数会把defered函数打包成一个_defer结构体对象挂入goroutine对应的g结构体对象的_defer链表中，_defer对象除了保存有defered函数的地址以及该函数需要的参数外，还会分别把call deferproc指令的下一条指令的地址以及此时函数调用栈顶指针保存在_defer.pc和_defer.sp成员之中，用于recover时恢复程序的正常执行流程；
    3. 当某个defered函数通过recover()函数捕获到一个panic之后，程序将从该defered函数对应的_defer结构体对象的pc成员所保存的指令地址处开始执行
    4. panic可以嵌套，当发生panic之后在执行defer函数时又发生了panic即为嵌套。每个还未被recover的panic都会对应着一个_panic结构体对象，它们会被依次挂入g结构体的_panic链表之中，最近一次发生的panic位于_panic链表表头，最早发生的panic位于链表尾。

