# [go  学习笔记](https://github.com/zhizunbao84/mygitblog/issues/7)

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
常量无法修改，修改就报错。
常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过。
在定义常量组时，如果不提供初始值，则表示将使用上行的表达式。
```
const (
    a = 1
    b
    c
    d
)

  fmt.Println(a)
  // b、c、d没有初始化，使用上一行(即a)的值
  fmt.Println(b)   // 输出1
  fmt.Println(c)   // 输出1
  fmt.Println(d)   // 输出1
```

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

---

## 循环语句
go只有for，没有while。
go支持goto语句，将控制转移到被标记的语句。
```
sum := 0
for i := 0; i <= 10; i++ {
        sum += i
}

sum := 0
for {
    sum++ // 无限循环下去
}

for true  {
    fmt.Printf("这是无限循环。\n");
}

// 读取 key 和 value
for key, value := range map1 {
  fmt.Printf("key is: %d - value is: %f\n", key, value)
}

// 读取 key
for key := range map1 {
  fmt.Printf("key is: %d\n", key)
}

// 读取 value
for _, value := range map1 {
  fmt.Printf("value is: %f\n", value)
}
```


## 函数
定义格式如下：
```
//函数名后面的大括号只能跟在函数名后面，不能另起一行
func function_name( [parameter list] ) [return_types] {
   函数体
}

func max(num1, num2 int) int {
}

func max(num1, num2 int) (int int) {
}

/* 声明函数变量 */
getSquareRoot := func(x float64) float64 {
  return math.Sqrt(x)
}
```
parameter list和return_types都不是必须的，都是可选的。

默认情况下，Go 语言使用的是值传递，即在调用过程中不会影响到实际参数。
值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
引用传递是指在调用函数时将实际参数的地址（传递指针参数）传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

函数作为参数传递，实现回调。

### 闭包
Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。匿名函数的优越性在于可以直接使用函数内的变量，不必申明。
```
func add(x1, x2 int) func(x3 int,x4 int)(int,int,int)  {
    i := 0
    return func(x3 int,x4 int) (int,int,int){ 
       i++
       return i,x1+x2,x3+x4
    }
}

// 闭包使用方法，函数声明中的返回值(闭包函数)不用写具体的形参名称
func add(x1, x2 int) func(int, int) (int, int, int) {
  i := 0
  return func(x3, x4 int) (int, int, int) {
    i += 1
    return i, x1 + x2, x3 + x4
  }
}

add_func := add(1,2)
fmt.Println(add_func(1,1))
fmt.Println(add_func(0,0))
fmt.Println(add_func(2,2))

```

### 方法
法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。语法格式如下：
```
func (variable_name variable_data_type) function_name() [return_type]{
   /* 函数体*/
}


/* 定义结构体 */
type Circle struct {
  radius float64
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64  {
   //c.radius 即为 Circle 类型对象中的属性
   return c.radius * c.radius
}
// 注意如果想要更改成功c的值，这里需要传指针
func (c *Circle) changeRadius(radius float64)  {
   c.radius = radius
}

// 以下操作将不生效
//func (c Circle) changeRadius(radius float64)  {
//   c.radius = radius
//}
// 引用类型要想改变值需要传指针
func change(c *Circle, radius float64)  {
   c.radius = radius
}

var c Circle
fmt.Println(c.radius)
c.radius = 10.00
fmt.Println(c.getArea())
c.changeRadius(20)
fmt.Println(c.radius)
change(&c, 30)
fmt.Println(c.radius)

```
Go 没有面向对象，而我们知道常见的 Java。
C++ 等语言中，实现类的方法做法都是编译器隐式的给函数加一个 this 指针，而在 Go 里，这个 this 指针需要明确的申明出来，其实和其它 OO 语言并没有很大的区别。


## 变量作用域
函数内定义的变量称为局部变量，函数外定义的变量称为全局变量，函数定义中的变量称为形式参数。

在函数体外声明的变量称之为全局变量，全局变量可以在整个包甚至外部包（被导出后）使用。
全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑。
可通过花括号来控制变量的作用域，花括号中的变量是单独的作用域，同名变量会覆盖外层。

