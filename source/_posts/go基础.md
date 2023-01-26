---
title: go基础
date: 2023-01-25 10:14:41
tags: go基础
categories: go
---

# 开发环境
## go的安装

* 安装 mercurial、git、gcc包
```
[root@localhost ~]# yum install mercurial git gcc
```

* 下载Go的压缩包
* 用tar 命令来解压压缩包。
```
tar -zxvf go1.13.linux-amd64.tar.gz
```

* 建立Go的工作空间（workspace，也就是GOPATH环境变量指向的目录），GO代码必须在工作空间内。工作空间是一个目录，其中包含三个子目录：
src ---- 里面每一个子目录，就是一个包。包内是Go的源码文件
pkg ---- 编译后生成的，包的目标文件
bin ---- 生成的可执行文件

这里，我们在/home目录下, 建立一个名为go(可以不是go, 任意名字都可以)的文件夹， 然后再建立三个子文件夹(子文件夹名必须为src、pkg、bin)。

* 添加PATH环境变量and设置GOPATH环境变量
```
[root@localhost go]# vi /etc/profile
```
加入下面这三行:
```
export GOROOT=/usr/local/go        ##Golang安装目录
export PATH=$GOROOT/bin:$PATH
export GOPATH=/home/go  ##Golang项目目录
```

保存后，执行以下命令，使环境变量立即生效:
```
[root@localhost go]# source /etc/profile ##刷新环境变量
```
至此，Go语言的环境已经安装完毕。

* 验证一下是否安装成功，如果出现下面的信息说明安装成功了

```
[root@localhost go]# go version        ##查看go版本
go version go1.13 linux/amd64
```

* 查看Go语言的环境信息

```
[root@localhost go]# go env
```
防止go下载依赖较慢，可设置代理：
```
go env -w GOPROXY=https://goproxy.cn,direct
go env -w  GO111MODULE=on
go env -w GOARCH=amd64
```
## windows
需要在环境变量中设置GOPATH
![](go基础/GOPATH-1.png)
![](go基础/GOPATH-2.png)
![](go基础/GOPATH-3.png)


## 配置GOPATH

GOPATH是一个环境变量，用来表明你写的go项目的存放路径。
GOPATH路径最好只设置一个，所有的项目代码都放到GOPATH的src目录下。

### go的项目目录

在进行Go语言开发的时候，我们的代码总是会保存在$GOPATH/src目录下。在工程经过go build、go install或go get等指令后，会将下载的第三方包源代码文件放在$GOPATH/src目录下， 产生的二进制可执行文件放在 $GOPATH/bin目录下，生成的中间缓存文件会被保存在 $GOPATH/pkg 下。如果我们使用版本管理工具（Version Control System，VCS。常用如Git）来管理我们的项目代码时，我们只需要添加$GOPATH/src目录的源代码即可。bin 和 pkg 目录的内容无需版本控制。

### 目前流行的项目结构

Go语言中也是通过包来组织代码文件，我们可以引用别人的包也可以发布自己的包，但是为了防止不同包的项目名冲突，我们通常使用顶级域名来作为包名的前缀，这样就不担心项目名冲突的问题了。因为不是每个个人开发者都拥有自己的顶级域名，所以目前流行的方式是使用个人的github用户名来区分不同的包。
![](go基础/项目结构.png)

# go基础

## Go语言的主要特征

1.自动立即回收。
2.更丰富的内置类型。
3.函数多返回值。
4.错误处理。
5.匿名函数和闭包。
6.类型和接口。
7.并发编程。
8.反射。
9.语言交互性。

### 思想

Less can be more 大道至简,小而蕴真 让事情变得复杂很容易，让事情变得简单才难 深刻的工程文化

### Go语言命名

Go的函数、变量、常量、自定义类型、包(package)的命名方式遵循以下规则：

1. 首字符可以是任意的Unicode字符或者下划线
2. 剩余字符可以是Unicode字符、下划线、数字
3. 字符长度不限

Go只有25个关键字：
```

    break           default         func            interface       select
    case             defer            go               map              struct
    chan            else              goto            package       switch
    const           fallthrough   if                 range           type
    continue     for                import         return           var
```

Go还有37个保留字：
```

Constants:    true  false  iota  nil

Types:    int  int8  int16  int32  int64  
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```

可见性：

1. 声明在函数内部，是函数的本地值，类似private
2. 声明在函数外部，是对当前包可见(包内所有.go文件都可见)的全局值，类似protect
3. 声明在函数外部且首字母大写是所有包可见的全局值,类似public

### Go语言声明

有四种主要声明方式：
```
var（声明变量）, const（声明常量）, type（声明类型） ,func（声明函数）。
```
Go的程序是保存在多个.go文件中，文件的第一行就是package XXX声明，用来说明该文件属于哪个包(package)，package声明下来就是import声明，再下来是类型，变量，常量，函数的声明。

### Go项目构建及编译

一个Go工程中主要包含以下三个目录：
```
src：源代码文件
pkg：包文件
bin：相关bin文件
```

1. 建立工程文件夹 goproject
2. 在工程文件夹中建立src,pkg,bin文件夹
3. 在GOPATH中添加projiect路径 例 e:/goproject
4. 如工程中有自己的包examplepackage，那在src文件夹下建立以包名命名的文件夹 例 examplepackage
5. 在src文件夹下编写主程序代码代码 goproject.go
6. 在examplepackage文件夹中编写 examplepackage.go 和 包测试文件 examplepackage_test.go
7. 编译调试包
```
go build examplepackage
go test examplepackage
go install examplepackage
```

这时在pkg文件夹中可以发现会有一个相应的操作系统文件夹如windows_386z, 在这个文件夹中会有examplepackage文件夹，在该文件中有examplepackage.a文件

8. 编译主程序
```
go build goproject.go
```
成功后会生成goproject.exe文件，至此一个Go工程编辑成功。

### go 编译问题

golang的编译使用命令 go build , go install;除非仅写一个main函数，否则还是准备好目录结构； GOPATH=工程根目录；其下应创建src，pkg，bin目录，bin目录中用于生成可执行文件，pkg目录中用于生成.a文件； golang中的import name，实际是到GOPATH中去寻找name.a, 使用时是该name.a的源码中生命的package 名字；这个在前面已经介绍过了。
注意点：
```
1.系统编译时 go install abc_name时，系统会到GOPATH的src目录中寻找abc_name目录，然后编译其下的go文件；
    2.同一个目录中所有的go文件的package声明必须相同，所以main方法要单独放一个文件，否则在eclipse和liteide中都会报错；
    编译报错如下：（假设test目录中有个main.go 和mymath.go,其中main.go声明package为main，mymath.go声明packag 为test);
        $ go install test
        can't load package: package test: found packages main (main.go) and test (mymath.go) in /home/wanjm/go/src/test
        报错说 不能加载package test（这是命令行的参数），因为发现了两个package，分别时main.go 和 mymath.go;
    3.对于main方法，只能在bin目录下运行 go build path_tomain.go; 可以用-o参数指出输出文件名；
    4.可以添加参数 go build -gcflags "-N -l" ****,可以更好的便于gdb；详细参见 http://golang.org/doc/gdb
    5.gdb全局变量主一点。 如有全局变量 a；则应写为 p 'main.a'；注意但引号不可少；
```

## Golang内置类型和函数

### 内置类型

#### 值类型
```
bool
int(32 or 64), int8, int16, int32, int64
uint(32 or 64), uint8(byte), uint16, uint32, uint64
float32, float64
string
complex64, complex128
array    -- 固定长度的数组
```

#### 引用类型：(指针类型)
```
slice   -- 序列数组(最常用)
map     -- 映射
chan    -- 管道
```
### 内置函数

Go 语言拥有一些不需要进行导入操作就可以使用的内置函数。它们有时可以针对不同的类型进行操作，例如：len、cap 和 append，或必须用于系统级的操作，例如：panic。因此，它们需要直接获得编译器的支持。
```
append          -- 用来追加元素到数组、slice中,返回修改后的数组、slice
close           -- 主要用来关闭channel
delete            -- 从map中删除key对应的value
panic            -- 停止常规的goroutine  （panic和recover：用来做错误处理）
recover         -- 允许程序定义goroutine的panic动作
real            -- 返回complex的实部   （complex、real imag：用于创建和操作复数）
imag            -- 返回complex的虚部
make            -- 用来分配内存，返回Type本身(只能应用于slice, map, channel)
new                -- 用来分配内存，主要用来分配值类型，比如int、struct。返回指向Type的指针
cap                -- capacity是容量的意思，用于返回某个类型的最大容量（只能用于切片和 map）
copy            -- 用于复制和连接slice，返回复制的数目
len                -- 来求长度，比如string、array、slice、map、channel ，返回长度
print、println     -- 底层打印函数，在部署环境中建议使用 fmt 包
```

### 内置接口error
```
type error interface { //只要实现了Error()函数，返回值为String的都实现了err接口
            Error()    String
    }
```
## Init函数和main函数
### init函数
go语言中init函数用于包(package)的初始化，该函数是go语言的一个重要特性。
有下面的特征：

1. init函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等
2. 每个包可以拥有多个init函数
3. 包的每个源文件也可以拥有多个init函数
4. 同一个包中多个init函数的执行顺序go语言没有明确的定义(说明)
5. 不同包的init函数按照包导入的依赖关系决定该初始化函数的执行顺序
6. init函数不能被其他函数调用，而是在main函数执行之前，自动被调用

### main函数
Go语言程序的默认入口函数(主函数)：func main()函数体用｛｝一对括号包裹。
```
func main(){
        //函数体
    }
```
### init函数和main函数的异同

相同点：
        两个函数在定义时不能有任何的参数和返回值，且Go程序自动调用。
不同点：
        init可以应用于任意包中，且可以重复定义多个。
        main函数只能用于main包中，且只能定义一个。


两个函数的执行顺序：

* 对同一个go文件的init()调用顺序是从上到下的。
* 对同一个package中不同文件是按文件名字符串比较“从小到大”顺序调用各文件中的init()函数。
* 对于不同的package，如果不相互依赖的话，按照main包中"先import的后调用"的顺序调用其包中的init()，如果package存在依赖，则先调用最早被依赖的package中的init()，最后调用main函数。
* 如果init函数中使用了println()或者print()你会发现在执行过程中这两个不会按照你想象中的顺序执行。这两个函数官方只推荐在测试环境中使用，对于正式环境不要使用。
## 命令

* go env用于打印Go语言的环境信息。
* go run命令可以编译并运行命令源码文件。
* go get可以根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。
* go build命令用于编译我们指定的源码文件或代码包以及它们的依赖包。
* go install用于编译并安装指定的代码包及它们的依赖包。
* go clean命令会删除掉执行其它命令时产生的一些文件和目录。
* go doc命令可以打印附于Go语言程序实体上的文档。我们可以通过把程序实体的标识符作为该命令的参数来达到查看其文档的目的。
* go test命令用于对Go语言编写的程序进行测试。
* go list命令的作用是列出指定的代码包的信息。
* go fix会把指定代码包的所有Go语言源码文件中的旧版本代码修正为新版本的代码。
* go vet是一个用于检查Go语言源码中静态错误的简单工具。
* go tool pprof命令来交互式的访问概要文件的内容。

## 运算符

### 算数运算符

| 运算符 | 描述 |
| ------ | ---- |
| +      | 相加 |
| -      | 相减 |
| *      | 相乘 |
| /      | 相除 |
| %      | 取余 |

**注意： ++（自增）和--（自减）在Go语言中是单独的语句，并不是运算符。**

### 关系运算符

* ==
* !=
* >
* <
* >=
* <=

### 逻辑运算符

* &&
* ||
* !

### 位运算符

* &
* |
* ^
*     >> ：左移n位就是乘以2的n次方。
* < <

### 赋值运算符

* =
* +=
* -=
*      * =
* /=
* %=
*   <<=
*   >>=
*   &=
*   |=
*   ^=

## 下划线

“_”是特殊标识符，用来忽略结果。

### 下划线在import中

在Golang里，import的作用是导入其他package。

import 下划线（如：import hello/imp）的作用：当导入一个包时，该包下的文件里所有init()函数都会被执行，然而，有些时候我们并不需要把整个包都导入进来，仅仅是是希望它执行init()函数而已。这个时候就可以使用 import 引用该包。即使用【import _ 包路径】只是引用该包，仅仅是为了调用init()函数，所以无法通过包名来调用包中的其他函数。
```
package main

import _ "./hello"

func main() {
    // hello.Print() 
    //编译报错：./main.go:6:5: undefined: hello}
```

### 下划线在代码中

占位符，意思是那个位置本应赋给某个值，但是咱们不需要这个值。所以就把该值赋给下划线，意思是丢掉不要。这样编译器可以更好的优化，任何类型的单个值都可以丢给下划线。这种情况是占位用的，方法返回两个结果，而你只想要一个结果。那另一个就用 " _ " 占位，而如果用变量的话，不使用，编译器是会报错的。
```
package main

import (
    "os"
)

func main() {
    buf := make([]byte, 1024)
    f, _ := os.Open("/Users/***/Desktop/text.txt")
    defer f.Close()
    for {
        n, _ := f.Read(buf)
        if n == 0 {
            break    
        }
        os.Stdout.Write(buf[:n])
    }}
```

## 变量和常量

### 变量

#### 变量类型

变量（Variable）的功能是存储数据。不同的变量保存的数据类型可能会不一样。经过半个多世纪的发展，编程语言已经基本形成了一套固定的类型，常见变量的数据类型有：整型、浮点型、布尔型等。Go语言中的每一个变量都有自己的类型，并且变量必须经过声明才能开始使用。

#### 变量声明

Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。并且Go语言的变量声明后必须使用。

#### 标准声明

Go语言的变量声明格式为：
```
 var 变量名 变量类型
```
变量声明以关键字var开头，变量类型放在变量的后面，行尾无需分号。 举个例子：
```
var name string
var age int
var isOk bool
```

#### 批量声明

每声明一个变量就需要写var关键字会比较繁琐，go语言中还支持批量变量声明：
```
 var (
        a string
        b int
        c bool
        d float32
    )
```
#### 变量的初始化
Go语言在声明变量的时候，会自动对变量对应的内存区域进行初始化操作。每个变量会被初始化成其类型的默认值，例如： 整型和浮点型变量的默认值为0。 字符串变量的默认值为空字符串。 布尔型变量默认为false。 切片、函数、指针变量的默认为nil。当然我们也可在声明变量的时候为其指定初始值。变量初始化的标准格式如下
```
 var 变量名 类型 = 表达式
```
或者一次初始化多个变量：
```
var name, sex = "pprof.cn", 1
```
有时候我们会将变量的类型省略，这个时候编译器会根据等号右边的值来推导变量的类型完成初始化。
```
var name = "pprof.cn"
var sex = 1
```
##### 短变量声明

在函数内部，可以使用更简略的 := 方式声明并初始化变量。
```
package main

import (
    "fmt")// 全局变量mvar m = 100

func main() {
    n := 10
    m := 200 // 此处声明局部变量m
    fmt.Println(m, n)}
```
##### 匿名变量

在使用多重赋值时，如果想要忽略某个值，可以使用匿名变量（anonymous variable）。 匿名变量用一个下划线_表示，例如：
```
func foo() (int, string) {
    return 10, "Q1mi"
}
func main() {
    x, _ := foo()
    _, y := foo()
    fmt.Println("x=", x)
    fmt.Println("y=", y)
}
```

匿名变量不占用命名空间，不会分配内存，所以匿名变量之间不存在重复声明。 (在Lua等编程语言里，匿名变量也被叫做哑元变量。)

注意事项：

1. 函数外的每个语句都必须以关键字开始（var、const、func等）
2. :=不能使用在函数外。
3.  _ 多用于占位，表示忽略值。

### 常量

相对于变量，常量是恒定不变的值，多用于定义程序运行期间不会改变的那些值。 常量的声明和变量声明非常类似，只是把var换成了const，常量在定义的时候必须赋值。
```
const pi = 3.1415
const e = 2.7182
```
多个常量也可以一起声明：
```
const (
        pi = 3.1415
        e = 2.7182
)
```
const同时声明多个常量时，如果省略了值则表示和上面一行的值相同。 例如：
```
const (
        n1 = 100
        n2
        n3
)
```
上面示例中，常量n1、n2、n3的值都是100。

#### iota

iota是go语言的常量计数器，只能在常量的表达式中使用。 iota在const关键字出现时将被重置为0。const中每新增一行常量声明将使iota计数一次(iota可理解为const语句块中的行索引)。 使用iota能简化定义，在定义枚举时很有用。例如：
```
const (
            n1 = iota //0
            n2        //1
            n3        //2
            n4        //3
        )
```
#### 几个常见的iota示例

使用_跳过某些值：
```
const (
            n1 = iota //0
            n2        //1
            _
            n4        //3
        )
```
iota声明中间插队：
```
const (
            n1 = iota //0
            n2 = 100  //100
            n3 = iota //2
            n4        //3
        )
    const n5 = iota //0
```

定义数量级 （这里的<<表示左移操作，1<<10表示将1的二进制表示向左移10位，也就是由1变成了10000000000，也就是十进制的1024。同理2<<2表示将2的二进制表示向左移2位，也就是由10变成了1000，也就是十进制的8。）
```
const (
            _  = iota
            KB = 1 << (10 * iota)
            MB = 1 << (10 * iota)
            GB = 1 << (10 * iota)
            TB = 1 << (10 * iota)
            PB = 1 << (10 * iota)
        )
```
多个iota定义在一行：
```
const (
            a, b = iota + 1, iota + 2 //1,2
            c, d                      //2,3
            e, f                      //3,4
        )
```

## 基本类型

### 基本类型介绍

Golang 更明确的数字类型命名，支持 Unicode，支持常用数据结构。

| 类型          | 长度（字节） | 默认值 | 说明                                      |
| ------------- | ------------ | ------ | ----------------------------------------- |
| bool          | 1            | false  |                                           |
| byte          | 1            | 0      | uint8                                     |
| rune          | 4            | 0      | Unicode Code Point, int32                 |
| int, uint     | 4或8         | 0      | 32 或 64 位                               |
| int8, uint8   | 1            | 0      | -128 ~ 127, 0 ~ 255，byte是uint8 的别名   |
| int16, uint16 | 2            | 0      | -32768 ~ 32767, 0 ~ 65535                 |
| int32, uint32 | 4            | 0      | -21亿~ 21亿, 0 ~ 42亿，rune是int32 的别名 |
| int64, uint64 | 8            | 0      |                                           |
| float32       | 4            | 0.0    |                                           |
| float64       | 8            | 0.0    |                                           |
| complex64     | 8            |        |                                           |
| complex128    | 16           |        |                                           |
| uintptr       | 4或8         |        | 以存储指针的 uint32 或 uint64 整数        |
| array         |              |        | 值类型                                    |
| struct        |              |        | 值类型                                    |
| string        |              | ""     | UTF-8 字符串                              |
| slice         |              | nil    | 引用类型                                  |
| map           |              | nil    | 引用类型                                  |
| channel       |              | nil    | 引用类型                                  |
| interface     |              | nil    | 接口                                      |
| function      |              | nil    | 函数                                      |

#### 整型

整型分为以下两个大类： 按长度分为：int8、int16、int32、int64对应的无符号整型：uint8、uint16、uint32、uint64其中，uint8就是我们熟知的byte型，int16对应C语言中的short型，int64对应C语言中的long型。

#### 浮点型

Go语言支持两种浮点型数：float32和float64。这两种浮点型数据格式遵循IEEE 754标准： float32 的浮点数的最大范围约为3.4e38，可以使用常量定义：math.MaxFloat32。 float64 的浮点数的最大范围约为 1.8e308，可以使用一个常量定义：math.MaxFloat64。

#### 复数

complex64和complex128复数有实部和虚部，complex64的实部和虚部为32位，complex128的实部和虚部为64位。

#### 布尔值

Go语言中以bool类型进行声明布尔型数据，布尔型数据只有true（真）和false（假）两个值。

**注意：**

*     布尔类型变量的默认值为false。
*     Go 语言中不允许将整型强制转换为布尔型.
*     布尔型无法参与数值运算，也无法与其他类型进行转换。

#### 字符串

