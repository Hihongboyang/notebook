# Golang学习

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

格式化动词里指定宽度，就可以对齐文本：%4v，就是向左填充到足够4个宽度。

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

`const` 用来声明常量： 常量的值不可以改变

`var` 用来声明变量： 想要使用变量首先需要进行声明。

```go
package main

import "fmt"

func main() {
	const lightSpeed = 100000
	var distance = 19999999
	fmt.Println(distance/lightSpeed, "seconds")

	distance = 4010000
	fmt.Println(distance/lightSpeed, "seconds")
}
```

可以同时声明多个变量

```go
var (
    distance = 50000
    speed = 999999
)

var distance, speed = 1000000, 222222
const houseprice, housenumber = 10000, 678
```

赋值运算符

````go
package main

func main() {
	var weight = 149.0
	weight = weight * 0.378
	weight *= 0.378  // 简写形式，和其他语言类似
}
````

自增运算

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



## Boolean 类型

其他语言会将，非空值定义为True，空值定义为False。**但是golang不行，只有True为真，False为假。相当于只有True和False才能进行布尔值判断**

### strings.Contains

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

### 比较

````
== != <= >= > <
````

### 

### 判断结构if

这与C语言的判断结构比较像

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

### switch

分支结构

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

### 循环结构

**for循环**可以有循环条件，也可以没有循环条件

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



### 变量的作用域

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

展示随即日期的课后作业

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





### go语言的整数类型

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

常量使用const关键字来声明，程序里的每个字面值都是常量

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



## 函数

### 函数声明

Go在标准库文档中列出了标准库每个包中声明的函数。

使用`func`关键字声明函数

````
func     Intn      (n int)          int
关键字    函数名   形参（名称 类型） 返回值和类型
````

**在Go里，大写字母开头的函数、变量或其他标识符都可以被导出，被其他包引用。**

**而小写字母开头的则不能被其他包引用**



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



## 数组

数组是一种**固定长度**的有序的元素集合

```go
var planets [8]string // 声明了一个长度为8的字符串数组
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



多维数组

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

假设planets是一个数组，那么planets[0:4]就是一个切片，它切分出了数组里前4个元素

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
[]string // 括号里没有长度
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
	dump("planets", planets[1:2])  // 这里会显示 容量是4 为什么？？？
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
map   [string]     int 
关键字  键的类型   值的类型
```

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

### map不会被赋值

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
	temperature := make(map[float64]int, 8)

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

















