# Golang学习

项目的构建主要是靠GOPATH来实现的。如果想要构建一个项目，就需要将这个项目的目录添加到 GOPATH 中，多个项目之间可以使用`;`分隔. 如果不配置 GOPATH，即使处于同一目录，代码之间也无法通过绝对路径相互调用.

# 工作空间

工作空间一般包含三个目录:

src目录  包含Go的源文件, 他们被组织成包 每个目录都对应一个包

pkg 目录 包含对象. 放置编译后生成的包/库的归档文件；

bin 目录 包含可执行命令. 放置编译后生成的可执行文件。

go工具用于构建源码包, 并将其生成的二进制文件安装到 pkg和bin目录中



#### src目录

用于以包 package 的形式组织并存放Go源文件, 这里的包与src下的每个子目录是一一对应的.  例如, 若一个源文件被声明属于log 包, 那么它就应当保存在 src/log 目录中.并不是说 src 目录下不能存放 Go 源文件，一般在测试或演示的时候也可以把 Go 源文件直接放在 src 目录下，但是这么做的话就只能声明该源文件属于 main 包了.

包是Go语言管理代码的重要机制，其作用类似于[Java](http://c.biancheng.net/java/)中的 package 和 C/[C++](http://c.biancheng.net/cplus/) 的头文件。Go 源文件中第一段有效代码必须是`package <包名> `的形式，如 package hello。

另外需要注意的是，Go语言会把通过`go get `命令获取到的库源文件下载到 src 目录下对应的文件夹当中。

#### pkg 目录

用于存放通过`go install `命令安装某个包后的归档文件。归档文件是指那些名称以“.a”结尾的文件。

#### bin 目录

与 pkg 目录类似，在通过`go install `命令完成安装后，保存由 Go 命令源文件生成的可执行文件

- 命令源文件：如果一个 Go 源文件被声明属于 main 包，并且该文件中包含 main 函数，则它就是命令源码文件。命令源文件属于程序的入口，可以通过Go语言的`go run `命令运行或者通过`go build `命令生成可执行文件。
- 库源文件：库源文件则是指存在于某个包中的普通源文件，并且库源文件中不包含 main 函数。

## 程序基本结构

````go
package main

import (
    "fmt"
)

func main() { // go的括号要求严格，这个左花括号一定要在这个位置
    somthing...
}  // 右花括号也一定要在单独一行
````

## 基本输出

````go
package main

import (
    "fmt"
)

func main() {      
    fmt.Println()  // 输出时后面自动接空格
    fmt.Print()  // 输出后不带空格
    fmt.Printf()  // 格式化输出，第一个参数一定是一个字符串，可以带格式化符号%v。
    
    fmt.Printf("my weight on the surface of %v is %v libs.\n", "Earth", 149.0)
}
````

### 格式化输出

文本对齐

格式化动词里指定宽度，就可以对齐文本：`%4v`，就是向左填充到足够4个宽度。

正数，向左填充空格

负数，向右填充空格

```go
package main

import "fmt"

func main() {
	fmt.Printf("%-15v $%4v\n", "SpaceX", 94)
	fmt.Printf("%-15v $%4v\n", "Virgin Galactic", 100)
}
```

### 格式化符号

使用Print或Println打印浮点类型的时候，默认的行为是尽可能的多显示几位数字。

当想要指定显示多少位的时候，就要使用格式化动词来指定位数了

````
%5.6f  指出至少显示5位（算数字中的点号），小数位为6位。默认位数不足时使用空格填充
%*5.6f 也可以使用*来填充它。
%T  打印数据的类型
%x  打印十六进制数
%b  打印每个bit  字节
%c  输出单个字符
%v  可以输出任何类型，+v 可以显示更多信息吧
````

 

## 常量和变量

`const` 用来声明常量： 常量的值不可以改变.. 

常量是在编译时被创建的，即使定义在函数内部也是如此，并且只能是布尔型、数字型（整数型、浮点型和复数）和字符串型。

`var` 用来声明变量： 想要使用变量首先需要进行声明。 `var name type`

当一个变量被声明之后，系统自动赋予它该类型的零值：int 为 0，float 为 0.0，bool 为 false，string 为空字符串，指针为 nil 等。所有的内存在 Go 中都是经过初始化的。

```go
package main

import "fmt"

func main() {
	const lightSpeed = 100000
	var distance int32 = 19999999
	fmt.Println(distance/lightSpeed, "seconds")

	distance = 4010000
	fmt.Println(distance/lightSpeed, "seconds")
}
```

可以同时声明多个变量

```go
var (
    distance int = 50000
    speed float64 = 999999
)

var distance, speed int = 1000000, 222222
const houseprice, housenumber int = 10000, 678
```

简短格式

`name := 表达式`

简单格式的限制

- 定义变量,同时显式初始化.
- 不能提供数据类型
- 只能在函数内部使用

### 初始化

的[Go语言在声明变量时，自动对变量对应的内存区域进行初始化操作。每个变量会初始化其类型的默认值，例如：

- 整型和浮点型变量的默认值为 0 和 0.0。
- 字符串变量的默认值为空字符串。
- 布尔型变量默认为 bool。
- 切片、函数、指针变量的默认为 nil。

初始化的标准格式

```go
var 变量名  类型 = 表达式
var hp int = 100
```

go的编译器也支持 类型推导.

### 匿名变量

匿名变量的特点是一个下画线“_”，“_”本身就是一个特殊的标识符，被称为空白标识符。它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。

```go
func GetData() (int, int) {
    return 100, 200
}

func main() {
    a, _ = GetData()
    _, b = GetData()
    fmt.Println(a, b)
}
```

### 赋值运算符

````go
package main

func main() {
	var weight = 149.0
	weight = weight * 0.378
	weight *= 0.378  // 简写形式，和其他语言类似
}
````

### 自增运算

有后缀形式的自增或者自减，但是没有前缀形式的

```go
package main

import "fmt"

func main() {
	var age = 41
	age = age + 1
	age += 1
	age++ // golang有 age++ 但是没有++age
	fmt.Println(age)
}

```



### 例子

打印随机数

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
    rand.Seed(10) // 可以设置随即种子
	var num = rand.Intn(10) + 1
	fmt.Println(num)

	num = rand.Intn(10) + 1
	fmt.Println(num)
}
```

作业题

```go
package main

import (
	"fmt"
)

func main() {
	var speed = -1
	const distance = 56000000
	const time = 28
	speed = distance / (time * 24)
	fmt.Printf("飞行速度至少为%v公里/每小时\n", speed)
}
```

### iota 常量生成器

常量声明可以使用 iota 常量生成器初始化，它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式。在一个 const 声明语句中，在第一个声明的常量所在的行，iota 将会被置为 0，然后在每一个有常量声明的行加一。

```go
type Weekday int

const (
	Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

iota 会依次给 他们赋值 0 - 6



## 数据类型

### 整数类型

`int8` `int16` `int32` `int64`

`uint8`、`uint16`、`uint32` 和 `uint64`

`int` 和 `uint`，它们分别对应特定 CPU 平台的字长（机器字大小）

### 浮点类型

`float32`  `float64`

浮点数取值范围的极限值可以在 math 包中找到：

- 常量 `math.MaxFloat32` 表示 `float32` 能取到的最大数值，大约是 `3.4e38`；
- 常量 math.MaxFloat64 表示 `float64` 能取到的最大数值，大约是 `1.8e308`；
- `float32` 和 `float64` 能表示的最小值分别为 `1.4e-45` 和 `4.9e-324`。



### Boolean 类型

其他语言会将，非空值定义为True，空值定义为False。**但是golang不行，只有True为真，False为假。相当于只有True和False才能进行布尔值判断**

#### strings.Contains

sting包的Contains函数可以判断某个字符串是否包含另外要给的字符串。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	fmt.Println("you find yourself in a aimly lit vavern.")
	var command = "walk outside"
	var exit = strings.Contains(command, "outside")  // exit就是布尔类型
	fmt.Println("you leave the cave", exit)
}
```

#### 比较

````
== != <= >= > <
````

### 字符串

UTF-8 是一种被广泛使用的编码格式，是文本文件的标准编码，其中包括 XML 和 JSON 在内也都使用该编码。由于该编码对占用字节长度的不定性，在Go语言中字符串也可能根据需要占用 1 至 4 个字节，这与其它编程语言不同. Go语言这样做不仅减少了内存和硬盘空间占用，同时也不用像其它语言那样需要对使用 UTF-8 字符集的文本进行编码和解码。

字符串是一种值类型，且值不可变，即创建某个文本后将无法再次修改这个文本的内容，更深入地讲，字符串是字节的定长数组。

可以使用双引号`""`来定义字符串，字符串中可以使用转义字符来实现换行、缩进等效果.

一般的比较运算符（==、!=、<、<=、>=、>）是通过在内存中按字节比较来实现字符串比较的，因此比较的结果是字符串自然编码的顺序。字符串所占的字节长度可以通过函数 len() 来获取，例如` len(str)`。字符串的内容（纯字节）可以通过标准索引法来获取，在方括号`[ ]`内写入索引，索引从 0 开始计数：

- 字符串 str 的第 1 个字节：str[0]
- 第 i 个字节：str[i - 1]
- 最后 1 个字节：str[len(str)-1]

需要注意的是，这种转换方案只对纯 ASCII 码的字符串有效。

### 字符串拼接  " + "

两个字符串 s1 和 s2 可以通过 s := s1 + s2 拼接在一起。将 s2 追加到 s1 尾部并生成一个新的字符串 s。

可以通过下面的方式来对代码中多行的字符串进行拼接：

```go
str := "Beginning of the string" +
"second part of the string"
```

> 提示：因为编译器会在行尾自动补全分号，所以拼接字符串用的加号“+”必须放在第一行末尾。

### 定义多行字符串

在Go语言中，使用双引号书写字符串的方式是字符串常见表达方式之一，被称为字符串字面量（string literal），这种双引号字面量不能跨行，如果想要在源码中嵌入一个多行字符串时，就必须使用```反引号，代码如下：

```go
const str = `第一行
第二行
第三行
`
```

转义符会按照原样输出

### Go语言字符类型

Go语言的字符有以下两种：

- 一种是 uint8 类型，或者叫 byte 型，代表了 ASCII 码的一个字符。
- 另一种是 rune 类型，代表一个 UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型。rune 类型等价于 int32 类型。


byte 类型是 uint8 的别名，对于只占用 1 个字节的传统 ASCII 编码的字符来说，完全没有问题，例如 var ch byte = 'A'，字符使用单引号括起来。

Go语言同样支持 Unicode（UTF-8），因此字符同样称为 Unicode 代码点或者 runes，并在内存中使用 int 来表示。在文档中，一般使用格式 U+hhhh 来表示，其中 h 表示一个 16 进制数。

在书写 Unicode 字符时，需要在 16 进制数之前加上前缀`\u`或者`\U`。因为 Unicode 至少占用 2 个字节，所以我们使用 int16 或者 int 类型来表示。如果需要使用到 4 字节，则使用`\u`前缀，如果需要使用到 8 个字节，则使用`\U`前缀。

```go
var ch int = '\u0041'
var ch2 int = '\u03B2'
var ch3 int = '\U00101234'
fmt.Printf("%d - %d - %d\n", ch, ch2, ch3) // integer
fmt.Printf("%c - %c - %c\n", ch, ch2, ch3) // character
fmt.Printf("%X - %X - %X\n", ch, ch2, ch3) // UTF-8 bytes
fmt.Printf("%U - %U - %U", ch, ch2, ch3)   // UTF-8 code point
```

### 指针

指针（pointer）在Go语言中可以被拆分为两个核心概念：

- 类型指针，允许对这个指针类型的数据进行修改，传递数据可以直接使用指针，而无须拷贝数据，类型指针不能进行偏移和运算。
- 切片，由指向起始元素的原始指针、元素数量和容量组成。

受益于这样的约束和拆分，Go语言的指针类型变量即拥有指针高效访问的特点，又不会发生指针偏移，从而避免了非法修改关键性数据的问题。同时，垃圾回收也比较容易对不会发生偏移的指针进行检索和回收。

切片比原始指针具备更强大的特性，而且更为安全。切片在发生越界时，运行时会报出宕机，并打出堆栈，而原始指针只会崩溃。

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用在变量名前面添加`&`操作符（前缀）来获取变量的内存地址（取地址操作），格式如下：

```go
ptr := &v // V的类型为T
```

其中 v 代表被取地址的变量，变量 v 的地址使用变量 ptr 进行接收，ptr 的类型为`*T`，称做 T 的指针类型，`*`代表指针。

```go
package main

import (
	"fmt"
)
func main() {
    var cat int = 1
    var str string = "banana"
    fmt.Printf("%p %p", &cat, &str)
}
```

当使用`&`操作符对普通变量进行**取地址操作**并得到**变量的指针**后，可以对指针使用`*`操作符，也就是指针取值，

```go
package main

import (
	"fmt"
)

func main() {
    // 准备一个字符串类型
	var house = "Malibu Point 10880, 90265"

    // 对字符串取地址, ptr类型为*string
	ptr := &house
    // 打印ptr的类型
	fmt.Printf("ptr type: %T\n", ptr)
    // 打印ptr的指针地址
	fmt.Printf("address: %p\n", ptr)
    // 对指针进行取值操作
	value := *ptr
    // 取值后的类型
	fmt.Printf("value type: %T\n", value)
    // 指针取值后就是指向变量的值
	fmt.Printf("value: %s\n", value)
}
```

变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

- 对变量进行取地址操作使用`&`操作符，可以获得这个变量的指针变量。
- 指针变量的值是指针地址。
- 对指针变量进行取值操作使用`*`操作符，可以获得指针变量指向的原变量的值。

#### 例子

获取命令行参数

```go
package main

import (
	"flag"
	"fmt"
)

// 定义命令行参数
// 返回一个字符串指针, 第一个参数: 参数名称：在命令行输入参数时，使用这个名称。 第二个参数: 默认值. 第三个参数: 使用help的提示
var mode = flag.String("mode", "", "process mode")

func main() {
	flag.Parse()
	fmt.Println(*mode)
}
```

执行命令 `go run main.go --mode=fase`

#### 使用new()创建指针

```go
new(类型)
```

```go
str := new(string)
*str = "Go语言"

fmt.Println(*str)
```

### 类型别名

类型别名的写法,

```go
type TypeAlias = Type
```

类型别名规定：TypeAlias 只是 Type 的别名，本质上 TypeAlias 与 Type 是同一个类型，

类型别名与类型定义表面上看只有一个等号的差异，那么它们之间实际的区别有哪些呢？下面通过一段代码来理解。

```go
package main

import (
	"fmt"
)
// 将NewInt 定义为int类型,  是一个新类型
type NewInt int
// 将int取一个别名叫 IntAlias
type IntAlias = int

func main() {
    // 将a声明为NewInt类型
	var a NewInt
    // 查看a的类型名
	fmt.Printf("a type:%T\n", a)
    // 将a2 声明为IntAlias 类型
	var a2 IntAlias
    // 查看a2的类型名
	fmt.Printf("a2 type: %T\n", a2)
}
```



## 变量的作用域

变量的作用域和python的作用域区别不大。但是需要的注意的是，在{}之间的作用域，在{}之间声明的变量，作用域就在{}之间。

go语言有一个短声明，`:=`。这个和`python3.8`新加入的符号一样，作用功能也一样，同样是变量声明加赋值，也就是在`if` 和`for`这些不便于使用`var`关键字地方使用，but，go语言中`：=`在其他地方也能够使用，只是不能在全局作用域下使用（暂且这么写）

```go
var count = 0
for count = 10; count > 0; count-- {
    fmt.Println(count)
}
fmt.Println(count)
```

for

```go
for count:= 10; count>0; count-- {
    fmt.Println(count)
}
```

 if

```go
if num := rand.Intn(3); num == 0 {
    fmt.Println("Space Adventures")
} else if num == 1 {
    fmt.Println("SpaceX")
} else {
    fmt.Println("Virgin Galactic")
}
```

switch

````go
switch num := rand.Intn(10); num {
    case 0:
    	fmt.Println("some")
    case 1:
    	fmt.Println("some")
    case 2:
    	fmt.Println("some")
    default:
    	fmt.Println("some")
}
````

展示随机日期的课后作业

```go
package main

import (
	"fmt"
	"math/rand"
)

var era = "AD"

func main() {
    var year = rand.Intn(3000) + 1
	var month = rand.Intn(12) + 1
	var daysInMonth = 31
	month = 2

	switch month {
	case 2:
		daysInMonth = 28
	case 4, 6, 9, 11:
		daysInMonth = 30
	}

	if (month == 2) && (year%400 == 0 || (year%4 == 0 && year%100 != 0)) {
		daysInMonth = 29
	}

	var day = rand.Intn(daysInMonth) + 1
	fmt.Println(era, year, month, day)
}

```

课后作业

```go
package main

import (
	"fmt"
	"math/rand"
	"strings"
)

var era = "AD"

func main() {
	const distance = 62100000
	var spaceline string
	var days = 0
	var types string
	var price = 0

	var format_str = "%-18v %5v %-10v $%3v\n"

	fmt.Printf(format_str, "Spaceline", "Days", "Trip type", "Price")
	fmt.Println(strings.Repeat("=", 40))
	for count := 0; count < 10; count++ {
		switch rand.Intn(3) {
		case 0:
			{
				spaceline = "Space Adventures"
			}
		case 1:
			{
				spaceline = "SpaceX"
			}
		case 2:
			{
				spaceline = "Virgin Galactic"
			}
		}

		days = (distance / (rand.Intn(14) + 16)) / (3600 * 24)
		price = rand.Intn(50)

		switch rand.Intn(2) {
		case 0:
			{
				types = "Round-way"
				days *= 2
				price *= 2
			}
		case 1:
			{
				types = "One-way"
			}
		}
		fmt.Printf(format_str, spaceline, days, types, price)

	}

}
```



### 声明浮点型变量

```go
some := 3.5
var some = 3.5
var some float64 = 3.5 // 双精度

var some float32 = 3.6 // 单精度
```

浮点类型默认使用的是 float64，除非指定使用float32。

### 零值

go里面每个类型在声明后没有初始化，那么它其中存储的就是零值

````go
var price float64
````



## go语言的整数类型

```go
int  // 与硬件架构有关
uint // 与硬件架构有关
int8
uint8
int16
uint16
int32
uint32
int64
uint64
```

与C相似，go中也提供了整数最大值和最小值的常量值

```go
math.MaxInt16
math.MaxInt64
```

#### 课后作业

```go
package main
import (
	"fmt"
	"math/rand"
)

func main() {
	var coin uint8 = 0
	var sum uint32 = 0
	var temp uint32 = 0

	for {
		switch rand.Intn(3) {
		case 0:
			coin = 5
		case 1:
			coin = 10
		case 2:
			coin = 25
		}
		sum += uint32(coin)
		temp = sum / 100
		fmt.Printf("余额:$%v.%v\n", temp, sum%100)
		if temp >= 20 {
			break
		}
	}
}

```



### 处理很大的数据

处理那些常规类型处理不料的数据。

big包

对于较大的整数（超过10^18) 使用big.Int

