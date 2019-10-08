# 流畅的python



## 字典和集合

与字典有关的内置函数都在

```
__builtins__.__dict__
```

模块中。字典是依赖散列表的，还有集合也是如此。

- 常见的字典方法
- 如何处理查找不到的键
- 标准库中 dict 类型的变种 set 和 frozenset 类型
- 散列表的工作原理
- 散列表带来的潜在影响（什么样的数据类型可作为键、不可预知的 顺序，等等）

在`collections.abc`模块中有一个`MutableMapping`和他的超类`UML`。这两个类主要是作为形式化的文档使用，作为最基本的接口模板。

要使一个对象是可散列的，需要实现`__hash__()`和`__qe__()`方法。

注意不仅仅由列表推导式，还有字典推导式。

书中主要探讨了三个字典类

```
dict
collections.defaultdict   
collections.OrderedDict  输入键值对是有顺序的
```

他们包含了很多通用的方法，这些方法见书p137。

其中使用频率最高的应该是`setdefault`这个函数。这个函数会处理找不到的建，如果给定的建找不到，那么他会创建这个键并赋予相应的默认值。如果找到了，会返回设定的默认值。

我觉得这个功能最有用的地方就是对字典的初始化了。就相当字典键带有了初始化值。

### 如何处理找不到的键

1. 使用`defaultdict`这个类型。
2. 子类化`dict`类。

`defaultdict`位于`collections`模块中，即`collections.defaultdict`。

在实例化一个 defaultdict 的时候，需要给构造方法提供 一个可调用对象，这个可调用对象会在 `__getitem__` 碰到找不到的键的时候被调用，让 `__getitem__` 返回某种默认值。 

```python
import collections
defaultdict = collections.defaultdict(list)
```

这样`defaultdict`在找不键的时候就会返回一个列表（注意list是个可调用对象）。

这个用来生成默认值的可调用对象存放在名为 default_factory 的 实例属性里。这个default_factory只在`__getitem__`里面被调用，在其他的地方并不发挥作用。

背后主要是使用了`__missing__`方法。所有映射类型在找不到键的情况下都会调用`__missing__`方法（不过dict类并没有实现这个方法）。**`__missing__`方法只会被`__getitem__`调用**。

### 其他的字典类型

`collections.OrderdDict`这个类会在添加键的时候保持顺序，因此键的迭代次序总是一致的。

`collections.ChainMap`容纳多个不同的映射对象，然后在进行键查找的时候，这些对象会被当做一个整体逐个查找，直到键被找到为止。

```
collections.ChainMap(locals(),globals(),vars(builtins))
```

`collections.Counter`这个类型，给键准备了一个整数计数器。每次更新一个键的时候都会增加这个计数器。

`collections.UserDict`只是将dict类用python实现了一遍，但为什么这样做？主要是为了能够使这个类更方便继承，使用户写出自己的字典类。

## 集合

集合中的元素也是可散列的。set集合本身是不可散列的，但是frozenset可以。

纯集合的运算也是相当快速的。而且集合实现了很多中缀表达式。

```
a | b,   a & b,    a - b
```

p161列出了很多集合所能做出的操作。

集合的操作很高效的原因是散列表，但是散列表是使用空间换时间的。他在空间上消耗很大，因为散列表是一个稀疏矩阵。当向散列表中添加键的时候，有可能是需要扩充散列表的大小的，此时键的顺序就会发生改变。而且在计算哈希值的时候有可能计算出的哈希值是相似的，那就需要再计算一次。

## 一等函数

一等函数的标准是

- 运行时创建
- 能赋值给变量或数据结构中的元素
- 能作为参数传给函数
- 能作为函数的返回结果

### 高阶函数

接受函数为参数，或者把函数作为结果返回的函数就是高阶函数。

例如map和sorted就是高阶函数。



### 匿名函数

匿名函数就是lambda表达式，在python中匿名函数是有限制的，只能使用纯表达式。这正使他能够方便的使用在参数列表中

```python
sorted(fruits, key=lambda word: word[::-1])
```



###  可调用对象

在python中有7中可调用对象，

1. 用户定义的函数：def     lambda表达式
2. 内置函数：
3. 内置方法：
4. 方法：在类中定义的函数
5. 类：
6. 类的实例：
7. 生成器函数：

判定一个对象是否是可调用的只要使用callable()



### 用户定义的可调用对象

只要实现了`__call__`实例方法，任何对象都可以是可调用的。

```python
import random
class BingoCage:
	def __init__(self,items):
	    self._items = list(items)
	    random.shuffle(self._items)
	def pick(self):
	    try:
	        return self._items.pop()
	    except IndexError:
	        raise LookupError("pick from empty BingoCage")
	def __call__(self):
	    return self.pick()
```

bingo.pick()的快捷方式是bingo()

### 函数内省

