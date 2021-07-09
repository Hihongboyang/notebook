# rust学习

## 变量

```rust
fn main()
{
    let x = 5; // 不可变的
    x = 3; // 会报错
    
    let mut x = 5; // 变量可变了
}
```

常量

常量在绑定一个值后也是不可变的。与不可变的变量的区别是：

- 不能使用mut，常量永远不可变。
- 声明常量使用const关键字，类型必须被标注
- 常量可以在任何作用域内进行声明，包括全局作用域
- 常量只可以绑定到常量表达式，无法绑定到函数的调用结果或者只能在运行时才能计算出

```rust
const MAX_POINTS: u32 = 100_000;
```



shadowing

可以使用相同的名字声明新的变量，新的变量就会shadow（隐藏）之前声明的同名变量，在后续的代码中这个变量名就是新变量。

````rust
fn main(){
    let spaces = "    ";
    let spaces = spaces.len();  // 可以改变变量的类型
}
````



数据类型

标量类型+复合类型

通常编译器可以推断出类型。但是复杂情况需要自己指定类新。

```rust
let guess:u32 = "42".parse().expect("Not a number");
```

标量类型

1. 整数类型
2. 浮点类型
3. 布尔类型
4. 字符类型

整数类型

| 位数 | 有符号 | 无符号 |
| ---- | ------ | ------ |
| 8    | i8     | u8     |
| 16   | i16    | u16    |
| 32   | i32    | u32    |
| 64   | i64    | u64    |
| 128  | i128   | u128   |
| arch | isize  | usize  |

isize 和usize是根据系统架构走的，64位系统就是64位。

整数的溢出，整数溢出后，rust在编译时会报错，但是运行时不过会，而且溢出后会循环257=》1



浮点类型

浮点类型的默认类型是 f64

```rust
fn main(){
    let some = 1.0 + 3.5; // f64
}
```



字符类型

字符类型用来描述但个字符，字符类型的字面值使用单引号。占用4个字节的大小。可以存储很多值，甚至可以存储emoji表情。

````rust
fn mian(){
    let x = 'z';
    let y: char = '￥'；
    let z = '😀'；
}
````



复合类型tuple

创建tuple，在小括号里，将值用逗号分开。tuple中的每一个位置都对应一个类型，tuple中各个元素类型可以不同。

获取tuple中的值，1.类似于python中的序列解包 2. 直接获得指定位置的值，点表记法，后接元素的索引号。

````rust
fn main(){
    let some:(i32, f64, u8) = (50, 1.4, 3);
    
    let (x, y, z) = some;
    
    println!("{},{},{}", some.0, some.1, some.2);
}
````



数组

数组中的每个元素的类型必须是相同的。 数组的长度也是固定的，声明之后不能改变。数组是stack上分配的单个块 的内存。

```rust
fn main(){
    let a[i32; 5] = [1,2,3,4,5];  // a[i32; 5] [类型； 长度]
    
    let a = [3; 5]; // a == [3,3,3,3,3] 初始化的方法
    
    let first = a[0]; // 访问数组元素
}
```



函数

声明函数使用`fn`关键字

```rust
fn main()
{ }
```



函数体中的语句和表达式

1. 函数体由一系列语句组成，可选的由一个表达式结束。
2. rust是一个基于表达式的语言。
3. 语句是执行一些动作的指令。
4. 表达式会计算产生一个值。

```rust
fn main(){
    let x = 5; // 语句，他没有一个返回值。
    let y = 6+7; // 6+7 是一个表达式，他会产生一个返回值
    
    let y = {
        let x = 1;
        x + 3
    }; // 这个块，也是一个表达式，其中x+3的值会作为返回值。
    
    let y = {
        let x = 1;
        x + 3； // 当有x+3; 这个块就不会有返回值了，返回的是一个空的tuple，
    }; 
}
```



函数的返回值

- 在`->`符号后面声明函数返回值的类型，但是不可以为返回值命名。
- rust中，默认返回值是函数体中最后一个表达式的值。
- 如果要提前返回值，需要用return关键字，指定一个值。

```rust
fn five() -> i32
{
    5 // 没有分号，采用默认的返回形式
}

fn main(){
    let x = five();
}
```