对于任意精度的浮点类型使用 big.Float

对于分数 big.Rat

```go
package main
import (
	"fmt"
	"math/big"
)

func main() {
	var lightsped = big.NewInt(299794)  // 创建了一个 *big.Int 的指针
	var secondsperday = big.NewInt(86400)
	fmt.Println(lightsped, secondsperday)
}
```

当使用超过int范围的数时，采用如下方法

```go
var distance = new(big.Int)
distance.SetString("24000000000000000000000", 10)  // 使用数值形式的字符串存储数据
```



````go
package main

import (
	"fmt"
	"math/big"
)

func main() {
	var lightsped = big.NewInt(299794)
	var secondsperday = big.NewInt(86400)

	var distance = new(big.Int)
	distance.SetString("24000000000000000000000", 10)
	fmt.Println("Andromeda Galaxy is", distance, "km away")

	var seconds = new(big.Int)
	seconds.Div(distance, lightsped)

	var days = new(big.Int)
	days.Div(seconds, secondsperday)

	fmt.Println("That is", days, "days of travel at light speed")
}
````



在go中常量是可以没有类型的

```go
const some = 2400000000000000 // 这里的常量是没有类型的，这里是untyped
```

常量使用const关键字来声明，程序里的**每个字面值都是常量**

这意味着，比较大的数值可以作为字面值 直接使用。

````go
fmt.Println("Andromeda Galaxy is", 240000000000000/299792/86400, "light days away")
````

**字面值和常量是在编译阶段完成的。**



### 多语言文本

字符串声明

```go
peace := "peace"
var peace = "peace"
var peace string = "peace"
```

使用``表示原始字符串。

```go
`somthing here \n someother here`  // 其中包含的是什么就输出什么
"somthing here \n someother here"
```



code points， runes，bytes

unicode联盟为每个字符分配的对应的数值，这个数值就是code point

为了表示这样的unicode code point，go语言提供了rune这个类型，它是int32的一个类型的别名。

byte是uint8类型的别名，目的是用于二进制数



类型别名

类型别名就是同一个类型的另一个名字，比如 rune和int32可以互换

定义类型别名

```go
type byte = uint8
type rune = int32
```

字符

````go
grade := 'A'
var grade1 = 'A'
var grade2 rune = 'A'  // 使用rune就是特地指明了这是一个字符类型
````

string

这和python中的字符串相似。字符串本身是不能修改的。

```go
func main() {
    message := "shalom"
    c := message[5]
    fmt.Printf("%c\n", c)
}
```



**range 关键字**

range可以遍历各种集合

```go
package main
import "fmt"

func main() {
    question := "shalom"
    for i, c := range question {
        fmt.Printf("%v %c\n", i, c)
    }
} 
```





### 类型不能混用

go语言中不同类型的值不能够直接一起使用，需要在使用之前将他们转换为相同类型的数据。

```go
age := 41
marsday := 687
earthday := 36.55
fmt.Println("i am", age*earthday/marsday, "years old on mars")  // 这里整型和浮点型也不能混合使用

fmt.Println("i am", float64(age)*earthday/float64(marsday), "years old on mars")
```

字符串转换

将rune、byte转化位string

```go
var pi rune = 960
var alpha rune = 940
var omega rune = 969
var bang byte = 33

fmt.Print(string(pi), string(alpha), string(omega), string(bang))
```

想把数值转换为string，它的值必须能够转化为从code  point，也就是指定的那个数值必须是能够在编码表中有的，没有的数是不能转为字符的。

将数字变为字符串10--》“10”。`strconv`包可以进行这个处理

```go
str := "launch in T minus" + strconv.Itoa(10) + "seconds."  // Itoa (int to ascii)
fmt.Println(str)
```

使用`Sprintf`将数值转化为string，和`Printf`类似，但是会返回一个string

```go
countdown := 10
str := fmt.Sprintf("Launch in T minus %v seconds.", countdown)
fmt.Println(str)
```

在使用`strconv.Atoi`中可能会出现错误，这个函数在返回时，是会将错误信息返回的，

```go
countdown, err := strconv.Atoi("10")
if err != nil { // nil 和None等价
    fmt.Println(err.Error())
}
```

注意：不能使用类型转换将 True False 转换位其他类型。同样也不能用其他类型转换到布尔类型

```
string(false) 
bool(1)
bool("yes")   这些都是不对的
```

## 数组

数组是一种**固定长度**的有序的元素集合

```go
var 数组变量名 [元素数量] Type
var planets [8]string // 声明了一个长度为8的字符串数组
```

- 数组变量名：数组声明及使用时的变量名。
- 元素数量：数组的元素数量，可以是一个表达式，但最终通过编译期计算的结果必须是整型数值，元素数量不能含有到运行时才能确认大小的数值。
- Type：可以是任意基本类型，包括数组本身，类型为数组本身时，可以实现多维数组。

### 数组的初始化

```go
var balance = [5]float64{1.0, 2.0, 3.0, 4.0, 5.0}
balance := [5]float64{1.0, 2.0, 3.0, 4.0, 5.0}

ar balance = [...]float64{1.0, 2.0, 3.0, 4.0, 5.0} // 自己计算长度
```



### 访问数组元素

通过[]来进行访问

```go
package main

import (
	"fmt"
)

func main() {
	var planets [5]string
	planets[0] = "some1"
	planets[1] = "some2"
	planets[2] = "some3"

	fmt.Println(planets[0])
    
    fmt.Println(len(planets))
    fmt.Println(planets[3] == "") // 在初始化时，会给所有的元素赋零值，在字符串中，就是空字符串了。所以这里和空字符串比较返回的就是True了
}
```

### 访问越界

go在编译阶段会检查是不是有数组越界的情况，但是这仅限于明确的会发生越界的情况，如果使用变量作为引用发生的越界是检查不出来的。

```go
func main() {
	var planets [5]string
	planets[0] = "some1"
	planets[1] = "some2"
	planets[2] = "some3"

	fmt.Println(planets[10])
    
    var some_index = 10
    fmt.Println(planets[some_index])

}
```

### 使用复合字面值初始化数组

复合字面值 是一种给复合类型初始化的紧凑语法。

go的复合字面值语法允许我们只用一步就完成数组声明和数组初始化两个步骤

```go
dwarfs := [5]string{"Ceres", "Pluto", "Haumea", "Makemake", "Eris"}
```