不同类型的局部和全局变量默认值为：int、float32为0，pointer为nil。

在 for 循环的 initialize（a:=0） 中，此时 initialize 中的 a 与外层的 a 不是同一个变量，initialize 中的 a 为 for 循环中的局部变量，因此在执行完 for 循环后，输出 a 的值仍然为 0。

---

## 数组
语法格式如下：
```
var variable_name [SIZE] variable_type

balance := [5] int {1,2,3,4,5}
//长度为5的数组，其元素值依次为：1，2，3，4，5。

balance := [5] int {1,2}
//长度为 5 的数组，其元素值依次为：1，2，0，0，0 。
//在初始化时没有指定初值的元素将会赋值为其元素类型 int 的默认值0，string 的默认值是 ""。

balance := [...] int {1,2,3,4,5}
//长度为 5 的数组，其长度是根据初始化时指定的元素个数决定的。

balance := [5] int { 2:1,3:2,4:3}
//长度为 5 的数组，key:value，其元素值依次为：0，0，1，2，3。在初始化时指定了 2，3，4 索引中对应的值：1，2，3

balance := [...] int {2:1,4:3}
//长度为5的数组，起元素值依次为：0，0，1，0，3。由于指定了最大索引 4 对应的值 3，根据初始化的元素个 数确定其长度为5赋值与使用。

balance := [...] int {1,2,3,4,5}
balance := [] int {1,2,3,4,5}
//第一个是数组，第二个是切片，没有所谓没有声明长度的数组存在。
//初始化数组中 {} 中的元素个数不能大于 [] 中的数字。
//如果忽略 [] 中的数字不设置数组大小，Go 语言会根据元素的个数来设置数组的大小
//初始化数组长度后，元素可以不进行初始化，或者不进行全部初始化，但未进行数组大小初始化的数组初始化结果元素大小就为多少。

//多维数组声明方式：
var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type
//声明了三维的整型数组
var threedim [5][10][4]int

// 遍历二维数组，3表示行数，2表示列数
var value = [3][2]int{{1, 2}, {3, 4}, {5, 6}}  
// 遍历二维数组的其他方法，使用 range  
// 其实，这里的 i, j 表示行游标和列游标  
// v2 就是具体的每一个元素  
// v  就是每一行的所有元素 
for i, v := range value {
    for j, v2 := range v {            
        fmt.Printf("value[%v][%v]=%v \t ", i, j, v2)  
        fmt.Printf("value[%v][%v]=%v \t ", i, j, value[i][j])      
    }        
    fmt.Print(v)        
    fmt.Println()    
}

//最后一行的 } 不能单独一行
//这样是没问题的
var arr = [3][4]int{
  {0, 1, 2, 3} ,      {4, 5, 6, 7} ,      {8, 9, 10, 11}}

//把外层括号单独拿到下一层就会报错
var arr = [3][4]int{
  {0, 1, 2, 3} ,      {4, 5, 6, 7} ,      {8, 9, 10, 11}
}

//把外层括号单独拿到下一层但是在结尾加上逗号就可以了
var arr = [3][4]int{
  {0, 1, 2, 3} ,      {4, 5, 6, 7} ,      {8, 9, 10, 11},   
}

//形参设定数组大小：
void myFunction(param [10]int)
//形参未设定数组大小：
void myFunction(param []int)


var array = []int{1, 2, 3, 4, 5}
/* 未定义长度的数组只能传给不限制数组长度的函数 */
setArray(array)
/* 定义了长度的数组只能传给限制了相同数组长度的函数 */
var array2 = [5]int{1, 2, 3, 4, 5}
setArray2(array2)
func setArray(params []int) {
    fmt.Println("params array length of setArray is : ", len(params))
}

func setArray2(params [5]int) {
    fmt.Println("params array length of setArray2 is : ", len(params))
}


// Go 语言的数组是值，其长度是其类型的一部分，作为函数参数时，是 值传递，函数中的修改对调用者不可见
func change1(nums [3]int) {    
    nums[0] = 4
}
// 传递进来数组的内存地址，然后定义指针变量指向该地址，则会改变数组的值
func change2(nums *[3]int) {    
    nums[0] = 5
}
// Go 语言中对数组的处理，一般采用 切片 的方式，切片包含对底层数组内容的引用，作为函数参数时，类似于 指针传递，函数中的修改对调用者可见
func change3(nums []int) {    
    nums[0] = 6
}
var nums1 = [3]int{1, 2, 3}   
var nums2 = []int{1, 2, 3}    
change1(nums1)    
fmt.Println(nums1)  //  [1 2 3]     
change2(&nums1)    
fmt.Println(nums1)  //  [5 2 3]    
change3(nums2)    
fmt.Println(nums2)  //  [6 2 3]

```
与 c 语言不同，go 的数组作为函数参数传递的是副本，函数内修改数组并不改变原来的数组。
用for、range遍历数组时，第一个位置的变量为索引号，第二个位置的变量为数组元素的值。