if表达式

```rust
if 
else if 
else
```



- if表达式的条件必须是bool类型的。不能是一个可以判定为真的值
- if表达式中，与条件相关联的代码块叫做分支（arm）
- 可选择在后面加上else表达式

if 也是一个表达式

```rust
fn main()
{
    let condition = true;
    let number = if condition { 5 } else { 6 }; // 注意这两个块中的数字，可以被返回
    println!("the value of number is: {}", number);
}
```



循环

loop 

执行无限循环，在内部判断要不要继续执行。

```rust
fn main()
{
    let mut counter = 0;
    let result = loop {
        counter += 1;
        
        if counter == 10 {
            break counter * 2;
        }
    };
    println!("the result is: {}", result);
}
```



while 循环

先判断条件再执行循环

```rust
fn main()
{
    let mut number = 3;
    while number != 0 {
        println!("{}!", number);
        number = number - 1;
    }
    println!("some !");
}
```

for 循环

```rust
fn main() {
    let a = [10 ,20 ,30 ,40 ,50];
    
    for element in a.iter() {
        println!("the value is: {}", element);
    }
    
    for number in (1..4).rev() {  // python 中Range的方式
        println!("{}!", number);
    }
}
```



rust中的所有权

什么是所有权？

在其他的语言中，都需要在运行时对计算机的内存进行管理。

有些语言有垃圾回收机制来处理不再使用的内存。

有些语言，需要显式的分配和释放内存。



rust采用另一种方式来管理内存：所有权。内存管理的工作是在编译时完成的。

所有权规则：

- 每个值都有一个变量，这个变量是该值的所有者
- 每个值同时只能有一个所有者
- 当所有者超出作用域（scope）时，该值将被删除。

作用域

```rust
fn main(){
    // s is not available
    let s = 6; // s is available
    // you can start operating the s
}// overflow the scope of s， s is disabled
```

以字符串为例子，讲解所有权的概念

字符串类型有两种形式：

1. 字符串字面值：程序里手写的那些字符串值。他们是不可变的。这些值是直接写在程序代码里的，存储在栈内存上。
2. String：在heap上分配的存储空间，能够存储在编译时未知数量的文本。

创建string类型的值

可以使用from函数从字符串字面值创建出string类型。`let s = String::from("hello");`

````rust
fn main(){
    let mut s = String::from("hello");
    s.push_str(",World");  // modify string
    println!("{}", s);
} // 超出作用域范围，会将s的内存释放
````

对于某个值来说，当拥有它的变量超出他的作用域范围时，内存会立即自动的交还给操作系统。

从上面所有权的规则和变量的作用域两个方面来讲，当我们将一个变量赋值给另一个变量时，数据的所有权就发生了变更，而当这两个变量超出了作用域的范围后，都应当被销毁。而此时就存在问题了，因为两个变量的实际存储的是相同的值，会不会将相同的内存释放两次。看如下的例子

```rust
fn main(){
    let x = 5;
    let y = x;  // 按照规则，5的所有权交给了y
    println!("{} {}", x, y); // 如果按照之前的逻辑，x，y都需要释放。
}
```

针对变量和数据（如5）之间的交互，rust设计了一个**移动（move）**的概念。

在这里是将数据的所有权交给了y，可以认为是一种浅拷贝，只是拷贝了数据的引用。不过如5或者字符串字面值这种简单直接的数据类型，浅拷贝和深拷贝的区别不大，因为这些数据本身就在数据栈中，只是重新加入了一遍。

如果是复杂的数据类型，比如String类型和数组类型等，他们的数据是放在堆内存中的，栈中只存放了数据的引用。他们的所有权移动（move）是另一种形式了。

以字符串String类型举例：

```rust
let s1 = String::from("hell拷贝o");
let s2 = s1;
```

一个 String 类型由3部分组成

| name                                                         | value      |
| ------------------------------------------------------------ | ---------- |
| ptr（指向存放字符串内容的内存指针）                          | 指向堆内存 |
| 拷贝len （一个长度，存放字符串内容所需的字节数）             | 5          |
| capacity（一个容量，string从操作系统总共获得内存的总字节数） | 5          |

