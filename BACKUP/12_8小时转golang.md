# [8小时转golang](https://github.com/zhizunbao84/mygitblog/issues/12)

## 语法特性
go无需分号。
一对大括号的第一个大括号必须紧跟在表达式后面，而不能另起一行。

## 函数
golang里面有两个保留的函数：init函数（能够应用于所有的package）和main函数（只能应用于package main）。这两个函数在定义时不能有任何的参数和返回值。
虽然一个package里面可以写任意多个init函数，但这无论是对于可读性还是以后的可维护性来说，我们都强烈建议用户在一个package中每个文件只写一个init函数。
go程序会自动调用init()和main()，所以你不需要在任何地方调用这两个函数。每个package中的init函数都是可选的，但package main就必须包含一个main函数。
main函数不能有参数，也不能有返回值。
### import
如果main包还导入了其它的包，那么就会在编译时将它们依次导入。有时一个包会被多个包同时导入，那么它只会被导入一次（例如很多包可能都会用到fmt包，但它只会被导入一次，因为没有必要导入多次）。

当一个包被导入时，如果该包还导入了其它的包，那么会先将其它包导入进来，然后再对这些包中的包级常量和变量进行初始化，接着执行init函数（如果有的话），依次类推。

等所有被导入的包都加载完毕了，就会开始对main包中的包级常量和变量进行初始化，然后执行main包中的init函数（如果存在的话），最后执行main函数。下图详细地解释了整个执行过程：

```
//Lib1.go
package InitLib1

import "fmt"

func init() {
    fmt.Println("lib1")
}

//Lib2.go
package InitLib2

import "fmt"

func init() {
    fmt.Println("lib2")
}

//main.go
package main

import (
    "fmt"
    _ "GolangTraining/InitLib1"
    _ "GolangTraining/InitLib2"
)

func init() {
    fmt.Println("libmain init")
}

func main() {
    fmt.Println("libmian main")
}

//结果是一些简单的输出
lib1
lib2
libmain init
libmian main

//现在就改动一个地方，Lib1包导入Lib2，main包不管
package InitLib1

import (
    "fmt"
    _ "GolangTraining/InitLib2"
)

func init() {
    fmt.Println("lib1")
}
//输出：
lib2
lib1
libmain init
libmian main

```
main包以及Lib1包都导入了Lib2，但是只出现一次，并且最先输出，

说明如果一个包会被多个包同时导入，那么它只会被导入一次，而先输出lib2是因为main包中导入Lib1时，Lib1又导入了Lib2，会首先初始化Lib2包的东西.

## defer
defer语句被用于预定对一个函数的调用。可以把这类被defer语句调用的函数称为延迟函数。
 
defer作用： 
- 释放占用的资源
- 捕捉处理异常
- 输出日志
  
如果一个函数中有多个defer语句，它们会以LIFO（类似于压栈，后进先出）的顺序执行。
```
func deferfunc() int {
    fmt.Println("deferfunc......")
    return 0
}

func returnfunc() int {
    fmt.Println("returnfunc......")
    return 0
}

func deferandreturn() int {
	defer deferfunc()
    return returnfunc()
}

func main() {
    deferandreturn()
}
//结果
returnfunc......
deferfunc......

```
可以看出defer的顺序是在函数执行结束之前，在最后的大括号之前。

---

## slice
```
type slice struct {
        array unsafe.Pointer  // 这里的指针是第一个元素的指针
        len   int
        cap   int
}
```
之所以对slice的传递可以修改原slice数据，是因为使用了slice这个引用类型的指针进行的传递，但依旧是值传递。
指针值传递”相当于把指针复制了一份，结果就是两个指针指向同一个值，之后不管使用哪个指针，改的都是同一个值。实在不懂的话建议把P10再多看两遍。