## 切片(Slice)
切片是对数组的抽象。数组的长度不可改变，在特定场景中这样的集合就不太适用，Go 中提供了一种灵活，功能强悍的内置类型切片("动态数组")，与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。

```
//定义切片
var identifier []type
var slice1 []type = make([]type, len)
slice1 := make([]type, len)

// len 是数组的长度并且也是切片的初始长度
//capacity 为可选参数
make([]T, length, capacity)

//初始化
s :=[] int {1,2,3 } 
//[] 表示是切片类型，{1,2,3} 初始化值依次是 1,2,3，其 cap=len=3。

s := arr[:] 
//初始化切片 s，是数组 arr 的引用。
//将 arr 中从下标 startIndex 到 endIndex-1 下的元素创建为一个新的切片。
s := arr[startIndex:endIndex] 
//默认 endIndex 时将表示一直到arr的最后一个元素。
s := arr[:endIndex] 
//默认 startIndex 时将表示从 arr 的第一个元素开始。
s := arr[:endIndex] 
//通过切片 s 初始化切片 s1。
s1 := s[startIndex:endIndex] 
//通过内置函数 make() 初始化切片s，[]int 标识为其元素类型为 int 的切片
s :=make([]int,len,cap) 

//切片是可索引的，并且可以由 len() 方法获取长度。
//切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。
var numbers = make([]int,3,5)
fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
//结果为   len=3 cap=5 slice=[0 0 0]

```
一个切片在未初始化之前默认为 nil，长度为 0
```
var numbers []int
if(numbers == nil){
  fmt.Printf("切片是空的")
}
```
可以通过设置起始和截止索引来设置截取切片。
如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。
append(list, [params])，动态扩容的机制还有点复杂，待研究，一般不用管。
使用 copy 函数要注意对于 copy(dst, src)，如果len(dst)>len(src)，则copy src的所有元素，若len(dst)《》len(src)，则只会copy len(dst)个元素。
```
var numbers []int
/* 向切片添加一个元素 */
numbers = append(numbers, 1)
/* 同时添加多个元素 */
numbers = append(numbers, 2,3,4)
/* 创建切片 numbers1 是之前切片的两倍容量*/
numbers1 := make([]int, len(numbers), (cap(numbers))*2)

/* 拷贝 numbers 的内容到 numbers1 */
copy(numbers1,numbers)
//将numbers展开，将其中每个元素增加到numbers1中
//数组也可以这么操作
numbers1 = append(numbers1,numbers...)

/* 数组 - 5 行 2 列*/
var a = [][]int{{0, 0}, {1, 2}, {2}, {3, 6}, {4, 8}}
var i, j int

/* 输出数组元素 */
for i = 0; i < len(a); i++ {
  for j = 0; j < len(a[i]); j++ {
     fmt.Printf("a[%d][%d] = %d\n", i, j, a[i][j])
  }
}
//1.二维数组中的元素(一位数组)个数 > 限制的列数,异常;
//2.二维数组中的元素(一位数组)个数 <= 限制的列数,正常;
//假设列数为 3, 某个一位数组 {1}, 则后两个元素,默认为 0。

```
切片，实际的是获取数组的某一部分，len切片<=cap切片<=len数组，切片由三部分组成：指向底层数组的指针、len、cap。

