# [go  学习笔记](https://github.com/zhizunbao84/gitblog/issues/7)

## 路径问题
GOROOT： GOROOT是Go的安装目录。Go安装程序会自动设置$GOROOT环境变量，一般不需要手动进行设置。

GOPATH：GOPATH目录是Go的工作目录，其中包含本地的项目文件，项目中引用的的第三方package，以及生成的二进制文件。
Go语言工具要求GOPATH的目录结构包含bin,pkg,src三个子目录。
安装依赖包时，多次提到过GOPATH。但是，因为改成vender模式进行包管理，现在已经不需要把项目放到GOPATH下了。


## 环境变量
执行`go env`可以查看目前go的所有环境变量。
执行`go env -w key=value`可以设置环境变量的值。并且覆盖默认的设置

## go get超时
由于golang.org被墙，导致安装第三方库的时候经常超时。解决办法：设置代理。
```
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
# go env -w GOPROXY=https://goproxy.cn,direct

# 设置不走 proxy 的私有仓库，多个用逗号相隔（可选）
go env -w GOPRIVATE=*.corp.example.com
# 设置不走 proxy 的私有组织（可选）
go env -w GOPRIVATE=example.com/org_name

# 因为Go 1.13设置了默认的GOSUMDB=sum.golang.org，这个网站是被墙了的，用于验证包的有效性，可以通过如下命令关闭：
go env -w GOSUMDB=off
# 这个是专门为国内提供的sum 验证服务
go env -w GOSUMDB="sum.golang.google.cn


```

## 数据类型

- 布尔型布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。
- 数字类型整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。
- 字符串类型:字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。
- 派生类型:包括：(a) 指针类型（Pointer）；(b) 数组类型；(c) 结构化类型(struct)；(d) Channel 类型；(e) 函数类型；(f) 切片类型；(g) 接口类型（interface）；(h) Map 类型
### 数字类型
Go 也有基于架构的类型，例如：int、uint 和 uintptr。
- uint8无符号 8 位整型 (0 到 255)
- uint16无符号 16 位整型 (0 到 65535)
- uint32无符号 32 位整型 (0 到 4294967295)
- uint64无符号 64 位整型 (0 到 18446744073709551615)
- int8有符号 8 位整型 (-128 到 127)
- int16有符号 16 位整型 (-32768 到 32767)
- int32有符号 32 位整型 (-2147483648 到 2147483647)
- int64有符号 64 位整型 (-9223372036854775808 到 9223372036854775807)
### 浮点型
- float32IEEE-754 32位浮点型数
- float64IEEE-754 64位浮点型数
- complex6432 位实数和虚数
- complex12864 位实数和虚数
### 其他数字类型
- byte类似 uint8
- rune类似 int32
- uint32 或 64 位
- int与 uint 一样大小
- uintptr无符号整型，用于存放一个指针

## 变量
Go 语言变量名由字母、数字、下划线组成，其中首个字符不能为数字。
单纯地给变量赋值也是不够的，这个值必须被使用。但是全局变量是允许声明但不使用的。 
声明变量的一般形式是使用 var 关键字：
```
var identifier type
//可以一次声明多个变量：
var identifier1, identifier2 type

//指定变量类型，如果没有初始化，则变量默认为零值(没有做初始化时系统默认设置的值)。
var v_name v_type
v_name = value
//数值类型（包括complex64/128）为 0
//布尔类型为 false
//字符串为 ""（空字符串）
//以下几种类型为 nil：
var a *int
var a []int
var a map[string] int
var a chan int
var a func(string) int
var a error // error 是接口

//根据值自行判定变量类型(不推荐)
var v_name = value

//使用 := 声明变量,能在函数体中出现，不可以用于全局变量的声明与赋值。
vname1, vname2, vname3 := v1, v2, v3 

//类型相同多个变量, 非全局变量
var vname1, vname2, vname3 type
vname1, vname2, vname3 = v1, v2, v3
var vname1, vname2, vname3 = v1, v2, v3 // 和 python 很像,不需要显示声明类型，自动推断
vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误
// 这种因式分解关键字的写法一般用于声明全局变量
var (
    vname1 v_type1
    vname2 v_type2
)


```
所有像 int、float、bool 和 string 这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值，你可以通过 &i 来获取变量 i 的内存地址。值类型变量的值存储在堆中。