Go语言中的字符串以原生数据类型出现，使用字符串就像使用其他原生数据类型（int、bool、float32、float64 等）一样。 Go 语言里的字符串的内部实现使用UTF-8编码。 字符串的值为双引号(")中的内容，可以在Go语言的源码中直接添加非ASCII码字符，例如：
```
s1 := "hello"
s2 := "你好"
```
#### 字符串转义符

Go 语言的字符串常见转义符包含回车、换行、单双引号、制表符等，如下表所示。

| 转义 | 含义                               |
| ---- | ---------------------------------- |
| \r   | 回车符（返回行首）                 |
| \n   | 换行符（直接跳到下一行的同列位置） |
| \t   | 制表符                             |
| \'   | 单引号                             |
| \"   | 双引号                             |
| \    | 反斜杠                             |

#### 多行字符串

Go语言中要定义一个多行字符串时，就必须使用反引号字符：
```
s1 := `第一行
    第二行
    第三行
    `
    fmt.Println(s1)
```
反引号间换行将被作为字符串中的换行，但是所有的转义字符均无效，文本将会原样输出。

#### 字符串的常用操作

| 方法                                | 介绍                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| len(str)                            | 求长度                                                       |
| +或fmt.Sprintf                      | 拼接字符串                                                   |
| strings.Split                       | 分割                                                         |
| strings.Contains                    | 判断是否包含                                                 |
| strings.HasPrefix,strings.HasSuffix | 前缀/后缀判断                                                |
| strings.Index(),strings.LastIndex() | 子串出现的位置                                               |
| strings.Join(a[]string, sep string) | join操作，连接指定数组的元素或集合的成员，在每个元素或成员之间使用指定的分隔符。 |

#### byte和rune类型   

组成每个字符串的元素叫做“字符”，可以通过遍历或者单个获取字符串元素获得字符。 字符用单引号（’）包裹起来，如：
```
var a := '中'
var b := 'x'
```
Go 语言的字符有以下两种：

1. uint8类型，或者叫 byte 型，代表了ASCII码的一个字符。
2. rune类型，代表一个 UTF-8字符。

当需要处理中文、日文或者其他复合字符时，则需要用到rune类型。rune类型实际是一个int32。 Go 使用了特殊的 rune 类型来处理 Unicode，让基于 Unicode的文本处理更为方便，也可以使用 byte 型进行默认字符串处理，性能和扩展性都有照顾。
```
// 遍历字符串
    func traversalString() {
        s := "pprof.cn博客"
        for i := 0; i < len(s); i++ { //byte
            fmt.Printf("%v(%c) ", s[i], s[i])
        }
        fmt.Println()
        for _, r := range s { //rune
            fmt.Printf("%v(%c) ", r, r)
        }
        fmt.Println()
    }
 
 输出：
 
 112(p) 112(p) 114(r) 111(o) 102(f) 46(.) 99(c) 110(n) 229(å) 141() 154() 229(å) 174(®) 162(¢)
 
 112(p) 112(p) 114(r) 111(o) 102(f) 46(.) 99(c) 110(n) 21338(博) 23458(客)
```
因为UTF8编码下一个中文汉字由3~4个字节组成，所以我们不能简单的按照字节去遍历一个包含中文的字符串，否则就会出现上面输出中第一行的结果。字符串底层是一个byte数组，所以可以和[]byte类型相互转换。字符串是不能修改的 字符串是由byte字节组成，所以字符串的长度是byte字节的长度。 rune类型用来表示utf8字符，一个rune字符由一个或多个byte组成。

#### 修改字符串

要修改字符串，需要先将其转换成[]rune或[]byte，完成后再转换为string。无论哪种转换，都会重新分配内存，并复制字节数组。
```
func changeString() {
        s1 := "hello"
        // 强制类型转换
        byteS1 := []byte(s1)
        byteS1[0] = 'H'
        fmt.Println(string(byteS1))

        s2 := "博客"
        runeS2 := []rune(s2)
        runeS2[0] = '狗'
        fmt.Println(string(runeS2))
 }
```

#### 类型转换

Go语言中只有强制类型转换，没有隐式类型转换。该语法只能在两个类型之间支持相互转换的时候使用。强制类型转换的基本语法如下：
`T(表达式)`
其中，T表示要转换的类型。表达式包括变量、复杂算子和函数返回值等.

比如计算直角三角形的斜边长时使用math包的Sqrt()函数，该函数接收的是float64类型的参数，而变量a和b都是int类型的，这个时候就需要将a和b强制类型转换为float64类型。
```
func sqrtDemo() {
        var a, b = 3, 4
        var c int
        // math.Sqrt()接收的参数是float64类型，需要强制转换
        c = int(math.Sqrt(float64(a*a + b*b)))
        fmt.Println(c)
 }
```
## 数组Array

Golang Array和以往认知的数组有很大不同：

1. 数组：是同一种数据类型的固定长度的序列。
2. 数组定义：var a [len]int，比如：var a [5]int，数组长度必须是常量，且是类型的组成部分。一旦定义，长度不能变。
3. 长度是数组类型的一部分，因此，var a[5] int和var a [10]int是不同的类型。
4. 数组可以通过下标进行访问，下标是从0开始，最后一个元素下标是：len-1
```
 for i := 0; i < len(a); i++ {
 }
 
 for index, v := range a {
 }
```

5. 访问越界，如果下标在数组合法范围之外，则触发访问越界，会panic
6. 数组是值类型，赋值和传参会复制整个数组，而不是指针。因此改变副本的值，不会改变本身的值。
7. 支持 "=="、"!=" 操作符，因为内存总是被初始化过的。
8. 指针数组 [n]*T，数组指针 *[n]T。

### 数组初始化
#### 一维数组
```
全局：
var arr0 [5]int = [5]int{1, 2, 3}
var arr1 = [5]int{1, 2, 3, 4, 5}
var arr2 = [...]int{1, 2, 3, 4, 5, 6}
var str = [5]string{3: "hello world", 4: "tom"}
局部：
a := [3]int{1, 2}           // 未初始化元素值为 0。
b := [...]int{1, 2, 3, 4}   // 通过初始化值确定数组长度。
c := [5]int{2: 100, 4: 200} // 使用索引号初始化元素。
d := [...]struct {
    name string
    age  uint8
    }{
        {"user1", 10}, // 可省略元素类型。
        {"user2", 20}, // 别忘了最后一行的逗号。
    }
```
代码：
```
package main

import (
    "fmt"
)

var arr0 [5]int = [5]int{1, 2, 3}
var arr1 = [5]int{1, 2, 3, 4, 5}
var arr2 = [...]int{1, 2, 3, 4, 5, 6}
var str = [5]string{3: "hello world", 4: "tom"}

func main() {
    a := [3]int{1, 2}           // 未初始化元素值为 0。
    b := [...]int{1, 2, 3, 4}   // 通过初始化值确定数组长度。
    c := [5]int{2: 100, 4: 200} // 使用引号初始化元素。
    d := [...]struct {
        name string
        age  uint8
    }{
        {"user1", 10}, // 可省略元素类型。
        {"user2", 20}, // 别忘了最后一行的逗号。
    }
    fmt.Println(arr0, arr1, arr2, str)
    fmt.Println(a, b, c, d)}
```
#### 多维数组
```
全局
var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}
局部：
a := [2][3]int{{1, 2, 3}, {4, 5, 6}}
b := [...][2]int{{1, 1}, {2, 2}, {3, 3}} // 第 2 纬度不能用 "..."。
```
代码：
```
package main

import (
    "fmt"
)

var arr0 [5][3]int
var arr1 [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}

func main() {
    a := [2][3]int{{1, 2, 3}, {4, 5, 6}}
    b := [...][2]int{{1, 1}, {2, 2}, {3, 3}} // 第 2 纬度不能用 "..."。
    fmt.Println(arr0, arr1)
    fmt.Println(a, b)
}
```
值拷贝行为会造成性能问题，通常会建议使用 slice，或数组指针。

##### 多维数组遍历
```
package main

import (
    "fmt"
)

func main() {

    var f [2][3]int = [...][3]int{{1, 2, 3}, {7, 8, 9}}

    for k1, v1 := range f {
        for k2, v2 := range v1 {
            fmt.Printf("(%d,%d)=%d ", k1, k2, v2)
        }
        fmt.Println()
    }
}
```

### 数组拷贝和传参
```
package main

import "fmt"

func printArr(arr *[5]int) {
    arr[0] = 10
    for i, v := range arr {
        fmt.Println(i, v)
    }
}

func main() {
    var arr1 [5]int
    printArr(&arr1)
    fmt.Println(arr1)
    arr2 := [...]int{2, 4, 6, 8, 10}
    printArr(&arr2)
    fmt.Println(arr2)
}
```

## 切片Slice

需要说明，slice 并不是数组或数组指针。它通过内部指针和相关属性引用数组片段，以实现变长方案。

1. 切片：切片是数组的一个引用，因此切片是引用类型。但自身是结构体，值拷贝传递。
2. 切片的长度可以改变，因此，切片是一个可变的数组。
3. 切片遍历方式和数组一样，可以用len()求长度。表示可用元素数量，读写操作不能超过该限制。 
4. cap可以求出slice最大扩张容量，不能超出数组限制。0 <= len(slice) <= len(array)，其中array是slice引用的数组。
5. 切片的定义：var 变量名 []类型，比如 var str []string  var arr []int。
6. 如果 slice == nil，那么 len、cap 结果都等于 0。
### 创建切片的各种方式
```
package main

import "fmt"

func main() {
   //1.声明切片
   var s1 []int
   if s1 == nil {
      fmt.Println("是空")
   } else {
      fmt.Println("不是空")
   }
   // 2.:=
   s2 := []int{}
   // 3.make()
   var s3 []int = make([]int, 0)
   fmt.Println(s1, s2, s3)
   // 4.初始化赋值
   var s4 []int = make([]int, 0, 0)
   fmt.Println(s4)
   s5 := []int{1, 2, 3}
   fmt.Println(s5)
   // 5.从数组切片
   arr := [5]int{1, 2, 3, 4, 5}
   var s6 []int
   // 前包后不包
   s6 = arr[1:4]
   fmt.Println(s6)}
```

### 切片初始化
```
全局：
var arr = [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
var slice0 []int = arr[start:end] 
var slice1 []int = arr[:end]        
var slice2 []int = arr[start:]        
var slice3 []int = arr[:] 
var slice4 = arr[:len(arr)-1]      //去掉切片的最后一个元素
局部：
arr2 := [...]int{9, 8, 7, 6, 5, 4, 3, 2, 1, 0}
slice5 := arr[start:end]
slice6 := arr[:end]        
slice7 := arr[start:]     
slice8 := arr[:]  
slice9 := arr[:len(arr)-1] //去掉切片的最后一个元素
```
![](go基础/切片.png)
代码：

```
package main

import (
    "fmt")

var arr = [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}var slice0 []int = arr[2:8]var slice1 []int = arr[0:6]        //可以简写为 var slice []int = arr[:end]var slice2 []int = arr[5:10]       //可以简写为 var slice[]int = arr[start:]var slice3 []int = arr[0:len(arr)] //var slice []int = arr[:]var slice4 = arr[:len(arr)-1]      //去掉切片的最后一个元素func main() {
    fmt.Printf("全局变量：arr %v\n", arr)
    fmt.Printf("全局变量：slice0 %v\n", slice0)
    fmt.Printf("全局变量：slice1 %v\n", slice1)
    fmt.Printf("全局变量：slice2 %v\n", slice2)
    fmt.Printf("全局变量：slice3 %v\n", slice3)
    fmt.Printf("全局变量：slice4 %v\n", slice4)
    fmt.Printf("-----------------------------------\n")
    arr2 := [...]int{9, 8, 7, 6, 5, 4, 3, 2, 1, 0}
    slice5 := arr[2:8]
    slice6 := arr[0:6]         //可以简写为 slice := arr[:end]
    slice7 := arr[5:10]        //可以简写为 slice := arr[start:]
    slice8 := arr[0:len(arr)]  //slice := arr[:]
    slice9 := arr[:len(arr)-1] //去掉切片的最后一个元素
    fmt.Printf("局部变量： arr2 %v\n", arr2)
    fmt.Printf("局部变量： slice5 %v\n", slice5)
    fmt.Printf("局部变量： slice6 %v\n", slice6)
    fmt.Printf("局部变量： slice7 %v\n", slice7)
    fmt.Printf("局部变量： slice8 %v\n", slice8)
    fmt.Printf("局部变量： slice9 %v\n", slice9)
 }
```

输出结果：
```
全局变量：arr [0 1 2 3 4 5 6 7 8 9]
全局变量：slice0 [2 3 4 5 6 7]
全局变量：slice1 [0 1 2 3 4 5]
全局变量：slice2 [5 6 7 8 9]
全局变量：slice3 [0 1 2 3 4 5 6 7 8 9]
全局变量：slice4 [0 1 2 3 4 5 6 7 8]
-----------------------------------
局部变量： arr2 [9 8 7 6 5 4 3 2 1 0]
局部变量： slice5 [2 3 4 5 6 7]
局部变量： slice6 [0 1 2 3 4 5]
局部变量： slice7 [5 6 7 8 9]
局部变量： slice8 [0 1 2 3 4 5 6 7 8 9]
局部变量： slice9 [0 1 2 3 4 5 6 7 8]
```

### 通过make来创建切片
```
var slice []type = make([]type, len)
slice  := make([]type, len)
slice  := make([]type, len, cap)
```
![](go基础/make创建切片.png)
代码：

```
package main

import (
    "fmt"
)

var slice0 []int = make([]int, 10)
var slice1 = make([]int, 10)
var slice2 = make([]int, 10, 10)

func main() {
    fmt.Printf("make全局slice0 ：%v\n", slice0)
    fmt.Printf("make全局slice1 ：%v\n", slice1)
    fmt.Printf("make全局slice2 ：%v\n", slice2)
    fmt.Println("--------------------------------------")
    slice3 := make([]int, 10)
    slice4 := make([]int, 10)
    slice5 := make([]int, 10, 10)
    fmt.Printf("make局部slice3 ：%v\n", slice3)
    fmt.Printf("make局部slice4 ：%v\n", slice4)
    fmt.Printf("make局部slice5 ：%v\n", slice5)}
```
输出结果：
```
 make全局slice0 ：[0 0 0 0 0 0 0 0 0 0]
 make全局slice1 ：[0 0 0 0 0 0 0 0 0 0]
 make全局slice2 ：[0 0 0 0 0 0 0 0 0 0]
 --------------------------------------
make局部slice3 ：[0 0 0 0 0 0 0 0 0 0]
make局部slice4 ：[0 0 0 0 0 0 0 0 0 0]
make局部slice5 ：[0 0 0 0 0 0 0 0 0 0]
```
切片的内存布局：
![](go基础/切片内存布局.png)

读写操作实际目标是底层数组，只需注意索引号的差别。
```
package main

import (
    "fmt"
)

func main() {
    data := [...]int{0, 1, 2, 3, 4, 5}

    s := data[2:4]
    s[0] += 100
    s[1] += 200

    fmt.Println(s)
    fmt.Println(data)}
```
输出:
```
[102 203]
[0 1 102 203 4 5]
```

可直接创建 slice 对象，自动分配底层数组。
```
package main

import "fmt"

func main() {
    s1 := []int{0, 1, 2, 3, 8: 100} // 通过初始化表达式构造，可使用索引号。
    fmt.Println(s1, len(s1), cap(s1))

    s2 := make([]int, 6, 8) // 使用 make 创建，指定 len 和 cap 值。
    fmt.Println(s2, len(s2), cap(s2))

    s3 := make([]int, 6) // 省略 cap，相当于 cap = len。
    fmt.Println(s3, len(s3), cap(s3))
}
```
输出结果:
```
[0 1 2 3 0 0 0 0 100] 9 9
[0 0 0 0 0 0] 6 8
[0 0 0 0 0 0] 6 6
```

使用 make 动态创建slice，避免了数组必须用常量做长度的麻烦。还可用指针直接访问底层数组，退化成普通数组操作。
```
package main

import "fmt"

func main() {
    s := []int{0, 1, 2, 3}
    p := &s[2] // *int, 获取底层数组元素指针。
    *p += 100

    fmt.Println(s)}
```
输出结果:
```
[0 1 102 3]
```
至于 [][]T，是指元素类型为 []T 。
可直接修改 struct array/slice 成员。
```
package main

import (
    "fmt"
)

func main() {
    d := [5]struct {
        x int
    }{}
    s := d[:]
    d[1].x = 10
    s[2].x = 20
    fmt.Println(d)
    fmt.Printf("%p, %p\n", &d, &d[0])
}
```
输出结果:
```
[{0} {10} {20} {0} {0}]
0xc4200160f0, 0xc4200160f0
```

### 用append内置函数操作切片（切片追加）
```
package main

import (
    "fmt"
)

func main() {
    var a = []int{1, 2, 3}
    fmt.Printf("slice a : %v\n", a)
    var b = []int{4, 5, 6}
    fmt.Printf("slice b : %v\n", b)
    c := append(a, b...)
    fmt.Printf("slice c : %v\n", c)
    d := append(c, 7)
    fmt.Printf("slice d : %v\n", d)
    e := append(d, 8, 9, 10)
    fmt.Printf("slice e : %v\n", e)
}
```
输出结果：
```
slice a : [1 2 3]
slice b : [4 5 6]
slice c : [1 2 3 4 5 6]
slice d : [1 2 3 4 5 6 7]
slice e : [1 2 3 4 5 6 7 8 9 10]
```
append ：向 slice 尾部添加数据，返回新的 slice 对象。

### 超出原 slice.cap 限制，就会重新分配底层数组，即便原数组并未填满。
```
package main

import (
    "fmt"
)

func main() {
    data := [...]int{0, 1, 2, 3, 4, 10: 0}
    s := data[:2:3]
    s = append(s, 100, 200) // 一次 append 两个值，超出 s.cap 限制。
    fmt.Println(s, data)         // 重新分配底层数组，与原数组无关。
    fmt.Println(&s[0], &data[0]) // 比对底层数组起始指针。
}
```
输出结果:
```
[0 1 100 200] [0 1 2 3 4 0 0 0 0 0 0]
0xc4200160f0 0xc420070060
```

从输出结果可以看出，append 后的 s 重新分配了底层数组，并复制数据。如果只追加一个值，则不会超过 s.cap 限制，也就不会重新分配。 通常以 2 倍容量重新分配底层数组。在大批量添加数据时，建议一次性分配足够大的空间，以减少内存分配和数据复制开销。或初始化足够长的 len 属性，改用索引号进行操作。及时释放不再使用的 slice 对象，避免持有过期数组，造成 GC 无法回收。

### slice中cap重新分配规律：
```
package main

import (
    "fmt"
)

func main() {
    s := make([]int, 0, 1)
    c := cap(s)
    for i := 0; i < 50; i++ {
        s = append(s, i)
        if n := cap(s); n > c {
            fmt.Printf("cap: %d -> %d\n", c, n)
            c = n
        }
    }
}
```
输出结果:
```
cap: 1 -> 2
cap: 2 -> 4
cap: 4 -> 8
cap: 8 -> 16
cap: 16 -> 32
cap: 32 -> 64
```

### 切片拷贝
```
package main

import (
    "fmt"
)

func main() {
    s1 := []int{1, 2, 3, 4, 5}
    fmt.Printf("slice s1 : %v\n", s1)
    s2 := make([]int, 10)
    fmt.Printf("slice s2 : %v\n", s2)
    copy(s2, s1)
    fmt.Printf("copied slice s1 : %v\n", s1)
    fmt.Printf("copied slice s2 : %v\n", s2)
    s3 := []int{1, 2, 3}
    fmt.Printf("slice s3 : %v\n", s3)
    s3 = append(s3, s2...)
    fmt.Printf("appended slice s3 : %v\n", s3)
    s3 = append(s3, 4, 5, 6)
    fmt.Printf("last slice s3 : %v\n", s3)
}
```
输出结果：
```
slice s1 : [1 2 3 4 5]
slice s2 : [0 0 0 0 0 0 0 0 0 0]
copied slice s1 : [1 2 3 4 5]
copied slice s2 : [1 2 3 4 5 0 0 0 0 0]
slice s3 : [1 2 3]
appended slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0]
last slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0 4 5 6]
```
copy ：函数 copy 在两个 slice 间复制数据，复制长度以 len 小的为准。两个 slice 可指向同一底层数组，允许元素区间重叠。
应及时将所需数据 copy 到较小的 slice，以便释放超大号底层数组内存。

### slice遍历
```
package main

import (
    "fmt"
)

func main() {
    data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    slice := data[:]
    for index, value := range slice {
        fmt.Printf("inde : %v , value : %v\n", index, value)
    }
}
```
输出结果：
```
    inde : 0 , value : 0
    inde : 1 , value : 1
    inde : 2 , value : 2
    inde : 3 , value : 3
    inde : 4 , value : 4
    inde : 5 , value : 5
    inde : 6 , value : 6
    inde : 7 , value : 7
    inde : 8 , value : 8
    inde : 9 , value : 9
```
### 切片resize（调整大小）
```

package main

import (
    "fmt"
)

func main() {
    var a = []int{1, 3, 4, 5}
    fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a))
    b := a[1:2]
    fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b))
    c := b[0:3]
    fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c))
}
```
输出结果：
```
    slice a : [1 3 4 5] , len(a) : 4
    slice b : [3] , len(b) : 1
    slice c : [3 4 5] , len(c) : 3
```

### 数组和切片的内存布局
![](go基础/数组和切片的内存布局.png)

### 字符串和切片（string and slice）

string底层就是一个byte的数组，因此，也可以进行切片操作。
```
package main

import (
    "fmt"
)

func main() {
    str := "hello world"
    s1 := str[0:5]
    fmt.Println(s1)
    s2 := str[6:]
    fmt.Println(s2)}
```
输出结果：
```
    hello
    world
```
string本身是不可变的，因此要改变string中字符。需要如下操作： 
#### 英文字符串
```
package main

import (
    "fmt"
)

func main() {
    str := "Hello world"
    s := []byte(str) //中文字符需要用[]rune(str)
    s[6] = 'G'
    s = s[:8]
    s = append(s, '!')
    str = string(s)
    fmt.Println(str)}
```
输出结果：Hello Go!

#### 中文字符串
```
package main

import (
    "fmt")

func main() {
    str := "你好，世界！hello world！"
    s := []rune(str) 
    s[3] = '够'
    s[4] = '浪'
    s[12] = 'g'
    s = s[:14]
    str = string(s)
    fmt.Println(str)}
```
输出结果：
```
你好，够浪！hello go
```
golang slice data[:6:8] 两个冒号的理解常规
slice , data[6:8]，从第6位到第8位（返回6，7），长度len为2， 最大可扩充长度cap为4（6-9）
另一种写法： data[:6:8] 每个数字前都有个冒号， slice内容为data从0到第6位，长度len为6，最大扩充项cap设置为8
a[x:y:z] 切片内容 [x:y] 切片长度: y-x 切片容量:z-x


## 指针

区别于C/C++中的指针，Go语言中的指针不能进行偏移和运算，是安全指针。
要搞明白Go语言中的指针需要先知道3个概念：指针地址、指针类型和指针取值。

### Go语言中的指针

Go语言中的函数传参都是值拷贝，当我们想要修改某个变量的时候，我们可以创建一个指向该变量地址的指针变量。传递数据使用指针，而无须拷贝数据。类型指针不能进行偏移和运算。Go语言中的指针操作非常简单，只需要记住两个符号：&（取地址）和* （根据地址取值）。

#### 指针地址和指针类型

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用&字符放在变量前面对变量进行“取地址”操作。 Go语言中的值类型（int、float、bool、string、array、struct）都有对应的指针类型，如：* int、* int64、* string等。
取变量指针的语法如下：
```
ptr := &v    // v的类型为T
```
其中：

* v:代表被取地址的变量，类型为T
*  ptr:用于接收地址的变量，ptr的类型就为* T，称做T的指针类型。* 代表指针。

举个例子：
```
func main() {
    a := 10
    b := &a
    fmt.Printf("a:%d ptr:%p\n", a, &a) // a:10 ptr:0xc00001a078
    fmt.Printf("b:%p type:%T\n", b, b) // b:0xc00001a078 type:*int
    fmt.Println(&b)                    // 0xc00000e018
 }
```

我们来看一下b := &a的图示：
![](go基础/指针地址和指针类型.png)

#### 指针取值

在对普通变量使用&操作符取地址后会获得这个变量的指针，然后可以对指针使用* 操作，也就是指针取值，代码如下。
```
func main() {
    //指针取值
    a := 10
    b := &a // 取变量a的地址，将指针保存到b中
    fmt.Printf("type of b:%T\n", b)
    c := *b // 指针取值（根据指针去内存取值）
    fmt.Printf("type of c:%T\n", c)
    fmt.Printf("value of c:%v\n", c)
 }
```
输出如下：
```
type of b:*int
type of c:int
value of c:10
```
总结： 取地址操作符&和取值操作符* 是一对互补操作符，&取出地址，* 根据地址取出地址指向的值。变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

1. 对变量进行取地址（&）操作，可以获得这个变量的指针变量。
2. 指针变量的值是指针地址。
3. 对指针变量进行取值（* ）操作，可以获得指针变量指向的原变量的值。

指针传值示例：
```
func modify1(x int) {
    x = 100}

func modify2(x *int) {
    *x = 100}

func main() {
    a := 10
    modify1(a)
    fmt.Println(a) // 10
    modify2(&a)
    fmt.Println(a) // 100}
```

#### 空指针

* 当一个指针被定义后没有分配到任何变量时，它的值为 nil
* 空指针的判断
```
package main

import "fmt"

func main() {
    var p *string
    fmt.Println(p)
    fmt.Printf("p的值是%v\n", p)
    if p != nil {
        fmt.Println("非空")
    } else {
        fmt.Println("空值")
    }}
```
#### new和make
```
func main() {
    var a *int
    *a = 100
    fmt.Println(*a)

    var b map[string]int
    b["测试"] = 100
    fmt.Println(b)}
```

执行上面的代码会引发panic，为什么呢？ 在Go语言中对于引用类型的变量，我们在使用的时候不仅要声明它，还要为它分配内存空间，否则我们的值就没办法存储。而对于值类型的声明不需要分配内存空间，是因为它们在声明的时候已经默认分配好了内存空间。要分配内存，就引出来今天的new和make。 Go语言中new和make是内建的两个函数，主要用来分配内存。

##### new

new是一个内置的函数，它的函数签名如下：
```
 func new(Type) *Type
```
其中，

1. Type表示类型，new函数只接受一个参数，这个参数是一个类型
2. * Type表示类型指针，new函数返回一个指向该类型内存地址的指针。

new函数不太常用，使用new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值。举个例子：
```
func main() {
    a := new(int)
    b := new(bool)
    fmt.Printf("%T\n", a) // *int
    fmt.Printf("%T\n", b) // *bool
    fmt.Println(*a)       // 0
    fmt.Println(*b)       // false}
```

本节开始的示例代码中var a * int只是声明了一个指针变量a但是没有初始化，指针作为引用类型需要初始化后才会拥有内存空间，才可以给它赋值。应该按照如下方式使用内置的new函数对a进行初始化之后就可以正常对其赋值了：
```
func main() {
    var a *int
    a = new(int)
    *a = 10
    fmt.Println(*a)}
```

##### make

make也是用于内存分配的，区别于new，它只用于slice、map以及chan的内存创建，而且它返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。make函数的函数签名如下：
```
func make(t Type, size ...IntegerType) Type
```
make函数是无可替代的，我们在使用slice、map以及channel的时候，都需要使用make进行初始化，然后才可以对它们进行操作。这个我们在上一章中都有说明，关于channel我们会在后续的章节详细说明。
本节开始的示例中var b map[string]int只是声明变量b是一个map类型的变量，需要像下面的示例代码一样使用make函数进行初始化操作之后，才能对其进行键值对赋值：
```
func main() {
    var b map[string]int
    b = make(map[string]int, 10)
    b["测试"] = 100
    fmt.Println(b)}
```
##### new与make的区别

1. 二者都是用来做内存分配的。
2. make只用于slice、map以及channel的初始化，返回的还是这三个引用类型本身；
3. 而new用于类型的内存分配，并且内存对应的值为类型零值，返回的是指向类型的指针。

## Map

map是一种无序的基于key-value的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

### map定义

Go语言中 map的定义语法如下：
```
map[KeyType]ValueType
```

其中，KeyType:表示键的类型，ValueType:表示键对应的值的类型。
map类型的变量默认初始值为nil，需要使用make()函数来分配内存。语法为：
```
make(map[KeyType]ValueType, [cap])
```
其中cap表示map的容量，该参数虽然不是必须的，但是我们应该在初始化map的时候就为其指定一个合适的容量。

### map基本使用

map中的数据都是成对出现的，map的基本使用示例代码如下：
```
func main() {
    scoreMap := make(map[string]int, 8)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    fmt.Println(scoreMap)
    fmt.Println(scoreMap["小明"])
    fmt.Printf("type of a:%T\n", scoreMap)
 }
 输出：
 
map[小明:100 张三:90]
100
type of a:map[string]int
```

map也支持在声明的时候填充元素，例如：
```
func main() {
    userInfo := map[string]string{
        "username": "pprof.cn",
        "password": "123456",
    }
    fmt.Println(userInfo) //}
```
### 判断某个键是否存在

Go语言中有个判断map中键是否存在的特殊写法，格式如下:
```
value, ok := map[key]
```
举个例子：
```
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    // 如果key存在ok为true,v为对应的值；不存在ok为false,v为值类型的零值
    v, ok := scoreMap["张三"]
    if ok {
        fmt.Println(v)
    } else {
        fmt.Println("查无此人")
    }}
```

### map的遍历

Go语言中使用for range遍历map。
```
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    for k, v := range scoreMap {
        fmt.Println(k, v)
    }}
```
但我们只想遍历key的时候，可以按下面的写法：
```
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    for k := range scoreMap {
        fmt.Println(k)
    }}
```
**注意： 遍历map时的元素顺序与添加键值对的顺序无关。**

### 使用delete()函数删除键值对

使用delete()内建函数从map中删除一组键值对，delete()函数的格式如下：
```
delete(map, key)
```
其中，map:表示要删除键值对的map， key:表示要删除的键值对的键
示例代码如下：
```
func main(){
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    delete(scoreMap, "小明")//将小明:100从map中删除
    for k,v := range scoreMap{
        fmt.Println(k, v)
    }}
```
### 按照指定顺序遍历map
```
func main() {
    rand.Seed(time.Now().UnixNano()) //初始化随机数种子

    var scoreMap = make(map[string]int, 200)

    for i := 0; i < 100; i++ {
        key := fmt.Sprintf("stu%02d", i) //生成stu开头的字符串
        value := rand.Intn(100)          //生成0~99的随机整数
        scoreMap[key] = value
    }
    //取出map中的所有key存入切片keys
    var keys = make([]string, 0, 200)
    for key := range scoreMap {
        keys = append(keys, key)
    }
    //对切片进行排序
    sort.Strings(keys)
    //按照排序后的key遍历map
    for _, key := range keys {
        fmt.Println(key, scoreMap[key])
    }}
```

### 元素为map类型的切片
```
func main() {
    var mapSlice = make([]map[string]string, 3)
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
    fmt.Println("after init")
    // 对切片中的map元素进行初始化
    mapSlice[0] = make(map[string]string, 10)
    mapSlice[0]["name"] = "王五"
    mapSlice[0]["password"] = "123456"
    mapSlice[0]["address"] = "红旗大街"
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }}
```
### 值为切片类型的map
```
func main() {
    var sliceMap = make(map[string][]string, 3)
    fmt.Println(sliceMap)
    fmt.Println("after init")
    key := "中国"
    value, ok := sliceMap[key]
    if !ok {
        value = make([]string, 0, 2)
    }
    value = append(value, "北京", "上海")
    sliceMap[key] = value
    fmt.Println(sliceMap)}
```

## 结构体

Go语言中没有“类”的概念，也不支持“类”的继承等面向对象的概念。Go语言中通过结构体的内嵌再配合接口比面向对象具有更高的扩展性和灵活性。

### 类型别名和自定义类型

#### 自定义类型

在Go语言中有一些基本的数据类型，如string、整型、浮点型、布尔等数据类型，Go语言中可以使用type关键字来定义自定义类型。自定义类型是定义了一个全新的类型。我们可以基于内置的基本类型定义，也可以通过struct定义。例如：
```
//将MyInt定义为int类型
type MyInt int
```
通过Type关键字的定义，MyInt就是一种新的类型，它具有int的特性。

#### 类型别名

类型别名是Go1.9版本添加的新功能。类型别名规定：TypeAlias只是Type的别名，本质上TypeAlias与Type是同一个类型。就像一个孩子小时候有小名、乳名，上学后用学名，英语老师又会给他起英文名，但这些名字都指的是他本人。
```
type TypeAlias = Type
```
我们之前见过的rune和byte就是类型别名，他们的定义如下：
```
type byte = uint8
type rune = int32
```

#### 类型定义和类型别名的区别

类型别名与类型定义表面上看只有一个等号的差异，我们通过下面的这段代码来理解它们之间的区别。
```
//类型定义type NewInt int

//类型别名type MyInt = int

func main() {
    var a NewInt
    var b MyInt
    fmt.Printf("type of a:%T\n", a) //type of a:main.NewInt
    fmt.Printf("type of b:%T\n", b) //type of b:int}
```

结果显示a的类型是main.NewInt，表示main包下定义的NewInt类型。b的类型是int。MyInt类型只会在代码中存在，编译完成时并不会有MyInt类型。

### 结构体

Go语言中的基础数据类型可以表示一些事物的基本属性，但是当我们想表达一个事物的全部或部分属性时，这时候再用单一的基本数据类型明显就无法满足需求了，Go语言提供了一种自定义数据类型，可以封装多个基本数据类型，这种数据类型叫结构体，英文名称struct。 也就是我们可以通过struct来定义自己的类型了。Go语言中通过struct来实现面向对象。
#### 结构体的定义

使用type和struct关键字来定义结构体，具体代码格式如下：
```
type 类型名 struct {
    字段名 字段类型
    字段名 字段类型
    …
}
```
其中：

1. 类型名：标识自定义结构体的名称，在同一个包内不能重复。
2. 字段名：表示结构体字段名。结构体中的字段名必须唯一。
3. 字段类型：表示结构体字段的具体类型。

举个例子，我们定义一个Person（人）结构体，代码如下：
```
type person struct {
    name string
    city string
    age  int8
}
```

同样类型的字段也可以写在一行，
```
type person1 struct {
    name, city string
    age        int8
}
```
这样我们就拥有了一个person的自定义类型，它有name、city、age三个字段，分别表示姓名、城市和年龄。这样我们使用这个person结构体就能够很方便的在程序中表示和存储人信息了。语言内置的基础数据类型是用来描述一个值的，而结构体是用来描述一组值的。比如一个人有名字、年龄和居住城市等，本质上是一种聚合型的数据类型。

#### 结构体实例化

只有当结构体实例化时，才会真正地分配内存。也就是必须实例化后才能使用结构体的字段。结构体本身也是一种类型，我们可以像声明内置类型一样使用var关键字声明结构体类型。
```
var 结构体实例 结构体类型
```

#### 基本实例化
```
type person struct {
    name string
    city string
    age  int8}

func main() {
    var p1 person
    p1.name = "pprof.cn"
    p1.city = "北京"
    p1.age = 18
    fmt.Printf("p1=%v\n", p1)  //p1={pprof.cn 北京 18}
    fmt.Printf("p1=%#v\n", p1) //p1=main.person{name:"pprof.cn", city:"北京", age:18}}
```
我们通过.来访问结构体的字段（成员变量）,例如p1.name和p1.age等。

#### 匿名结构体

在定义一些临时数据结构等场景下还可以使用匿名结构体。
```
package main

import (
    "fmt")

func main() {
    var user struct{Name string; Age int}
    user.Name = "pprof.cn"
    user.Age = 18
    fmt.Printf("%#v\n", user)}
```

#### 创建指针类型结构体

我们还可以通过使用new关键字对结构体进行实例化，得到的是结构体的地址。 格式如下：
```
var p2 = new(person)
fmt.Printf("%T\n", p2)     //*main.person
fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"", city:"", age:0}
```

从打印的结果中我们可以看出p2是一个结构体指针。需要注意的是在Go语言中支持对结构体指针直接使用.来访问结构体的成员。
```
var p2 = new(person)
p2.name = "测试"
p2.age = 18
p2.city = "北京"
fmt.Printf("p2=%#v\n", p2) //p2=&main.person{name:"测试", city:"北京", age:18}
```
#### 取结构体的地址实例化

使用&对结构体进行取地址操作相当于对该结构体类型进行了一次new实例化操作。
```
p3 := &person{}
fmt.Printf("%T\n", p3)     //*main.person
fmt.Printf("p3=%#v\n", p3) //p3=&main.person{name:"", city:"", age:0}
p3.name = "博客"
p3.age = 30
p3.city = "成都"
fmt.Printf("p3=%#v\n", p3) //p3=&main.person{name:"博客", city:"成都", age:30}
```

p3.name = "博客"其实在底层是(* p3).name = "博客"，这是Go语言帮我们实现的语法糖。

#### 结构体初始化
```
type person struct {
    name string
    city string
    age  int8}

func main() {
    var p4 person
    fmt.Printf("p4=%#v\n", p4) //p4=main.person{name:"", city:"", age:0}}
```
##### 使用键值对初始化

使用键值对对结构体进行初始化时，键对应结构体的字段，值对应该字段的初始值。
```
p5 := person{
    name: "pprof.cn",
    city: "北京",
    age:  18,}
fmt.Printf("p5=%#v\n", p5) //p5=main.person{name:"pprof.cn", city:"北京", age:18}
```
也可以对结构体指针进行键值对初始化，例如：
```
p6 := &person{
    name: "pprof.cn",
    city: "北京",
    age:  18,}
fmt.Printf("p6=%#v\n", p6) //p6=&main.person{name:"pprof.cn", city:"北京", age:18}
```
当某些字段没有初始值的时候，该字段可以不写。此时，没有指定初始值的字段的值就是该字段类型的零值。
```
p7 := &person{
    city: "北京",}
fmt.Printf("p7=%#v\n", p7) //p7=&main.person{name:"", city:"北京", age:0}
```
##### 使用值的列表初始化

初始化结构体的时候可以简写，也就是初始化的时候不写键，直接写值：
```
p8 := &person{
    "pprof.cn",
    "北京",
    18,}
fmt.Printf("p8=%#v\n", p8) //p8=&main.person{name:"pprof.cn", city:"北京", age:18}
```
使用这种格式初始化时，需要注意：

1. 必须初始化结构体的所有字段。
2. 初始值的填充顺序必须与字段在结构体中的声明顺序一致。
3. 该方式不能和键值初始化方式混用。


#### 结构体内存布局
```
type test struct {
    a int8
    b int8
    c int8
    d int8}
n := test{
    1, 2, 3, 4,}
fmt.Printf("n.a %p\n", &n.a)
fmt.Printf("n.b %p\n", &n.b)
fmt.Printf("n.c %p\n", &n.c)
fmt.Printf("n.d %p\n", &n.d)
```
输出：
```
n.a 0xc0000a0060
n.b 0xc0000a0061
n.c 0xc0000a0062
n.d 0xc0000a0063
```

#### 构造函数

Go语言的结构体没有构造函数，我们可以自己实现。 例如，下方的代码就实现了一个person的构造函数。 因为struct是值类型，如果结构体比较复杂的话，值拷贝性能开销会比较大，所以该构造函数返回的是结构体指针类型。
```
func newPerson(name, city string, age int8) *person {
    return &person{
        name: name,
        city: city,
        age:  age,
    }}
```
调用构造函数：
```
p9 := newPerson("pprof.cn", "测试", 90)
fmt.Printf("%#v\n", p9)
```

#### 方法和接收者

Go语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收者（Receiver）。接收者的概念就类似于其他语言中的this或者 self。

方法的定义格式如下：
```
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
        函数体
    }
```
其中，

1. 接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是self、this之类的命名。例如，Person类型的接收者变量应该命名为 p，Connector类型的接收者变量应该命名为c等。
2. 接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。
3. 方法名、参数列表、返回参数：具体格式与函数定义相同。
```
//Person 结构体
type Person struct {
    name string
    age  int8}

//NewPerson 构造函数
func NewPerson(name string, age int8) *Person {
    return &Person{
        name: name,
        age:  age,
    }}

//Dream Person做梦的方法
func (p Person) Dream() {
    fmt.Printf("%s的梦想是学好Go语言！\n", p.name)}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()}
```

方法与函数的区别是，函数不属于任何类型，方法属于特定的类型。

#### 指针类型的接收者
指针类型的接收者由一个结构体的指针组成，由于指针的特性，调用方法时修改接收者指针的任意成员变量，在方法结束后，修改都是有效的。这种方式就十分接近于其他语言中面向对象中的this或者self。 例如我们为Person添加一个SetAge方法，来修改实例变量的年龄。
```
// SetAge 设置p的年龄
// 使用指针接收者
func (p *Person) SetAge(newAge int8) {
    p.age = newAge
}
```
调用该方法：
```
func main() {
    p1 := NewPerson("测试", 25)
    fmt.Println(p1.age) // 25
    p1.SetAge(30)
    fmt.Println(p1.age) // 30}
```

#### 值类型的接收者

当方法作用于值类型接收者时，Go语言会在代码运行时将接收者的值复制一份。在值类型接收者的方法中可以获取接收者的成员值，但修改操作只是针对副本，无法修改接收者变量本身。
```
// SetAge2 设置p的年龄// 使用值接收者
func (p Person) SetAge2(newAge int8) {
    p.age = newAge
}

func main() {
    p1 := NewPerson("测试", 25)
    p1.Dream()
    fmt.Println(p1.age) // 25
    p1.SetAge2(30) // (*p1).SetAge2(30)
    fmt.Println(p1.age) // 25}
```

#### 什么时候应该使用指针类型接收者

1. 需要修改接收者中的值
2. 接收者是拷贝代价比较大的大对象
3. 保证一致性，如果有某个方法使用了指针接收者，那么其他的方法也应该使用指针接收者。

#### 任意类型添加方法

在Go语言中，接收者的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。 举个例子，我们基于内置的int类型使用type关键字可以定义新的自定义类型，然后为我们的自定义类型添加方法。
```

//MyInt 将int定义为自定义MyInt类型
type MyInt int

//SayHello 为MyInt添加一个SayHello的方法
func (m MyInt) SayHello() {
    fmt.Println("Hello, 我是一个int。")}
    
func main() {
    var m1 MyInt
    m1.SayHello() //Hello, 我是一个int。
    m1 = 100
    fmt.Printf("%#v  %T\n", m1, m1) //100  main.MyInt}
```
注意事项： 非本地类型不能定义方法，也就是说我们不能给别的包的类型定义方法。

#### 结构体的匿名字段

结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。
```

//Person 结构体Person类型
type Person struct {
    string
    int}

func main() {
    p1 := Person{
        "pprof.cn",
        18,
    }
    fmt.Printf("%#v\n", p1)        //main.Person{string:"pprof.cn", int:18}
    fmt.Println(p1.string, p1.int) //pprof.cn 18}
```
匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个。

#### 嵌套结构体

一个结构体中可以嵌套包含另一个结构体或结构体指针。
```
//Address 地址结构体
type Address struct {
    Province string
    City     string}

//User 用户结构体
type User struct {
    Name    string
    Gender  string
    Address Address
}

func main() {
    user1 := User{
        Name:   "pprof",
        Gender: "女",
        Address: Address{
            Province: "黑龙江",
            City:     "哈尔滨",
        },
    }
    fmt.Printf("user1=%#v\n", user1)//user1=main.User{Name:"pprof", Gender:"女", Address:main.Address{Province:"黑龙江", City:"哈尔滨"}}}
```

#### 嵌套匿名结构体
```
//Address 地址结构体
type Address struct {
    Province string
    City     string}

//User 用户结构体
type User struct {
    Name    string
    Gender  string
    Address //匿名结构体}

func main() {
    var user2 User
    user2.Name = "pprof"
    user2.Gender = "女"
    user2.Address.Province = "黑龙江"    //通过匿名结构体.字段名访问
    user2.City = "哈尔滨"                //直接访问匿名结构体的字段名
    fmt.Printf("user2=%#v\n", user2) //user2=main.User{Name:"pprof", Gender:"女", Address:main.Address{Province:"黑龙江", City:"哈尔滨"}}}
```
当访问结构体成员时会先在结构体中查找该字段，找不到再去匿名结构体中查找。

#### 嵌套结构体的字段名冲突

嵌套结构体内部可能存在相同的字段名。这个时候为了避免歧义需要指定具体的内嵌结构体的字段。
```
//Address 地址结构体
type Address struct {
    Province   string
    City       string
    CreateTime string}

//Email 邮箱结构体
type Email struct {
    Account    string
    CreateTime string}

//User 用户结构体
type User struct {
    Name   string
    Gender string
    Address
    Email
}

func main() {
    var user3 User
    user3.Name = "pprof"
    user3.Gender = "女"
    // user3.CreateTime = "2019" //ambiguous selector user3.CreateTime
    user3.Address.CreateTime = "2000" //指定Address结构体中的CreateTime
    user3.Email.CreateTime = "2000"   //指定Email结构体中的CreateTime}
```

#### 结构体的“继承”

Go语言中使用结构体也可以实现其他编程语言中面向对象的继承。
```
//Animal 动物
type Animal struct {
    name string}

func (a *Animal) move() {
    fmt.Printf("%s会动！\n", a.name)}

//Dog 狗
type Dog struct {
    Feet    int8
    *Animal //通过嵌套匿名结构体实现继承}

func (d *Dog) wang() {
    fmt.Printf("%s会汪汪汪~\n", d.name)}

func main() {
    d1 := &Dog{
        Feet: 4,
        Animal: &Animal{ //注意嵌套的是结构体指针
            name: "乐乐",
        },
    }
    d1.wang() //乐乐会汪汪汪~
    d1.move() //乐乐会动！}
```

#### 结构体字段的可见性

结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。

#### 结构体与JSON序列化
```
//Student 学生
type Student struct {
    ID     int
    Gender string
    Name   string}

//Class 班级
type Class struct {
    Title    string
    Students []*Student
}

func main() {
    c := &Class{
        Title:    "101",
        Students: make([]*Student, 0, 200),
    }
    for i := 0; i < 10; i++ {
        stu := &Student{
            Name:   fmt.Sprintf("stu%02d", i),
            Gender: "男",
            ID:     i,
        }
        c.Students = append(c.Students, stu)
    }
    //JSON序列化：结构体-->JSON格式的字符串
    data, err := json.Marshal(c)
    if err != nil {
        fmt.Println("json marshal failed")
        return
    }
    fmt.Printf("json:%s\n", data)
    //JSON反序列化：JSON格式的字符串-->结构体
    str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"},{"ID":3,"Gender":"男","Name":"stu03"},{"ID":4,"Gender":"男","Name":"stu04"},{"ID":5,"Gender":"男","Name":"stu05"},{"ID":6,"Gender":"男","Name":"stu06"},{"ID":7,"Gender":"男","Name":"stu07"},{"ID":8,"Gender":"男","Name":"stu08"},{"ID":9,"Gender":"男","Name":"stu09"}]}`
    c1 := &Class{}
    err = json.Unmarshal([]byte(str), c1)
    if err != nil {
        fmt.Println("json unmarshal failed!")
        return
    }
    fmt.Printf("%#v\n", c1)}