```
//切片内部结构：
struct Slice
{   
    byte*    array;       // actual data
    uintgo    len;        // number of elements
    uintgo    cap;        // allocated number of elements

};
```
第一个字段表示 array 的指针，是真实数据的指针第二个是表示 slice 的长度，第三个是表示 slice 的容量。
所以 unsafe.Sizeof(切片)永远都是 24。

当把 slice 作为参数，本身传递的是值，但其内容就 byte* array，实际传递的是引用，所以可以在函数内部修改，但如果对 slice 本身做 append，而且导致 slice 进行了扩容，实际扩容的是函数内复制的一份切片，对于函数外面的切片没有变化。
之所以对slice的传递可以修改原slice数据，是因为使用了slice这个引用类型的指针进行的传递，但依旧是值传递。
指针值传递”相当于把指针复制了一份，结果就是两个指针指向同一个值，之后不管使用哪个指针，改的都是同一个值。实在不懂的话建议把P10再多看两遍。待考证！

```
slice_test := []int{1, 2, 3, 4, 5}
fmt.Println(unsafe.Sizeof(slice_test))
fmt.Printf("main:%#v,%#v,%#v\n", slice_test, len(slice_test), cap(slice_test))
slice_value(slice_test)
fmt.Printf("main:%#v,%#v,%#v\n", slice_test, len(slice_test), cap(slice_test))
slice_ptr(&slice_test)
fmt.Printf("main:%#v,%#v,%#v\n", slice_test, len(slice_test), cap(slice_test))
fmt.Println(unsafe.Sizeof(slice_test))
func slice_value(slice_test []int) {
    slice_test[1] = 100                // 函数外的slice确实有被修改
    slice_test = append(slice_test, 6) // 函数外的不变
    fmt.Printf("slice_value:%#v,%#v,%#v\n", slice_test, len(slice_test), cap(slice_test))
}

func slice_ptr(slice_test *[]int) { // 这样才能修改函数外的slice
    *slice_test = append(*slice_test, 7)
    fmt.Printf("slice_ptr:%#v,%#v,%#v\n", *slice_test, len(*slice_test), cap(*slice_test))
}

//结果如下：
24
main:[]int{1, 2, 3, 4, 5},5,5

slice_value:[]int{1, 100, 3, 4, 5, 6},6,10
main:[]int{1, 100, 3, 4, 5},5,5

slice_ptr:[]int{1, 100, 3, 4, 5, 7},6,10
main:[]int{1, 100, 3, 4, 5, 7},6,10
24
```
slice 的底层是数组指针，所以 slice a 和 b 指向的是同一个底层数组，所以当修改 s[0] 时，a 也会被修改。
但是append改变a的时候并不会改变b。
```
a := []int{1, 2, 3, 4}
b := a
a[0] = 9
// a 和 b 都是  [9 2 3 4]
a = append(a, 5) //此时会导致切片a扩容,会创建了新数组替换旧数组,a的内存地址也会变化。b也不等于a了。
a[0] = 1 //此时a改变，但是b不变。

a := make([]int, 4, 10)
a[0] = 1
a[1] = 2
a[2] = 3
a[3] = 4

b := a
a = append(a, 5) //此时由于a的容量够，不会扩容，,a的内存地址不会变化
a[0] = 1   //此时a和b都会改变
```

建议：做 slice 截取时建议用两个参数，尤其是从底层数组进行切片操作时，因为这样在进行第一次 append 操作时，会给切片重新分配空间，这样减少切片对数组的影响。

结论：s = s[low : high : max] 切片的三个参数的切片截取的意义为 low 为截取的起始下标（含）， high 为窃取的结束下标（不含 high），max 为切片保留的原切片的最大下标（不含 max）；即新切片从老切片的 low 下标元素开始，len = high - low, cap = max - low；high 和 max 一旦超出在老切片中越界，就会发生 runtime err，slice out of range。另外如果省略第三个参数的时候，第三个参数默认和第二个参数相同，即 len = cap。max必须要大于等于high。
```
s1 := []int {0, 1, 2, 3, 4, 5, 6,7, 8, 9} //长度和容量都为10
s := s1[1:9:10] //正确
s := s1[1:9:11] //出错：slice bounds out of range
```

## 指针