这一部分存放于栈内存中。

堆内存拷贝

| index | value |
| ----- | ----- |
| 0     | h     |
| 1     | e     |
| 2     | 拷贝l |
| 3     | l     |
| 4的   | o     |

在rust中当s2 = s1时，并没有复制堆内存中的东西，而只是复制了引用，同时rust使**s1的引用失效**。所以在超出作用域释放内存时，就不用再释放s1了。

同时如果在之后的代码中使用失效的s1变量，是会导致错误的。

```rust
fn main(){
    let x = 5;
    let y = x;
    println!("{} {}", x, y); // Error: borrow of moved value: 's1'
}
```

这里隐含的一个设计原则是：rust不会自动创建数据的深拷贝，都是默认建立的浅拷贝。

当我们需要深拷贝的时候，需要调用克隆（clone）这个方法

```rust
fn main(){
    let x = 5;
    let y = x.clone(); // 深拷贝
    println!("{} {}", x, y);
}
```



于此对应，在栈上的数据的拷贝叫 复制。一个叫`Copy trait`的方法。

- `Copy trait`, 可以用于像整数这样完全存放在stack上面的类型
- 如果一个类型实现了copy这个trait，那么旧的变量在赋值后仍然可用
- 如果一个类型或者该类型的一部分实现了`Drop trait`，那么rust不允许他再实现`Copy trait`

一些拥有Copy trait 的类型

- 任何简单标量的组合类型都可以是copy的
- 任何需要分配内存或某种资源的都不是copy的
- 一些拥有copy trait的类型
  - 所有的整数类型
  - bool
  - char
  - 所有浮点类型
  - tuple类型，如果tuple中所有的字段都是可以copy的，否则不行。



所有权和函数

将值传递给函数和将值传递给变量，产生的效果是相似的。传递给函数只会发生两种情况：移动或者复制。

```rust
fn main(){
    let s = String::from("Hello World");
    take_ownership(s); // 将s的所有权转向函数内部了
    let x = 5;
    makes_copy(x);  // 由于类型的原因，x在外面还是可以访问的
    println!("x: {}", x);
}

fn take_ownership(some_string: String)
{
    println!("{}", some_string)
}

fn makes_copy(some_number: i32)
{
    println!("{}", some_number);
}

```

返回值和作用域和所有权

函数在返回值的过程中，同样也会发生所有权的转移

一个变量的所有权总是遵循同样的模式：

- 把一个值赋给其他变量时就会发生移动
- 当一个包含heap数据的变量离开作用域时，它的值就会被drop函数清理，除非数据的所有权转移到另一个变量上。

```rust
fn main(){
    let s1 = gives_ownership();
    let s2 = String::from("hello");
    let s3 = takes_and_gives_back(s2);
    // 这里s2不再可用
}// s1 s2 s3 都被销毁

fn gives_ownership()->String{
    let some_string = String::from("hello");
    some_string  // some_string 的所有权返回到main函数了
}

fn takes_and_gives_back(a_string: String)->String{
    a_string  // s2 的所有权传递进来，然后又被传递出去。
}
```

如何让函数使用某个值，但不获得其所有权？

使用 引用或者借用。什么是引用，将参数的类型变为 &String 而不是String

&符号就表示引用：允许你引用某些值而不取得其所有权

````rust
fn main(){
    let s1 = String::from("Hello");
    let len = calculate_length(&s1);
    
    println!("the length of '{}' is {}", s1, len);
}

fn calvulate_length(s: &String)->usize{
    s.len() // 超出作用域 但并不清理存储空间，因为并未获得所有权
}
````

是否能够修改借用？ 不行的，和变量一样，默认都是不可以修改的。除非我们将他设置为可变的mut。

````rust
fn main(){
    let mut s1 = String::from("Hello");
    let len = calculate_length(&mut s1);
    
    println!("the length of '{}' is {}", s1, len);
}

fn calvulate_length(s: &mut String)->usize{
    s.push_str(", world");
    s.len() // 超出作用域 但并不清理存储空间，因为并未获得所有权
}
````

可变引用的重要限制是：在特定的作用域内，对某一块数据，只能有一个可变的引用。这样做的好处是可在编译时防止数据竞争。