可以在复合字面值里使用`...`作为数组的长度，这样go编译器会为你算出数组的元素数量。

```go
dwarfs := [...]string{"Ceres", "Pluto", "Haumea", "Makemake", "Eris"}
```

无论何种方式声明，数组的长度都是不可变的。



### 遍历数组

使用for循环

```go
func main() {
	dwarfs := [...]string{"Ceres", "Pluto", "Haumea", "Makemake", "Eris"}
	for i := 0; i < len(dwarfs); i++ {
		dwarf := dwarfs[i]
		fmt.Println(i, dwarf)
	}
}
```

使用range

```go
func main() {
	dwarfs := [...]string{"Ceres", "Pluto", "Haumea", "Makemake", "Eris"}
    for i, dwarf := range dwarfs {
        fmt.Println(i, dwarf)
    }
}
```



### 数组的复制

无论是将数组赋值给新的变量还是将它传递给函数，都会产生一个完整的数组副本。相当于深拷贝，C语言中一般是传递指针进行操作的。

```go
func terraform(planets [8]string) {
	for i := range planets {
		planets[i] = "New" + planets[i]  // 这里说明，函数中获得的是一个深拷贝，因为并没有修改原始的数据
	}
}

func main() {
	planets := [...]string{
		"some1",
		"some2",
		"some3",
		"some4",
		"some5",
		"some6",
		"some7",
		"some8",
	}

	terraform(planets)  // planets 要和terraform中声明的数组长度相同，否则会报错
	fmt.Println(planets)
}

```

数组的长度也是数组类型的一部分，尝试将长度不符的数组作为参数传递，就会报错

函数一般使用slice而不是数组作为参数。



### 多维数组

```go
func main() {
	var board [9][9]string  // 声明二维数组

	board[0][0] = "t"
	board[0][8] = "r" // 赋值

	for column := range board[1] {
		board[1][column] = "p"
	}
	fmt.Println(board)
}
```



### slice切片

假设planets是一个数组，那么planets[0:4]就是一个切片，它切分出了数组里前4个元素, 所以切片是一个引用类型

切分数组不会导致数组被修改，它只是创建了指向数组的一个窗口或视图，这种视图就是`slice`类型

和其他语言一样go的切片 也是左闭右开区间。

````go
func main() {
	planets := [...]string{
		"some1",
		"some2",
		"some3",
		"some4",
		"some5",
		"some6",
		"some7",
		"some8",
	}
    some1 := planets[0:4]
	fmt.Println(some1)
}
````

**索引不能是负数**

切分数组的语法也可以用于切分字符串，切分字符串时，索引代表的是字节数而非rune的数字。有些符号可能是两个字节表示一个字符，所以切分这些字符时要格外小心



### slice的复合字面值

go里面很多函数都倾向于使用slice而不是数组作为参数。想要获得与地层数组相同的元素的slice，那么可以使用[:]进行切分

直接创建数组切片

```go
var name []Type // 括号里没有长度
```

```go
func main() {
    dwarfArray := [...]string {
        "some1","some2","some3","some4","some5",
    }
    
    dwarfSlice := dwarfArray[:]
    dwarfs := []string {
        "some1","some2","some3","some4","some5",
    }
    fmt.Println(dwarfSlice, dwarfs)
}
```

数组切片可以不指定数组的长度，它可以接受任意长度的数组

```go
func hyperspace(worlds []string) {  // 不需要指定数组的长度
	for i, j := range worlds {
		worlds[i] = strings.TrimSpace(j)
		fmt.Println(worlds[i])
	}
}

func main() {
	planets := []string{"some  ", "  some2  ", "som3 "}
	hyperspace(planets)
	fmt.Println(strings.Join(planets, ""))
}
```



### 带有方法的切片

````go
type StringSlice []string  // 声明一个slice的类型

func (p StringSlice) Sort() {  // 为类型添加方法

}

func main() {
	planets := []string{
		"some1", "some2", "some3", "some4",
	}

	sort.StringSlice(planets).Sort()  // 在sort中就有StringSlice类型，同样有一个sort方法。StringSlice返回的就是一个StringSlice类型的切片
	fmt.Println(planets)
}
````



### 更大slice

为了获得，一个能不断增长的数组而产生的。

```go
func main() {
	planets := []string{
		"some1", "some2", "some3", "some4",
	}

	planets = append(planets, "some5", "some6")  // append 向数组中添加了2个元素，这些元素是添加到哪里了？
	fmt.Println(planets)
}

```

这里涉及一个新的概念，容量。

长度和容量，分别就表示 实际可以存储多少元素（容量），以及 现在已经存储了多少元素（长度）。

容量 代表切片的底层数组可以容纳数据的量， 长度 是切片的长度。

````go
len(slice)  // 获取长度
cap(slice)  // 获取容量
````



```go
func dump(label string, slice []string) {
	fmt.Printf("%v: length %v, capacity %v %v\n", label, len(slice), cap(slice), slice)

}

func main() {
	planets := []string{
		"some1", "some2", "some3", "some4", "some5",
	}

	dump("planets", planets)
    dump("planets", planets[1:2])  // 这里会显示 容量是4 为什么？？？应该是planets本身是一个切片，再截取[1:2]的部分，相当于第一个元素被舍弃了。切片缩小了。
}

```

当不断向切片中添加值的时候，当超出切片地层数组容量的时候，go会重新生成一个数组（扩大一倍的容量），并将之前的数组值复制到新的数组中。

```go
func dump(label string, slice []string) {
	fmt.Printf("%v: length %v, capacity %v %v\n", label, len(slice), cap(slice), slice)

}

func main() {
	planets := []string{
		"some1", "some2", "some3", "some4", "some5",
	}

	planets2 := append(planets, "some22")
	planets3 := append(planets, "some33", "some33", "some33")

	dump("planets", planets)  // 长度5 容量5
	dump("planets2", planets2) // 长度6 容量10
	dump("planets3", planets3) // 长度8 容量10
    // planets2 和 planets3 是同一个数组，而planets1 是另一个数组了
}
```

`slice[0:4:4]`第三个数字表示新建切片的容量，这里可以限制新建数组的容量大小

```go
func dump(label string, slice []string) {
	fmt.Printf("%v: length %v, capacity %v %v\n", label, len(slice), cap(slice), slice)

}

func main() {
	planets := []string{
		"some1", "some2", "some3", "some4", "some5",
	}

	planets2 := planets[0:4:4] // 这里限制了切片的大小
	planets3 := append(planets2, "some33") // 再向切片添加的时候，因为大小的限制，会新建数组

	dump("planets", planets)
	dump("planets2", planets2)
	dump("planets3", planets3)
}


	planets2 := planets[0:4] // 取消切片大小限制
	planets3 := append(planets2, "some33") // !!!再向切片添加的时候，会修改原来数组的值!!!

	dump("planets", planets)
	dump("planets2", planets2)
	dump("planets3", planets3)
```

什么时候使用三切片的操作？ 在只需要切片范围内的数据时，并且在后续添加数据时，会重新创建数组。

在切片开头添加元素

```go
var a = []int{1,2,3}
a = append([]int{0}, a...)
a = append([]int{-3, -2, -1}, a...)
```

在位置i 添加元素

```go
a = append(a[:i], append([]int{x}, a[i:]...)...)
a = append(a[:i], append([]int{1,2,3}, a[i:]...)...)
```



### 使用make函数对 slice进行预分配

```go
make([]string, 0, 10)  // 第一个参数是切片， 第二个参数是长度， 第三个参数是 容量
						// 如果只有两个参数，第二个参数代表 长度=容量 两个值
```

```go
func dump(label string, slice []int) {
	fmt.Printf("%v: length %v, capacity %v %v\n", label, len(slice), cap(slice), slice)
}

func main() {
	dwarfs := make([]int, 0, 10)
    
	dump("dwarfs", dwarfs)
    
	dwarfs = append(dwarfs, 1, 2, 3, 4, 5)
    
	dump("dwarfs", dwarfs)
}
```

### 从切片中删除元素

Go语言并没有对删除切片元素提供专用的语法或者接口，需要使用切片本身的特性来删除元素，根据要删除元素的位置有三种情况，分别是从开头位置删除、从中间位置删除和从尾部删除，其中删除切片尾部的元素速度最快。

从开头删除

```go
a = []int{1,2,3}
a = a[1:] // 删除开头1个元素
a = a[N:]

a = append(a[:0], a[1:]...)
a = append(a[:0], a[N:]...)

a = a[:copy(a, a[1:])]
a = a[:copy(a, a[N:])]
```

从中间位置删除

```go
a = []int{1,2,3}

a = append(a[:i], a[i+1:]...)
a = append(a[:i], a[i+N:]...)

a = a[:i+copy(a[i:], a[i+1:])]
a = a[:i+copy(a[i:], a[i+N:])]
```

从尾部删除

```go
a = []int{1,2,3}
a = a[:len(a)-1]
a = a[:len(a)-N]
```



### 创建接受可变数量参数的函数

```go
func terraform(prefix string, worlds ...string) []string { // ...作为可变参数收集
	newWorlds := make([]string, len(worlds))
	for i := range worlds {
		newWorlds[i] = prefix + " " + worlds[i] // !!注意!! 对切片的修改，会反映到原始数组上
	}
	return newWorlds
}

func main() {
	twoWorlds := terraform("New", "Venus", "Mars")
	fmt.Println(twoWorlds)

	planets := []string{"Venus", "Mars", "Jupiter"}
	newPlanets := terraform("New", planets...) // 这里的... 类似于python 的序列解包
	fmt.Println(newPlanets)
}
```

`...` 三种用法  

1. 作为函数参数的收集参数
2. 在实参中作为序列解包用
3. 在创建数组时，指定根据初始化值，推断出数组数量



## Map 字典

map是一种哈希类型。类似于python中的字典

### 声明map

```
var mapname map   [string]     int 
           关键字  键的类型   值的类型
```

在声明的时候不需要知道 map 的长度，因为 map 是可以动态增长的，未初始化的 map 的值是 nil，使用函数 len() 可以获取 map 中 pair 的数目。

```go
func main() {
	temperature := map[string]int{
		"Earth": 15,
		"Mars":  -65,
	}  // 声明map

	temp := temperature["Earth"]  // 获取字典的值
	fmt.Printf("On average the Earth is %v C \n", temp)
	temperature["Earth"] = 16
	temperature["Venus"] = 464  // 向字典中加值

	fmt.Println(temperature)

	moon := temperature["Moon"]  // 获取字典中没有的值，返回的应该时int的零值，因为声明时 值为int
	fmt.Println(moon)
    
    if moon, ok := temperature["Moon"]; ok {  // 返回两个值，第一个是返回值，第二个是布尔值，标记是否成功返回
		fmt.Printf("On average the moon is %v C.\n", moon)
	} else {
		fmt.Println("Where is the moon?")
	}
}
```

### map不会被复制

数组、int、float64等类型在赋值给新变量或传递至函数/方法的时候会创建相应的副本。

但是map不会复制。

```go
func main() {
	temperature := map[string]int{
		"Earth": 15,
		"Mars":  -65,
	} // 声明map

	other_some := temperature
	other_some["Earth"] = 1000  // 同时会修改temperature的值

	fmt.Println(temperature)

	delete(temperature, "Earth") // 删除字典中的值
	fmt.Println(temperature)
}
```



### 使用make函数对map进行预分配

1. 除非你使用复合字面值来初始化map，否则必须使用内置的make函数来为map分配空间。(突然好像明白，有初始化时，相当于为数据分配了空间，而没有初始化值时，就需要分配空间的函数来分配空间)
2. 创建map时，make函数可接受一个或两个参数。第二个参数用于为指定数量的key预先分配空间。
3. 使用make函数创建的map的初始长度为0

```go
func main() {
    temperature := make(map[float64]int, 8)  // map[float]int{}

	fmt.Println(temperature)
    fmt.Println(len(temperature))
}
```

一个用map计数的程序

```go
func main() {
	temperature := []float64{
		-28.0, 32.0, -32.0, -29.0, -23.0, -29.0, -28.0, -33.0,
	}

	frequency := make(map[float64]int)

	for _, t := range temperature {
		frequency[t]++
	}

	for t, num := range frequency { // range返回的是键和值
		fmt.Printf("%+.f occurs %d times \n", t, num)
	}
}
```



```go
func main() {
	temperature := []float64{
		-28.0, 32.0, -32.0, -29.0, -23.0, -29.0, -28.0, -33.0,
	}

	frequency := make(map[float64][]float64)

	for _, t := range temperature {
		g := math.Trunc(t/10) * 10
		frequency[g] = append(frequency[g], t)
	}

	for t, num := range frequency { // range返回的是键和值
		fmt.Printf("%v: %v\n", t, num)
	}
}
```