```

#### 结构体标签（Tag）

Tag是结构体的元信息，可以在运行的时候通过反射的机制读取出来。Tag在结构体字段的后方定义，由一对反引号包裹起来，具体的格式如下：
```
 `key1:"value1" key2:"value2"`
```

结构体标签由一个或多个键值对组成。键与值使用冒号分隔，值用双引号括起来。键值对之间使用一个空格分隔。 注意事项： 为结构体编写Tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如不要在key和value之间添加空格。
例如我们为Student结构体的每个字段定义json序列化时使用的Tag：
```
//Student 学生
type Student struct {
    ID     int    `json:"id"` //通过指定tag实现json序列化该字段时的key
    Gender string //json序列化是默认使用字段名作为key
    name   string //私有不能被json包访问}

func main() {
    s1 := Student{
        ID:     1,
        Gender: "女",
        name:   "pprof",
    }
    data, err := json.Marshal(s1)
    if err != nil {
        fmt.Println("json marshal failed!")
        return
    }
    fmt.Printf("json str:%s\n", data) //json str:{"id":1,"Gender":"女"}}
```
# 流程控制

## 条件语句if

 if 语句 由一个布尔表达式后紧跟一个或多个语句组成。

Go 编程语言中 if 语句的语法如下：

* 可省略条件表达式括号。
* 持初始化语句，可定义代码块局部变量。 
* 代码块左 括号必须在条件表达式尾部。
```
if 布尔表达式 {
    /* 在布尔表达式为 true 时执行 */
}
```
```
x := 0

if n := "abc"; x > 0 {     // 初始化语句未必就是定义变量， 如 println("init") 也是可以的。
    println(n[2])} else if x < 0 {    // 注意 else if 和 else 左大括号位置。
    println(n[1])} else {
    println(n[0])
}
```
注意：不支持三元操作符(三目运算符) "a > b ? a : b"。

Go 编程语言中 if...else 语句的语法如下：
```
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
} else {
  /* 在布尔表达式为 false 时执行 */
}
```

## 条件语句switch

switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上直下逐一测试，直到匹配为止。 Golang switch 分支表达式可以是任意类型，不限于常量。可省略 break，默认自动终止。

Go 编程语言中 switch 语句的语法如下：
```
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

变量 var1 可以是任何类型，而 val1 和 val2 则可以是同类型的任意值。类型不被局限于常量或整数，但必须是相同的类型；或者最终结果为相同类型的表达式。 您可以同时测试多个可能符合条件的值，使用逗号分割它们，例如：case val1, val2, val3。例如：
```
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var grade string = "B"
   var marks int = 90

   switch marks {
      case 90: grade = "A"
      case 80: grade = "B"
      case 50,60,70 : grade = "C"
      default: grade = "D"  
   }

   switch {
      case grade == "A" :
         fmt.Printf("优秀!\n" )     
      case grade == "B", grade == "C" :
         fmt.Printf("良好\n" )      
      case grade == "D" :
         fmt.Printf("及格\n" )      
      case grade == "F":
         fmt.Printf("不及格\n" )
      default:
         fmt.Printf("差\n" )
   }
   fmt.Printf("你的等级是 %s\n", grade )
   }
```
以上代码执行结果为：
```
优秀!
你的等级是 A
```

### Type Switch

switch 语句还可以被用于 type-switch 来判断某个 interface 变量中实际存储的变量类型。
Type Switch 语法格式如下：
```
switch x.(type){
    case type:
       statement(s)      
    case type:
       statement(s)
    /* 你可以定义任意个数的case */
    default: /* 可选 */
       statement(s)
}
```
例如：
```
package main

import "fmt"

func main() {
    var x interface{}
    //写法一：
    switch i := x.(type) { // 带初始化语句
    case nil:
        fmt.Printf(" x 的类型 :%T\r\n", i)
    case int:
        fmt.Printf("x 是 int 型")
    case float64:
        fmt.Printf("x 是 float64 型")
    case func(int) float64:
        fmt.Printf("x 是 func(int) 型")
    case bool, string:
        fmt.Printf("x 是 bool 或 string 型")
    default:
        fmt.Printf("未知型")
    }
    //写法二
    var j = 0
    switch j {
    case 0:
    case 1:
        fmt.Println("1")
    case 2:
        fmt.Println("2")
    default:
        fmt.Println("def")
    }
    //写法三
    var k = 0
    switch k {
    case 0:
        println("fallthrough")
        fallthrough
        /*
            Go的switch非常灵活，表达式不必是常量或整数，执行的过程从上至下，直到找到匹配项；
            而如果switch没有表达式，它会匹配true。
            Go里面switch默认相当于每个case最后带有break，
            匹配成功后不会自动向下执行其他case，而是跳出整个switch,
            但是可以使用fallthrough强制执行后面的case代码。
        */
    case 1:
        fmt.Println("1")
    case 2:
        fmt.Println("2")
    default:
        fmt.Println("def")
    }
    //写法三
    var m = 0
    switch m {
    case 0, 1:
        fmt.Println("1")
    case 2:
        fmt.Println("2")
    default:
        fmt.Println("def")
    }
    //写法四
    var n = 0
    switch { //省略条件表达式，可当 if...else if...else
    case n > 0 && n < 10:
        fmt.Println("i > 0 and i < 10")
    case n > 10 && n < 20:
        fmt.Println("i > 10 and i < 20")
    default:
        fmt.Println("def")
    }
 }
```
以上代码执行结果为：
```
x 的类型 :<nil>
fallthrough
1
1
def
```
## 条件语句select

select 语句类似于 switch 语句，但是select会随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。select 是Go中的一个控制结构，类似于用于通信的switch语句。每个case必须是一个通信操作，要么是发送要么是接收。 select 随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。一个默认的子句应该总是可运行的。
Go 编程语言中 select 语句的语法如下：
```
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s);
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
```
以下描述了 select 语句的语法：

* 每个case都必须是一个通信
* 所有channel表达式都会被求值
* 所有被发送的表达式都会被求值
* 如果任意某个通信可以进行，它就执行；其他被忽略。
* 如果有多个case都可以运行，Select会随机公平地选出一个执行。其他不会执行。
* 否则：
    如果有default子句，则执行该语句。
    如果没有default字句，select将阻塞，直到某个通信可以运行；Go不会重新对channel或值进行求值。

例如：
```
package main

import "fmt"

func main() {
   var c1, c2, c3 chan int
   var i1, i2 int
   select {
      case i1 = <-c1:
         fmt.Printf("received ", i1, " from c1\n")
      case c2 <- i2:
         fmt.Printf("sent ", i2, " to c2\n")
      case i3, ok := (<-c3):  // same as: i3, ok := <-c3
         if ok {
            fmt.Printf("received ", i3, " from c3\n")
         } else {
            fmt.Printf("c3 is closed\n")
         }
      default:
         fmt.Printf("no communication\n")
   }    
}
```
以上代码执行结果为：
```
no communication
```
select可以监听channel的数据流动
select的用法与switch语法非常类似，由select开始的一个新的选择块，每个选择条件由case语句来描述
与switch语句可以选择任何使用相等比较的条件相比，select由比较多的限制，其中最大的一条限制就是每个case语句里必须是一个IO操作
```
select { //不停的在这里检测
    case <-chanl : //检测有没有数据可以读
    //如果chanl成功读取到数据，则进行该case处理语句
    case chan2 <- 1 : //检测有没有可以写
    //如果成功向chan2写入数据，则进行该case处理语句


    //假如没有default，那么在以上两个条件都不成立的情况下，就会在此阻塞//一般default会不写在里面，select中的default子句总是可运行的，因为会很消耗CPU资源
    default:
    //如果以上都没有符合条件，那么则进行default处理流程
    }
```

在一个select语句中，Go会按顺序从头到尾评估每一个发送和接收的语句。如果其中的任意一个语句可以继续执行（即没有被阻塞），那么就从那些可以执行的语句中任意选择一条来使用。 如果没有任意一条语句可以执行（即所有的通道都被阻塞），那么有两种可能的情况： ①如果给出了default语句，那么就会执行default的流程，同时程序的执行会从select语句后的语句中恢复。 ②如果没有default语句，那么select语句将被阻塞，直到至少有一个case可以进行下去。

### Golang select的使用及典型用法

#### 超时判断
```
//比如在下面的场景中，使用全局resChan来接受response，如果时间超过3S,resChan中还没有数据返回，则第二条case将执行
var resChan = make(chan int)// do requestfunc test() {
    select {
    case data := <-resChan:
        doData(data)
    case <-time.After(time.Second * 3):
        fmt.Println("request time out")
    }}

func doData(data int) {
    //...}
```
#### 退出
```
//主线程（协程）中如下：var shouldQuit=make(chan struct{})
fun main(){
    {
        //loop
    }
    //...out of the loop
    select {
        case <-c.shouldQuit:
            cleanUp()
            return
        default:
        }
    //...
}

//再另外一个协程中，如果运行遇到非法操作或不可处理的错误，就向shouldQuit发送数据通知程序停止运行
close(shouldQuit)
```

#### 判断
```
//在某些情况下是存在不希望channel缓存满了的需求的，可以用如下方法判断
ch := make (chan int, 5)//...
data：=0
select {
    case ch <- data:
    default:
        //做相应操作，比如丢弃data。视需求而定
}
```
## 循环语句for
Golang for支持三种循环方式，包括类似 while 的语法。for循环是一个循环控制结构，可以执行指定次数的循环。
Go语言的For循环有3中形式，只有其中的一种使用分号。
```
for init; condition; post { }
for condition { }
for { }
init： 一般为赋值表达式，给控制变量赋初值；
condition： 关系表达式或逻辑表达式，循环控制条件；
post： 一般为赋值表达式，给控制变量增量或减量。
for语句执行过程如下：
①先对表达式 init 赋初值；
②判别赋值表达式 init 是否满足给定 condition 条件，若其值为真，满足循环条件，则执行循环体内语句，然后执行 post，进入第二次循环，再判别 condition；否则判断 condition 的值为假，不满足条件，就终止for循环，执行循环体外语句。
```
例如：
```
s := "abc"

for i, n := 0, len(s); i < n; i++ { // 常见的 for 循环，支持初始化语句。
    println(s[i])}

n := len(s)for n > 0 {                // 替代 while (n > 0) {}
    println(s[n])        // 替代 for (; n > 0;) {}
    n-- 
}

for {                    // 替代 while (true) {}
    println(s)            // 替代 for (;;) {}
}
```
不要期望编译器能理解你的想法，在初始化语句中计算出全部结果是个好主意。
```
package main

func length(s string) int {
    println("call length.")
    return len(s)
}

func main() {
    s := "abcd"

    for i, n := 0, length(s); i < n; i++ {     // 避免多次调用 length 函数。
        println(i, s[i])
    } 
}
```
输出:
```
    call length.
    0 97
    1 98
    2 99
    3 100
```

### 循环嵌套

以下为 Go 语言嵌套循环的格式：
```
for [condition |  ( init; condition; increment ) | Range]{
   for [condition |  ( init; condition; increment ) | Range]
   {
      statement(s)
   }
   statement(s)
}
```

### 无限循环

如过循环中条件语句永远不为 false 则会进行无限循环，我们可以通过 for 循环语句中只设置一个条件表达式来执行无限循环：
```
package main

import "fmt"

func main() {
    for true  {
        fmt.Printf("这是无限循环。\n");
    }
}
```

## 循环语句range

Golang range类似迭代器操作，返回 (索引, 值) 或 (键, 值)。for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。格式如下：
```
for key, value := range oldMap {
    newMap[key] = value
}
```

|             | 1st value | 2nd value |               |
| ----------- | --------- | --------- | ------------- |
| string      | index     | s[index]  | unicode, rune |
| array/slice | index     | s[index]  |               |
| map         | key       | m[key]    |               |
| channel     | element   |           |               |

可忽略不想要的返回值，或 " _ " 这个特殊变量。   
```
package main

func main() {
    s := "abc"
    // 忽略 2nd value，支持 string/array/slice/map。
    for i := range s {
        println(s[i])
    }
    // 忽略 index。
    for _, c := range s {
        println(c)
    }
    // 忽略全部返回值，仅迭代。
    for range s {

    }

    m := map[string]int{"a": 1, "b": 2}
    // 返回 (key, value)。
    for k, v := range m {
        println(k, v)
    }
}
```
输出：
```
97
98
99
97
98
99
a 1
b 2
```
**注意：range 会复制对象。**
```
package main

import "fmt"

func main() {
    a := [3]int{0, 1, 2}

    for i, v := range a { // index、value 都是从复制品中取出。

        if i == 0 { // 在修改前，我们先修改原数组。
            a[1], a[2] = 999, 999
            fmt.Println(a) // 确认修改有效，输出 [0, 999, 999]。
        }

        a[i] = v + 100 // 使用复制品中取出的 value 修改原数组。

    }

    fmt.Println(a) // 输出 [100, 101, 102]。
}
```
输出结果：
```
    [0 999 999]
    [100 101 102]
```
建议改用引用类型，其底层数据不会被复制。
```
package main

func main() {
    s := []int{1, 2, 3, 4, 5}

    for i, v := range s { // 复制 struct slice { pointer, len, cap }。

        if i == 0 {
            s = s[:3]  // 对 slice 的修改，不会影响 range。
            s[2] = 100 // 对底层数据的修改。
        }

        println(i, v)
    }}
```
输出结果:
```
    0 1
    1 2
    2 100
    3 4
    4 5
```
另外两种引用类型 map、channel 是指针包装，而不像 slice 是 struct。

for 和 for range有什么区别?主要是使用场景不同
for可以

* 遍历array和slice
* 遍历key为整型递增的map
* 遍历string

for range可以

* 完成所有for可以做的事情，却能做到for不能做的，包括遍历key为string类型的map并同时获取key和value
* 遍历channel

## 循环控制Goto、Break、Continue

循环控制语句循环控制语句可以控制循环体内语句的执行过程。GO 语言支持以下几种循环控制语句：Goto、Break、Continue

* 三个语句都可以配合标签(label)使用
* 标签名区分大小写，定以后若不使用会造成编译错误
* continue、break配合标签(label)可用于多层循环跳出
* goto是调整执行位置，与continue、break配合标签(label)的结果并不相同

# 函数

## 函数定义

### golang函数特点

* 无需声明原型
* 支持不定变参
* 支持多返回值
* 支持命名返回参数
* 支持匿名函数和闭包
* 函数也是一种类型，一个函数可以赋值给变量
* 不支持 嵌套 (nested) 一个包不能有两个名字一样的函数
* 不支持 重载 (overload) 
* 不支持 默认参数 (default parameter)

### 函数声明

函数声明包含一个函数名，参数列表， 返回值列表和函数体。如果函数没有返回值，则返回列表可以省略。函数从第一条语句开始执行，直到执行return语句或者执行函数的最后一条语句。

函数可以没有参数或接受多个参数。注意类型在变量名之后 。当两个或多个连续的函数命名参数是同一类型，则除了最后一个类型之外，其他都可以省略。函数可以返回任意数量的返回值。使用关键字 func 定义函数，左大括号依旧不能另起一行。
```
func test(x, y int, s string) (int, string) {
    // 类型相同的相邻参数，参数类型可合并。 多返回值必须用括号。
    n := x + y          
    return n, fmt.Sprintf(s, n)
}
```

函数是第一类对象，可作为参数传递。建议将复杂签名定义为函数类型，以便于阅读。
```
package main

import "fmt"

func test(fn func() int) int {
    return fn()}// 定义函数类型。type FormatFunc func(s string, x, y int) string 

func format(fn FormatFunc, s string, x, y int) string {
    return fn(s, x, y)}

func main() {
    s1 := test(func() int { return 100 }) // 直接将匿名函数当参数。

    s2 := format(func(s string, x, y int) string {
        return fmt.Sprintf(s, x, y)
    }, "%d, %d", 10, 20)

    println(s1, s2)}
```
输出：
```
 100 10, 20
```
有返回值的函数，必须有明确的终止语句，否则会引发编译错误。你可能会偶尔遇到没有函数体的函数声明，这表示该函数不是以Go实现的。这样的声明定义了函数标识符。
```
package math

func Sin(x float64) float //implemented in assembly language
```

## 参数

### 函数参数

函数定义时指出，函数定义时有参数，该变量可称为函数的形参。形参就像定义在函数体内的局部变量。但当调用函数，传递过来的变量就是函数的实参，函数可以通过两种方式来传递参数：值传递：指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
```
func swap(x, y int) int {
       ... ...
}
```
引用传递：是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。
```
package main

import (
    "fmt")

/* 定义相互交换值的函数 */
func swap(x, y *int) {
    var temp int

    temp = *x /* 保存 x 的值 */
    *x = *y   /* 将 y 值赋给 x */
    *y = temp /* 将 temp 值赋给 y*/

}

func main() {
    var a, b int = 1, 2
    /*
        调用 swap() 函数
        &a 指向 a 指针，a 变量的地址
        &b 指向 b 指针，b 变量的地址
    */
    swap(&a, &b)

    fmt.Println(a, b)}
```
输出结果：
```
   2 1
```
在默认情况下，Go 语言使用的是值传递，即在调用过程中不会影响到实际参数。

**注意1：无论是值传递，还是引用传递，传递给函数的都是变量的副本，不过，值传递是值的拷贝。引用传递是地址的拷贝，一般来说，地址拷贝更为高效。而值拷贝取决于拷贝的对象大小，对象越大，则性能越低。
注意2：map、slice、chan、指针、interface默认以引用的方式传递。**

不定参数传值 就是函数的参数不是固定的，后面的类型是固定的。（可变参数）Golang 可变参数本质上就是 slice。只能有一个，且必须是最后一个。在参数赋值时可以不用用一个一个的赋值，可以直接传递一个数组或者切片，特别注意的是在参数后加上“…”即可。
```
func myfunc(args ...int) {    //0个或多个参数
  }

  func add(a int, args…int) int {    //1个或多个参数
  }

  func add(a int, b int, args…int) int {    //2个或多个参数
  }
```
注意：其中args是一个slice，我们可以通过arg[index]依次访问所有参数,通过len(arg)来判断传递参数的个数.

任意类型的不定参数： 就是函数的参数和每个参数的类型都不是固定的。用interface{}传递任意类型数据是Go语言的惯例用法，而且interface{}是类型安全的。
```
func myfunc(args ...interface{}) {
  }
```
例如：
```
package main

import (
    "fmt")

func test(s string, n ...int) string {
    var x int
    for _, i := range n {
        x += i
    }

    return fmt.Sprintf(s, x)}

func main() {
    println(test("sum: %d", 1, 2, 3))}
```
输出结果：
```
    sum: 6
```
使用 slice 对象做变参时，必须展开。（slice...）
```
package main

import (
    "fmt")

func test(s string, n ...int) string {
    var x int
    for _, i := range n {
        x += i
    }

    return fmt.Sprintf(s, x)}

func main() {
    s := []int{1, 2, 3}
    res := test("sum: %d", s...)    // slice... 展开slice
    println(res)}
```

## 返回值

### 函数返回值

" _ "标识符，用来忽略函数的某个返回值
Go 的返回值可以被命名，并且就像在函数体开头声明的变量那样使用。
返回值的名称应当具有一定的意义，可以作为文档使用。
没有参数的 return 语句返回各个返回变量的当前值。这种用法被称作“裸”返回。
直接返回语句仅应当用在像下面这样的短函数中。在长的函数中它们会影响代码的可读性。

Golang返回值不能用容器对象接收多返回值。只能用多个变量，或 "_ " 忽略。

多返回值可直接作为其他函数调用实参。
```
package main

func test() (int, int) {
    return 1, 2}

func add(x, y int) int {
    return x + y
}

func sum(n ...int) int {
    var x int
    for _, i := range n {
        x += i
    }
    return x
}

func main() {
    println(add(test()))
    println(sum(test()))
}
```
输出结果：
```
    3
    3
```
命名返回参数可看做与形参类似的局部变量，最后由 return 隐式返回。
```
package main

func add(x, y int) (z int) {
    z = x + y
    return}

func main() {
    println(add(1, 2))
}
```
输出结果：
```
3
```
命名返回参数可被同名局部变量遮蔽，此时需要显式返回。
```
func add(x, y int) (z int) {
    { // 不能在一个级别，引发 "z redeclared in this block" 错误。
        var z = x + y
        // return   // Error: z is shadowed during return
        return z // 必须显式返回。
    }
 }
```
命名返回参数允许 defer 延迟调用通过闭包读取和修改。
```
package main

func add(x, y int) (z int) {
    defer func() {
        z += 100
    }()

    z = x + y
    return}

func main() {
    println(add(1, 2)) 
}
```
输出结果：
```
103
```
显式 return 返回前，会先修改命名返回参数。
```
package main

func add(x, y int) (z int) {
    defer func() {
        println(z) // 输出: 203
    }()

    z = x + y
    return z + 200 // 执行顺序: (z = z + 200) -> (call defer) -> (return)
}

func main() {
    println(add(1, 2)) // 输出: 203}
```
输出结果：
```
    203
    203
```
## 匿名函数

匿名函数是指不需要定义函数名的一种函数实现方式。1958年LISP首先采用匿名函数。
在Go里面，函数可以像普通变量一样被传递或使用，Go语言支持随时在代码里定义匿名函数。匿名函数由一个不带函数名的函数声明和函数体组成。匿名函数的优越性在于可以直接使用函数内的变量，不必申明。
```
package main

import (
    "fmt"
    "math")

func main() {
    getSqrt := func(a float64) float64 {
        return math.Sqrt(a)
    }
    fmt.Println(getSqrt(4))}
```
输出结果：
```
    2
```
上面先定义了一个名为getSqrt 的变量，初始化该变量时和之前的变量初始化有些不同，使用了func，func是定义函数的，可是这个函数和上面说的函数最大不同就是没有函数名，也就是匿名函数。这里将一个函数当做一个变量一样的操作。

Golang匿名函数可赋值给变量，做为结构字段，或者在 channel 里传送。
```
package main

func main() {
    // --- function variable ---
    fn := func() { println("Hello, World!") }
    fn()

    // --- function collection ---
    fns := [](func(x int) int){
        func(x int) int { return x + 1 },
        func(x int) int { return x + 2 },
    }
    println(fns[0](100))

    // --- function as field ---
    d := struct {
        fn func() string
    }{
        fn: func() string { return "Hello, World!" },
    }
    println(d.fn())

    // --- channel of function ---
    fc := make(chan func() string, 2)
    fc <- func() string { return "Hello, World!" }
    println((<-fc)())
 }
```
输出结果：
```
    Hello, World!
    101
    Hello, World!
    Hello, World!
```
## 闭包、递归

### 闭包详解

闭包的应该都听过，但到底什么是闭包呢？
闭包是由函数及其相关引用环境组合而成的实体(即：闭包=函数+引用环境)。
“官方”的解释是：所谓“闭包”，指的是一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。
```
package main

import (
    "fmt")

func a() func() int {
    i := 0
    b := func() int {
        i++
        fmt.Println(i)
        return i
    }
    return b
}

func main() {
    c := a()
    c()
    c()
    c()

    a() //不会输出i}
```

输出结果：
```
    1
    2
    3
```
闭包复制的是原对象指针，这就很容易解释延迟引用现象。
```
package main

import "fmt"

func test() func() {
    x := 100
    fmt.Printf("x (%p) = %d\n", &x, x)

    return func() {
        fmt.Printf("x (%p) = %d\n", &x, x)
    }}

func main() {
    f := test()
    f()
}
```
输出:
```
    x (0xc42007c008) = 100
    x (0xc42007c008) = 100
```
在汇编层 ，test 实际返回的是 FuncVal 对象，其中包含了匿名函数地址、闭包对象指针。当调匿名函数时，只需以某个寄存器传递该对象即可。
```
 FuncVal { func_address, closure_var_pointer ... }
```


外部引用函数参数局部变量
```
package main

import "fmt"

// 外部引用函数参数局部变量
func add(base int) func(int) int {
    return func(i int) int {
        base += i
        return base
    }}

func main() {
    tmp1 := add(10)
    fmt.Println(tmp1(1), tmp1(2))
    // 此时tmp1和tmp2不是一个实体了
    tmp2 := add(100)
    fmt.Println(tmp2(1), tmp2(2))
}
```
同时可以返回多个闭包：
```
package main

import "fmt"

// 返回2个函数类型的返回值
func test01(base int) (func(int) int, func(int) int) {
    // 定义2个函数，并返回
    // 相加
    add := func(i int) int {
        base += i
        return base
    }
    // 相减
    sub := func(i int) int {
        base -= i
        return base
    }
    // 返回
    return add, sub
}

func main() {
    f1, f2 := test01(10)
    // base一直是没有消
    fmt.Println(f1(1), f2(2))
    // 此时base是9
    fmt.Println(f1(3), f2(4))
}
```

### Go 语言递归函数

斐波那契数列(Fibonacci)：

这个数列从第3项开始，每一项都等于前两项之和。
```
package main

import "fmt"

func fibonaci(i int) int {
    if i == 0 {
        return 0
    }
    if i == 1 {
        return 1
    }
    return fibonaci(i-1) + fibonaci(i-2)}

func main() {
    var i int
    for i = 0; i < 10; i++ {
        fmt.Printf("%d\n", fibonaci(i))
    }}
```

## 延迟调用（defer）

### Golang延迟调用

#### defer特性

1. 关键字 defer 用于注册延迟调用。
2. 这些调用直到 return 前才被执。因此，可以用来做资源清理。
3. 多个defer语句，按先进后出的方式执行。
4. defer语句中的变量，在defer声明时就决定了。

#### defer用途

1. 关闭文件句柄
2. 锁资源释放
3. 数据库连接释放


go 语言的defer功能强大，对于资源管理非常方便，但是如果没用好，也会有陷阱。defer 是先进后出这个很自然,后面的语句会依赖前面的资源，因此如果先前面的资源先释放了，后面的语句就没法执行了。
```
package main

import "fmt"

func main() {
    var whatever [5]struct{}

    for i := range whatever {
        defer fmt.Println(i)
    }}
```
输出结果：
```
    4
    3
    2
    1
    0
```
#### defer 碰上闭包
```
package main

import "fmt"

func main() {
    var whatever [5]struct{}
    for i := range whatever {
        defer func() { fmt.Println(i) }()
    }}
```
输出结果：
```
    4
    4
    4
    4
    4
```

其实go说的很清楚,我们一起来看看go spec如何说的
>Each time a "defer" statement executes, the function value and parameters to the call are evaluated as usualand saved anew but the actual function is not invoked.

也就是说函数正常执行,由于闭包用到的变量 i 在执行的时候已经变成4,所以输出全都是4.

#### defer f.Close

这个大家用的都很频繁,但是go语言编程举了一个可能一不小心会犯错的例子.
```
package main

import "fmt"

type Test struct {
    name string
}

func (t *Test) Close() {
    fmt.Println(t.name, " closed")
}
    
func main() {
    ts := []Test{{"a"}, {"b"}, {"c"}}
    for _, t := range ts {
        defer t.Close()
    }
 }
```
输出结果：
```
    c  closed
    c  closed
    c  closed
```

这个输出并不会像我们预计的输出c b a,而是输出c c c可是按照前面的go spec中的说明,应该输出c b a才对啊.那我们换一种方式来调用一下.
```
package main

import "fmt"

type Test struct {
    name string
}

func (t *Test) Close() {
    fmt.Println(t.name, " closed")}
    
func Close(t Test) {
    t.Close()
}
    
func main() {
    ts := []Test{{"a"}, {"b"}, {"c"}}
    for _, t := range ts {
        defer Close(t)
    }
}
```
输出结果：
```
    c  closed
    b  closed
    a  closed
```
当然,如果你不想多写一个函数,也很简单,可以像下面这样,同样会输出c b a看似多此一举的声明：
```
package main

import "fmt"

type Test struct {
    name string
}

func (t *Test) Close() {
    fmt.Println(t.name, " closed")
}
    
func main() {
    ts := []Test{{"a"}, {"b"}, {"c"}}
    for _, t := range ts {
        t2 := t
        defer t2.Close()
    }
}
```
输出结果：
```
    c  closed
    b  closed
    a  closed
```

可以得出下面的结论：defer后面的语句在执行的时候，函数调用的参数会被保存起来，但是不执行。也就是复制了一份。但是并没有说struct这里的this指针如何处理，通过这个例子可以看出go语言并没有把这个明确写出来的this指针当作参数来看待。多个 defer 注册，按 FILO 次序执行 ( 先进后出 )。哪怕函数或某个延迟调用发生错误，这些调用依旧会被执行。
```
package main

func test(x int) {
    defer println("a")
    defer println("b")

    defer func() {
        println(100 / x) // div0 异常未被捕获，逐步往外传递，最终终止进程。
    }()

    defer println("c")
}

func main() {
    test(0)
}
```
输出：
```
    c
    b
    a
    panic: runtime error: integer divide by zero
```

延迟调用参数在注册时求值或复制，可用指针或闭包 "延迟" 读取。
```
package main

func test() {
    x, y := 10, 20

    defer func(i int) {
        println("defer:", i, y) // y 闭包引用
    }(x) // x 被复制

    x += 10
    y += 100
    println("x =", x, "y =", y)
}

func main() {
    test()
}
```
输出结果:
```
    x = 20 y = 120
    defer: 10 120
```

滥用 defer 可能会导致性能问题，尤其是在一个 "大循环" 里。
```
package main

import (
    "fmt"
    "sync"
    "time")

var lock sync.Mutex

func test() {
    lock.Lock()
    lock.Unlock()
}

func testdefer() {
    lock.Lock()
    defer lock.Unlock()
}

func main() {
    func() {
        t1 := time.Now()

        for i := 0; i < 10000; i++ {
            test()
        }
        elapsed := time.Since(t1)
        fmt.Println("test elapsed: ", elapsed)
    }()
    func() {
        t1 := time.Now()

        for i := 0; i < 10000; i++ {
            testdefer()
        }
        elapsed := time.Since(t1)
        fmt.Println("testdefer elapsed: ", elapsed)
    }()

}
```
输出：
```
    test elapsed:  223.162µs
    testdefer elapsed:  781.304µs
```

#### defer陷阱

##### defer 与 closure
```
package main

import (
    "errors"
    "fmt")

func foo(a, b int) (i int, err error) {
    defer fmt.Printf("first defer err %v\n", err)
    defer func(err error) { fmt.Printf("second defer err %v\n", err) }(err)
    defer func() { fmt.Printf("third defer err %v\n", err) }()
    if b == 0 {
        err = errors.New("divided by zero!")
        return
    }

    i = a / b
    return}

func main() {
    foo(2, 0)}
```
输出结果：
```
    third defer err divided by zero!
    second defer err <nil>
    first defer err <nil>
```
解释：如果 defer 后面跟的不是一个 closure 最后执行的时候我们得到的并不是最新的值。

##### defer 与 return
```
package main

import "fmt"

func foo() (i int) {

    i = 0
    defer func() {
        fmt.Println(i)
    }()

    return 2
}

func main() {
    foo()
}
```
输出结果：
```
    2
```

解释：在有具名返回值的函数中（这里具名返回值为 i），执行 return 2 的时候实际上已经将 i 的值重新赋值为 2。所以defer closure 输出结果为 2 而不是 1。

##### defer nil 函数
```
package main

import (
    "fmt")

func test() {
    var run func() = nil
    defer run()
    fmt.Println("runs")
}

func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()
    test()
}
```
输出结果：
```
runs
runtime error: invalid memory address or nil pointer dereference
```
解释：名为 test 的函数一直运行至结束，然后 defer 函数会被执行且会因为值为 nil 而产生 panic 异常。然而值得注意的是，run() 的声明是没有问题，因为在test函数运行完成后它才会被调用。

##### 在错误的位置使用 defer

当 http.Get 失败时会抛出异常。
```
package main

import "net/http"

func do() error {
    res, err := http.Get("http://www.google.com")
    defer res.Body.Close()
    if err != nil {
        return err
    }

    // ..code...

    return nil
}

func main() {
    do()
}
```
输出结果：
```
    panic: runtime error: invalid memory address or nil pointer dereference
```

因为在这里我们并没有检查我们的请求是否成功执行，当它失败的时候，我们访问了 Body 中的空变量 res ，因此会抛出异常

**解决方案：**

总是在一次成功的资源分配下面使用 defer ，对于这种情况来说意味着：当且仅当 http.Get 成功执行时才使用 defer
```
package main

import "net/http"

func do() error {
    res, err := http.Get("http://xxxxxxxxxx")
    if res != nil {
        defer res.Body.Close()
    }

    if err != nil {
        return err
    }

    // ..code...

    return nil
}

func main() {
    do()
}
```

在上述的代码中，当有错误的时候，err 会被返回，否则当整个函数返回的时候，会关闭 res.Body 。
解释：在这里，你同样需要检查 res 的值是否为 nil ，这是 http.Get 中的一个警告。通常情况下，出错的时候，返回的内容应为空并且错误会被返回，可当你获得的是一个重定向 error 时， res 的值并不会为 nil ，但其又会将错误返回。上面的代码保证了无论如何 Body 都会被关闭，如果你没有打算使用其中的数据，那么你还需要丢弃已经接收的数据。

##### 不检查错误

在这里，f.Close() 可能会返回一个错误，可这个错误会被我们忽略掉。
```
package main

import "os"

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }

    if f != nil {
        defer f.Close()
    }

    // ..code...

    return nil
}

func main() {
    do()
}
```
再改进一下通过命名的返回变量来返回 defer 内的错误。
```
package main

import "os"

func do() (err error) {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }

    if f != nil {
        defer func() {
            if ferr := f.Close(); ferr != nil {
                err = ferr
            }
        }()
    }

    // ..code...

    return nil
}

func main() {
    do()
}
```

释放相同的资源如果你尝试使用相同的变量释放不同的资源，那么这个操作可能无法正常执行。

当延迟函数执行时，只有最后一个变量会被用到，因此，f 变量 会成为最后那个资源 (another-book.txt)。而且两个 defer 都会将这个资源作为最后的资源来关闭解决方案：
```
package main

import (
    "fmt"
    "io"
    "os")

func do() error {
    f, err := os.Open("book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func(f io.Closer) {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close book.txt err %v\n", err)
            }
        }(f)
    }

    // ..code...

    f, err = os.Open("another-book.txt")
    if err != nil {
        return err
    }
    if f != nil {
        defer func(f io.Closer) {
            if err := f.Close(); err != nil {
                fmt.Printf("defer close another-book.txt err %v\n", err)
            }
        }(f)
    }

    return nil
}

func main() {
    do()
}
```

## 异常处理

Golang 没有结构化异常，使用 panic 抛出错误，recover 捕获错误。异常的使用场景简单描述：Go中可以抛出一个panic的异常，然后在defer中通过recover捕获这个异常，然后正常处理。

**panic：**

1. 内置函数
2. 假如函数F中书写了panic语句，会终止其后要执行的代码，在panic所在函数F内如果存在要执行的defer函数列表，按照defer的逆序执行
3. 返回函数F的调用者G，在G中，调用函数F语句之后的代码不会执行，假如函数G中存在要执行的defer函数列表，按照defer的逆序执行
4. 直到goroutine整个退出，并报告错误

**recover：**

1. 内置函数
2. 用来控制一个goroutine的panicking行为，捕获panic，从而影响应用的行为
3. 一般的调用建议
        a). 在defer函数中，通过recever来终止一个goroutine的panicking过程，从而恢复正常代码的执行
        b). 可以获取通过panic传递的error