以下三种行为下会发生数据竞争：

- 两个或多个指针同时访问同一个数据。
- 至少有一个指针用于写入数据
- 没有使用任何机制来同步对数据的访问



可以通过创建新的作用域，来允许非同时的创建多个可变引用

````rust
fn main(){
    let mut s = String::from("Hello");
    {
        let s1 = &mut s;
    }
    let s2 = &mut s; // 虽然有两个可变引用，但是他们的作用域不重叠
}
````

另一个限制：不可以同时拥有一个可变和一个不可变的引用。多个不可变引用是可以的。

引用有引发悬空指针的问题

```rust
fn main(){
    let r = dangle();
}

fn dangle() -> &String{
    let s = String::from("hello");
    &s  // 返回引用，但是s在函数结束的时候就要被销毁了，这个引用也会失效。  这里会引入一个生命周期的概念，之后会讲
}
```



字符串切片

字符串切片是指字符串中一部分内容的引用。

形式： [开始索引..结束索引]

- 开始索引就是切片起始位置的索引值
- 结束索引是切片终止位置的下一个索引值

```rust
fn main(){
    let s = String::from("Hello world");
    
    let hello = &s[0..5];
    let world = &s[6..11];
    println!("{}, {}", hello, world);
}
```

字符串字面值就是一个切片，字符串字面值被直接存储在二进制程序中。

```rust
fn main(){
    let s = "Hello world";  // s是&str 字符串切片类型的
    println!("{}", s);
}
```

将字符串切片作为参数传递

````rust
fn first_word(s: &String) -> &str{}
// 改为
fn first_word(s: &str) -> &str{}
````

使用方法 ：1. 使用字符串切片直接调用该函数。2. 使用String，可以创建一个完整的String切片来调用该函数。

定义函数时使用字符串切片来代替字符串会使我们的api更加通用，且不会损失任何功能。

````rust
fn main() {
    let my_string = String::from("Hello world");
    let wordIndex = first_world(&my_string[..]);
    
    let my_string_literal = "hello_world";
    let wordIndex = first_world(my_string_literal);
}

fn first_world(s: &str) -> &str{ // 采用字符串切片
    let bytes = s.as_bytes();
    for(i, &item) in bytes.iter().enumerate()
    {
        it item == b' '
        {
            return &s[..];
        }
    }
    &s[..]
}

````



## `rust`中的`struct`

什么是struct？ 结构体，自定义的数据类型，为相关联的值命名，打包=>有意义的组合

### 定义struct

```rust
struct User{  // 使用struct关键字
    username: String, // 字段名称和类型
    email: String, 
    sign_in_count: u64,
    active: bool, // 最后的字段也要有逗号
}
```

### 实例化struct

想要使用必须先实例化。

- 为每个字段指定具体的值
- 无需按声明顺序进行指定

```rust
struct User{
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main(){
    let user1 = User{
        email: String::from("abc@123.com"),
        usernameL: String::from("Nikky"),
        active: true,
        sign_in_count: 556,  // 初始化时要给每个成员都赋值
    };
}
```

### 获取struct里面的某个值

使用点 标记法

```rust
let mut user1 = User{  // 如果使用mut标记，标记struct是可变的，那么整个struct的字段都是可变的
        email: String::from("abc@123.com"),
        usernameL: String::from("Nikky"),
        active: true,
        sign_in_count: 556,
};
user1.email = String::from("anotheremail@123.com");
```



### struct作为函数返回值

将struct作为函数最后的表达式，就可以将struct作为函数的返回值使用。

### 初始化字段的简写

当字段名与字段值对应的变量名相同时，就可以使用字段初始化简写的方式：

```rust
struct User{
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn build_user(email: String, username: String)->User{
    User {
        email, // 使用了简写的方式
        username,  // 使用了简写的方式
        active: true,
        sign_in_count: 0,
    }
}
```

### struct 中的更新语法

当想要基于一个struct实例创建一个新实例的时候，可以使用struct更新语法：

````rust
let user2 = User{
    email: String::from("anotheremail@123.com")
    username: String::from("anothername"),
    active: user1.active,  // 如果不使用更新的语法，需要从之前的struct指定值
    sign_in_count: user1.sign_in_count,
};