### 使用delete() 函数从map中删除键值对

格式如下

```go
delete(map, 键)
```

其中 map 为要删除的 map 实例，键为要删除的 map 中键值对的键。

```go
scene := make(map[string]int)
// 准备map数据
scene["route"] = 66
scene["brazil"] = 4
scene["china"] = 960
delete(scene, "brazil")
for k, v := range scene {
    fmt.Println(k, v)
}
```

清空map中的所有元素

Go语言中并没有为 map 提供任何清空所有元素的函数、方法，清空 map 的唯一办法就是重新 make 一个新的 map，不用担心垃圾回收的效率，Go语言中的并行垃圾回收效率比写一个清空函数要高效的多。



### 线程安全的map

Go语言中的 map 在并发情况下，只读是线程安全的，同时读写是线程不安全的。

```go
m := make(map[int]int)  // 创建一个int到int的映射

// 开启一段并发代码
go func() {
    for {
        m[1] = 1 // 不停写入
    }
}()

go func() {
    for {
        _ = m[1]  // 不停读取
    }
}()

// 无线循环
for {
}
```

这里会发生错误, 并发的对map进行读写, 这在编译阶段就会报错.

go提供了一个并发安全的map对象 ` sync.Map `, 他存在与sync 包下

sync.Map 有以下特性：

- 无须初始化，直接声明即可。
- sync.Map 不能使用 map 的方式进行取值和设置等操作，而是使用 sync.Map 的方法进行调用，Store 表示存储，Load 表示获取，Delete 表示删除。
- 使用 Range 配合一个**回调函数**进行遍历操作，通过回调函数返回内部遍历出来的值，Range 参数中回调函数的返回值在需要继续迭代遍历时，返回 true，终止迭代遍历时，返回 false。

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var scene sync.Map
    
    // 将键值对保存到 sync.Map
    scene.Store("greece", 97)
    scene.Store("london", 100)
    scene.Store("egypt", 200)
    
    // 从sync.Map 中根据键取值
    fmt.Println(scene.Load("london"))
    
    //  根据键删除对应的键值对
    scene.Delete("london")
    
    // 遍历所有sync.Map中的键值对 
    scene.Range(func(k, v interface{}) bool {
        fmt.Println("iterate:",k,v)
        return true
    })
    
}

```



### go中没有集合类型

使用map实现一个集合类型。 本质是用map的键值作为集合使用，用值来标记 键有没有出现过。

```go
func main() {
	temperature := []float64{
		-28.0, 32.0, -32.0, -29.0, -23.0, -29.0, -28.0, -33.0,
	}

	set := make(map[float64]bool)
	for _, t := range temperature {
		set[t] = true
	}

	if set[-28.0] {
		fmt.Println("set member")
	}

	fmt.Println(set)
}
```

对集合进行排序

```go
func main() {
	temperature := []float64{
		-28.0, 32.0, -32.0, -29.0, -23.0, -29.0, -28.0, -33.0,
	}

	set := make(map[float64]bool)
	for _, t := range temperature {
		set[t] = true
	}

	if set[-28.0] {
		fmt.Println("set member")
	}

	fmt.Println(set)

	unique := make([]float64, 0, len(set))  // 这里新建了一个数组
	for t := range set {
		unique = append(unique, t)
	}

	sort.Float64s(unique)  // 通过对数组的遍历完成对集合 键的遍历
	fmt.Println(unique)
}
```



### make和new的区别

make和new是两个内置函数, 主要用来创建并分配内存. 两者的区别是: new 只分配内存, 而make只能用于slice  map  channel的初始化.

new的函数描述如下

```go
// The new built-in function allocates memory. The first argument is a type,
// not a value, and the value returned is a pointer to a newly
// allocated zero value of that type.
func new(Type) *Type
```

从上面的代码可以看出，new 函数只接受一个参数，这个参数是一个类型，并且返回一个指向该类型内存地址的指针。同时 new 函数会把分配的内存置为零，也就是类型的零值。

new不仅可以为默认数据类型分配空间,  还可以为自定义类型分配空间

```go
type Student struct {
    name string
    age int
}

var s *Student
s = new(Student)
s.name = "dede"

fmt.Println(s)
```

这里如果我们不使用 new 函数为自定义类型分配空间（将第 7 行注释），就会报错

make

make 也是用于内存分配的，但是和 new 不同，它只用于 chan、map 以及 slice 的内存创建，而且它返回的类型就是这三个类型本身，而不是他们的指针类型，因为这三种类型就是引用类型，所以就没有必要返回他们的指针了。

Go语言中的 new 和 make 主要区别如下：

- make 只能用来分配及初始化类型为 slice、map、chan 的数据。new 可以分配任意类型的数据；
- new 分配返回的是指针，即类型 *Type。make 返回引用，即 Type；
- new 分配的空间被清零。make 分配空间后，会进行初始化；



作业题

统计一段话中的单词的频率，然后输出频率大于1的单词

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	some_text := "As far as eye could reach he saw nothing but the stems of the great plants about him receding in the violet shade, and far overhead the multiple transparency of huge leaves filtering the sunshine to the solemn splendour of twilight in which he walked. Whenever he felt able he ran again; the ground continued soft and springy, covered with the same resilient weed which was the first thing his hands had touched in Malacandra. Once or twice a small red creature scuttled across his path, but otherwise there seemed to be no life stirring in the wood; nothing to fear—except the fact of wandering unprovisioned and alone in a forest of unknown vegetation thousands or millions of miles beyond the reach or knowledge of man."

	count_map := make(map[string]int, len(some_text))
	for _, t := range strings.Fields(some_text) {
		count_map[strings.ToLower(strings.Trim(t, ""))]++
	}

	for k, v := range count_map {
		if v > 1 {
			fmt.Printf("%v:%v ", k, v)
		}
	}
	fmt.Println()

}
```



## 判断结构if

这与C语言的判断结构比较像

```go
if 布尔表达式 {
    /* 在布尔表达式为 true 时执行*/
}

if 布尔表达式 {
    /* 在布尔表达式为 true 时执行*/
} else {
    /* 在布尔表达式为 false 时执行*/
}
```

关键字 if 和 else 之后的左大括号`{`必须和关键字在同一行，如果你使用了 else if 结构，则前段代码块的右大括号`}`必须和 else if 关键字在同一行，这两条规则都是被编译器强制规定的。

````go
package main

import (
	"fmt"
)

func main() {
	var command = "go east"
	if command == "go east" {
		fmt.Println("you head future up the mountain.")
	} else if command == "go inside" {
		fmt.Println("you enter the cave where you live out the rest of your life.")
	} else {
		fmt.Println("Didn't quite get that.")
	}
}

````

### 特殊写法

if 还有一种特殊的写法，可以在 if 表达式之前添加一个执行语句，再根据变量值进行判断，代码如下：

```go
if err := Connect(); err != nil {
    fmt.Println(err)
    return
}
```

Connect 是一个带有返回值的函数，err:=Connect() 是一个语句，执行 Connect 后，将错误保存到 err 变量中。

err != nil 才是 if 的判断表达式，当 err 不为空时，打印错误并返回。



### 逻辑运算符

`||` `&&` 与C语言中的用法类似，也具有短路特性，当第一条件判断成功时，就不判断后面的条件了

```go
package main

import (
	"fmt"
)

func main() {

	fmt.Println("the year is 2100, should you leap?")
	var year = 2100
	var leap = year%400 == 0 || (year%4 == 0 && year%100 != 0)

	if leap {
		fmt.Println("Look before you leap")
	} else {
		fmt.Println("keep your feet on the ground.")
	}
}
```

## switch

分支结构

switch 语句执行的过程从上至下，直到找到匹配项，匹配项后面也不需要再加 break。

switch 默认情况下 case 最后自带 break 语句，匹配成功后就不会执行其他 case，如果我们需要执行后面的 case，可以使用 `fallthrough`。

```go
switch var1 {
    case val1:
    	...
    case val2:
    	...
    case val3:
    	...
    default:
    	...
}
```



```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("there is a cavern entrance here and a path to the east")
	var command = "go inside"
	switch command {
	case "go east":
		fmt.Println("you head further up the mountain")
	case "enter cave", "go inside": // 这里有两个条件可以匹配
		fmt.Println("you find yourself in a dimly lit cavern.")
        fallthrough
	case "read sign":
		fmt.Println("the sign reads 'No minors") // fallthrough会执行这里
	default:
		fmt.Println("didn't quites get that")
	}

}

```

`fallthrough` 关键字，用来执行下一个和case的body部分。当使用了这个关键字后，会执行剩下所有的case的body。**go没有break关键字跳出switch**





## 循环结构

与多数语言不同的是，Go语言中的循环语句只支持 for 关键字，而不支持 while 和 do-while 结构，关键字 for 的基本使用方法与C语言和 [C++](http://c.biancheng.net/cplus/) 中非常接近：

**for循环**可以有循环条件，也可以没有循环条件

for 有3种形式

1. 和C的for一样

```go
for init; condition; post {
    pass
}
```

2. 和C的while一样

```go
for condition {}
```

3. 和C的for(;;)一样，无限循环

```go
for {}
```

例子

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var count = 10
	for count > 0 {
		fmt.Println(count)
		time.Sleep(time.Second)
		count--
	}
	fmt.Println("Liftoff!")
}