其他更复杂的数据通常会需要使用多个字，这些数据一般使用引用类型保存。一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置。
这个内存地址称之为指针，这个指针实际上也被存在另外的某一个值中。
同一个引用类型的指针指向的多个字可以是在连续的内存地址中（内存布局是连续的），这也是计算效率最高的一种存储形式；也可以将这些字分散存放在内存中，每个字都指示了下一个字所在的内存地址。
当使用赋值语句 r2 = r1 时，只有引用（地址）被复制。
如果 r1 的值被改变了，那么这个值的所有引用都会指向被修改后的内容，在这个例子中，r2 也会受到影响。

如果你想要交换两个变量的值，则可以简单地使用 a, b = b, a，两个变量的类型必须是相同。
空白标识符 _ 也被用于抛弃值，如值 5 在：_, b = 5, 7 中被抛弃。
_ 实际上是一个只写变量，你不能得到它的值。这样做是因为 Go 语言中你必须使用所有被声明的变量，但有时你并不需要使用从一个函数得到的所有返回值。
并行赋值也被用于当一个函数返回多个返回值时，比如这里的 val 和错误 err 是通过调用 Func1 函数同时得到：val, err = Func1(var1)。

## 常量
常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

```
//可以省略类型说明符 [type]，因为编译器可以根据变量的值来推断其类型。
const identifier [type] = value
const c_name1, c_name2 = value1, value2
```
常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过。
### iota
iota，特殊常量，可以认为是一个可以被编译器修改的常量。
iota 在 const关键字出现时将被重置为 0(const 内部的第一行之前)，const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。
```
    const (
            a = iota   //0
            b          //1
            c          //2
            d = "ha"   //独立值，iota += 1
            e          //"ha"   iota += 1
            f = 100    //iota +=1
            g          //100  iota +=1
            h = iota   //7,恢复计数
            i          //8
    )
//结果为：0 1 2 ha ha 100 100 7 8

const (
    i=1<<iota
    j=3<<iota
    k
    l
)
//ota 表示从 0 开始自动加 1，所以 i=1<<0, j=3<<1（<< 表示左移的意思），即：i=1, j=6，这没问题
//关键在 k 和 l，从输出结果看 k=3<<2，l=3<<3。
```

## 运算符
二元运算符的运算方向均是从左至右。由上至下代表优先级由高到低
优先级 | 运算符
   5         |   *   /    %   <<     >>     &    &^
   4         |   +   -    | ^
   3         |   ==    !=    <     <=    >    >=
   2         |   &&
   1         |   ||



---

## 条件语句
if else跟Python一样，也可以嵌套。
有三目运算符，所以不支持 ?: 形式的条件判断。
### switch 语句
switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。

switch 语句执行的过程从上至下，直到找到匹配项，匹配项后面也不需要再加 break。

switch 默认情况下 case 最后自带 break 语句，匹配成功后就不会执行其他 case，如果我们需要执行后面的 case，可以使用 fallthrough 。

switch 的 default 不论放在哪都是最后执行。

switch 中可以添加if语句，配合break进行终止。

### select 语句
select 是 Go 中的一个控制结构，类似于用于通信的 switch 语句。每个 case 必须是一个通信操作，要么是发送要么是接收。

select 随机执行一个可运行的 case。如果没有 case 可运行，它将阻塞，直到有 case 可运行。一个默认的子句应该总是可运行的。select 是随机执行的不是循环检测，是为了避免饥饿问题。

每个 case 都必须是一个通信。
所有 channel 表达式都会被求值。
所有被发送的表达式都会被求值。
如果任意某个通信可以进行，它就执行，其他被忽略。
如果有多个 case 都可以运行，Select 会随机公平地选出一个执行。其他不会执行。否则：1.如果有 default 子句，则执行该语句。2.如果没有 default 子句，select 将阻塞，直到某个通信可以运行；Go 不会重新对 channel 或值进行求值。