注意:

1. 利用recover处理panic指令，defer 必须放在 panic 之前定义，另外 recover 只有在 defer 调用的函数中才有效。否则当panic时，recover无法捕获到panic，无法防止panic扩散。
2. recover 处理异常后，逻辑并不会恢复到 panic 那个点去，函数跑到 defer 之后的那个点。
3. 多个 defer 会形成 defer 栈，后定义的 defer 语句会被最先调用。

```
package main

func main() {
    test()
}

func test() {
    defer func() {
        if err := recover(); err != nil {
            println(err.(string)) // 将 interface{} 转型为具体类型。
        }
    }()

    panic("panic error!")
}
```
输出结果：
```
    panic error!
```
由于 panic、recover 参数类型为 interface{}，因此可抛出任何类型对象。

向已关闭的通道发送数据会引发panic
```
package main

import (
    "fmt")

func main() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println(err)
        }
    }()

    var ch chan int = make(chan int, 10)
    close(ch)
    ch <- 1
}
```
输出结果：
```
    send on closed channel
```
延迟调用中引发的错误，可被后续延迟调用捕获，但仅最后一个错误可被捕获。
```
package main

import "fmt"

func test() {
    defer func() {
        fmt.Println(recover())
    }()

    defer func() {
        panic("defer panic")
    }()

    panic("test panic")
}

func main() {
    test()
}
```
输出:
```
   defer panic
```
捕获函数 recover 只有在延迟调用内直接调用才会终止错误，否则总是返回 nil。任何未捕获的错误都会沿调用堆栈向外传递。
```
package main

import "fmt"

func test() {
    defer func() {
        fmt.Println(recover()) //有效
    }()
    defer recover()              //无效！
    defer fmt.Println(recover()) //无效！
    defer func() {
        func() {
            println("defer inner")
            recover() //无效！
        }()
    }()

    panic("test panic")
}

func main() {
    test()
}
```
输出:
```
    defer inner
    <nil>
    test panic
```
使用延迟匿名函数或下面这样都是有效的。
```
package main

import (
    "fmt")

func except() {
    fmt.Println(recover())
}

func test() {
    defer except()
    panic("test panic")
}

func main() {
    test()
}
```
输出结果：
```
    test panic
```
如果需要保护代码段，可将代码块重构成匿名函数，如此可确保后续代码被执 。
```
package main

import "fmt"

func test(x, y int) {
    var z int

    func() {
        defer func() {
            if recover() != nil {
                z = 0
            }
        }()
        panic("test panic")
        z = x / y
        return
    }()

    fmt.Printf("x / y = %d\n", z)}

func main() {
    test(2, 1)
}
```
输出：
```
    x / y = 0
```
除用 panic 引发中断性错误外，还可返回 error 类型错误对象来表示函数调用状态。
```
type error interface {
    Error() string
}
```
标准库 errors.New 和 fmt.Errorf 函数用于创建实现 error 接口的错误对象。通过判断错误对象实例来确定具体错误类型。
```
package main

import (
    "errors"
    "fmt")

var ErrDivByZero = errors.New("division by zero")

func div(x, y int) (int, error) {
    if y == 0 {
        return 0, ErrDivByZero
    }
    return x / y, nil}

func main() {
    defer func() {
        fmt.Println(recover())
    }()
    switch z, err := div(10, 0); err {
    case nil:
        println(z)
    case ErrDivByZero:
        panic(err)
    }
}
```
输出结果：
```
    division by zero
```
Go实现类似 try catch 的异常处理
```
package main

import "fmt"

func Try(fun func(), handler func(interface{})) {
    defer func() {
        if err := recover(); err != nil {
            handler(err)
        }
    }()
    fun()
}

func main() {
    Try(func() {
        panic("test panic")
    }, func(err interface{}) {
        fmt.Println(err)
    })
}
```
输出结果：
```
    test panic
```
惯例是:导致关键流程出现不可修复性错误的使用 panic，其他使用 error。
## 单元测试