```

或者没有循环条件，使用break来结束循环

````go
package main

import (
	"fmt"
	"time"
)

func main() {
	var count = 10
	for {
        if count <0 {
            break
        }
		fmt.Println(count)
		time.Sleep(time.Second)
		count--
	}
	fmt.Println("Liftoff!")
}

````

使用循环语句时，需要注意的有以下几点：

- 左花括号`{`必须与 for 处于同一行。
- Go语言中的 for 循环与C语言一样，都允许在循环条件中定义和初始化变量，唯一的区别是，Go语言不支持以逗号为间隔的多个赋值语句，必须使用平行赋值的方式来初始化多个变量。
- Go语言的 for 循环同样支持 continue 和 break 来控制循环，但是它提供了一个更高级的 break，可以选择中断哪一个循环，如下例：

```go
for j := 0; j < 5; j++ {
    for i := 0; i < 10; i++ {
        if i > 5 {
            break JLoop
        }
        fmt.Println(i)
    }
}
JLoop:
// ...
```

### for 中的初始语句——开始循环时执行的语句

初始语句是在第一次循环前执行的语句，一般使用初始语句执行变量初始化，如果变量在此处被声明，其作用域将被局限在这个 for 的范围内。

初始语句可以被忽略，但是初始语句之后的分号必须要写，代码如下：

```go
setp := 2
for ;step>0; step-- {
    fmt.Println(setp)
}
```


### for range

for range 结构是Go语言特有的一种的迭代结构，在许多情况下都非常有用，for range 可以遍历数组、切片、字符串、map 及通道（channel），for range 语法上类似于其它语言中的 foreach 语句，一般形式为：

```go
for key, val := range some {
    
}
```

需要要注意的是，val 始终为集合中对应索引的值拷贝，因此它一般只具有只读性质，对它所做的任何修改都不会影响到集合中原有的值。

通过 for range 遍历的返回值有一定的规律：

- 数组、切片、字符串返回索引和值。
- map 返回键和值。
- 通道（channel）只返回通道内的值。



#### 作业题

````go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	const rand_val = 31
	var some_val = rand.Intn(100)

	for {
		if some_val == rand_val {
			break
		} else if some_val > rand_val {
			fmt.Println("your number is bigger than my point")
		} else {
			fmt.Println("your number is smaller than my point")
		}

		some_val = rand.Intn(100)

	}
	fmt.Println("congratulation!!! your right!")
}
````



## 函数

Go语言里面拥三种类型的函数：

- 普通的带有名字的函数
- 匿名函数或者 lambda 函数
- 方法

### 函数声明

Go在标准库文档中列出了标准库每个包中声明的函数。

使用`func`关键字声明函数

````
func     Intn      (n int)          (int)
关键字    函数名   形参（名称 类型） 返回值和类型
````

**在Go里，大写字母开头的函数、变量或其他标识符都可以被导出，被其他包引用。**

**而小写字母开头的则不能被其他包引用**

形式参数列表描述了函数的参数名以及参数类型，这些参数作为局部变量，其值由参数调用者提供，返回值列表描述了函数返回值的变量名以及类型，如果函数返回一个无名变量或者没有返回值，返回值列表的括号是可以省略的。

每一次函数在调用时都必须按照声明顺序为所有参数提供实参（参数值），在函数调用时，**Go语言没有默认参数值，也没有任何方法可以通过参数名指定形参**，因此形参和返回值的变量名对于函数调用者而言没有意义。



### 多个参数

```go
func Unix(sec int64, nsec int64) Time
```

如果多个参数类型相同，那么该类型只写一次即可

```go
func Unix(sec, nesci int64) Time
```

### 多个返回值

多个返回值要用括号括起来。

```go
func Atoi(s string) (i int, err error)
```

这里可以将返回值的名称去掉，只保留类型。返回值本身的名称并不重要

```go
func Atoi(s string) (int,error)
```



### 可变参数

类似`Println`可以接收任意数量的参数

```go
func Println(a...interface{}) (int, error)
```

`...`表示函数的参数的数量是可变的

参数a的类型为`interface{}`,是一个空接口，意思是所有类型都实现了这个接口。

`...`和`interface{}`一起使用就表示接收任意数量，类型的参数。

练习题

````go
package main

import (
	"fmt"
)

func kelvinToCelsius(k float64) float64 {
	k -= 273.15
	return k
}

func celsiusToFahrenheit(v float64) float64 {
	return (v * 9.0 / 5.0) + 32.0
}

func kelvinToFahrenheit(k float64) float64 {
	return -459.67 + k
}
func main() {
	kelvin := 294.0
	celsius := kelvinToCelsius(kelvin)
	fmt.Print(kelvin, "k is ", celsius, "C\n")
	fmt.Print(kelvinToCelsius(kelvin), "C is ", celsiusToFahrenheit(kelvinToCelsius(kelvin)), "F \n")
	fmt.Print(kelvin, "k is ", kelvinToFahrenheit(0), "F\n")
}
````



## 方法

与某个类型关联的函数，go中没有类和对象？

### 声明新类型

使用type关键字 声明新类型：

```go
type celsius float64
var temperature celsius = 20
```

celsius 和float64虽然地层是相同的，但是，已经变成两个类型了。所以 也不能同时进行运算。 

### 通过方法为类型添加行为

可以将方法与包中声明的任何类型相关联，但不可以是int、float64等预声明的类型进行关联。

```go
type celsius float64
type kelvin float64

func kelvinToCelsius(k kelvin) celsius {
    return celsius(k - 273.15)
}

func (k kelvin) celsius() celsius {  // (k kelvin) 就表示celsius这个方法和kelvin这个类型关联， k是类型参数的接收者
    return celsius(k - 273.15)
}

```

在方法体中，接受者的行为和其他函数一样

```
func    (k  kelvin)    celsius()   celsius
关键字  接收者参数 类型      方法名    返回值和类型
```

调用方式

`变量.方法`

```go
type kelvin float64
var k kelvin = 234.0

c = k.celsius()
```



练习题

```go
package main

import (
	"fmt"
)

type celsius float64
type fahrenheit float64
type kelvin float64

func (k kelvin) kelvinToCelsius() celsius {
	k -= 273.15
	return celsius(k)
}

func (c celsius) celsiusToFahrenheit() fahrenheit {
	return fahrenheit((c * 9.0 / 5.0) + 32.0)
}

func (k kelvin) kelvinToFahrenheit() fahrenheit {
	return fahrenheit(-459.67 + k)
}

func main() {
	var k kelvin = 294.0
	var c celsius = k.kelvinToCelsius()
	fmt.Print(k, "k is ", c, "C\n")
	fmt.Print(k.kelvinToCelsius(), "C is ", c.celsiusToFahrenheit(), "F \n")
	fmt.Print(0, "k is ", kelvin(0).kelvinToFahrenheit(), "F\n")
}
```

## 一等函数

在Go里，函数的是头等的，它可以用在整数、字符串或其他类型能用的地方。

- 将函数赋值给变量。
- 将函数作为参数传递
- 将函数作为函数的返回值

```go
package main

import (
	"math/rand"
	"fmt"
)

type kelvin float64

func fakeSensor() kelvin {
	return kelvin(rand.Intn(151) + 150)
}

func realSensor() kelvin {
	return 0
}

func main() {
	sensor := fakeSensor // 将函数传给变量
	fmt.Println(sensor())

	sensor = realSensor
	fmt.Println(sensor())
}
```



将函数传递 给其他函数

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

type kelvin float64

func measureTemperature(samples int, sensor func() kelvin) { // 注意这里，sensor是变量 kelvin是返回值类型
	for i := 0; i < samples; i++ {
		k := sensor()
		fmt.Printf("%v k\n", k)
		time.Sleep(time.Second)
	}
}

func fakeSensor() kelvin {
	return kelvin(rand.Intn(151) + 150)
}

func main() {
	measureTemperature(3, fakeSensor)
}
```

### 声明函数类型

为声明函数类型有助于精简和明确调用者的代码

```go
type sensor func() kelvin
/*可以将函数中的参数替换*/
func measureTemperature(sample int, s func() kelvin)

func measureTemperature(samples int, s sensor)
```



### 闭包和匿名函数

匿名函数

格式如下

```
func(参数列表)(返回值列表){
   函数体
}
```

没有函数名

```go
var f = func() {
	fmt.Println("Dress up for the masquerade")
}

func main() {
	f()
    
    var f2 = func(message string) {
		fmt.Println(message)
	}

	f2("something happened")
}
```

因为函数数字字面值需要保留外部作用域的变量引用，所以函数字面值都是闭包的。

闭包就是由于匿名函数封闭并包围作用域中的变量而得名的。

```go
package main

import (
	"fmt"
)

type kelvin float64
type sensor func() kelvin

func realSensor() kelvin {
	return 0
}

func calibrate(s sensor, offset kelvin) sensor {
	return func() kelvin {  // 闭包
		return s() + offset
	}
}

func main() {
	sensor := calibrate(realSensor, 5)
	fmt.Println(sensor())
}
```



### 作业题

````go
package main

import (
	"fmt"
	"strings"
)

const format = "|%-10.1f|%-10.1f|\n"
const equal_num = 23

func print_equal(num int) {
	fmt.Println(strings.Repeat("=", num))
}

func print_title(val1 string, val2 string) {
	fmt.Printf("|%-10v|%-10v|\n", val1, val2)
}
func print_val(val1 float64, val2 float64) {
	fmt.Printf(format, val1, val2)
}

func celsiusToFahrenheit(c float64) float64 {
	return (c * 9.0 / 5.0) + 32.0
}

func main() {

	print_equal(equal_num)
	print_title("C", "F")
	print_equal(equal_num)
	for count := -40.0; count < 101.0; count += 5.0 {
		print_val(float64(count), celsiusToFahrenheit(float64(count)))
	}
	print_equal(equal_num)

	print_equal(equal_num)
	print_title("F", "C")
	print_equal(equal_num)
	for count := -40.0; count < 101.0; count += 5.0 {
		print_val(celsiusToFahrenheit(float64(count)), float64(count))
	}
	print_equal(equal_num)

}
````





## struct

为了将分散的零件组成一个完整的结构体，go提供了struct类型。

struct 允许你将不同类型的东西组合在一起。整形+字符串+浮点

声明struct  方法一

```go
var curiosity struct {
    lat float64
    long float64
} // 声明了一个struct变量

curiosity.lat = -123.0
curiosity.long = 123.4  // 赋值

fmt.Println(curiosity.lat, curiosity.long)
```

方法二  使用type，和C语言中类型相似，声明了一个类型

````go
type location struct {  // 可以声明多个对象
    lat float64
    long float64
}

var spirit location  // 声明一个location类型
spirit.lat = 234.0
spirit.long = 579.0

var oppertunity location
oppertunity.lat = 234.0
oppertunity.long = 123.4
````

### struct实例化

```go
var spirit location
ins := new(location)  # 创建结构体指针
ins := &location{}  # 通过取地址实例化
```



### struct初始化

````go
type location struct {
    lat, long float64
}

oppertunity := location {lat: 83.9, long: 123.4}  // 优势是：即使改变struct字段的结构，初始化值也不用变
oppertunity2 := location {83.9, 123.4}  // 优点就是简单。但是要注意初始化值的顺序，与struct中的值对应
````

### struct 的复制

在struct复制的时候，也会将其中的值完整的复制给新的变量，形成一个全新的变量。

```go
type location struct {
    lat, long float64
}

oppertunity := location {lat: 83.9, long: 123.4}
oppertunity2 := oppertunity
```



### 由struct组成的slice

比单独使用 字符串和整数要更为紧凑和清晰

```go
func main() {
	type location struct {
		name string
		lat  float64
		long float64
	}

	locations := []location{
		{name: "Bradbury Landing", lat: -4.534, long: 137.33},
		{name: "Columbia Memorial Station", lat: -14.23, long: 175.32},
		{name: "Challenger Memorial Station", lat: -482.3, long: 368.5},
	}
	fmt.Println(locations)
}
```

### 将struct转为json

导出json需要使用`json.Marshal`这个函数，函数返回两个值，一个是处理后的返回值，一个是是否成功的标志。

```go
func main() {
	type location struct {
		Lat, Long float64
	}

	locations := []location{
		{Lat: -4.534, Long: 137.33},
		{Lat: -14.23, Long: 175.32},
		{Lat: -482.3, Long: 368.5},
	}

	bytes, err := json.Marshal(locations)
	
	fmt.Println(err)
	fmt.Println(string(bytes))  // 输出还是要转化为字符串

}
```

**！！！！！格外要注意的是   Marshal函数只会对struct中被导出的字段进行编码。而只有首字母是大写的struct字段才是可以被导出的！！！！**

go语言中的json包要求struct中的字段必须以大写字母开头，类似CameCase的驼峰命名规范

可以使用字段注明标签，使得json包在进行编码的时候能够按照标签里的样式修改字段名。

```go 
	type location struct {
		Lat  float64 `json:"latitude"`  // 这样在编码json的时候就会使用latitude作为名称
		Long float64 `json:"longitude"`  // 注意 冒号后面不能有空格，需要紧贴名称字符串
	}
```



## go中没有类

没有class 没有对象，没有继承

go是使用另一种方式实现的， 如给类型添加方法。使用 struct + 方法的形式可以实现类似类的效果

````go
type coordinate struct { // 声明了一个结构体，用于存储数据的结构
	d, m, s float64
	h       rune
}

func (c coordinate) decimal() float64 { // 为结构体绑定了方法
	sign := 1.0
	switch c.h {
	case 'S', 'W', 's', 'w':
		sign = -1
	}
	return sign * (c.d + c.m/60 + c.s/3600)
}

func main() {
	lat := coordinate{4, 35, 22.2, 'S'}  // 声明结构体的变量，也相当于声明了一个“类的实例”
	long := coordinate{137, 26, 30.12, 'E'}
	fmt.Println(lat.decimal(), long.decimal())
}

````

### "构造函数"

可以使用struct复合字面值来初始化你所要的数据（如上面例子中声明struct变量那样）。但是如果struct初始化的时候还要做很多事情，那就可以考虑写一个构造用的函数。通常这个构造函数以new开头 后面接会生成的类型的名字。

```go
type location struct {
	lat, long float64
}

func newLocation(lat, long coordinate) location {  // 构造的函数
	return location{lat.decimal(), long.decimal()}
}

func main() {
	lat := coordinate{4, 35, 22.2, 'S'}
	long := coordinate{137, 26, 30.12, 'E'}
	fmt.Println(lat.decimal(), long.decimal())
    
	curiosity := newLocation(lat, long)
	fmt.Println(curiosity)

}
```

```go
type location struct {
	lat, long float64
}

type world struct {
	radius float64
}

func (w world) distance(p1, p2 location) float64 {
	var s1, c1 float64 = math.Sincos((rad(p1.lat)))
	var s2, c2 float64 = math.Sincos(rad(p2.lat))
	var clong float64 = math.Cos(rad(p1.long - p2.long))
	return w.radius * math.Acos(s1*s2+c1*c2*clong)
}

func rad(deg float64) float64 {
	return deg * math.Pi / 180
}

func main() {
	var mars = world{radius: 3389.5}
	var spirit location = location{-14.5684, 175.472636}
	var opportunity location = location{-1.9462, 354.4734}

	var dist = mars.distance(spirit, opportunity)
	fmt.Printf("%.2f km\n", dist)

}
```



练习题

```go
type coordinate struct { // 声明了一个结构体，用于存储数据的结构
	d, m, s float64
	h       rune
}

type location struct {
	lat, long float64
}

type world struct {
	radius float64
}

func (c coordinate) decimal() float64 { // 为结构体绑定了方法
	sign := 1.0
	switch c.h {
	case 'S', 'W', 's', 'w':
		sign = -1
	}
	return sign * (c.d + c.m/60 + c.s/3600)
}

func newLocation(lat, long coordinate) location { // 构造的函数
	return location{lat.decimal(), long.decimal()}
}

func (w world) distance(p1, p2 location) float64 {
	var s1, c1 float64 = math.Sincos((rad(p1.lat)))
	var s2, c2 float64 = math.Sincos(rad(p2.lat))
	var clong float64 = math.Cos(rad(p1.long - p2.long))
	return w.radius * math.Acos(s1*s2+c1*c2*clong)
}

func rad(deg float64) float64 {
	return deg * math.Pi / 180
}

func main() {
	var lat coordinate = coordinate{14, 34, 6.2, 'S'}
	var long coordinate = coordinate{175, 28, 21.5, 'E'}
	var spirit location = newLocation(lat, long)
	fmt.Println(spirit)

	lat = coordinate{1, 56, 46.3, 'S'}
	long = coordinate{354, 28, 24.2, 'E'}
	opportunity := newLocation(lat, long)
	fmt.Println(opportunity)

	lat = coordinate{4, 35, 22.2, 'S'}
	long = coordinate{137, 26, 30.1, 'E'}
	curiosity := newLocation(lat, long)
	fmt.Println(curiosity)

	lat = coordinate{4, 35, 0.0, 'S'}
	long = coordinate{135, 54, 0.0, 'E'}
	insight := newLocation(lat, long)
	fmt.Println(insight)

	var location_list = [...]location{spirit, opportunity, curiosity, insight}

	var mars = world{radius: 3389.5}
	var min_distance float64 = 9999999
	var max_distance float64 = 0
	
    // 计算其中最大值和最小距离
	for i := 0; i < len(location_list)-1; i++ {
		for j := i + 1; j < len(location_list); j++ {
			var dist = mars.distance(location_list[i], location_list[j])
			if min_distance > dist {
				min_distance = dist
			}
			if max_distance < dist {
				max_distance = dist
			}
		}
	}
	fmt.Printf("the max distance is %.3f. the min distance is %.3f\n", max_distance, min_distance)
	// 计算伦敦和巴黎的距离
	lat = coordinate{51, 30, 0, 'N'}
	long = coordinate{0, 8, 0, 'W'}
	london := newLocation(lat, long)

	lat = coordinate{48, 51, 0, 'N'}
	long = coordinate{2, 21, 0, 'E'}
	paris := newLocation(lat, long)

	mars = world{radius: 6371}
	var dist = mars.distance(london, paris)
	fmt.Printf("the distance from london to paris is %.3f km\n", dist)
	
    // 计算 mount sharp 和 olympus mons的距离
	lat = coordinate{5, 4, 48, 'S'}
	long = coordinate{137, 51, 0, 'E'}
	mount_sharp := newLocation(lat, long)

	lat = coordinate{18, 39, 0, 'N'}
	long = coordinate{226, 12, 0, 'E'}
	olympus_mons := newLocation(lat, long)

	mars = world{radius: 3389.5}
	dist = mars.distance(mount_sharp, olympus_mons)
	fmt.Printf("the distance from monut sharp to olympus mons is %.3f km\n", dist)

}
```



## 组合与转发

在面向对象的世界中，对象由更小的对象组合而成。

go通过结构体实现组合（composition）。go提供了"嵌入"（embedding）特性，它可以实现方法的转发（forwarding）。

go语言中没有继承。

首先看一个例子，这些类型全部封装在了一个结构中，但是有可以分成三组。

```go
type report struct {
    sol int
    high, low float64
    lat, long float64
}
```

使用struct再将其中的数据类型再进行封装

```go
type celsius float64

type report struct {
    sol  int
    temperature temperature
    location    location
}

type temperature struct { // 分别进行了拆分
    heigh, low celsius
}

type location struct {
    lat, long float64
}
```

拆开以后每个字段的复用性就增强了

```go 
func main() {
	bradbury := location{-4.5895, 137.4417}
	t := temperature{high: -1.0, low: -78.0}
	report := report{
		sol:         15,
		temperature: t,
		location:    bradbury,
	}

	fmt.Printf("%+v\n", report)  // 在这里每个类型的变量都可以单独返回。
	fmt.Printf("a balmy %v C\n", report.temperature.high) // 也可以返回其中某些内容，根据路径读取内容也比较直观
}
```

如果给temperature类型添加了一个方法

```go
func (t temperature) average() celsius {
    return (t.high + t.low) / 2
}
func (r report) average() celsius {  // 这是一个转发
    return r.temperature.average()
}

t.average() // 我们可以通过temperature类型进行调用

report.temperature.average() // 也可以通过report类型进行调用

report.average() // 通过转发调用子字段的方法
```

### 转发方法

go可以通过**struct嵌入**来实现方法的转发。在struct中只给定字段类型，不给定字段名称即可。

```go 
type sol int
type report struct {
    sol
    temperature // 将字段名省略
    location  // 将字段名省略  这种省略就叫 嵌入
}
```

优势是： 在temperature或者lcoation上附加的方法可以直接为report所用。那么没有字段名该如何调用其中的字段呢，直接使用类型名就可以。

```go
func (t temperature) average() celsius {
	return (t.high + t.low) / 2
}
func (s sol) days(s2 sol) int {
	days := int(s2 - s)
	if days < 0 {
		days = -days
	}
	return days
}

func main() {
	report := report{sol: 15}

	fmt.Println(report.sol.days(1446))  // 在嵌入类型中，可以通过路径调用方法
	fmt.Println(report.days(1446))  // 也可以直接调用方法
}
```

如果再在location上也加一个days方法呢？

```` go
func (l location) days(l2 location) int {
    return 5
}

fmt.Println(report.days(1446))  // 如此调用是会出错的。
// 我们需要在report上声明一个days方法才能不出错。其实也就是在report上的方法优先级高
func (r report) days(s2 sol) int {
    return r.sol.days(s2)
}
fmt.Println(report.days(1446)) // 这样就不报错
````



## 接口

- 接口关注于类型可以做什么，而不是存储了什么。 
- 接口通过列举类型必须满足的一组方法来进行声明。
- 在go语言中，不需要显式声明接口。（什么是显式）

```go
var t interface {  // 声明了一个接口
    talk string
}

type martian struct {}

func (m martian) talk() string { // 实现了接口
	return "nack nack"
}

type laser int 

func (l laser) talk() string {  // 实现了接口
	return strings.Repeat("pew ", int(l))
}
```

用interface声明了一个接口的形式，然后在类型中按照这个形式声明方法，实现接口。那如果没有声明接口呢？那些方法应该也是可行的。

```go
func main() {
    t = martian{}  // 注意这里，并没有声明变量t，而是直接给它赋值
    fmt.Println(t.talk())  // 然后就可以调用talk方法
    // t的类型是 var t interface{talk() string}
    t = laser(3)
    fmt.Println(t.talk())
}
```

如果没有用interface声明接口

```go
func main() {
    t := martian{}  // 需要重新声明变量t
    fmt.Println(t.talk())  // 当然也可以通过t调用talk
    
    s := laser(3)  // 但是我们不能复用t了，因为t是martian类型的变量，所以只能重新声明一个变量。
    fmt.Println(s.talk())
}
```

在使用接口之后，变量t相当于可以接受多种类型的变量，但是前提是类型要实现了接口的方法。这相当于实现了多态。

### 接口类型

为了复用，通常会把接口声明为类型。按照约定，接口名称通常以`er`结尾。

```go
var t interface {
    talk() string
}
==》
type talker interface { // 声明为类型
    talk() string
}
// 声明为类型后，要使之前的t还能用，要
var t talker

```

声明一个以接口为参数的函数

```go
func shout(t talker) {
    louder := strings.ToUpper(t.talk())
    fmt.Println(louder)
}

shout(martian{})  // 直接将变量传入
shout(laser(2))
```

这样的好处是，当新添加接口实现后，使用接口的函数不用发生改变。

**接口可以和struct嵌入特性一同使用。同时使用组合和接口将构成非常强大的设计工具。**

```go
func (l laser) talk() string {
	return strings.Repeat("pew ", int(l))
}

func shout(t talker) {
	louder := strings.ToUpper(t.talk())
	fmt.Println(louder)
}

type startship struct {
	laser  // struct 嵌入
}

func main() {
	var s startship = startship{laser(3)}
	fmt.Println(s.talk())  // s也可以调用talk，相当于starship也实现了talker接口
	shout(s)
}
```



### 探索接口

go语言的接口都是隐式实现的。

go语言允许在实现代码的过程中，随时创建接口，包括那些已经存在的代码。

```go
func stardate(t time.Time) float64 {
	day := float64(t.YearDay())
	h := float64(t.Hour()) / 24.0
	return 1000 + day + h
}

func main() {
	day := time.Date(2021, 8, 6, 5, 17, 0, 0, time.UTC)
	fmt.Printf("%.1f Curiosity has landed\n", stardate(day))
}
```

这里`time.Time`有两个函数YearDay和Hour。如果我们将它改造为接口

```go
type stardater interface {
    YearDay() int
    Hour() int
}

func stardate(t stardate) float64 { // 现在就能接收实现了接口的任何类型了
	day := float64(t.YearDay())
	h := float64(t.Hour()) / 24.0
	return 1000 + day + h
}
```

### 满足接口

go标准库导出了很多只有单个方法的接口。go通过简单的、通常只有单个方法的接口，来鼓励组合而不是继承，这些接口在各个组件之间形成了简单易懂的界限。

例子  fmt包声明的Stringer接口

```go
type Stringer interface {
    String() string
}
```

我们声明一个struct

```go
type location struct {
    lat, long float64
}

func (l location) String() string {  // 实现接口
    return fmt.Sprintf("%v, %v", l.lat, l.long) // 返回一个字符串
}

func main() {
    curiosity := location{-1.342, 137.32}
    fmt.Println(curiosity) // 在打印的时候会调用String接口
}
```

作业题

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type coordinate struct {
	d, m, s float64
	h       rune
}

type location struct {
	Lat, Long coordinate
	name      string
}

func (c coordinate) String() string {
	return fmt.Sprintf("%v° %v' %v″ %c", c.d, c.m, c.s, c.h)
}

func (c coordinate) decimal() float64 {
	sign := 1.0
	switch c.h {
	case 'S', 'W', 's', 'w':
		sign = -1
	}
	return sign * (c.d + c.m/60 + c.s/3600)
}

func (c coordinate) MarshalJSON() ([]byte, error) {  // 实现json打印的接口
	var t = struct {
		DD  float64 `json:"decimal"`
		DMS string  `json:"dms"`
		D   float64 `json:"degrees"`
		M   float64 `json:"minutes"`
		S   float64 `json:"seconds"`
		H   string  `json:"hemisphere"`
	}{
		DD:  c.decimal(),
		DMS: c.String(),
		D:   c.d,
		M:   c.m,
		S:   c.s,
		H:   string(c.h),
	}
	return json.Marshal(t)
}

func (l location) String() string {  // locaiton打印
	return fmt.Sprintf("%v is at %v, %v", l.name, l.Lat, l.Long)
}

func main() {
	var locate = location{
		Lat:  coordinate{4, 35, 22.2, 'S'},
		Long: coordinate{4, 35, 22.2, 'S'},
		name: "Elysium Plantitia",
	}
	bytes, err := json.MarshalIndent(locate, "", "  ")

	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(bytes))
}