let user2 = User{
    email: String::from("anotheremail@123.com")
    username: String::from("anothername"),
    ..user1  // struct更新语法，使用两个..。剩下的值使用user1的值
};
````

### tuple struct

定义类似tuple的struct。需求：

1. tuple struct整体有个名字，但里面的元素没有名字
2. 适用范围：想给整个tuple起名，并让它不同于其他tuple，而且又不需要给每个元素起名。

定义tuple struct： struct + 名称(元素类型)

````rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
let black = Color(0,0,0);
let origin = Point(0,0,0);
````



### 没有任何字段的struct (unit-Like Struct)

可以定义没有任何字段的struct，叫做unit-like struct

适用于需要在某个类型上实现某个trait，但是在里面又没有想要存储的数据。

### struct数据的所有权

````rust
struct User{
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
````

上述结构体的字段使用了String而不是&str，并且u64等基础类型

- 所以该struct实例拥有其所有的数据的所有权
- 只要struct的实例是有效的，那么里面的字段数据也是有效的。

struct里面也可以存放引用，但这需要使用生命周期（以后再讲）

- 生命周期保证只要struct实例是有效的，那么里面的引用也是有效的
- 如果struct里面存储引用，而不使用生命周期，就会报错。

### 使用struct的例子

假如要计算一个矩形的面积，我们需要长宽进行计算，也就是我们需要两个参数。

```rust
fn main()
{
    let w = 30;
    let l = 50;
    prinfln!("{}", area(w, l));
}

fn area(wigth: u32, length: u32)
{
    wigth * length
}
```

上面的例子的问题是矩形的长宽是独立的，没有将他们联系起来，也就是不统一。

使用元组可以将两个参数组合起来，但是它的问题的是哪个是长，哪个是宽，容易混淆

````rust
fn main()
{
    let rect = (30, 50);

    println!("{}", area(rect));
}

fn area(dim: (u32, u32)) ->u32
{
    dim.0 * dim.1
}
````

使用struct对每个参数起名字

```rust
struct Rectangle
{
    width: u32,
    length: u32,
} // 使用结构体来保存长宽的数据

fn main()
{
    let rect = Rectangle{
        width: 30,
        length: 50,
    };
    println!("{}", area(&rect));  // 保留了所有权
    println!("{}", rect); // 这里打印会报错
}

fn area(rect: &Rectangle) ->u32
{
    rect.width * rect.length
}

```

### struct的调试模式

如上面例子所示

````rust
println!("{}", rect);
````

这里直接打印一个struct是不行的，因为这个struct并没有实现`std::fmt::Display`这个接口，类似于python中没有实现`__str__`这个方法，无法打印出struct中具体的数据。

修改提示可以使用`{:?}`进行格式化

```rust
println!("{:?}", rect);
println!("{:#?}", rect);
```

但是这样也会报错，因为没有开启struct的debug模式，我们需要显示的开启这个功能

```rust
#[derive(Debug)]
struct Rectangle
{
    width: u32,
    length: u32,
} 
```

其中derive是一种派生，这是实现一种trait的方式。

### struct的方法

方法和函数类似：`fn`关键字、名称、参数、返回值相似

不同之处：

- 方法是在struct（或者`enum`、`trait`对象）的上下文中定义
- 第一个参数是self，表示方法被调用的struct实例

方法的定义

- 使用`impl`关键字，定义在`impl`块中，
- 方法的第一个参数可以是&self，也可以获得其所有权或可变借用，和其他参数一样。

```rust
#[derive(Debug)]
struct Rectangle
{
    width: u32,
    length: u32,
}

impl Rectangle{
    fn area(&self) ->u32
    {
        self.width * self.length
    }
}

fn main()
{
    let rect = Rectangle{
        width: 30,
        length: 50,
    };

    println!("{}", rect.area());
    println!("{:#?}", rect);
}
```

#### 方法调用的运算符

rust没有->运算符

rust会自动解引用或者自动引用：在调用方法时就会发生这种行为

在调用方法时，rust根据情况自动添加`& &mut *`，以便object可以匹配方法的签名。

下面两行代码效果相同：

-  p1.distance(&p2);
- (&p1).distance(&p2);

```rust
impl Rectangle{
    fn area(&self) ->u32
    {
        self.width * self.length
    }
    
    fn can_hold(&self, other: &Rectangle)->bool{
        self.width > other.width && self.length > other.length
    }
}
```

如上我们添加一个方法之后。试着调用函数

```rust
fn main()
{
    let rect1 = Rectangle{
        width: 30,
        length: 50,
    };
    
    let rect2 = Rectangle{
        width: 20,
        length: 10,
    }

    println!("{}", rect1.can_hold(&rect2));
}
```

#### 关联函数

可以在`impl`块里定义不把self作为第一个参数的函数，他们叫关联函数例如 String::from()

关联函数通常用于构造器，用来生成关联类型的实例。

```rust
impl Rectangle{
    fn area(&self) ->u32
    {
        self.width * self.length
    }
    
    fn can_hold(&self, other: &Rectangle)->bool{
        self.width > other.width && self.length > other.length
    }
    
    fn square(size: u32) -> Rectangle{
        Rectangle {
            width: size,
            length: size,
        }
    }
}

fn main(){
    let s = Rectangle::square(20);
}
```

**每个`struct`可以有多个`impl`块**

------

## 枚举与模式匹配

### 定义枚举

以ip地址作为例子，举例

```rust
enum IpAddrKind {
    V4,
    V6, // 枚举的变体
}

let four = IpAddrKind::V4; // 创建枚举值， 但是 里面没有具体的参数值阿？
let six = IpAddrKind::V6;
```

将枚举作为参数传递

```rust
enum IpAddrKind {
    V4,
    V6,
}

fn route(ip_kind: IpAddrKind){}

fn main(){
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;

    route(four);
    route(six);
    route(IpAddrKind::V6);
}
```

枚举作为struct中的一个成员使用。

```rust
enum IpAddrKind {
    V4,
    V6,
}
struct IpAddr {
    kind: IpAddrKind,
    address: String,
}
fn main() {
    let home = IpAddr{
        kind: IpAddrKind::V4, // 这里还是没有具体的值
        address: String::from("127.0.0.1"),
    };
    let loopback = IpAddr{
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };
}
```

将数据附加到枚举变体中

```rust
enum IpAddr {
    V4(u8,u8,u8,u8),  // 在变体中指定就可以向其中存储数据
    V6(String),
}

fn main() {
    let home = IpAddrKind::V4(127,0,0,1);
    let loopback = IpAddrKind::V6(String::from("::1"));
}
```

优点： 

- 不需要额外使用struct结构
- 每个变体可以拥有不同的类型以及关联的数据量

### 为枚举定义方法

与`struct`相似，枚举的方法也是定义在`impl`中

```rust
impl Message {
    fn call(&self) {}
}
```

### Option枚举类型

Option定义于标准库中。在prelude预导入模块中。

他描述了：某个值可能存在（某种类型）或者不存在的情况。为了替换NULL这个值。

RUST 中是没有NULL的。当你尝试使用非NULL值那样使用NULL值的时候，就会引起某种错误。

rust中类似NULL的概念的是 Option<T>

标准库中的定义是

```rust
enum Option<T> {
    Some(T),
    None,
}
```

他包含在prelude中，可以直接使用如下几种接口，其中T是指具体的类型

```rust
Option<T>
Some(T)
None
```

具体用法

````rust
fn main() {
    let x:i8 = 5;
    let y: Option<i8> = Some(5);
    
    let sum = x + y; // 这里会报错，因为Option<i8>并不等于i8，两者并不是一个类型，所以不能够相加，想要进行计算需要将Option进行转换为i8。
}
````



## match

允许一个值和一系列模式进行匹配，并执行匹配的模式对应的代码。

模式可以是`字面值`，`变量名` ，`通配符`等

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("penny");
            1
        }, // {}用来括住多行语句
        Coin::Nickel => 5,  // =>后是匹配成功后，执行的代码。
        Coin::Dime => 10,
        Coin::Quarter => 25,
    } // 匹配成功的表达式值，会作为整个match表达式的值返回
}