## 压力测试

# 方法

Golang 方法总是绑定对象实例，并隐式将实例作为第一实参 (receiver)。

* 只能为当前包内命名类型定义方法。
* 参数 receiver 可任意命名。如方法中未曾使用 ，可省略参数名。
* 参数 receiver 类型可以是 T 或 * T。基类型 T 不能是接口或指针。
* 不支持方法重载，receiver 只是参数签名的组成部分。
* 可用实例 value 或 pointer 调用全部方法，编译器自动转换。

一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。

## 方法定义
```
func (recevier type) methodName(参数列表)(返回值列表){}

参数和返回值可以省略
```
```
package main

type Test struct{}

// 无参数、无返回值
func (t Test) method0() {

}

// 单参数、无返回值
func (t Test) method1(i int) {

}

// 多参数、无返回值
func (t Test) method2(x, y int) {

}

// 无参数、单返回值
func (t Test) method3() (i int) {
    return
}

// 多参数、多返回值
func (t Test) method4(x, y int) (z int, err error) {
    return
}

// 无参数、无返回值
func (t *Test) method5() {

}

// 单参数、无返回值
func (t *Test) method6(i int) {

}

// 多参数、无返回值
func (t *Test) method7(x, y int) {

}

// 无参数、单返回值
func (t *Test) method8() (i int) {
    return
}

// 多参数、多返回值
func (t *Test) method9(x, y int) (z int, err error) {
    return
}

func main() {}
```

下面定义一个结构体类型和该类型的一个方法：
```
package main

import (
    "fmt")

//结构体
type User struct {
    Name  string
    Email string
}

//方法
func (u User) Notify() {
    fmt.Printf("%v : %v \n", u.Name, u.Email)
}


func main() {
    // 值类型调用方法
    u1 := User{"golang", "golang@golang.com"}
    u1.Notify()
    // 指针类型调用方法
    u2 := User{"go", "go@go.com"}
    u3 := &u2
    u3.Notify()
}
```
输出结果：
```
    golang : golang@golang.com 
    go : go@go.com
```

解释： 首先我们定义了一个叫做 User 的结构体类型，然后定义了一个该类型的方法叫做 Notify，该方法的接受者是一个 User 类型的值。要调用 Notify 方法我们需要一个 User 类型的值或者指针。