```



## 指针

指针是指向另一个变量地址的变量。

### `&`和`*`

变量会将他们的值存储在计算机的RAM中，存储位置就是该变量的内存地址

&表示地址操作符，通过&可以获得变量的内存地址，这与C语言相同。

```go
answer := 42
fmt.Println(&answer)
```

&操作符无法获得**字符串**/**数值**/**布尔字面值**的地址

*操作符与&的作用相反，它用来解引用，提供内存地址指向的值。

```go
answer := 43
address := &answer // 获得answer的地址， address是一个指针
fmt.Println(*address) // 对指针解引用
```

### 指针类型

指针存储的是内存地址。

指针类型和其他普通类型一样，出现在所有需要用到类型的地方，如变量声明、函数参数、返回值类型、结构体字段等。

go的指针和C语言中的指针用法类似。 

如果指针变量持有相同的内存地址，那么他们就是相等的。

与字符串和数值不一样，复合字面量的前面可以放置&

```go
type person struct {
    name, superpower string
    age int
}

timmy := &person {  // 获取变量的地址
    name: "Timothy",
    age: 10,
}

timmy.superpower = "flying"  // 指针的使用和变量没有什么区别
（*timmy).superpower = "flying"
```



### 指向数组的指针

- 和结构体一样，可以把&放在数组的复合字面值前面来创建指向数组的指针。

  ```go
  superpowers := &[3]sting{"flight"， "invest", "super strength"}
  fmt.Println(superpowers[0])
  fmt.Println(superpowers[1:2])
  ```

  

- 数组在执行索引或切片操作时会自动解引用。没必要写`(*superpower)[0]`这种形式。

- 与C语言不一样，Go里面数组和指针是两种完全独立的类型。

- Slice和map的复合字面值前面也可以放置&操作符，但是Go并没有为他们提供**自动解引用**的功能。

### 指针的修改

go语言的函数和方法都是按值传递参数的，这意味着函数总是操作于被传递参数的副本。

当指针被传递到函数时，函数将接收传入的内存地址的副本。之后函数可以通过解引用内存地址来修改指针指向的值。

```go
type person struct {
	name, superpower string
	age              int
}