我理解的内省就是  向内做检查，即自我检查。



#### 定位参数和仅限关键字参数

````python
def tag(name, *content, cls=None, **sttrs)；
    """生成一个或者多个HTML标签"""
	if cls is not None:
        attrs["class"] = cls
    if attrs:
        attr_str = ''.join('%s=%s'%(attr，value) 
                               for attr, value in sorted(attrs.items()))
    else:
        attr_str = ''
    if content:
        return '\n'.join('<%s%s>%s<%s>'%(name,attr_str,c,name) for c in content)
    else:
         return '<%s%s />' % (name,sttr_str)
````

输出效果

```html
tag('br') >>>> '<br />' 这个相当于，cls=None content=None sttrs=None 直接执行最后一句

 tag('p', 'hello') >>>>  '<p>hello</p>'  此时相当于name 和content有值。执行倒数第二句，一个生成器。不断换c的值
 
 tag('p', 'hello', 'world') >>>>  <p>hello</p>       <p>world</p>  如上，此时就生成了两个
 
 tag('p', 'hello', id=33)  >>>> <p id="33">hello</p>   id=33 就用到了关键字参数。解包之后使用
 
 tag('p', 'hello', 'world', cls='sidebar') >>>>  <p class="sidebar">hello</p> 
                                                 <p class="sidebar">world</p> 
                                                 
 tag(content='testing', name="img")   >>>>   <img content="testing" />
 
 my_tag = {'name': 'img', 'title': 'Sunset Boulevard', 'src': 'sunset.jpg', 'cls': 'framed'} 
  tag(**my_tag) 
 <img class="framed" src="sunset.jpg" title="Sunset Boulevard" />
 
```

第一个参数后任意个参数会被*content捕获，存入一个元组。没有明确指定名称的参数，会被**attrs捕获，存入一个字典。而cls参数只能作为关键字参数传入。它一定不会捕获未命名的定位参数。

定义函数时若想指定仅限关键字参数，要把它们放到前面有 * 的**参数**后面。如果不想支持数量不定的位置参数，但是想支持仅限关键字参数，在签名中放 一个 *

````
def f(a,*,b):
    return a,b
````

### 获取参数的信息

在函数对象中有一个`__defaults__`属性，他的值是元组，其中存放的是**位置参数和关键字参数**的***默认值***。而仅限关键字参数的默认值是放在`__kedefaults__`中的。

但是参数的名称是放在`__code__`属性中的。在code中参数的名称是放在`__code__.co_varnames`中的，但是其中还存放了函数中创建的局部变量。函数参数是`__code__.co_varnames`中前N个数，N的数值是由`__code__.co_argcount`所确定的。

python中有一个模块———inspect，来处理相同功







### 装饰器

装饰器的基本知识

- python如何计算装饰器句法
- python如何判断变量是不是局部的
- 闭包存在的原因和工作原理
- nonlocal能解决什么问题

更近一步

- 如何实现行为良好的装饰器
- 标准库中有用的装饰器
- 实现一个参数化的装饰器

-----------------------

1.装饰器的用法

```python
@decorate
def target():
    print("running target()")
```

上述代码如下写法相同

```python
def targer():
   print("running target()")

target = decorate(target)  # 以目标函数为参数，再返回与目标函数同名的函数，但实际上函数名已经绑定到其他地址了。
```

装饰器的一大特性是：将被装饰的函数换成其他函数。第二个特性是：装饰器**在加载模块时立即执行**。

```python
registry = []
def register(func):
    print("running register(%s)" % func)
    registry.append(func)
    return func
@register
def f1():
    print("running f1()")
    
@register
def f2():
    print("running f2()")
    
def f3():
    print("running f3()")
    
def main():
    print("running main()")
    print("register ->", register)
    f1()
    f2()
    f3()
if __name__ == ""
```

在真正代码中装饰器有几个特性：

- 装饰器函数与被装饰函数通常是分开定义的。装饰器通常在一个模块中定义，然后应用到其他模块中的函数上。
- 装饰器返回的函数，通常与传入的函数不是同意个函数。大多数情况下，装饰器会在内部定义一个函数，然后将其返回。

2.变量作用域规则

python不要求声明变量，但是假定在函数定义体中赋值的变量是局部变量。

3.闭包

闭包是指延伸了作用域的函数，其中包含函数定义体中  引用了、但是不在定义体中定义的非全局变量。函数是不是匿名没有关系，关键是他能够访问定义体之外定义的非全局变量。

```python
def make_averager()
  # --------闭包---------------
	series = []  
    def averager(new_value)
    	series.append(new_value)  # 自由变量series
        total = sum(series)
        return total/len(series)
  # --------闭包----------------
```

4.nonlocal 的变量作用域

```python
def make_averager():
    count = 0
    total = 0
    def averager(new_value):
        count += 1
        total += new_value
        return total / count
```