声明格式如下：
```
//var-type 为指针类型，var_name 为指针变量名，* 号用于指定变量是作为一个指针
var var_name *var-type


//当一个指针被定义后没有分配到任何变量时，它的值为 nil。
//nil 指针也称为空指针。
var  ptr *int
fmt.Printf("ptr 的值为 : %x\n", ptr  ) //ptr为0
//空指针判断：
if(ptr != nil)     /* ptr 不是空指针 */
if(ptr == nil)    /* ptr 是空指针 */


var ptrs [3]*int //指针数组
v1 := [...]int{1, 2, 3}
//将number数组的值的地址赋给ptrs
for i, x := range v1 {
    ptrs[i] = &x  //这样有问题，最后指针数组的三个元素值和地址都一样
    ptrs[i] = &v1[i]   //这样才正确
    //这样也可以
    //原因在于，x临时变量仅被声明一次，此后都是将迭代 number 出的值赋值给 x ，x 变量的内存地址始终未变
    //这样再将 x 的地址发送给 ptrs 数组，自然也是相同的。
    //通过临时变量tmp中转也能达到目的
    //tmp := x
    //ptrs[i] = &tmp
}
for i, x := range ptrs {
    fmt.Printf("指针数组：索引:%d 值:%d 值的内存地址:%d\n", i, *x, x)
}


var arr [3]int
//注意指针数组和数组指针
var parr [3]*int // 指针数组
var p *[3]int = &arr // 数组指针
for k, _ := range arr {
    parr[k] = &arr[k];
}

// 输出地址比对
for i := 0; i < 3; i+=1 {
    fmt.Println(&arr[i], parr[i], &(*p)[i]);
}

//如果一个指针变量存放的又是另一个指针变量的地址，则称这个指针变量为指向指针的指针变量。
//当定义一个指向指针的指针变量时，第一个指针存放第二个指针的地址，第二个指针存放变量的地址
var ptr **int;
```
指针也可以作为函数的参数，形如`func swap(x *int, y *int)`。

---

## 结构体
```
//结构体的格式如下：
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
//声明语法格式如下：
variable_name := structure_variable_type {value1, value2...valuen}

variable_name := structure_variable_type { key1: value1, key2: value2..., keyn: valuen}


```
如果要访问结构体成员，需要使用点号 . 操作符，格式为：结构体.成员名。
结构体作为函数参数的值传递，如果要想在参数中修改结构体的信息，需要传入结构体指针。

struct 类似于 java 中的类，可以在 struct 中定义成员变量。
要访问成员变量，可以有两种方式：
- 1.通过 struct 变量.成员 变量来访问。
- 2.通过 struct 指针.成员 变量来访问。
不需要通过 getter, setter 来设置访问权限。
```
type Rect struct{   //定义矩形类
    x,y float64       //类型只包含属性，并没有方法
    width,height float64
}
func (r *Rect) Area() float64{    //为Rect类型绑定Area的方法，*Rect为指针引用可以修改传入参数的值
    return r.width*r.height         //方法归属于类型，不归属于具体的对象，声明该类型的对象即可调用该类型的方法
}
```

结构体中属性的首字母大小写问题

- 首字母大写相当于 public。
- 首字母小写相当于 private。
注意: 这个 public 和 private 是相对于包（go 文件首行的 package 后面跟的包名）来说的。
结构体如果只在当前包内使用，结构体的属性不用区分大小写。如果想要被其他的包引用，那么结构体的属性的首字母需要大写。
敲黑板，划重点:当要将结构体对象转换为 JSON 时，对象中的属性首字母必须是大写，才能正常转换为 JSON。
```
type Person struct {
    Name string　　　　　　//Name字段首字母大写
    age int               //age字段首字母小写
}

person:=Person{"小明",18}
if result,err:=json.Marshal(&person);err==nil{  //json.Marshal 将对象转换为json字符串
    fmt.Println(string(result))
//结果为：
{"Name":"小明"}    //只有Name，没有age
}

//如果修改为：
type Person  struct{
   　　Name  string      //都是大写
   　　Age    int               
}
//结果为：
{"Name":"小明","Age":18}   //两个字段都有
//那这样 JSON 字符串以后就只能是大写了么？ 当然不是，可以使用 tag 标记要返回的字段名。
type Person  struct{
   　　Name  string   `json:"name"`　  //标记json名字为name　　　
   　　Age    int     `json:"age"`    //变量名还得都为首字母大写
   　　Time int64    `json:"-"`        // 标记忽略该字段

}
//结果为：
{"name":"小明","age":18}

```