func bitrhday(p *person) {  // 参数p是将指针的值复制了一份
	p.age++ // 隐藏了解引用的操作
}

func main() {
	rebecca := person{
		name:       "Rebecca",
		superpower: "imagination",
		age:        14,
	}

	bitrhday(&rebecca)  // 取rebecca中的值
	fmt.Printf("%+v\n", rebecca)
}
```

指针作为方法的接收者和放在参数中，使用是非常相似的。

```go 
func (p *person) bitrhday() { // 这里变成接收者了
	p.age++
}

func main() {
	rebecca := &person{ // 创建了一个指针
		name:       "Rebecca",
		superpower: "imagination",
		age:        14,
	}

	rebecca.birthday()
	fmt.Printf("%+v\n", rebecca)
}
```

go语言在变量通过 点 标记法进行调用的时候，自动使用&取得变量的内存地址。也就是不用写`(&rebecca).birthday()`这种形式也可以运行。即

```go
func (p *person) bitrhday() { // 这里变成接收者了
	p.age++
}

func main() {
	rebecca := person{ // 这里改称使用变量也可以调用birthday
	}
	rebecca.birthday()
	fmt.Printf("%+v\n", rebecca)
}
```

### 内部指针

go提供了 内部指针 这种特性

它用于确定结构体中指定字段的内存地址， &操作符不仅可以获得结构体的内存地址，还可以获得结构体中指定字段的内存地址

```go
type stats struct {
    level int
    endurance, health int
}

func levelUp(s *stats) {
    s.level++
    s.endurance = 42 + (14 * s.level)
    s.health = 5 * s.endurance
}

type character struct {
    name string
    stats stats
}

func main() {
    player := character{name: "Matthias"}
    levelUp(&player.stats)  // 获取到了结构体中字段的地址
    fmt.Printf("%+v\n", player.stats)
}
```

### 修改数组

与C语言中大致相同

### 隐式指针

go语言中一些内置的集合类型就在暗中使用指针。

map在被赋值或作为参数传递的时候不会被复制：

- map就是一种隐式指针
- 这种写法就是多此一举： `func demolish(planets *map[string]string)`

map的键和值都可以是指针类型

需要将指针指向map的情况并不多见

### slice指向数组

slice是指向数组的窗口，实际上slice在指向数组元素的时候也使用了指针。

每个slice内部都会被表示为一个包含3个元素的结构，他们分别指向：

- 数组的指针
- slice的容量
- slice的长度

当slice被直接传递至函数或方法时，slice的内部指针就可以对底层数据进行修改

指向slice的显式指针的唯一作用就是修改slice本身：slice的长度、容量以及其起始偏移量。

```go
func reclassify(planets *[]string) {
	*planets = (*planets)[0:6]  // 对数组进行修改
}

func main() {
	planets := []string{
		"some1", "some1", "some1", "some1",
		"some1", "some1", "some1", "some1",
		"some1", "some1",
	}
	reclassify(&planets)  // 将数组的地址传递给函数
	fmt.Println(planets)
}
```

### 指针和接口

```go
type talker interface {
	talk() string
}

func shout(t talker) {
	louder := strings.ToUpper(t.talk())
	fmt.Println(louder)
}

type martian struct {}

func (m martian) talk() string {
	return "nack nack"
}

func main() {
	shout(martian{}) // 这里
	shout(&martian{})
}

```

这个例子中无论martian还是指向martian的指针，都可以满足talker接口

如果方法使用的是指针接收者，那么情况会有所不同

```go
type laser int

func (l *laser) talk() string { // 这里指明要一个指针接收者
	return strings.Repeat("pew ", int(*l))
}

func main() {
	pew := laser(2)
	shout(&pew)
    shout(pew) // 这样使用会报错。上面只是说laser指针满足了接口，而不是laser类型。
}
```



## nil

nil表示无或者零，这是一个零值。nil会导致panic。

```go
var nowhere *int
fmt.Println(nowhere)
fmt.Println(*nowhere) // 对nil解引用会出错
```

因为值为nil的接收者和值为nil的参数在行为上并没有区别，所以go语言即使在接收者为nil的情况下，也会继续调用方法

```go
type person struct {
    age int
}

func (p *person) birthday() {
    p.age++  // 但是这里的使用会报错。nil没有age的属性
}

func main() {
    var nobody *person
    fmt.Println(nobody) // 这里打印出来也是nil
    nobody.birthday() // 可以正常的调用函数
}
```

### nil函数值

```go
func main() {
    var fn func(a, b int) int // fn的默认值是nil。这样居然能声明成功！
    fmt.Println(fn == nil)
}
```

所以要检查函数值是否为nil，并在有需要时提供默认行为。

```go

func sortStrings(s []string, less func(i, j int) bool) {
	if less == nil { // 为less提供默认行为
		less = func(i, j int) bool {
			return s[i] < s[j]
		}
	}
	sort.Slice(s, less)
}

func main() {
	food := []string{"onion", "carrot", "celery"}
	sortStrings(food, nil)
	fmt.Println(food)
}
```

### nil slice

如果slice在声明之后没有使用复合字面值或内置的make函数进行初始化，那么它的值就是nil。幸运的是，range、len、append等内置函数都可以正常处理值为nil的slice

```go
func main() {
	var soup []string
	fmt.Println(soup == nil)

	for _, ingredient := range soup { // 虽然是nil但是可以执行，只是没有进入循环
		fmt.Println(ingredient)
	}

	fmt.Println(len(soup)) // nil也可以计算长度，长度为0
	soup = append(soup, "onion", "carrot", "celery")  // 向nil中添加元素它也不会报错
	fmt.Println(soup)
}
```

虽然空slice和值为nil的slice并不相等。但他们通常可以替换使用。

```go
func mirepoix(ingredients []string) []string {  // 这个函数就对空slice和nil slice有相同的处理逻辑
    return append(ingredients, "onion", "carrot", "celery")
}
```

### nil map

和slice一样，如果map的声明后没有使用复合字面值或内置的make函数进行初始化，那么它的值将会是默认的nil。

```go
func main() {
	var soup map[string]int // 声明一个空字典
	fmt.Println(soup == nil)

	measurement, ok := soup["onion"] // 没有取到onion的值并不会报错，但是会取到空值。

	if ok {
		fmt.Println(measurement)
	}

	for ingredient, measurement := range soup { // 这里也不会执行
		fmt.Println(ingredient, measurement)
	}
}
```

如下修改就可以为map赋值

```go
soup = make(map[string]int) // 为map分配空间
soup["onion"] = 10
measurement, ok := soup["onion"] // 可以获取到直

```



### nil接口

声明为接口的变量在未被赋值时，它的零值是nil。对于一个未被赋值的接口变量来说，它的接口类型和值都是nil，并且变量本身也等于nil。

````go
func main() {
    var v interface{}
    fmt.Printf("%T %v %v\n", v, v, v == nil) // 打印的都会是nil
}
````

当接口类型的变量被赋值后，接口就会在内部指向该变量的类型和值。

```go
var p *int
v = p
fmt.Printf("%T %v %v\n", v, v, v == nil)  // 这里类型不为nil但是值还是nil的
```

go中，接口类型的变量只有在类型和值都为nil时才等于nil。即使接口变量的值仍为nil，但只要它的类型不是nil，那么该变量就不等于nil。



## 错误处理

go语言没有异常处理机制

go语言允许函数和方法同时返回多个值。按照惯例，函数在返回错误时，最后边的返回值应该用来表示错误。调用函数后，应立即检查是否发生错误。

- 如果没有错误发生，那么返回的错误值为nil

```go
func main() {
	files, err := ioutil.ReadDir(".")
	if err != nil { // 立即检查错误
		fmt.Println(err)
		os.Exit(1)
	}

	for _, file := range files {
		fmt.Println(file.Name())
	}
}
```



### 如何优雅的处理错误

减少错误处理代码的一种策略是：将程序中不会出错的部分和包含潜在错误隐患的部分隔离开来。

对于不得不返回错误的代码，应尽力简化相应的错误处理代码。

### 文件写入

写入文件的时候可能出错。那如何处理错误？

文件写入完毕后，必须被关闭，确保文件被刷到磁盘上，避免资源的泄漏。类型error用来表示错误。

```go

func proverbs(name string) error {
	f, err := os.Create(name)
	if err != nil {  // 创建文件，有没有报错
		return err
	}

	_, err = fmt.Fprintln(f, "Error are values")
	if err != nil {  // 写入语句有没有报错
		f.Close()
		return err
	}
	_, err = fmt.Fprintln(f, "Don't just check errors, handle them gracefully")
	f.Close() // 关闭文件
	return err
}

func main() {
	err := proverbs("proverbs.txt")
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
}
```



### defer关键字

使用defer关键字，go可以确保所有deferred的动作可以在**函数返回**前执行。

```go
func proverbs(name string) error {
	f, err := os.Create(name)
	if err != nil {
		return err
	}
    defer f.Close()  // 使用defer来关闭文件，以保证在函数返回前文件句柄一定是关闭的

	_, err = fmt.Fprintln(f, "Error are values")
	if err != nil {
		return err
	}
	_, err = fmt.Fprintln(f, "Don't just check errors, handle them gracefully")
	return err
}
```

- defer可以用于任何函数和方法
- defer并不是专门为错误处理的
- defer可以消除必须时惦记执行**资源释放的**负担

### 多种错误处理方式

```go
type safeWriter struct {
    w  io.Writer
    err error
}

func (sw *safeWriter) writeln(s string) {
    if sw.err != nil { // 检查之前的err是否有错误产生
        return
    }
    _, sw.err = fmt.Fprintln(sw.w, s)  // 如果有错误，是存储在struct的err中的
}
```

### 收集于网络的关于文件关闭的文章

每当有一个实现了`io.Closer` 接口的对象 `x` 时，在得到对象并检查错误之后，会立即使用`defer x.Close()` 以保证函数返回时 `x` 对象的关闭 。以下给出两个惯用写法例子。

- **HTTP 请求**

```go
resp, err := http.Get("https://golang.google.cn/")
if err != nil {
    return err
}
defer resp.Body.Close()
// The following code: handle resp
```

- **访问文件**

```go
f, err := os.Open("/home/golangshare/gopher.txt")
if err != nil {
    return err
}
defer f.Close()
// The following code: handle f
```

上述的这种写法有潜在的问题，`defer x.Close()`会忽略他的返回值，但在执行x.Close()时，并不能保证x一定可以正常关闭，万一有错误怎么办？

在posix系统中关闭文件可能会有EIO错误，EIO错误是写入数据还未写入到磁盘上，就关闭了文件。

解决方案：

第一种，不使用defer

```go
func sloution() error {
    f, err := os.Create("/home/golangshare/gopher.txt")
    if err != nil {
        return err
    }
    
    if _, err = io.WriteString(f, "hello"); err != nil{
        f.Close()
        return err
    }
    return f.Close()
}
```

缺点就是我们需要在任务失败时，自己再次调用Close。并且在每个地方都如此使用。

第二种，通过命名返回值err和闭包处理

```go
func sloution() (err error) {
    f, err := os.Create("/home/golangshare/gopher.txt")
    if err != nil {
        return err
    }
    
    defer func() {
        closeErr := f.Close()
        if err == nil {
            err = closeErr
        }
    }()
    
    _, err = os.WriteString(f, "hello")
    return
}
```

第三种，在函数最后return语句之前，显示调用一次f.Close()

```go
func solution() error {
    f, err := os.Create("/home/golangshare/gopher.txt")
    if err != nil {
        return err
    }
    
    defer f.Close()
    if _, err := io.WriteString(f, "hello"); err != nil {
        return err
    }
    
    if err := f.Close(); err != nil {
        return err
    }
    return nil
}
```

这种解决方案能在 `io.WriteString` 发生错误时，由于 `defer f.Close()` 的存在能得到 `close` 调用。也能在 `io.WriteString` 未发生错误，但缓存未刷新到磁盘时，得到 `err := f.Close()` 的错误，而且由于 `defer f.Close()` 并不会返回错误，所以并不担心两次 `Close()` 调用会将错误覆盖。

第四种，函数return执行f.Sync()

```go
func solution() error {
    f, err := os.Create("/home/golangshare/gopher.txt")
    if err != nil {
        return err
    }
    
    defer f.Close()
    
    if _, err = io.WriteString(f, "hello"); err != nil {
        return err
    }
    
    return f.Sync()
}
```

由于调用 `close()` 是最后一次获取操作系统返回错误的机会，但是在我们关闭文件时，缓存不一定被会刷到磁盘上。那么，我们可以调用 `f.Sync()`（其内部调用系统函数 `fsync` ）强制性让内核将缓存持久到磁盘上去。由于强制性刷盘，这种方案虽然能很好地保证数据安全性，但是在执行效率上却会大打折扣。

### 

### New error

errors 包里有一个构造用New函数，它接收string 作为参数用来表示错误信息，该函数返回error类型。**就是返回自定义的错误信息**。

```go
func (g *Grid) Set(row, column int, digit int8) error {
	if !inBounds(row, column) {  // 如果越界就报错
		return errors.New("out of bounds")
	}
	g[row][column] = digit
	return nil
}
```

可以把错误信息当作用户界面的一部分，无论对最终用户还是开发者。

### 按需返回错误

按照惯例，包含错误信息的变量名应以Err开头。

```go
var (
    ErrBounds = errors.New("out of bounds")
    ErrDigit = errors.New("invalid digit")
)