fn main() {}
```

#### 绑定值的模式

匹配的分支（match中的分支）可以绑定到被匹配对象（传进来的值）的部分值。

因此，可以从`enum`变体中提取值。

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("penny");
            1
        } // {}用来括住多行语句
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => { // 由于Quarter可以存储值，所以使用state存储值。这就是绑定值的匹配
            println!("State quarter from {:?}!", state);
            25
        }
    }
}

fn main() {
    let c = Coin::Quarter(UsState::Alaska);
    println!("{}", value_in_cents(c));
}

```


### 匹配Option值

```rust
fn main() {
    let five = Some(6);
    let six = plus_one(five);
    let none = plus_one(None);
}

fn plus_one(x: Option<i32>) -> Option<i32>{
    match x{
        None => None,
        Some(i) => Some(i+1),
    } // 返回的是Option对象
}
```

match在匹配时要覆盖到所有的可能情况。比如上方的例子，x是Option的类型，其中Option中有可能有None值，需要覆盖到这种情况。

可以使用通配符 `_`来覆盖其余的情况，比如下面的方式

```rust
fn main() {
    let v = 0u8;
    match v {
        1 => println!("one"),
        2 => println!("three"),
        3 => println!("five"),
        4 => println!("seven"),
        _ => (),
    }
}
```