## range
range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。
```
for key, value := range oldMap {
    newMap[key] = value
}
//只想读取 key
for key := range oldMap
for key, _ := range oldMap
//只想读取 value
for _, value := range oldMap
```

---

## Map(集合)
Map 是一种无序的键值对的集合。Map 最重要的一点是通过 key 来快速检索数据，key 类似于索引，指向数据的值。
Map 是一种集合，所以我们可以像迭代数组和切片那样迭代它。不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 Map 是使用 hash 表来实现的。
```
//可以使用内建函数 make 也可以使用 map 关键字来定义 Map
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```
如果不初始化 map，那么就会创建一个 nil map。nil map 不能用来存放键值对。
```
/*查看元素在集合中是否存在 */
capital, ok := countryCapitalMap [ "American" ] /*如果确定是真实的,则存在,否则不存在 */
/*fmt.Println(capital) */
/*fmt.Println(ok) */
if (ok) {
    fmt.Println("American 的首都是", capital)
} else {
    fmt.Println("American 的首都不存在")
}

/*删除元素*/ 
//key存在则删除，key不存在则不操作
delete(countryCapitalMap, "France")
```


## 类型转换
`type_name(expression)`，type_name 为类型，expression 为表达式。
go 不支持隐式转换类型。

## 接口
主要目的是为了不同类型的数据提供统一的接口（方法）。
```
// 定义接口
type Phone interface {
    call() string 
}

type Android struct {
    brand string
}

type IPhone struct {
    version string
}

func (android Android) call() string {
    return "I am Android " + android.brand
}

func (iPhone IPhone) call() string {
    return "I am iPhone " + iPhone.version
}

//这个里面参数p既可以是Android，也可以是IPhone
//因为这两种数据类型都实现了Phone接口
func printCall(p Phone) {
    fmt.Println(p.call() + ", I can call you!")
}
```
如果想要通过接口方法修改属性，需要在传入指针的结构体才行。

```
//实现组合接口
type reader interface {
    read() string
}

type writer interface {
    write() string
}

type rw interface {
    reader //不能写成reader()
    writer  //不能写成writer()
}

type mouse struct{}

func (m mouse) read() string {
    return "mouse reading..."
}

func (m *mouse) write() string {
    return "mouse writing..."
}

var rw1 rw
// 只要有一个指针实现，则此处必须是指针
rw1 = &mouse{}
// rw1 = new(mouse)

fmt.Println(rw1.read())
fmt.Println(rw1.write())



// interface本质就是一个指针
type Reader interface {  
    ReadBook()
}

type Writer interface {
    WriteBook()
}

type Book struct{
}

func (t *Book) ReadBook() {
    fmt.Println("read a book")
}

func (t *Book) WriteBook() {
    fmt.Println("write a book")
}

func main() {
    // b : pair<type:Book, value:book{}地址>
    b := &Book{}
    
    // r: pair<type, value>
    var r Reader
    // r : pair<type:Book, value:book{}地址>   pair是不变的
    r = b   // interface r 本质就是一个指针，所以b也要是指针类型
    r.ReadBook()
    
    var w Writer
    // w : pair<type:Book, value:book{}地址>    pair是不变的
    w = r.(Writer)   // w和r的type一致，所以断言成功(断言就是强转?)
    
    w.WriteBook()
}
```

## 错误处理

1、panic 在没有用 recover 前以及在 recover 捕获那一级函数栈，panic 之后的代码均不会执行；一旦被 recover 捕获后，外层的函数栈代码恢复正常，所有代码均会得到执行；
 2、panic 后，不再执行后面的代码，立即按照逆序执行 defer，并逐级往外层函数栈扩散；defer 就类似 finally；
 3、利用 recover 捕获 panic 时，defer 需要再 panic 之前声明，否则由于 panic 之后的代码得不到执行，因此也无法 recover；