func (g *Grid) Set(row, column int, digit int8) error {
    if !inBounds(row, column) {
        return ErrBounds // 返回错误
    }
    g[row][column] = digit
    return nil
}

func main() {
    var g Grid
    err := g.Set(0, 0, 15)
    if err != nil {
        switch err {
            case ErrBounds, ErrDigit:
            fmt.Println("les erreurs de parametres")
            default:
            fmt.Println(err)
        }
    }
}
```

errors.New 这个构造函数是使用指针实现的，所以上例中的switch语句比较的是内存地址，而不是错误包含的文字信息。

### 自定义错误类型

error类型是一个内置的接口，任何类型只要实现了返回string的Error()方法就满足了接口。

```go
type SudokuError []error // 创建错误类型

func (se SudokuError) Error() string { // 实现Error接口
	var s []string
	for _, err := range se {
		s = append(s, err.Error())
	}
	return strings.Join(s, ", ")
}
```



### 类型断言

使用类型断言来访问每一种错误

使用类型断言，可以把接口类型转化成地层的具体类型。例如`err.(SudokuError)`

```go
if err, ok := err.(SudokuError); ok { // 判断错误类型确实是SudokuError，就会把SudokuError赋值给err 
    fmt.Printf("%d error(s) occurred:\n", len(errs))
}
```



### don't panic

go中没有异常，但是它有panic。当panic发生的时候，程序就会崩溃。

其他语言如果不捕获异常，异常就会向上冒泡。go语言并没有这种机制。

go语言中错误值更加简单灵活：

忽略错误是有意识的决定，从代码上看是显而易见的。



创建panic

```go
panic("i forgot my towel")
```

panic 可以使用任何类型



### 错误值、panic、os.Exit

- 通常，更推荐使用错误值，其次才是panic
- panic比os.Exit更好: panic后会执行所有defer的动作，而os.Exit不会
- 有时候go会panic而不是返回错误值，比如用0做了除数

为了防止panic导致程序崩溃，go提供了recover函数。

但如果defer的函数调用了recover，panic就会停止，程序将继续运行。

```go
func main() {
	defer func() {
		if e := recover(); e != nil {
			fmt.Println(e)
		}
	}()
	panic("i forgot my towel")  // 会输出i forgot my towel但是程序不会停止，因为recover了
}
```



## goroutine 并发

go 中，独立的任务叫做goroutine

- goroutine和其他协程、进程、线程都有相似之处，但是并不完全相同。
- goroutine的创建效率非常高，go能直接协同多个并发（concurrent）操作。
- 在go中，无需修改现在顺序式的代码，就可以通过goroutine以并发的方式运行任意数量的任务。

### 启动 goroutine

只需在调用前加一个`go`关键字即可

```go
func sleepGopher() {
	time.Sleep(3 * time.Second)
	fmt.Println("...some...")
}

func main() {
	go sleepGopher()  // 相当于创建了一个线程去执行。但是当主进程结束时，也会结束这个子线程
	time.Sleep(4 * time.Second)  // 主线路
}
```

每次使用go关键字都会产生一个 新的goroutine。goroutine的执行顺序时无法确定的，这和其他语言相同。 

```go
func main() {
    for i := 0; i < 5; i++ { 
        go sleepGopher()
    }
	time.Sleep(4 * time.Second)  // 主线路
}
```



## 通道 channel

通道可以在多个goroutine之间安全的传值，通道可以作为变量，函数参数、结构体字段等。。创建通道要用`make`函数，并指定其传输数据的类型

```go
c := make(chan int)
```

感觉类似于一个队列。

使用左箭头操作符<-向通道发送值 或 从通道接收值

```go
c <- 99  // 发送值
r:= <- c // 接收值
```



发送操作会等待直到另个以goroutine尝试对该通道进行接收操作为止。

- 执行发送操作的goroutine在等待期间无法执行其他操作
- 未在等待通道操作的goroutine仍然可以继续自由运行

执行接收操作的goroutine将等待直到另一个goroutine尝试向该通道进行发送操作为止。

（感觉它是只能存储一个值的队列？而且在发送和接收值时会阻塞）

```go
func sleepGopher(id int, c chan int) {
	time.Sleep(3 * time.Second)
	fmt.Println("...", id, "...")
	c <- id
}

func main() {
	c := make(chan int)
	for i := 0; i < 5; i++ {
		go sleepGopher(i, c) // 相当于创建了一个线程去执行。但是当主进程结束时，也会结束这个子线程
	}
	for i := 0; i < 5; i++ {
		gopherID := <-c
		fmt.Println("goher", gopherID, "has finished")
	}
}
```



## 使用select处理多个通道

同时等待多个通道的值，因为每个通道的值可能类型不同。

time.After函数，返回一个通道，该通道在指定时间后会接收到一个值（发送该值的goroutine是go运行时的一部分)

select 和switch有点像。

该语句包含的每个case都持有一个通道，用来发送或接收数据。select会**等待**直到某个case分支的操作者**就绪**，然后就会执行该case分支。

注意：即使已经停止等待goroutine，但只要main函数还没有返回，仍在运行的goroutine将会继续占用内存。

select语句在不包含任何case的情况下将永远的等下去。。。

```go
func sleepGopher(id int, c chan int) {
	time.Sleep(time.Duration(rand.Intn(4000)) * time.Millisecond)
	c <- id
}

func main() {
	c := make(chan int)
	for i := 0; i < 5; i++ {
		go sleepGopher(i, c)
	}

	timeout := time.After(2 * time.Second)
	for i := 0; i < 5; i++ {
		select { // 一直等待，直到有一个条件满足然后继续执行
		case gopherID := <-c:
			fmt.Println("goher ", gopherID, " has finished sleeping")
		case <-timeout:
			fmt.Println("my patience ran out")
			return
		}
	}
}

```



### nil通道

如果不使用make初始化通道，那么通道变量的值就是nil（零值）

对nil通道进行发送或接收不会引起panic，但会导致永久阻塞

对nil通道执行close函数，那么会引起panic

nil通道的用处：

对于包含select语句的循环，如果不希望每次循环都等待select所涉及的所有通道，那么可以先将某些通道社为nil，等到发送值准备就绪后，再将通道变成一个非nil值并执行发送操作。



### 阻塞和死锁

当goroutine在等待通道的发送或接收时，我们就说它被阻塞了。

除了goroutine本身占用少量的内存外，被阻塞的goroutine并不消耗其他资源。goroutine静静的停在那里，等待导致其阻塞的事情来接触阻塞。

当一个或多个goroutine因为某些永远无法发生的事情被阻塞时，我们称这种情况为死锁。而出现死锁的程序通常会崩溃或挂起。

```go 
package main

func main() {
	c := make(chan int)
	<-c  // 会导致死锁
}
```

流水线模式

```go
func sourceGopher(downstream chan string) {
	for _, v := range []string{"some1", "some2", "some bad"} {
		downstream <- v
	}
	downstream <- ""  // 发送结束标识
}

func filterGopher(upstream, downstream chan string) {
	for {
		item := <-upstream
		if item == "" {
			downstream <- ""
			return
		}
		if !strings.Contains(item, "bad") {
			downstream <- item
		}
	}
}

func printGopher(upstream chan string) {
	for {
		v := <-upstream
		if v == "" {
			return
		}
		fmt.Println(v)
	}
}

func main() {
	c0 := make(chan string)
	c1 := make(chan string)
	go sourceGopher(c0)  // 开启流水线
	go filterGopher(c0, c1)
	printGopher(c1)
}

```

go允许在没有值可供发送的情况下通过close函数关闭通道例如  `close(c)`

通道被关闭后无法写入任何值，如果尝试写入将会引发panic。

尝试读取被关闭的通道会获得于通道类对应的零值。

注意： 如果在循环里读取一个已关闭的通道，并没有检查通道是否关闭，那么该循环可能会一直运转下去，耗费大量cpu时间。

执行以下代码可得知通道是否被关闭 `v, ok := <-c`

对上面的代码修改就是

```go
func sourceGopher(downstream chan string) {
	for _, v := range []string{"some1", "some2", "some bad"} {
		downstream <- v
	}
    close(downstream)
}

func filterGopher(upstream, downstream chan string) {
	for {
		item, ok := <-upstream  // 检查通道是否关闭
		if !ok {
            close(upstream)
			return
		}
	}
}
```



###  常见的关闭通道的模式

使用range关键字，这和python中for会自动处理stopiter错误一样。下面对他们进行修改

```go
func filterGopher(upstream, downstream chan string) {
    for item := range upstream {
		if !strings.Contains(item, "bad") {
			downstream <- item
		}
	}
    close(downstream)
}
```



## 并发

共享值，要确定共享值会不会引发竞争。

### go的互斥锁（mutex）

在使用共享变量时获得锁，并在使用变量过程中，一直持有锁

```go
mutex = mutual exclusive
Lock() Unlock()
```

互斥锁在sync包中。

```go
var mu sync.Mutex
func main() {
    mu.lock()
    defer mu.Unlock()
}
```

下面是一个互斥锁的完整例子：

````go
type Visited struct {
	mu      sync.Mutex  // 将锁存在一个变量中
	visited map[string]int  // 字典值
}

func (v *Visited) VisitLink(url string) int {
	v.mu.Lock()  // 加锁之后，防止别的线程再访问v的地址
	defer v.mu.Unlock()
	count := v.visited[url]
	count++
	v.visited[url] = count
	return count
}
````



### 长时间运行的工作进程

工作进程（worker），感觉像守护进程。通常会被写成包含select语句的for循环

### 事件循环和goroutine

go通过提供goroutine作为核心概念，消除了对中心循环的需求。



------

所有类型声明及初始化模板

声明变量：

```go
const const_val int = 1000  // 声明一个常量

var var_val float64
var str_val string // 声明变量，默认值都是零值
var bool_val bool

var list_val [5]int // 声明数组

var slice_val []int  // 声明切片

var map_val map[string]int  // 声明一个map变量

var struct_val struct {  // 声明一个
	some1 float64
	some2 float64
}
var chan_val chan int  // 声明一个通道

var interface_val interface {}  // 声明一个接口

var ErrVal error // 声明一个错误

func func_val() string {
	return "some return"
}  // 声明一个函数
```

声明时初始化

```go
const const_val int = 1000

var var_val float64 = 1000.0
var str_val string = "somthing"
var bool_val bool = true

var list_val [5]int = [5]int{1, 2, 3, 4, 5}

var slice_val []int = list_val[1:3]

var map_val map[string]int = map[string]int{"some1": 1, "some2": 2}

var struct_val = struct {
	some1 float64
	some2 float64
}{
	some1: 1.0,
	some2: 2.0,
}

var chan_val chan int

var interface_val interface {
	talk() string
}

var ErrVal error = errors.New("something error")

func func_val() string {
	return "some return"
}
```

#### 实现堆排序

```go
func BuildMaxHeap(A []int, length int) {
	for i := length / 2; i > 0; i-- {
		HeadAdjust(A, i, length)
	}
}

func HeadAdjust(A []int, k int, length int) {
	A[0] = A[k]
	for i := 2 * k; i <= length; i *= 2 {
		if i < length && A[i] < A[i+1] {
			i++
		}
		if A[0] >= A[i] {
			break
		} else {
			A[k] = A[i]
			k = i
		}
	}
	A[k] = A[0]
}

func HeapSort(A []int, length int) []int {
	BuildMaxHeap(A, length)
	for i := length; i > 1; i-- {
		A[i], A[1] = A[1], A[i]
		HeadAdjust(A, 1, i-1)
	}
}

func main() {
	var some_list = [10]int{0, 87, 45, 78, 32, 17, 65, 53, 9}
	fmt.Println(HeapSort(some_list[:], 8))
	fmt.Println(some_list[1:])
}
```