#### if let一个简单的控制流

只关心一种匹配而忽略其他匹配的情况。相当于只有一个 if 剩下都是else

````rust
fn main() {
    let v = Some(0u8);
    match v {
        Some(3) => println!("three"),
        _ => (),
    }
    
    if let Some(3) = v {
        println!("three");
    }
    else {
        println!("others");
    }
    // 上面的段代码是等价的。
}
````



## Package crate module

rust的代码组织包含什么内容：

- 那些细节可以暴露，那些细节是私有的
- 作用域内哪些名称有效。等等



模块系统：

- package：cargo的特性，让你构建、测试、共享crate
- crate：一个模块树，它可与i产生一个library或可执行文件
- module、use：让你控制代码的组织、作用域、私有路径
- path：为struct、function、或module等命名的方式

### package 和crate

crate的类型：

1. `binary`
2. `library`

crate root:

源代码文件。rust编译器从这里开始，组成你的Crate的根Module（整个package的入口？）。

一个package中：

1. 包含一个Cargo.toml，它描述了如何构建这些Crates
2. 只能包含0-1个library crate
3. 可以包含任意数量的binary crate
4. 但必须至少包含一个crate（library或binary）

### cargo中对library和binary的区分

在rust的项目目录中，src存放源文件而

src/main.rs:

- 是binary crate的carte root
- crate名称与package名称相同

src/lib.rs:

- package 包含一个library crate
- library crate 的crate root
- crate名称与package名称相同

cargo的惯例

一个package可以同时包含 src/main.rs 和 src/lib.rs。

一个package可以有多个binary crate。文件放在src/bin，每个文件是单独的binary crate。

carte的作用

将相关功能组合到一个作用域内，便于在项目间进行共享。防止命名冲突。

#### module来控制作用域和私有性

作用： 

1. module在一个carte内，将代码进行分类
2. 增加程序的可读性，易于复用
3. 控制项目的私有性。public、private

建立module

- 使用mod关键字
- 可以嵌套定义
- 可以包含其他项目（struct、enum、常量、trait、函数等）的定义

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
        fn seat_at_table() {}
    }
    mod serving {
        fn take_order() {}
        fn serve_order() {}
    }
}
```

src/main.rs 和src/lib.rs 叫做crate roots。 这两个文件（任意一个）的内容形成了名为cratre的模块，位于整个模块树的根部。如下是命名空间的结构

````
crate
	front_of_house
	    hosting
	    	add_to_waitlist
	    	seat_at_table
	   	serving
	   		take_order
	   		server_order
````



### 路径path

你想要找到模块中的某个东西，需要知道他的路径。类似命名空间

路径有两种形式：

- 绝对路径：从crate root开始，使用crate名或者字面值crate
- 相对路径：从当前某块开始，使用self，super或当前模块的标识符（？？）

路径至少由一个标识符组成，标识符之间使用::

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    crate::front_of_house::hosting::add_to_waitlist();  // 绝对路径。crate作为当前文件的称呼
    front_of_house::hosting::add_to_waitlist(); // 相对路径
}
```