在这个例子中当我们使用指针时，Go 调整和解引用指针使得调用可以被执行。注意，当接受者不是一个指针时，该方法操作对应接受者的值的副本(意思就是即使你使用了指针调用函数，但是函数的接受者是值类型，所以函数内部操作还是对副本的操作，而不是指针操作。

我们修改 Notify 方法，让它的接受者使用指针类型：
```
package main

import (
    "fmt")

//结构体
type User struct {
    Name  string
    Email string
}

//方法
func (u *User) Notify() {
    fmt.Printf("%v : %v \n", u.Name, u.Email)
}
    
func main() {
    // 值类型调用方法
    u1 := User{"golang", "golang@golang.com"}
    u1.Notify()
    // 指针类型调用方法
    u2 := User{"go", "go@go.com"}
    u3 := &u2
    u3.Notify()
}
```
输出结果：
```
    golang : golang@golang.com 
    go : go@go.com
```

注意：当接受者是指针时，即使用值类型调用那么函数内部也是对指针的操作。

方法不过是一种特殊的函数，只需将其还原，就知道 receiver T 和 * T 的差别。
```
package main

import "fmt"

type Data struct {
    x int
}

func (self Data) ValueTest() { // func ValueTest(self Data);
    fmt.Printf("Value: %p\n", &self)
}

func (self *Data) PointerTest() { // func PointerTest(self *Data);
    fmt.Printf("Pointer: %p\n", self)
}

func main() {
    d := Data{}
    p := &d
    fmt.Printf("Data: %p\n", p)

    d.ValueTest()   // ValueTest(d)
    d.PointerTest() // PointerTest(&d)

    p.ValueTest()   // ValueTest(*p)
    p.PointerTest() // PointerTest(p)}
```
输出:
```
    Data: 0xc42007c008
    Value: 0xc42007c018
    Pointer: 0xc42007c008
    Value: 0xc42007c020
    Pointer: 0xc42007c008
```

### 普通函数与方法的区别

1. 对于普通函数，接收者为值类型时，不能将指针类型的数据直接传递，反之亦然。
2. 对于方法（如struct的方法），接收者为值类型时，可以直接用指针类型的变量调用方法，反过来同样也可以。

```
package main

//普通函数与方法的区别（在接收者分别为值类型和指针类型的时候）

import (
    "fmt")

//1.普通函数//接收值类型参数的函数
func valueIntTest(a int) int {
    return a + 10
}

//接收指针类型参数的函数
func pointerIntTest(a *int) int {
    return *a + 10
}

func structTestValue() {
    a := 2
    fmt.Println("valueIntTest:", valueIntTest(a))
    //函数的参数为值类型，则不能直接将指针作为参数传递
    //fmt.Println("valueIntTest:", valueIntTest(&a))
    //compile error: cannot use &a (type *int) as type int in function argument

    b := 5
    fmt.Println("pointerIntTest:", pointerIntTest(&b))
    //同样，当函数的参数为指针类型时，也不能直接将值类型作为参数传递
    //fmt.Println("pointerIntTest:", pointerIntTest(b))
    //compile error:cannot use b (type int) as type *int in function argument}

//2.方法
type PersonD struct {
    id   int
    name string
}

//接收者为值类型
func (p PersonD) valueShowName() {
    fmt.Println(p.name)
}

//接收者为指针类型
func (p *PersonD) pointShowName() {
    fmt.Println(p.name)
}

func structTestFunc() {
    //值类型调用方法
    personValue := PersonD{101, "hello world"}
    personValue.valueShowName()
    personValue.pointShowName()

    //指针类型调用方法
    personPointer := &PersonD{102, "hello golang"}
    personPointer.valueShowName()
    personPointer.pointShowName()

    //与普通函数不同，接收者为指针类型和值类型的方法，指针类型和值类型的变量均可相互调用}

func main() {
    structTestValue()
    structTestFunc()
}
```
输出结果：
```
    valueIntTest: 12
    pointerIntTest: 15
    hello world
    hello world
    hello golang
    hello golang
```
## 匿名字段

Golang匿名字段 ：可以像字段成员那样访问匿名字段方法，编译器负责查找。
```
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
}

func (self *User) ToString() string { // receiver = &(Manager.User)
    return fmt.Sprintf("User: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"}}
    fmt.Printf("Manager: %p\n", &m)
    fmt.Println(m.ToString())
}
```
输出结果:
```
    Manager: 0xc42000a060
    User: 0xc42000a060, &{1 Tom}
```

通过匿名字段，可获得和继承类似的复用能力。依据编译器查找次序，只需在外层定义同名方法，就可以实现 "override"。
```
package main

import "fmt"

type User struct {
    id   int
    name string
}

type Manager struct {
    User
    title string
}

func (self *User) ToString() string {
    return fmt.Sprintf("User: %p, %v", self, self)
}

func (self *Manager) ToString() string {
    return fmt.Sprintf("Manager: %p, %v", self, self)
}

func main() {
    m := Manager{User{1, "Tom"}, "Administrator"}

    fmt.Println(m.ToString())

    fmt.Println(m.User.ToString())}
```
输出结果:
```
    Manager: 0xc420074180, &{{1 Tom} Administrator}
    User: 0xc420074180, &{1 Tom}
```

## 方法集

Golang方法集 ：每个类型都有与之关联的方法集，这会影响到接口实现规则。

* 类型 T 方法集包含全部 receiver T 方法。
* 类型 * T 方法集包含全部 receiver T + * T 方法。
* 如类型 S 包含匿名字段 T，则 S 和 * S 方法集包含 T 方法。 
* 如类型 S 包含匿名字段 * T，则 S 和 * S 方法集包含 T + * T 方法。
* 不管嵌入 T 或 * T，* S 方法集总是包含 T + * T 方法。


用实例 value 和 pointer 调用方法 (含匿名字段) 不受方法集约束，编译器总是查找全部方法，并自动转换 receiver 实参。

Go 语言中内部类型方法集提升的规则：类型 T 方法集包含全部 receiver T 方法。
```
package main

import (
    "fmt")

type T struct {
    int
}

func (t T) test() {
    fmt.Println("类型 T 方法集包含全部 receiver T 方法。")
}

func main() {
    t1 := T{1}
    fmt.Printf("t1 is : %v\n", t1)
    t1.test()
}
```
输出结果：
```
    t1 is : {1}
    类型 T 方法集包含全部 receiver T 方法。
```
类型 * T 方法集包含全部 receiver T + * T 方法。
```
package main

import (
    "fmt")

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("类型 *T 方法集包含全部 receiver T 方法。")
}

func (t *T) testP() {
    fmt.Println("类型 *T 方法集包含全部 receiver *T 方法。")
}

func main() {
    t1 := T{1}
    t2 := &t1
    fmt.Printf("t2 is : %v\n", t2)
    t2.testT()
    t2.testP()
}
```
输出：
```
    t2 is : &{1}
    类型 *T 方法集包含全部 receiver T 方法。
    类型 *T 方法集包含全部 receiver *T 方法。
```

给定一个结构体类型 S 和一个命名为 T 的类型，方法提升像下面规定的这样被包含在结构体方法集中：
如类型 S 包含匿名字段 T，则 S 和 * S 方法集包含 T 方法。
这条规则说的是当我们嵌入一个类型，嵌入类型的接受者为值类型的方法将被提升，可以被外部类型的值和指针调用。
```
package main

import (
    "fmt")

type S struct {
    T
}

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。")
}

func main() {
    s1 := S{T{1}}
    s2 := &s1
    fmt.Printf("s1 is : %v\n", s1)
    s1.testT()
    fmt.Printf("s2 is : %v\n", s2)
    s2.testT()}
```
输出：
```
    s1 is : {{1}}
    如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。
    s2 is : &{{1}}
    如类型 S 包含匿名字段 T，则 S 和 *S 方法集包含 T 方法。
```
如类型 S 包含匿名字段 * T，则 S 和 * S 方法集包含 T + * T 方法。

这条规则说的是当我们嵌入一个类型的指针，嵌入类型的接受者为值类型或指针类型的方法将被提升，可以被外部类型的值或者指针调用。
```
package main

import (
    "fmt")

type S struct {
    T
}

type T struct {
    int
}

func (t T) testT() {
    fmt.Println("如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法")
}
    
func (t *T) testP() {
    fmt.Println("如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法")
}

func main() {
    s1 := S{T{1}}
    s2 := &s1
    fmt.Printf("s1 is : %v\n", s1)
    s1.testT()
    s1.testP()
    fmt.Printf("s2 is : %v\n", s2)
    s2.testT()
    s2.testP()}
```
输出：
```
    s1 is : {{1}}
    如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法
    如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法
    s2 is : &{{1}}
    如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 T 方法
    如类型 S 包含匿名字段 *T，则 S 和 *S 方法集包含 *T 方法
```
## 表达式

Golang 表达式 ：根据调用者不同，方法分为两种表现形式:
```
    instance.method(args...) ---> <type>.func(instance, args...)
```
前者称为 method value，后者 method expression。
两者都可像普通函数那样赋值和传参，区别在于 method value 绑定实例，而 method expression 则须显式传参。
```
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) Test() {
    fmt.Printf("%p, %v\n", self, self)
}

func main() {
    u := User{1, "Tom"}
    u.Test()

    mValue := u.Test
    mValue() // 隐式传递 receiver

    mExpression := (*User).Test
    mExpression(&u) // 显式传递 receiver}
```
输出结果:
```
    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}
    0xc42000a060, &{1 Tom}
```
需要注意，method value 会复制 receiver。
```
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self User) Test() {
    fmt.Println(self)
}

func main() {
    u := User{1, "Tom"}
    mValue := u.Test // 立即复制 receiver，因为不是指针类型，不受后续修改影响。

    u.id, u.name = 2, "Jack"
    u.Test()

    mValue()
}
```
输出：
```
    {2 Jack}
    {1 Tom}
```

在汇编层面，method value 和闭包的实现方式相同，实际返回 FuncVal 类型对象。
```
    FuncVal { method_address, receiver_copy }
```

可依据方法集转换 method expression，注意 receiver 类型的差异。
```
package main

import "fmt"

type User struct {
    id   int
    name string
}

func (self *User) TestPointer() {
    fmt.Printf("TestPointer: %p, %v\n", self, self)
}

func (self User) TestValue() {
    fmt.Printf("TestValue: %p, %v\n", &self, self)
}

func main() {
    u := User{1, "Tom"}
    fmt.Printf("User: %p, %v\n", &u, u)

    mv := User.TestValue
    mv(u)

    mp := (*User).TestPointer
    mp(&u)

    mp2 := (*User).TestValue // *User 方法集包含 TestValue。签名变为 func TestValue(self *User)。实际依然是 receiver value copy。
    mp2(&u)
}
```
输出:
```
    User: 0xc42000a060, {1 Tom}
    TestValue: 0xc42000a0a0, {1 Tom}
    TestPointer: 0xc42000a060, &{1 Tom}
    TestValue: 0xc42000a100, {1 Tom}
```
将方法 "还原" 成函数，就容易理解下面的代码了。
```
package main

type Data struct{}

func (Data) TestValue() {}

func (*Data) TestPointer() {}

func main() {
    var p *Data = nil
    p.TestPointer()

    (*Data)(nil).TestPointer() // method value
    (*Data).TestPointer(nil)   // method expression

    // p.TestValue()            // invalid memory address or nil pointer dereference

    // (Data)(nil).TestValue()  // cannot convert nil to type Data
    // Data.TestValue(nil)      // cannot use nil as type Data in function argument}
```

## 自定义error

### 系统抛
```
package main

import "fmt"

// 系统抛
func test01() {
   a := [5]int{0, 1, 2, 3, 4}
   a[1] = 123
   fmt.Println(a)
   //a[10] = 11
   index := 10
   a[index] = 10
   fmt.Println(a)
}

func getCircleArea(radius float32) (area float32) {
   if radius < 0 {
      // 自己抛
      panic("半径不能为负")
   }
   return 3.14 * radius * radius
}

func test02() {
   getCircleArea(-5)
}

func test03() {
   // 延时执行匿名函数
   // 延时到何时？（1）程序正常结束   （2）发生异常时
   defer func() {
      // recover() 复活 恢复
      // 会返回程序为什么挂了
      if err := recover(); err != nil {
         fmt.Println(err)
      }
   }()
   getCircleArea(-5)
   fmt.Println("这里有没有执行")
}

func test04()  {
   test03()
   fmt.Println("test04")
}

func main() {
   test04()
}
```

### 返回异常
```
package main

import (
   "errors"
   "fmt")

func getCircleArea(radius float32) (area float32, err error) {
   if radius < 0 {
      // 构建个异常对象
      err = errors.New("半径不能为负")
      return
   }
   area = 3.14 * radius * radius
   return
}

func main() {
   area, err := getCircleArea(-5)
   if err != nil {
      fmt.Println(err)
   } else {
      fmt.Println(area)
   }
}
```

### 自定义error
```
package main

import (
    "fmt"
    "os"
    "time")

type PathError struct {
    path       string
    op         string
    createTime string
    message    string
}

func (p *PathError) Error() string {
    return fmt.Sprintf("path=%s \nop=%s \ncreateTime=%s \nmessage=%s", p.path,
        p.op, p.createTime, p.message)
}

func Open(filename string) error {

    file, err := os.Open(filename)
    if err != nil {
        return &PathError{
            path:       filename,
            op:         "read",
            message:    err.Error(),
            createTime: fmt.Sprintf("%v", time.Now()),
        }
    }

    defer file.Close()
    return nil
}

func main() {
    err := Open("/Users/5lmh/Desktop/go/src/test.txt")
    switch v := err.(type) {
    case *PathError:
        fmt.Println("get path error,", v)
    default:

    }

}
```

输出结果：
```
    get path error, path=/Users/pprof/Desktop/go/src/test.txt 
    op=read 
    createTime=2018-04-05 11:25:17.331915 +0800 CST m=+0.000441790 
    message=open /Users/pprof/Desktop/go/src/test.txt: no such file or directory
```
# 面向对象

## 匿名字段

go支持只提供类型而不写字段名的方式，也就是匿名字段，也称为嵌入字段
```
package main

import "fmt"

//go支持只提供类型而不写字段名的方式，也就是匿名字段，也称为嵌入字段

//人
type Person struct {
    name string
    sex  string
    age  int
}

type Student struct {
    Person
    id   int
    addr string
}

func main() {
    // 初始化
    s1 := Student{Person{"5lmh", "man", 20}, 1, "bj"}
    fmt.Println(s1)

    s2 := Student{Person: Person{"5lmh", "man", 20}}
    fmt.Println(s2)

    s3 := Student{Person: Person{name: "5lmh"}}
    fmt.Println(s3)
}
```

输出结果：
```
    {{5lmh man 20} 1 bj}
    {{5lmh man 20} 0 }
    {{5lmh  0} 0 }
```

同名字段的情况
```
package main

import "fmt"

//人
type Person struct {
    name string
    sex  string
    age  int
}

type Student struct {
    Person
    id   int
    addr string
    //同名字段
    name string
}

func main() {
    var s Student
    // 给自己字段赋值了
    s.name = "5lmh"
    fmt.Println(s)

    // 若给父类同名字段赋值，如下
    s.Person.name = "枯藤"
    fmt.Println(s)}
```
输出结果：
```
    {{  0} 0  5lmh}
    {{枯藤  0} 0  5lmh}
```

所有的内置类型和自定义类型都是可以作为匿名字段去使用
```
package main

import "fmt"

//人
type Person struct {
    name string
    sex  string
    age  int
}

// 自定义类型
type mystr string

// 学生
type Student struct {
    Person
    int
    mystr
}

func main() {
    s1 := Student{Person{"5lmh", "man", 18}, 1, "bj"}
    fmt.Println(s1)
}
```
输出结果：
```
    {{5lmh man 18} 1 bj}
```

指针类型匿名字段
```
package main

import "fmt"

//人
type Person struct {
    name string
    sex  string
    age  int
}

// 学生
type Student struct {
    *Person
    id   int
    addr string
}

func main() {
    s1 := Student{&Person{"5lmh", "man", 18}, 1, "bj"}
    fmt.Println(s1)
    fmt.Println(s1.name)
    fmt.Println(s1.Person.name)
}
```
输出：
```
    {0xc00005c360 1 bj}
    zs
    zs
```
## 接口

接口（interface）定义了一个对象的行为规范，只定义规范不实现，由具体的对象来实现规范的细节。

### 接口类型

在Go语言中接口（interface）是一种类型，一种抽象的类型。
interface是一组method的集合，是duck-type programming的一种体现。接口做的事情就像是定义一个协议（规则），只要一台机器有洗衣服和甩干的功能，我就称它为洗衣机。不关心属性（数据），只关心行为（方法）。
为了保护你的Go语言职业生涯，请牢记接口（interface）是一种类型。

### 为什么要使用接口
```
type Cat struct{}

func (c Cat) Say() string { 
    return "喵喵喵" 
}

type Dog struct{}

func (d Dog) Say() string { return "汪汪汪" }

func main() {
    c := Cat{}
    fmt.Println("猫:", c.Say())
    d := Dog{}
    fmt.Println("狗:", d.Say())
}
```

上面的代码中定义了猫和狗，然后它们都会叫，你会发现main函数中明显有重复的代码，如果我们后续再加上猪、青蛙等动物的话，我们的代码还会一直重复下去。那我们能不能把它们当成“能叫的动物”来处理呢？
Go语言中为了解决类似上面的问题，就设计了接口这个概念。接口区别于我们之前所有的具体类型，接口是一种抽象的类型。当你看到一个接口类型的值时，你不知道它是什么，唯一知道的是通过它的方法能做什么。

### 接口的定义

Go语言提倡面向接口编程。

接口是一个或多个方法签名的集合。
    任何类型的方法集中只要拥有该接口'对应的全部方法'签名。
    就表示它 "实现" 了该接口，无须在该类型上显式声明实现了哪个接口。
    这称为Structural Typing。
    所谓对应方法，是指有相同名称、参数列表 (不包括参数名) 以及返回值。
    当然，该类型还可以有其他方法。

    接口只有方法声明，没有实现，没有数据字段。
    接口可以匿名嵌入其他接口，或嵌入到结构中。
    对象赋值给接口时，会发生拷贝，而接口内部存储的是指向这个复制品的指针，既无法修改复制品的状态，也无法获取指针。
    只有当接口存储的类型和对象都为nil时，接口才等于nil。
    接口调用不会做receiver的自动转换。
    接口同样支持匿名字段方法。
    接口也可实现类似OOP中的多态。
    空接口可以作为任何类型数据的容器。
    一个类型可实现多个接口。
    接口命名习惯以 er 结尾。


每个接口由数个方法组成，接口的定义格式如下：
```
    type 接口类型名 interface{
        方法名1( 参数列表1 ) 返回值列表1
        方法名2( 参数列表2 ) 返回值列表2
        …
    }
```

其中：

   1.接口名：使用type将接口定义为自定义的类型名。Go语言的接口在命名时，一般会在单词后面添加er，如有写操作的接口叫Writer，有字符串功能的接口叫Stringer等。接口名最好要能突出该接口的类型含义。
    2.方法名：当方法名首字母是大写且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
    3.参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以省略。
    
### 实现接口的条件

一个对象只要全部实现了接口中的方法，那么就实现了这个接口。换句话说，接口就是一个需要实现的方法列表。我们来定义一个Sayer接口：
```
// Sayer 接口
type Sayer interface {
    say()
}
```
定义dog和cat两个结构体：
```
type dog struct {}

type cat struct {}
```

因为Sayer接口里只有一个say方法，所以我们只需要给dog和cat 分别实现say方法就可以实现Sayer接口了。
```

// dog实现了Sayer接口
func (d dog) say() {
    fmt.Println("汪汪汪")
}

// cat实现了Sayer接口
func (c cat) say() {
    fmt.Println("喵喵喵")
}
```

接口的实现就是这么简单，只要实现了接口中的所有方法，就实现了这个接口。

### 接口类型变量

那实现了接口有什么用呢？接口类型变量能够存储所有实现了该接口的实例。 例如上面的示例中，Sayer类型的变量能够存储dog和cat类型的变量。
```
func main() {
    var x Sayer // 声明一个Sayer类型的变量x
    a := cat{}  // 实例化一个cat
    b := dog{}  // 实例化一个dog
    x = a       // 可以把cat实例直接赋值给x
    x.say()     // 喵喵喵
    x = b       // 可以把dog实例直接赋值给x
    x.say()     // 汪汪汪
}
```

### 值接收者和指针接收者实现接口的区别

使用值接收者实现接口和使用指针接收者实现接口有什么区别呢？接下来我们通过一个例子看一下其中的区别。我们有一个Mover接口和一个dog结构体。
```
type Mover interface {
    move()
}

type dog struct {}
```

#### 值接收者实现接口
```
func (d dog) move() {
    fmt.Println("狗会动")
}
```
此时实现接口的是dog类型：
```
func main() {
    var x Mover
    var wangcai = dog{} // 旺财是dog类型
    x = wangcai         // x可以接收dog类型
    var fugui = &dog{}  // 富贵是*dog类型
    x = fugui           // x可以接收*dog类型
    x.move()
}
```

从上面的代码中我们可以发现，使用值接收者实现接口之后，不管是dog结构体还是结构体指针* dog类型的变量都可以赋值给该接口变量。因为Go语言中有对指针类型变量求值的语法糖，dog指针fugui内部会自动求值* fugui。

#### 指针接收者实现接口

同样的代码我们再来测试一下使用指针接收者有什么区别：
```
func (d *dog) move() {
    fmt.Println("狗会动")}func main() {
    var x Mover
    var wangcai = dog{} // 旺财是dog类型
    x = wangcai         // x不可以接收dog类型
    var fugui = &dog{}  // 富贵是*dog类型
    x = fugui           // x可以接收*dog类型
}
```

此时实现Mover接口的是* dog类型，所以不能给x传入dog类型的wangcai，此时x只能存储* dog类型的值。

#### 下面的代码是一个比较好的面试题

请问下面的代码是否能通过编译？
```
type People interface {
    Speak(string) string
}

type Student struct{}

func (stu *Stduent) Speak(think string) (talk string) {
    if think == "sb" {
        talk = "你是个大帅比"
    } else {
        talk = "您好"
    }
    return
}

func main() {
    var peo People = Student{}
    think := "bitch"
    fmt.Println(peo.Speak(think))
}
```

### 类型与接口的关系

#### 一个类型实现多个接口

一个类型可以同时实现多个接口，而接口间彼此独立，不知道对方的实现。 例如，狗可以叫，也可以动。我们就分别定义Sayer接口和Mover接口，如下： Mover接口。
```
// Sayer 接口
type Sayer interface {
    say()
}

// Mover 接口
type Mover interface {
    move()
}
```

dog既可以实现Sayer接口，也可以实现Mover接口。
```
type dog struct {
    name string
}

// 实现Sayer接口
func (d dog) say() {
    fmt.Printf("%s会叫汪汪汪\n", d.name)
}

// 实现Mover接口
func (d dog) move() {
    fmt.Printf("%s会动\n", d.name)
}

func main() {
    var x Sayer
    var y Mover

    var a = dog{name: "旺财"}
    x = a
    y = a
    x.say()
    y.move()
}
```

#### 多个类型实现同一接口

Go语言中不同的类型还可以实现同一接口 首先我们定义一个Mover接口，它要求必须由一个move方法。
```
// Mover 接口
type Mover interface {
    move()
}
```

例如狗可以动，汽车也可以动，可以使用如下代码实现这个关系：
```
type dog struct {
    name string
}

type car struct {
    brand string
}

// dog类型实现Mover接口
func (d dog) move() {
    fmt.Printf("%s会跑\n", d.name)
}

// car类型实现Mover接口
func (c car) move() {
    fmt.Printf("%s速度70迈\n", c.brand)
}
```

这个时候我们在代码中就可以把狗和汽车当成一个会动的物体来处理了，不再需要关注它们具体是什么，只需要调用它们的move方法就可以了。
```
func main() {
    var x Mover
    var a = dog{name: "旺财"}
    var b = car{brand: "保时捷"}
    x = a
    x.move()
    x = b
    x.move()
}
```
上面的代码执行结果如下：
```
    旺财会跑
    保时捷速度70迈
```

并且一个接口的方法，不一定需要由一个类型完全实现，接口的方法可以通过在类型中嵌入其他类型或者结构体来实现。
```
// WashingMachine 洗衣机
type WashingMachine interface {
    wash()
    dry()
}

// 甩干器
type dryer struct{}

// 实现WashingMachine接口的dry()方法
func (d dryer) dry() {
    fmt.Println("甩一甩")
}

// 海尔洗衣机
type haier struct {
    dryer //嵌入甩干器
}

// 实现WashingMachine接口的wash()方法
func (h haier) wash() {
    fmt.Println("洗刷刷")
}
```

### 接口嵌套

接口与接口间可以通过嵌套创造出新的接口。
```
// Sayer 接口
type Sayer interface {
    say()
}

// Mover 接口
type Mover interface {
    move()
}

// 接口嵌套
type animal interface {
    Sayer
    Mover
}
```

嵌套得到的接口的使用与普通接口一样，这里我们让cat实现animal接口：
```
type cat struct {
    name string
}

func (c cat) say() {
    fmt.Println("喵喵喵")
}

func (c cat) move() {
    fmt.Println("猫会动")
}

func main() {
    var x animal
    x = cat{name: "花花"}
    x.move()
    x.say()
}
```

### 空接口

#### 空接口的定义

空接口是指没有定义任何方法的接口。因此任何类型都实现了空接口。
空接口类型的变量可以存储任意类型的变量。
```
func main() {
    // 定义一个空接口x
    var x interface{}
    s := "pprof.cn"
    x = s
    fmt.Printf("type:%T value:%v\n", x, x)
    i := 100
    x = i
    fmt.Printf("type:%T value:%v\n", x, x)
    b := true
    x = b
    fmt.Printf("type:%T value:%v\n", x, x)
}
```

#### 空接口的应用


##### 空接口作为函数的参数

使用空接口实现可以接收任意类型的函数参数。
```
// 空接口作为函数参数
func show(a interface{}) {
    fmt.Printf("type:%T value:%v\n", a, a)
}
```

##### 空接口作为map的值

使用空接口实现可以保存任意值的字典。
```
// 空接口作为map值
    var studentInfo = make(map[string]interface{})
    studentInfo["name"] = "李白"
    studentInfo["age"] = 18
    studentInfo["married"] = false
    fmt.Println(studentInfo)
```

#### 类型断言

空接口可以存储任意类型的值，那我们如何获取其存储的具体数据呢？

##### 接口值

一个接口的值（简称接口值）是由一个具体类型和具体类型的值两部分组成的。这两部分分别称为接口的动态类型和动态值。我们来看一个具体的例子：
```
var w io.Writer
w = os.Stdout
w = new(bytes.Buffer)
w = nil
```
![](go基础/类型断言.png)

想要判断空接口中的值这个时候就可以使用类型断言，其语法格式：
```
    x.(T)
```
其中：
    x：表示类型为interface{}的变量
    T：表示断言x可能是的类型。
    

该语法返回两个参数，第一个参数是x转化为T类型后的变量，第二个值是一个布尔值，若为true则表示断言成功，为false则表示断言失败。
```
func main() {
    var x interface{}
    x = "pprof.cn"
    v, ok := x.(string)
    if ok {
        fmt.Println(v)
    } else {
        fmt.Println("类型断言失败")
    }
}
```

上面的示例中如果要断言多次就需要写多个if判断，这个时候我们可以使用switch语句来实现：
```
func justifyType(x interface{}) {
    switch v := x.(type) {
    case string:
        fmt.Printf("x is a string，value is %v\n", v)
    case int:
        fmt.Printf("x is a int is %v\n", v)
    case bool:
        fmt.Printf("x is a bool is %v\n", v)
    default:
        fmt.Println("unsupport type！")
    }
}
```

因为空接口可以存储任意类型值的特点，所以空接口在Go语言中的使用十分广泛。关于接口需要注意的是，只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才需要定义接口。不要为了接口而写接口，那样只会增加不必要的抽象，导致不必要的运行时损耗。
# 网络编程


# 数据操作

## GORM
### 入门指南
#### 概览

* 全特性 ORM (几乎包含所有特性)
* 模型关联 (一对一， 一对多，一对多（反向）， 多对多， 多态关联)
* 钩子 (Before/After Create/Save/Update/Delete/Find)
* 预加载
* 事务
* 复合主键
* SQL 构造器
* 自动迁移
* 日志
* 基于GORM回调编写可扩展插件
* 全特性测试覆盖

#### 安装
```
go get -u github.com/jinzhu/gorm
```

#### 快速开始
```
package main

import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/sqlite"
)

type Product struct {
  gorm.Model
  Code string
  Price uint
}

func main() {
  db, err := gorm.Open("sqlite3", "test.db")
  if err != nil {
    panic("failed to connect database")
  }
  defer db.Close()

  //自动检查 Product 结构是否变化，变化则进行迁移
  db.AutoMigrate(&Product{})

  // 增
  db.Create(&Product{Code: "L1212", Price: 1000})

  // 查
  var product Product
  db.First(&product, 1) // 找到id为1的产品
  db.First(&product, "code = ?", "L1212") // 找出 code 为 l1212 的产品

  // 改 - 更新产品的价格为 2000
  db.Model(&product).Update("Price", 2000)

  // 删 - 删除产品
  db.Delete(&product)
}
```

### 模型定义

模型一般都是普通的 Golang 的结构体，Go的基本数据类型，或者指针。sql.Scanner 和 driver.Valuer，同时也支持接口。
```
type User struct {
  gorm.Model
  Name         string
  Age          sql.NullInt64
  Birthday     *time.Time
  Email        string  `gorm:"type:varchar(100);unique_index"`
  Role         string  `gorm:"size:255"` //设置字段的大小为255个字节
  MemberNumber *string `gorm:"unique;not null"` // 设置 memberNumber 字段唯一且不为空
  Num          int     `gorm:"AUTO_INCREMENT"` // 设置 Num字段自增
  Address      string  `gorm:"index:addr"` // 给Address 创建一个名字是  `addr`的索引
  IgnoreMe     int     `gorm:"-"` //忽略这个字段
}
```

#### 结构标签

标签是声明模型时可选的标记。GORM 支持以下标记：

##### **支持的结构标签**

| 标签            | 说明                                     |
| --------------- | ---------------------------------------- |
| Column          | 指定列的名称                             |
| Type            | 指定列的类型                             |
| Size            | 指定列的大小，默认是 255                 |
| PRIMARY_KEY     | 指定一个列作为主键                       |
| UNIQUE          | 指定一个唯一的列                         |
| DEFAULT         | 指定一个列的默认值                       |
| PRECISION       | 指定列的数据的精度                       |
| NOT NULL        | 指定列的数据不为空                       |
| AUTO_INCREMENT  | 指定一个列的数据是否自增                 |
| INDEX           | 创建带或不带名称的索引，同名创建复合索引 |
| UNIQUE_INDEX    | 类似 索引，创建一个唯一的索引            |
| EMBEDDED        | 将 struct 设置为 embedded                |
| EMBEDDED_PREFIX | 设置嵌入式结构的前缀名称                 |
| -               | 忽略这些字段                             |

##### 关联的结构标签

| 标签                             | 说明                   |
| -------------------------------- | ---------------------- |
| MANY2MANY                        | 指定连接表名称         |
| FOREIGNKEY                       | 指定外键               |
| ASSOCIATION_FOREIGNKEY           | 指定关联外键           |
| POLYMORPHIC                      | 指定多态类型           |
| POLYMORPHIC_VALUE                | 指定多态的值           |
| JOINTABLE_FOREIGNKEY             | 指定连接表的外键       |
| ASSOCIATION_JOINTABLE_FOREIGNKEY | 指定连接表的关联外键   |
| SAVE_ASSOCIATIONS                | 是否自动保存关联       |
| ASSOCIATION_AUTOUPDATE           | 是否自动更新关联       |
| ASSOCIATION_AUTOCREATE           | 是否自动创建关联       |
| ASSOCIATION_SAVE_REFERENCE       | 是否引用自动保存的关联 |
| PRELOAD                          | 是否自动预加载关联     |


### 惯例

#### gorm.Model

gorm.Model 是一个包含一些基本字段的结构体, 包含的字段有 ID，CreatedAt， UpdatedAt， DeletedAt。

你可以用它来嵌入到你的模型中，或者也可以用它来建立自己的模型。
```
// gorm.Model 定义
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}

// 将字段 `ID`, `CreatedAt`, `UpdatedAt`, `DeletedAt` 注入到 `User` 模型中
type User struct {
  gorm.Model
  Name string
}

// 声明 gorm.Model 模型
type User struct {
  ID   int
  Name string
}
```

#### ID 作为主键

GORM 默认使用 ID 作为主键名。
```
type User struct {
  ID   string // 字段名 `ID` 将被作为默认的主键名
}

// 设置字段 `AnimalID` 为默认主键
type Animal struct {
  AnimalID int64 `gorm:"primary_key"`
  Name     string
  Age      int64
}
```

#### 复数表名

表名是结构体名称的复数形式
```
type User struct {} // 默认的表名是 `users`

// 设置 `User` 的表名为 `profiles`
func (User) TableName() string {
  return "profiles"
}

func (u User) TableName() string {
    if u.Role == "admin" {
        return "admin_users"
    } else {
        return "users"
    }
}

// 如果设置禁用表名复数形式属性为 true，`User` 的表名将是 `user`
db.SingularTable(true)
```

#### 指定表名
```
// 用 `User` 结构体创建 `delete_users` 表
db.Table("deleted_users").CreateTable(&User{})

var deleted_users []User
db.Table("deleted_users").Find(&deleted_users)
//// SELECT * FROM deleted_users;

db.Table("deleted_users").Where("name = ?", "jinzhu").Delete()
//// DELETE FROM deleted_users WHERE name = 'jinzhu';
```

#### 修改默认表名

你可以通过定义 DefaultTableNameHandler 字段来对表名使用任何规则。
```
gorm.DefaultTableNameHandler = func (db *gorm.DB, defaultTableName string) string  {
    return "prefix_" + defaultTableName;
}
```

#### 蛇形列名

列名是字段名的蛇形小写形式
```
type User struct {
  ID        uint      // 字段名是 `id`
  Name      string    // 字段名是 `name`
  Birthday  time.Time // 字段名是 `birthday`
  CreatedAt time.Time // 字段名是 `created_at`
}

// 重写列名
type Animal struct {
    AnimalId    int64     `gorm:"column:beast_id"`         // 设置列名为 `beast_id`
    Birthday    time.Time `gorm:"column:day_of_the_beast"` // 设置列名为 `day_of_the_beast`
    Age         int64     `gorm:"column:age_of_the_beast"` // 设置列名为 `age_of_the_beast`
}
```

#### 时间戳跟踪

##### CreatedAt

对于有 CreatedAt 字段的模型，它将被设置为首次创建记录的当前时间。
```
db.Create(&user) // 将设置 `CreatedAt` 为当前时间

// 你可以使用 `Update` 方法来更改默认时间
db.Model(&user).Update("CreatedAt", time.Now())
```

##### UpdatedAt

对于有 UpdatedAt 字段的模型，它将被设置为记录更新时的当前时间。
```
db.Save(&user) // 将设置 `UpdatedAt` 为当前时间

db.Model(&user).Update("name", "jinzhu") // 将设置 `UpdatedAt` 为当前时间
```

##### DeletedAt

对于有 DeletedAt 字段的模型，当删除它们的实例时，它们并没有被从数据库中删除，只是将 DeletedAt 字段设置为当前时间。参考 Soft Delete

### 连接数据库

为了连接数据库，你首先要导入数据库驱动程序。例如：
```
import _ "github.com/go-sql-driver/mysql"
```

GORM 已经包含了一些驱动程序，为了方便的去记住它们的导入路径，你可以像下面这样导入 mysql 驱动程序
```
import _ "github.com/jinzhu/gorm/dialects/mysql"
// import _ "github.com/jinzhu/gorm/dialects/postgres"
// import _ "github.com/jinzhu/gorm/dialects/sqlite"
// import _ "github.com/jinzhu/gorm/dialects/mssql"
```

#### 支持的数据库

##### MySQL

注意： 为了正确的处理 time.Time ，你需要包含 parseTime 作为参数。 (More supported parameters)
```
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mysql"
)

func main() {
  db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
  defer db.Close()
}
```

##### PostgreSQL
```
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/postgres"
)

func main() {
  db, err := gorm.Open("postgres", "host=myhost port=myport user=gorm dbname=gorm password=mypassword")
  defer db.Close()
}
```
##### Sqlite3
```
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/sqlite"
)

func main() {
  db, err := gorm.Open("sqlite3", "/tmp/gorm.db")
  defer db.Close()
}
```

##### SQL Server

[Get started with SQL Server](https://sqlchoice.azurewebsites.net/en-us/sql-server/developer-get-started/go/windows/)，它可以通过 Docker 运行在你的 Mac， Linux 上。
```
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mssql"
)

func main() {
  db, err := gorm.Open("mssql", "sqlserver://username:password@localhost:1433?database=dbname")
  defer db.Close()
}
```





# 并发编程

## 并发介绍

多线程程序在一个核的cpu上运行，就是并发。
多线程程序在多个核的cpu上运行，就是并行。

### 协程和线程

协程：独立的栈空间，共享堆空间，调度由用户自己控制，本质上有点类似于用户级线程，这些用户级线程的调度也是自己实现的。
线程：一个线程上可以跑多个协程，协程是轻量级的线程。


goroutine 只是由官方实现的超级"线程池"。每个实例4~5KB的栈内存占用和由于实现机制而大幅减少的创建和销毁开销是go高并发的根本原因。

并发主要由切换时间片来实现"同时"运行，并行则是直接利用多核实现多线程的运行，go可以设置使用核数，以发挥多核计算机的能力。

goroutine 奉行通过通信来共享内存，而不是共享内存来通信。

## Goroutine

在java/c++中我们要实现并发编程的时候，我们通常需要自己维护一个线程池，并且需要自己去包装一个又一个的任务，同时需要自己去调度线程执行任务并维护上下文切换，这一切通常会耗费程序员大量的心智。那么能不能有一种机制，程序员只需要定义很多个任务，让系统去帮助我们把这些任务分配到CPU上实现并发执行呢？Go语言中的goroutine就是这样一种机制，goroutine的概念类似于线程，但 goroutine是由Go的运行时（runtime）调度和管理的。Go程序会智能地将 goroutine 中的任务合理地分配给每个CPU。Go语言之所以被称为现代化的编程语言，就是因为它在语言层面已经内置了调度和上下文切换的机制。在Go语言编程中你不需要去自己写进程、线程、协程，你的技能包里只有一个技能–goroutine，当你需要让某个任务并发执行的时候，你只需要把这个任务包装成一个函数，开启一个goroutine去执行这个函数就可以了，就是这么简单粗暴。


### 使用goroutine

Go语言中使用goroutine非常简单，只需要在调用函数的时候在前面加上go关键字，就可以为一个函数创建一个goroutine。一个goroutine必定对应一个函数，可以创建多个goroutine去执行相同的函数。


### 启动单个goroutine

启动goroutine的方式非常简单，只需要在调用的函数（普通函数和匿名函数）前面加上一个go关键字。
```

func hello() {
    fmt.Println("Hello Goroutine!")
}
    
func main() {
    hello()
    fmt.Println("main goroutine done!")
}
```

这个示例中hello函数和下面的语句是串行的，执行的结果是打印完Hello Goroutine!后打印main goroutine done!。接下来我们在调用hello函数前面加上关键字go，也就是启动一个goroutine去执行hello这个函数。
```
func main() {
    go hello() // 启动另外一个goroutine去执行hello函数
    fmt.Println("main goroutine done!")
}
```

这一次的执行结果只打印了main goroutine done!，并没有打印Hello Goroutine!。为什么呢？在程序启动时，Go程序就会为main()函数创建一个默认的goroutine。当main()函数返回的时候该goroutine就结束了，所有在main()函数中启动的goroutine会一同结束，main函数所在的goroutine就像是权利的游戏中的夜王，其他的goroutine都是异鬼，夜王一死它转化的那些异鬼也就全部GG了。所以我们要想办法让main函数等一等hello函数，最简单粗暴的方式就是time.Sleep了。
```
func main() {
    go hello() // 启动另外一个goroutine去执行hello函数
    fmt.Println("main goroutine done!")
    time.Sleep(time.Second)
}
```
执行上面的代码你会发现，这一次先打印main goroutine done!，然后紧接着打印Hello Goroutine!。首先为什么会先打印main goroutine done!是因为我们在创建新的goroutine的时候需要花费一些时间，而此时main函数所在的goroutine是继续执行的。

### 启动多个goroutine

在Go语言中实现并发就是这样简单，我们还可以启动多个goroutine。让我们再来一个例子： （这里使用了sync.WaitGroup来实现goroutine的同步）
```
var wg sync.WaitGroup

func hello(i int) {
    defer wg.Done() // goroutine结束就登记-1
    fmt.Println("Hello Goroutine!", i)
}
    
func main() {

    for i := 0; i < 10; i++ {
        wg.Add(1) // 启动一个goroutine就登记+1
        go hello(i)
    }
    wg.Wait() // 等待所有登记的goroutine都结束
}
```

多次执行上面的代码，会发现每次打印的数字的顺序都不一致。这是因为10个goroutine是并发执行的，而goroutine的调度是随机的。

注意：

如果主协程退出了，其他任务还执行吗（运行下面的代码测试一下吧）
```
package main

import (
    "fmt"
    "time")

func main() {
    // 合起来写
    go func() {
        i := 0
        for {
            i++
            fmt.Printf("new goroutine: i = %d\n", i)
            time.Sleep(time.Second)
        }
    }()
    i := 0
    for {
        i++
        fmt.Printf("main goroutine: i = %d\n", i)
        time.Sleep(time.Second)
        if i == 2 {
            break
        }
    }
}
```

### goroutine与线程

#### 可增长的栈

OS线程（操作系统线程）一般都有固定的栈内存（通常为2MB）,一个goroutine的栈在其生命周期开始时只有很小的栈（典型情况下2KB），goroutine的栈不是固定的，他可以按需增大和缩小，goroutine的栈大小限制可以达到1GB，虽然极少会用到这个大。所以在Go语言中一次创建十万左右的goroutine也是可以的。

#### goroutine调度

GPM是Go语言运行时（runtime）层面的实现，是go语言自己实现的一套调度系统。区别于操作系统调度OS线程。

1. G很好理解，就是个goroutine的，里面除了存放本goroutine信息外 还有与所在P的绑定等信息。
2. P管理着一组goroutine队列，P里面会存储当前goroutine运行的上下文环境（函数指针，堆栈地址及地址边界），P会对自己管理的goroutine队列做一些调度（比如把占用CPU时间较长的goroutine暂停、运行后续的goroutine等等）当自己的队列消费完了就去全局队列里取，如果全局队列里也消费完了会去其他P的队列里抢任务。
3. M（machine）是Go运行时（runtime）对操作系统内核线程的虚拟， M与内核线程一般是一一映射的关系， 一个groutine最终是要放到M上执行的；


P与M一般也是一一对应的。他们关系是： P管理着一组G挂载在M上运行。当一个G长久阻塞在一个M上时，runtime会新建一个M，阻塞G所在的P会把其他的G 挂载在新建的M上。当旧的G阻塞完成或者认为其已经死掉时 回收旧的M。

P的个数是通过runtime.GOMAXPROCS设定（最大256），Go1.5版本之后默认为物理线程数。 在并发量大的时候会增加一些P和M，但不会太多，切换太频繁的话得不偿失。

单从线程调度讲，Go语言相比起其他语言的优势在于OS线程是由OS内核来调度的，goroutine则是由Go运行时（runtime）自己的调度器调度的，这个调度器使用一个称为m:n调度的技术（复用/调度m个goroutine到n个OS线程）。 其一大特点是goroutine的调度是在用户态下完成的， 不涉及内核态与用户态之间的频繁切换，包括内存的分配与释放，都是在用户态维护着一块大的内存池， 不直接调用系统的malloc函数（除非内存池需要改变），成本比调度OS线程低很多。 另一方面充分利用了多核的硬件资源，近似的把若干goroutine均分在物理线程上， 再加上本身goroutine的超轻量，以上种种保证了go调度方面的性能。

## runtime包

### runtime.Gosched()

让出CPU时间片，重新等待安排任务(大概意思就是本来计划的好好的周末出去烧烤，但是你妈让你去相亲,两种情况第一就是你相亲速度非常快，见面就黄不耽误你继续烧烤，第二种情况就是你相亲速度特别慢，见面就是你侬我侬的，耽误了烧烤，但是还馋就是耽误了烧烤你还得去烧烤)
```
package main

import (
    "fmt"
    "runtime")

func main() {
    go func(s string) {
        for i := 0; i < 2; i++ {
            fmt.Println(s)
        }
    }("world")
    // 主协程
    for i := 0; i < 2; i++ {
        // 切一下，再次分配任务
        runtime.Gosched()
        fmt.Println("hello")
    }
}
```

### runtime.Goexit()

退出当前协程(一边烧烤一边相亲，突然发现相亲对象太丑影响烧烤，果断让她滚蛋，然后也就没有然后了)
```
package main

import (
    "fmt"
    "runtime")

func main() {
    go func() {
        defer fmt.Println("A.defer")
        func() {
            defer fmt.Println("B.defer")
            // 结束协程
            runtime.Goexit()
            defer fmt.Println("C.defer")
            fmt.Println("B")
        }()
        fmt.Println("A")
    }()
    for {
    }
}
```

### runtime.GOMAXPROCS

Go运行时的调度器使用GOMAXPROCS参数来确定需要使用多少个OS线程来同时执行Go代码。默认值是机器上的CPU核心数。例如在一个8核心的机器上，调度器会把Go代码同时调度到8个OS线程上（GOMAXPROCS是m:n调度中的n）。

Go语言中可以通过runtime.GOMAXPROCS()函数设置当前程序并发时占用的CPU逻辑核心数。Go1.5版本之前，默认使用的是单核心执行。Go1.5版本之后，默认使用全部的CPU逻辑核心数。我们可以通过将任务分配到不同的CPU逻辑核心上实现并行的效果，这里举个例子：
```
func a() {
    for i := 1; i < 10; i++ {
        fmt.Println("A:", i)
    }
}

func b() {
    for i := 1; i < 10; i++ {
        fmt.Println("B:", i)
    }
}

func main() {
    runtime.GOMAXPROCS(1)
    go a()
    go b()
    time.Sleep(time.Second)
}
```
两个任务只有一个逻辑核心，此时是做完一个任务再做另一个任务。 将逻辑核心数设为2，此时两个任务并行执行，代码如下。
```
func a() {
    for i := 1; i < 10; i++ {
        fmt.Println("A:", i)
    }
}

func b() {
    for i := 1; i < 10; i++ {
        fmt.Println("B:", i)
    }
}

func main() {
    runtime.GOMAXPROCS(2)
    go a()
    go b()
    time.Sleep(time.Second)
}
```

Go语言中的操作系统线程和goroutine的关系：

* 一个操作系统线程对应用户态多个goroutine。
* go程序可以同时使用多个操作系统线程。
* goroutine和OS线程是多对多的关系，即m:n。


## Channel

单纯地将函数并发执行是没有意义的。函数与函数间需要交换数据才能体现并发执行函数的意义。

虽然可以使用共享内存进行数据交换，但是共享内存在不同的goroutine中容易发生竞态问题。为了保证数据交换的正确性，必须使用互斥量对内存进行加锁，这种做法势必造成性能问题。

Go语言的并发模型是CSP（Communicating Sequential Processes），提倡通过通信共享内存而不是通过共享内存而实现通信。

如果说goroutine是Go程序并发的执行体，channel就是它们之间的连接。channel是可以让一个goroutine发送特定值到另一个goroutine的通信机制。

Go 语言中的通道（channel）是一种特殊的类型。通道像一个传送带或者队列，总是遵循先入先出（First In First Out）的规则，保证收发数据的顺序。每一个通道都是一个具体类型的导管，也就是声明channel的时候需要为其指定元素类型。

### channel类型

channel是一种类型，一种引用类型。声明通道类型的格式如下：
```
    var 变量 chan 元素类型
```
例如：
```
    var ch1 chan int   // 声明一个传递整型的通道
    var ch2 chan bool  // 声明一个传递布尔型的通道
    var ch3 chan []int // 声明一个传递int切片的通道
```

### 创建channel

通道是引用类型，通道类型的空值是nil。
```
var ch chan int
fmt.Println(ch) // <nil>
```
声明的通道后需要使用make函数初始化之后才能使用。
创建channel的格式如下：
```
    make(chan 元素类型, [缓冲大小])
```

channel的缓冲大小是可选的。
```
ch4 := make(chan int)
ch5 := make(chan bool)
ch6 := make(chan []int)
```

### channel操作

通道有发送（send）、接收(receive）和关闭（close）三种操作。
发送和接收都使用<-符号。现在我们先使用以下语句定义一个通道：
```
ch := make(chan int)
```

#### 发送

将一个值发送到通道中。
```
ch <- 10 // 把10发送到ch中
```

#### 接收

从一个通道中接收值。
```
x := <- ch // 从ch中接收值并赋值给变量x
<-ch       // 从ch中接收值，忽略结果
```

#### 关闭

我们通过调用内置的close函数来关闭通道。
```
    close(ch)
```

关于关闭通道需要注意的事情是，只有在通知接收方goroutine所有的数据都发送完毕的时候才需要关闭通道。通道是可以被垃圾回收机制回收的，它和关闭文件是不一样的，在结束操作之后关闭文件是必须要做的，但关闭通道不是必须的。

关闭后的通道有以下特点：

1. 对一个关闭的通道再发送值就会导致panic。
2. 对一个关闭的通道进行接收会一直获取值直到通道为空。
3. 对一个关闭的并且没有值的通道执行接收操作会得到对应类型的零值。
4. 关闭一个已经关闭的通道会导致panic。

### 无缓冲的通道
![](go基础/无缓冲的通道.png)

无缓冲的通道又称为阻塞的通道。我们来看一下下面的代码：
```
func main() {
    ch := make(chan int)
    ch <- 10
    fmt.Println("发送成功")
}
```
上面这段代码能够通过编译，但是执行的时候会出现以下错误：
```
    fatal error: all goroutines are asleep - deadlock!

    goroutine 1 [chan send]:
    main.main()
            .../src/github.com/pprof/studygo/day06/channel02/main.go:8 +0x54
```

为什么会出现deadlock错误呢？

因为我们使用ch := make(chan int)创建的是无缓冲的通道，无缓冲的通道只有在有人接收值的时候才能发送值。就像你住的小区没有快递柜和代收点，快递员给你打电话必须要把这个物品送到你的手中，简单来说就是无缓冲的通道必须有接收才能发送。
上面的代码会阻塞在ch <- 10这一行代码形成死锁，那如何解决这个问题呢？一种方法是启用一个goroutine去接收值，例如：
```
func recv(c chan int) {
    ret := <-c
    fmt.Println("接收成功", ret)
}
    
func main() {
    ch := make(chan int)
    go recv(ch) // 启用goroutine从通道接收值
    ch <- 10
    fmt.Println("发送成功")
}
```

无缓冲通道上的发送操作会阻塞，直到另一个goroutine在该通道上执行接收操作，这时值才能发送成功，两个goroutine将继续执行。相反，如果接收操作先执行，接收方的goroutine将阻塞，直到另一个goroutine在该通道上发送一个值。

使用无缓冲通道进行通信将导致发送和接收的goroutine同步化。因此，无缓冲通道也被称为同步通道。

### 有缓冲的通道
解决上面问题的方法还有一种就是使用有缓冲区的通道。
![](go基础/有缓冲的通道.png)

我们可以在使用make函数初始化通道的时候为其指定通道的容量，例如：
```
func main() {
    ch := make(chan int, 1) // 创建一个容量为1的有缓冲区通道
    ch <- 10
    fmt.Println("发送成功")
}
```

只要通道的容量大于零，那么该通道就是有缓冲的通道，通道的容量表示通道中能存放元素的数量。就像你小区的快递柜只有那么个多格子，格子满了就装不下了，就阻塞了，等到别人取走一个快递员就能往里面放一个。
我们可以使用内置的len函数获取通道内元素的数量，使用cap函数获取通道的容量，虽然我们很少会这么做。

### close()

可以通过内置的close()函数关闭channel（如果你的管道不往里存值或者取值的时候一定记得关闭管道）
```
package main

import "fmt"

func main() {
    c := make(chan int)
    go func() {
        for i := 0; i < 5; i++ {
            c <- i
        }
        close(c)
    }()
    for {
        if data, ok := <-c; ok {
            fmt.Println(data)
        } else {
            break
        }
    }
    fmt.Println("main结束")
}
```
### 如何优雅的从通道循环取值

当通过通道发送有限的数据时，我们可以通过close函数关闭通道来告知从该通道接收值的goroutine停止等待。当通道被关闭时，往该通道发送值会引发panic，从该通道里接收的值一直都是类型零值。那如何判断一个通道是否被关闭了呢？我们来看下面这个例子：
```
// channel 练习
func main() {
    ch1 := make(chan int)
    ch2 := make(chan int)
    // 开启goroutine将0~100的数发送到ch1中
    go func() {
        for i := 0; i < 100; i++ {
            ch1 <- i
        }
        close(ch1)
    }()
    // 开启goroutine从ch1中接收值，并将该值的平方发送到ch2中
    go func() {
        for {
            i, ok := <-ch1 // 通道关闭后再取值ok=false
            if !ok {
                break
            }
            ch2 <- i * i
        }
        close(ch2)
    }()
    // 在主goroutine中从ch2中接收值打印
    for i := range ch2 { // 通道关闭后会退出for range循环
        fmt.Println(i)
    }
}
```
从上面的例子中我们看到有两种方式在接收值的时候判断通道是否被关闭，我们通常使用的是for range的方式。

### 单向通道

有的时候我们会将通道作为参数在多个任务函数间传递，很多时候我们在不同的任务函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接收。
Go语言中提供了单向通道来处理这种情况。例如，我们把上面的例子改造如下：
```
func counter(out chan<- int) {
    for i := 0; i < 100; i++ {
        out <- i
    }
    close(out)
}

func squarer(out chan<- int, in <-chan int) {
    for i := range in {
        out <- i * i
    }
    close(out)}func printer(in <-chan int) {
    for i := range in {
        fmt.Println(i)
    }
}

func main() {
    ch1 := make(chan int)
    ch2 := make(chan int)
    go counter(ch1)
    go squarer(ch2, ch1)
    printer(ch2)
}
```
其中，
```
    1.chan<- int是一个只能发送的通道，可以发送但是不能接收；
    2.<-chan int是一个只能接收的通道，可以接收但是不能发送。
```

在函数传参及任何赋值操作中将双向通道转换为单向通道是可以的，但反过来是不可以的。

### 通道总结

channel常见的异常总结，如下图：
![](go基础/通道总结.png)
注意:关闭已经关闭的channel也会引发panic。

## Goroutine池

### worker pool（goroutine池）

* 本质上是生产者消费者模型
* 可以有效控制goroutine数量，防止暴涨

需求：

* 计算一个数字的各个位数之和，例如数字123，结果为1+2+3=6
* 随机生成数字进行计算

```
package main

import (
    "fmt"
    "math/rand")

type Job struct {
    // id
    Id int
    // 需要计算的随机数
    RandNum int
}

type Result struct {
    // 这里必须传对象实例
    job *Job
    // 求和
    sum int
}

func main() {
    // 需要2个管道
    // 1.job管道
    jobChan := make(chan *Job, 128)
    // 2.结果管道
    resultChan := make(chan *Result, 128)
    // 3.创建工作池
    createPool(64, jobChan, resultChan)
    // 4.开个打印的协程
    go func(resultChan chan *Result) {
        // 遍历结果管道打印
        for result := range resultChan {
            fmt.Printf("job id:%v randnum:%v result:%d\n", result.job.Id,
                result.job.RandNum, result.sum)
        }
    }(resultChan)
    var id int
    // 循环创建job，输入到管道
    for {
        id++
        // 生成随机数
        r_num := rand.Int()
        job := &Job{
            Id:      id,
            RandNum: r_num,
        }
        jobChan <- job
    }}

// 创建工作池// 参数1：开几个协程func createPool(num int, jobChan chan *Job, resultChan chan *Result) {
    // 根据开协程个数，去跑运行
    for i := 0; i < num; i++ {
        go func(jobChan chan *Job, resultChan chan *Result) {
            // 执行运算
            // 遍历job管道所有数据，进行相加
            for job := range jobChan {
                // 随机数接过来
                r_num := job.RandNum
                // 随机数每一位相加
                // 定义返回值
                var sum int
                for r_num != 0 {
                    tmp := r_num % 10
                    sum += tmp
                    r_num /= 10
                }
                // 想要的结果是Result
                r := &Result{
                    job: job,
                    sum: sum,
                }
                //运算结果扔到管道
                resultChan <- r
            }
        }(jobChan, resultChan)
    }
}
```

## 定时器

Timer：时间到了，执行只执行1次
```
package main

import (
    "fmt"
    "time")

func main() {
    // 1.timer基本使用
    //timer1 := time.NewTimer(2 * time.Second)
    //t1 := time.Now()
    //fmt.Printf("t1:%v\n", t1)
    //t2 := <-timer1.C
    //fmt.Printf("t2:%v\n", t2)

    // 2.验证timer只能响应1次
    //timer2 := time.NewTimer(time.Second)
    //for {
    // <-timer2.C
    // fmt.Println("时间到")
    //}

    // 3.timer实现延时的功能
    //(1)
    //time.Sleep(time.Second)
    //(2)
    //timer3 := time.NewTimer(2 * time.Second)
    //<-timer3.C
    //fmt.Println("2秒到")
    //(3)
    //<-time.After(2*time.Second)
    //fmt.Println("2秒到")

    // 4.停止定时器
    //timer4 := time.NewTimer(2 * time.Second)
    //go func() {
    // <-timer4.C
    // fmt.Println("定时器执行了")
    //}()
    //b := timer4.Stop()
    //if b {
    // fmt.Println("timer4已经关闭")
    //}

    // 5.重置定时器
    timer5 := time.NewTimer(3 * time.Second)
    timer5.Reset(1 * time.Second)
    fmt.Println(time.Now())
    fmt.Println(<-timer5.C)

    for {
    }
}
```

Ticker：时间到了，多次执行
```
package main

import (
    "fmt"
    "time")

func main() {
    // 1.获取ticker对象
    ticker := time.NewTicker(1 * time.Second)
    i := 0
    // 子协程
    go func() {
        for {
            //<-ticker.C
            i++
            fmt.Println(<-ticker.C)
            if i == 5 {
                //停止
                ticker.Stop()
            }
        }
    }()
    for {
    }
}
```

## select

### select多路复用

在某些场景下我们需要同时从多个通道接收数据。通道在接收数据时，如果没有数据可以接收将会发生阻塞。你也许会写出如下代码使用遍历的方式来实现：
```
for{
    // 尝试从ch1接收值
    data, ok := <-ch1
    // 尝试从ch2接收值
    data, ok := <-ch2
    …
}
```

这种方式虽然可以实现从多个通道接收值的需求，但是运行性能会差很多。为了应对这种场景，Go内置了select关键字，可以同时响应多个通道的操作。

select的使用类似于switch语句，它有一系列case分支和一个默认的分支。每个case会对应一个通道的通信（接收或发送）过程。select会一直等待，直到某个case的通信操作完成时，就会执行case分支对应的语句。具体格式如下：
```
    select {
    case <-chan1:
       // 如果chan1成功读到数据，则进行该case处理语句
    case chan2 <- 1:
       // 如果成功向chan2写入数据，则进行该case处理语句
    default:
       // 如果上面都没有成功，则进入default处理流程
    }
```

select可以同时监听一个或多个channel，直到其中一个channel ready
```
package main

import (
   "fmt"
   "time")

func test1(ch chan string) {
   time.Sleep(time.Second * 5)
   ch <- "test1"}func test2(ch chan string) {
   time.Sleep(time.Second * 2)
   ch <- "test2"
}

func main() {
   // 2个管道
   output1 := make(chan string)
   output2 := make(chan string)
   // 跑2个子协程，写数据
   go test1(output1)
   go test2(output2)
   // 用select监控
   select {
   case s1 := <-output1:
      fmt.Println("s1=", s1)
   case s2 := <-output2:
      fmt.Println("s2=", s2)
   }
}
```

如果多个channel同时ready，则随机选择一个执行
```
package main

import (
   "fmt")

func main() {
   // 创建2个管道
   int_chan := make(chan int, 1)
   string_chan := make(chan string, 1)
   go func() {
      //time.Sleep(2 * time.Second)
      int_chan <- 1
   }()
   go func() {
      string_chan <- "hello"
   }()
   select {
   case value := <-int_chan:
      fmt.Println("int:", value)
   case value := <-string_chan:
      fmt.Println("string:", value)
   }
   fmt.Println("main结束")
}
```
可以用于判断管道是否存满
```
package main

import (
   "fmt"
   "time")

// 判断管道有没有存满
func main() {
   // 创建管道
   output1 := make(chan string, 10)
   // 子协程写数据
   go write(output1)
   // 取数据
   for s := range output1 {
      fmt.Println("res:", s)
      time.Sleep(time.Second)
   }
}

func write(ch chan string) {
   for {
      select {
      // 写数据
      case ch <- "hello":
         fmt.Println("write hello")
      default:
         fmt.Println("channel full")
      }
      time.Sleep(time.Millisecond * 500)
   }
}
```

## 并发安全和锁

有时候在Go代码中可能会存在多个goroutine同时操作一个资源（临界区），这种情况会发生竞态问题（数据竞态）。类比现实生活中的例子有十字路口被各个方向的的汽车竞争；还有火车上的卫生间被车厢里的人竞争。
```
var x int64var wg sync.WaitGroup

func add() {
    for i := 0; i < 5000; i++ {
        x = x + 1
    }
    wg.Done()
}
    
func main() {
    wg.Add(2)
    go add()
    go add()
    wg.Wait()
    fmt.Println(x)
}
```

上面的代码中我们开启了两个goroutine去累加变量x的值，这两个goroutine在访问和修改x变量的时候就会存在数据竞争，导致最后的结果与期待的不符。

### 互斥锁

互斥锁是一种常用的控制共享资源访问的方法，它能够保证同时只有一个goroutine可以访问共享资源。Go语言中使用sync包的Mutex类型来实现互斥锁。 使用互斥锁来修复上面代码的问题：
```
var x int64var wg sync.WaitGroup
var lock sync.Mutex

func add() {
    for i := 0; i < 5000; i++ {
        lock.Lock() // 加锁
        x = x + 1
        lock.Unlock() // 解锁
    }
    wg.Done()}func main() {
    wg.Add(2)
    go add()
    go add()
    wg.Wait()
    fmt.Println(x)
}
```

使用互斥锁能够保证同一时间有且只有一个goroutine进入临界区，其他的goroutine则在等待锁；当互斥锁释放后，等待的goroutine才可以获取锁进入临界区，多个goroutine同时等待一个锁时，唤醒的策略是随机的。

### 读写互斥锁

互斥锁是完全互斥的，但是有很多实际的场景下是读多写少的，当我们并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用读写锁是更好的一种选择。读写锁在Go语言中使用sync包中的RWMutex类型。

读写锁分为两种：读锁和写锁。当一个goroutine获取读锁之后，其他的goroutine如果是获取读锁会继续获得锁，如果是获取写锁就会等待；当一个goroutine获取写锁之后，其他的goroutine无论是获取读锁还是写锁都会等待。
```
var (
    x      int64
    wg     sync.WaitGroup
    lock   sync.Mutex
    rwlock sync.RWMutex
)

func write() {
    // lock.Lock()   // 加互斥锁
    rwlock.Lock() // 加写锁
    x = x + 1
    time.Sleep(10 * time.Millisecond) // 假设读操作耗时10毫秒
    rwlock.Unlock()                   // 解写锁
    // lock.Unlock()                     // 解互斥锁
    wg.Done()
}

func read() {
    // lock.Lock()                  // 加互斥锁
    rwlock.RLock()               // 加读锁
    time.Sleep(time.Millisecond) // 假设读操作耗时1毫秒
    rwlock.RUnlock()             // 解读锁
    // lock.Unlock()                // 解互斥锁
    wg.Done()
}

func main() {
    start := time.Now()
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go write()
    }

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go read()
    }

    wg.Wait()
    end := time.Now()
    fmt.Println(end.Sub(start))
}
```

需要注意的是读写锁非常适合读多写少的场景，如果读和写的操作差别不大，读写锁的优势就发挥不出来。

## Sync

### sync.WaitGroup

在代码中生硬的使用time.Sleep肯定是不合适的，Go语言中可以使用sync.WaitGroup来实现并发任务的同步。 sync.WaitGroup有以下几个方法：

| 方法名                          | 功能                |
| ------------------------------- | ------------------- |
| (wg * WaitGroup) Add(delta int) | 计数器+delta        |
| (wg * WaitGroup) Done()         | 计数器-1            |
| (wg * WaitGroup) Wait()         | 阻塞直到计数器变为0 |

sync.WaitGroup内部维护着一个计数器，计数器的值可以增加和减少。例如当我们启动了N 个并发任务时，就将计数器值增加N。每个任务完成时通过调用Done()方法将计数器减1。通过调用Wait()来等待并发任务执行完，当计数器值为0时，表示所有并发任务已经完成。
```
var wg sync.WaitGroup

func hello() {
    defer wg.Done()
    fmt.Println("Hello Goroutine!")}func main() {
    wg.Add(1)
    go hello() // 启动另外一个goroutine去执行hello函数
    fmt.Println("main goroutine done!")
    wg.Wait()}
```

需要注意sync.WaitGroup是一个结构体，传递的时候要传递指针。

### sync.Once

说在前面的话：这是一个进阶知识点。在编程的很多场景下我们需要确保某些操作在高并发的场景下只执行一次，例如只加载一次配置文件、只关闭一次通道等。
Go语言中的sync包中提供了一个针对只执行一次场景的解决方案–sync.Once。sync.Once只有一个Do方法，其签名如下：
```
func (o *Once) Do(f func()) {}
```
注意：如果要执行的函数f需要传递参数就需要搭配闭包来使用。

#### 加载配置文件示例

延迟一个开销很大的初始化操作到真正用到它的时候再执行是一个很好的实践。因为预先初始化一个变量（比如在init函数中完成初始化）会增加程序的启动耗时，而且有可能实际执行过程中这个变量没有用上，那么这个初始化操作就不是必须要做的。我们来看一个例子：
```
var icons map[string]image.Image

func loadIcons() {
    icons = map[string]image.Image{
        "left":  loadIcon("left.png"),
        "up":    loadIcon("up.png"),
        "right": loadIcon("right.png"),
        "down":  loadIcon("down.png"),
    }
}

// Icon 被多个goroutine调用时不是并发安全的func Icon(name string) image.Image {
    if icons == nil {
        loadIcons()
    }
    return icons[name]
}
```

多个goroutine并发调用Icon函数时不是并发安全的，现代的编译器和CPU可能会在保证每个goroutine都满足串行一致的基础上自由地重排访问内存的顺序。loadIcons函数可能会被重排为以下结果：
```
func loadIcons() {
    icons = make(map[string]image.Image)
    icons["left"] = loadIcon("left.png")
    icons["up"] = loadIcon("up.png")
    icons["right"] = loadIcon("right.png")
    icons["down"] = loadIcon("down.png")
}
```

在这种情况下就会出现即使判断了icons不是nil也不意味着变量初始化完成了。考虑到这种情况，我们能想到的办法就是添加互斥锁，保证初始化icons的时候不会被其他的goroutine操作，但是这样做又会引发性能问题。
使用sync.Once改造的示例代码如下：
```
var icons map[string]image.Image

var loadIconsOnce sync.Once

func loadIcons() {
    icons = map[string]image.Image{
        "left":  loadIcon("left.png"),
        "up":    loadIcon("up.png"),
        "right": loadIcon("right.png"),
        "down":  loadIcon("down.png"),
    }}

// Icon 是并发安全的
func Icon(name string) image.Image {
    loadIconsOnce.Do(loadIcons)
    return icons[name]
}
```
sync.Once其实内部包含一个互斥锁和一个布尔值，互斥锁保证布尔值和数据的安全，而布尔值用来记录初始化是否完成。这样设计就能保证初始化操作的时候是并发安全的并且初始化操作也不会被执行多次。

### sync.Map

Go语言中内置的map不是并发安全的。请看下面的示例：
```
var m = make(map[string]int)

func get(key string) int {
    return m[key]
}

func set(key string, value int) {
    m[key] = value
}

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 20; i++ {
        wg.Add(1)
        go func(n int) {
            key := strconv.Itoa(n)
            set(key, n)
            fmt.Printf("k=:%v,v:=%v\n", key, get(key))
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```

上面的代码开启少量几个goroutine的时候可能没什么问题，当并发多了之后执行上面的代码就会报fatal error: concurrent map writes错误。
像这种场景下就需要为map加锁来保证并发的安全性了，Go语言的sync包中提供了一个开箱即用的并发安全版map–sync.Map。开箱即用表示不用像内置的map一样使用make函数初始化就能直接使用。同时sync.Map内置了诸如Store、Load、LoadOrStore、Delete、Range等操作方法。
```
var m = sync.Map{}

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 20; i++ {
        wg.Add(1)
        go func(n int) {
            key := strconv.Itoa(n)
            m.Store(key, n)
            value, _ := m.Load(key)
            fmt.Printf("k=:%v,v:=%v\n", key, value)
            wg.Done()
        }(i)
    }
    wg.Wait()
 }
```
## 原子操作(atomic包)

### 原子操作

代码中的加锁操作因为涉及内核态的上下文切换会比较耗时、代价比较高。针对基本数据类型我们还可以使用原子操作来保证并发安全，因为原子操作是Go语言提供的方法它在用户态就可以完成，因此性能比加锁操作更好。Go语言中原子操作由内置的标准库sync/atomic提供。