```
func main() {
  fmt.Println("外层开始111")
  defer func() {
    fmt.Println("外层准备recover222")
    if err := recover(); err != nil {
      fmt.Printf("%#v-%#v\n", "外层", err) // err已经在上一级的函数中捕获了，这里没有异常，只是例行先执行defer，然后执行后面的代码
    } else {
      fmt.Println("外层没做啥事333")
    }
    fmt.Println("外层完成recover444")
  }()
  fmt.Println("外层即将异常555")
  f()
  fmt.Println("外层异常后666")
  defer func() {
    fmt.Println("外层异常后defer777")
  }()
}

func f() {
  fmt.Println("内层开始8888")
  defer func() {
    fmt.Println("内层recover前的defer9999")
  }()

  defer func() {
    fmt.Println("内层准备recover1010101010")
    if err := recover(); err != nil {
      fmt.Printf("%#v-%#v\n", "内层", err) // 这里err就是panic传入的内容
    }

    fmt.Println("内层完成recover111111")
  }()

  defer func() {
    fmt.Println("内层异常前recover后的defer12121212")
  }()

  panic("异常信息131313")
  //这后面的代码不会执行
  defer func() {
    fmt.Println("内层异常后的defer141414")
  }()

  fmt.Println("内层异常后语句151515") //recover捕获的一级或者完全不捕获这里开始下面代码不会再执行
}
```

## 并发
Go 语言支持并发，我们只需要通过 go 关键字来开启 goroutine 即可。
goroutine 是轻量级线程，goroutine 的调度是由 Golang 运行时进行管理的。
Go 允许使用 go 语句开启一个新的运行期线程， 即 goroutine，以一个不同的、新创建的 goroutine 来执行一个函数。 同一个程序中的所有 goroutine 共享同一个地址空间。
goroutine 语法格式：
```
go 函数名( 参数列表 )
```
### 通道（channel）
通道（channel）是用来传递数据的一个数据结构。
通道可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符 <- 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道。
```
ch <- v    // 把 v 发送到通道 ch
v := <-ch  // 从 ch 接收数据
           // 并把值赋给 v
//Channel 是可以控制读写权限的 具体如下:
go func(c chan int) { //读写均可的channel c } (a)
go func(c <- chan int) { //只读的Channel } (a)
go func(c chan <- int) {  //只写的Channel } (a)

//声明一个通道很简单,通道在使用前必须先创建
ch := make(chan int)
//TYPE 指的是 channel 中传输的数据类型，第二个参数是可选的，指通道的缓冲区大小
make(chan TYPE {, NUM})  
```
注意：默认情况下，通道是不带缓冲区的。发送端发送数据，同时必须有接收端相应的接收数据。

使用close(c chan)关闭通道,并不会丢失里面的数据，只是让读取通道数据的时候不会读完之后一直阻塞等待新数据写入
```
func say(s string) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s, (i+1)*100)
    }
}
func say2(s string) {
    for i := 0; i < 5; i++ {
        time.Sleep(150 * time.Millisecond)
        fmt.Println(s, (i+1)*150)
    }
}
func main() {
    //由于say2函数执行完所需的时间比say函数要长
    //当主线程中say函数执行完后，也不会等子线程中say2函数执行完，而是程序强制退出
    go say2("world")
    say("hello")
}



func say2(s string, ch chan int) {
    for i := 0; i < 5; i++ {
        time.Sleep(150 * time.Millisecond)
        fmt.Println(s, (i+1)*150)
    }
    ch <- 0
    close(ch)
}
func main() {
    //引入一个信道，默认的，信道的存消息和取消息都是阻塞的，
    //在 goroutine 中执行完成后给信道一个值 0，则主函数会一直等待信道中的值
    //一旦信道有值，主函数才会结束。
    ch := make(chan int)
    go say2("world", ch)
    say("hello")
    fmt.Println(<-ch)
}

//通道遵循先进先出原则。
ch := make(chan int, 2)

ch <- 1
a := <-ch
ch <- 2
ch <- 3

fmt.Println(<-ch)
fmt.Println(<-ch)
fmt.Println(a)
// 结果为
2
3
1

```