上面的函数是对之前函数的改进，但是，在运行过程中会出错。因为对 count+=1 会使他变为局部变量，total也相同。因此python中引入了nonlocal关键字，来处理局部和全局变量之间的那层变量。

- nonlocal的作用是把变量标记为自由变量，即使在函数中为变量赋予新值，也不会改变自由变量


更新如下

```python
def make_averager():
    count = 0
    total = 0
    def averager(new_value):
        nonlocal count, total
        count += 1
        total += new_value
        return total / count
```

5.一个简单的例子

```python
# clockdeco.py
import time

def clock(func):
    def clocked(*args):
        t0 = time.perf_counter()
        result = func(*args)
        elapsed = time.perf_counter() - t0
        name = func.__name__
        arg_str = ", ".join(repr(arg) for arg in args)
        print("[%0.8fs] %s(%s) -> %r" % (elapsed, name, arg_str, result))
        return result
    return clocked
```

将一个函数的运行的时间，传入的参数和调用的结果显示出来。

```python
import time
from clockdeco import clock
@clock
def snooze(seconds):
    time.sleep(seconds)
    
@clock
def factorial(n):
    return 1 if n < 2 else n*factorial(n-1)

if __name__ == "__main__":
    print("*" * 40, "calling snooze(.123)")
    snooze(.123)
    print("*" * 40, "calling factorial(6)")
    print("6! =", factorial(6))
```

这是装饰器比较经典的作用，把装饰的函数替换成新的函数，二者接受相同的参数，而且返回被装饰的函数本该返回的值，同时还会做一些额外的操作。

上面clock装饰器，掩盖了`__name__`  和 `__doc__` 属性。而functool.wraps 装饰器能把相关属性从func复制到clocked中，而且还能正确处理关键字参数，这是一种加强的操作。

```python
import time
import functools

def clock(func):
    @functools.wraps(func)
    def clocked(*args,**kwargs):
        t0 = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - t0
        name = func.__name__
        arg_lst = []
        if args:
            arg_lst.append(", ".join(repr(arg) for arg in args))
        if kwargs:
            pairs = ["%s=%r" % (k, w) for k, w in sorted(kwargs.items())]
            arg_lst.append(", ".join(arg_lst))
        arg_str = ", ".join(arg_lst)
        print("[%0.8fs] %s(%s) -> %r" % (elapsed, name, arg_str, result))
        return result
    return clocked
```

6.标准库中的装饰器

**functools.lru_cache**

他实现了备忘功能，**他把耗时的函数的结果保存起来**，避免传入相同的参数时重复计算。LRU是"Least Recently Used"的缩写，表明缓存不会无限制增长，一段时间不用的缓存条目会被扔掉。

生成第n个斐波那契数这种慢速递归函数特别适合使用lru_cache。

```python
import functools
from clockdeco import clock

@functools.lru_cache()  # 必须有一对括号
@clock
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-2) + fibonacci(n-1)

if __name__ == "__main__":
    print(fibonacci(6))
```

lru_cache可以使用两个参数。

```python
functools.lru_cache(maxsize=128, typed=False)
```

maxsize参数指定存储多少个调用的结果（最好是2的幂次方）。缓存满了之后，旧的结果会被抛弃。

typed参数如果设为True，会把不同参数类型得到的结果分开保存，即把通常认为相等的浮点数和整数参数（如1和1.0）区分开。

因为lru_cache使用字典存储结果，而且键根据调用时传入的定位参数和关键字参数创建，所以被lru_cache装饰的函数，他的所有参数都必须是可散列的。



**functools.singledispatch**

单分派泛函数

类似于重载函数，但python本身并不存在方法重载。

```python
import html
def htmlize(obj):
    content = html.escape(repr(obj))
    return "<pre>{}</pre>".format(content)
```

在Python中，一种常见的做法是把htmlize变成一个分派函数，使用一串if/elif/elif，调用专门的函数，如htmlize_str、htmlize_int等。这样不便于模块的用户扩展，还很笨。时间一长，分派函数htmlize会变得很大，而且他与各个专门函数之间的耦合也很紧密。

```python
from functools import singledispatch
from collections import abc
import numbers
import html

@singledispatch  # singledispatch标记处理object类型的基函数
def htmlize(obj):
    content = html.escape(repr(obj))
    return "<pre>{}</pre>".format(content)

@htmlize.register(str)  # 各个专门函数使用<<base_function>>.register(<<type>>)装饰
def _(text):
    content = html.escape(text).replace("\n", "<br>\n")
    return "<p>{0}</p>".format(n)

@htmlize.register(numbers.Integral)  # numbers.Integral是int的虚拟超类
def _(n):
    return "<pre>{0} (0x{0:x})</pre>".format(n)

@htmlize.register(tuple)  # 可以叠放多个register装饰器，让同一个函数支持不同类型
@htmlize.register(abc.MutableSequence)
def _(seq):
    inner = "</li>\n<li>".join(htmlize(item) for item in seq)
    return "<ul>\n<li>" + inner + "</li>\n<ul>"

```