#### 私有边界

模块不仅可以组织代码，还可以定义私有边界

如果想把**函数**或**struct**等设为私有，可以将它放到莫个模块中。

rust 中所有的条目（函数，方法，struct，enum，模块，常量）**默认**是**私有的**，不能被外部访问。

将这些结构默认都为私有的，是为了区分哪些是私有的哪些是公共的，并且默认的将所有内部实现的细节都隐藏了起来，只有当需要公开的时候才开放接口。

```rust
mod front_of_house { // 私有的
    mod hosting {  // 私有的
        fn add_to_waitlist() {}  // 私有的
    }
}

pub fn eat_at_restaurant() {  // 公共的
    crate::front_of_house::hosting::add_to_waitlist();  // 这里会报错，说hosting是私有的 
    front_of_house::hosting::add_to_waitlist();  // 这里会报错，说hosting是私有的
}
```

```
 --> src/lib.rs:8:28
  |
8 |     crate::front_of_house::hosting::add_to_waitlist();
  |                            ^^^^^^^ private module
  |
```

父模块无法访问子模块中的私有条目

子模块里可以使用所有祖先模块中的条目

#### pub关键字

使用pub关键字将某些条目标记为公共的

```rust
mod front_of_house { // 这里不用加是因为他在根级的路径下，同级别的其他内容可以调用他
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    crate::front_of_house::hosting::add_to_waitlist(); // 这时就不会报错了，因为将他们都设置为公共的了
    front_of_house::hosting::add_to_waitlist();
}
```

#### super关键字

super：用来访问父级模块路径中的内容，类似于文件系统的`..`，这是在相对路径中使用的，绝对路径不需要这个。

```rust
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order(); // 这里就使用了super来调用模块外的函数
        crate::serve_order(); // 使用绝对路径 
    }
    
    fn cook_order() {}
}
```

#### pub struct

- pub 放在struct前
- struct是公共的  struct的字段默认是私有的

这也是在模块中 使用的，（放在根结构中就不需要了，除非要在其他文件中访问吧？）

`````rust
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast { // 创建struct的实例方法
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    let mut meal = back_of_house::Breakfast::summer("Rye");
    meal.toast = String::from("Wheat");  // 因为toast是public的，所以能够修改它
    println!("I'd like {} toast please", meal.toast);
    meal.seasonal_fruit = String::from("blueberries"); // 而seasonal_fruit 不是public的不能修改它
}
`````

#### pub enum

当将pub方在枚举前时，整个枚举就都是公共的了，这与struct不同了。

```rust
mod back_of_house {
    pub enum Appetizer {
        Soup,
        Salad,
    }
}
```

#### use 关键字

使用use关键字将路径导入到作用域内，这仍然遵守私有性规定，(导入的模块需要是公有的，导入之后，在当前的模块仍然是私有的，除非加上`pub`关键字)

并且 依然可以使用相对路径进行引用

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;  // 从此以后可以直接使用hosting。绝对路径
use front_of_house::hosting;  // 相对路经

pub use crate::front_of_house::hosting; // 使用pub使其可以从其他文件调用hosting

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist()();
}
```



#### as关键字

`as` 关键字可以为引入的路径指定本地的**别名**

````rust
use std::io::Result as IoResult;
````



### 使用外部包 package

1. 在`cargo.toml`中添加依赖包的名称和版本
2. 使用`use`将特定的条目引入作用域

```rust
[dependencies]
rand = "0.5.5"
```

标准库（`std`）也被当作外部包使用。但是不用 在`cargo.toml`中添加std包

使用嵌套路径整理重复的导入

`路径相同的部分::{路径差异的部分}`

```rust
use std::{cmp::Ordering, io};
use std::io::{self, Write}; // self用来引用自己，即前面公共的部分（std::io）
```

通配符 * 。和python中的功能相同。

````rust
use std::io::*
````



将模块的内容移动到其他文件

模块定义时，如果模块名后面是`;`，而不是代码块

- rust会从与模块同名的文件中加载内容
- 模块树的结构不会变化



