注册的专门函数应该处理抽象基类（如numbers.Integral和abc.MutableSequence），不要处理具体实现(int和list)。

singledispatch机制的一个显著的特征是，你可以在系统的任何地方和任何模块中注册专门函数。如果后来在新的模块中定义了新的类型，可以轻松地添加一个新的专门函数来处理那个类型。此外，你还可以为不是自己编写的或者不能修改的类添加自定义函数。

7.参数化装饰器

怎么让装饰器接受其他参数？创建一个装饰器工厂函数，把参数传给他，返回一个装饰器，然后再把它应用到要装饰的函数上。

下面的函数不是装饰器，而是装饰器工厂函数。调用他会返回真正的装饰器，这才是应用到目标函数上的装饰器。

```python
register = set()
def register(active=True):
    def decorate(func):
        print("running register(active=%s)->decorate(%s)" % (active, func))
    	if active:
        	register.add(func)
    	else:
        	register.discard(func)
    	return func
    return decorate

@register(active=False)  # 工厂函数必须作为函数调用，并且传入所需的参数。
def f1():
    print("running f1()")

@register()  # 必须作为函数调用
def f2():
    print("running f2()")

def f3():
    print("running f3()")

```



参数化clock装饰器

添加一个功能，让用户传入一个格式化字符串，控制被装饰函数的输出。

```python
import time
DEFAULT_FMT = "[{elapsed:0.8f}s] {name}({args}) -> {result}"

def clock(fmt = DEFAULT_FMT):  # 装饰器工厂函数。。这一层只是为了能够使装饰器有参数
    def decorate(func):  # 真正的装饰器。。 从func看出，这一层使用要传入要包装的函数的
        def clocked(*_args):  # 包装被装饰的函数。。而这一层是为了能够使用被包装函数的参数
            t0 = time.time()
            _result = func(*_args)
            elapsed = time.time() - t0
            name = func.__name__
            args = ", ".join(repr(arg) for arg in _args)
            result = repr(_result)
            print(fmt.format(**locals()))
            return _result  # 真正被返回的值
        return clocked
    return decorate
if __name__ == "__main__":
    @clock()
    def snooze(seconds):
        time.sleep(seconds)
    
    for i in range(3):
        snooze(.123)
    
    @clock("{name}:{elapsed}s")
    def snooze(seconds):
        time.sleep(seconds)
    for i in range(3):
        snooze(.123)
```



## 对象引用、可变性和垃圾回收机制

### 变量不是盒子

变量不是盒子是说：变量不是实际的装有数据，而是说，变量引用了数据。至少对python来讲是这样的。

对引用式变量来说，把变量分配给对象更为合理，反过来说就有问题。这是说，把变量分配给一个对象，通过这个变量来使用这个对象。

此时使用**标注**来形容变量，便比较恰当。这就是对实际的对象或者数据打上了一个标签，而标签可以有很多个。此时这些标签就是别名。



### 标识、相等性、别名

```python
charles = {"name":"Cher Le", "born":"1999"}  
lewis = charles  # lewis 相当于是别名
lewis is charles  # 两者是相等的
id(lewis) == id(charles)  # 两者的标识是相等的。也就是id相同。

alex = {"name":"Cher Le", "born":"1999"}  
charles == alex  # 两者的值相同
charles is not alex  # 但是两者并不是同一个对象
```

每个变量都有标识、类型和值，对象一旦建立，他的标识是不会变的。

由上面的比较就引出了对`is`和`==`比较的问题。

`==`使用的是`__eq__`方法来检查两者是不是相同。而is是使用id()函数判断两者是不是同一个对象。比较来说，is会比`==`快。但是更多的情况是，比较两个变量的值是不是相同，而不是判断两个变量是不是同一个对象。

这个时候就引出了元组可变与不可变的问题。

### 元组的相对不可变性

元组是不可变的，但是如果元组内有类似列表一样的可变元素的时候，可以向这个列表中添加值，原则就是可变的了。主要原因应该还是因为元组中其实存储的是列表的首地址，这个地址本质还是没变的。

```python
t1 = (1, 2, [30, 40])
```



### 深复制和浅复制

默认情况下是做浅复制的，浅复制本质上没有问题，但是如果复制的对象中存在可变对象的话，会有影响。因为其中的可变元素可能还是引用同一个对象。

```
[3, [123, 444], (214,553)]
```

那么这就引出如何做深复制？

使用copy中的deepcopy()函数。

```python
import copy
a = copy.deepcopy(b)
```















