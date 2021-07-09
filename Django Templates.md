# Django Templates

记录模板的用法和语法

```html
<html>
<head><title>Ordering notice</title></head>

<body>

<h1>Ordering notice</h1>

<p>Dear {{ person_name }},</p>

<p>Thanks for placing an order from {{ company }}. It's scheduled to
ship on {{ ship_date|date:"F j, Y" }}.</p>

<p>Here are the items you've ordered:</p>

<ul>
{% for item in item_list %}
    <li>{{ item }}</li>
{% endfor %}
</ul>

{% if ordered_warranty %}
    <p>Your warranty information will be included in the packaging.</p>
{% else %}
    <p>You didn't order a warranty, so you're on your own when
    the products inevitably stop working.</p>
{% endif %}

<p>Sincerely,<br />{{ company }}</p>

</body>
</html>
```

用两个大括号括起来的文字（例如 `{{ person_name }}` ）称为 *变量(variable)* 。这意味着在此处插入指定变量的值。 如何指定变量的值呢？ 稍后就会说明。

被大括号和百分号包围的文本(例如 `{% if ordered_warranty %}` )是 *模板标签(template tag)* 。标签(tag)定义比较明确，即： 仅通知模板系统完成某些工作的标签。

这个例子中的模板包含一个`for`标签（ `{% for item in item_list %}` ）和一个`if` 标签（`{% if ordered_warranty %}` ）

for标签类似Python的for语句，可让你循环访问序列里的每一个项目。 `if` 标签，正如你所料，是用来执行逻辑判断的。 在这里，tag标签检查ordered_warranty值是否为True。如果是，模板系统将显示`{% if ordered_warranty %}和{% else %}`之间的内容；否则将显示`{% else %}`和`{% endif %}`之间的内容。`{% else %}`是可选的。

最后，这个模板的第二段中有一个关于*filter*过滤器的例子，它是一种最便捷的转换变量输出格式的方式。 如这个例子中的`{{ship_date|date:”F j, Y” }}`，我们将变量ship_date传递给date过滤器，同时指定参数”F j,Y”。date过滤器根据参数进行格式输出。 过滤器是用管道符(|)来调用的，具体可以参见Unix管道符。

## 如何使用模板系统

模板系统也是一个独立的系统，我们可以单独使用它。

在Python代码中使用Django模板的最基本方式如下：

1. 可以用原始的模板代码字符串创建一个 `Template` 对象， Django同样支持用指定模板文件路径的方式来创建 `Template` 对象;
2. 调用模板对象的render方法，并且传入一套变量context。它将返回一个基于模板的展现字符串，模板中的变量和标签会被context值替换。

```python
from django import template

t = template.Template('My name is {{ name }}.')  # 设定模板
c = template.Context({'name': 'Adrian'})  # 设定参数
print t.render(c)  # 渲染
>>> My name is Adrian.
```

### 方法调用行为

方法调用比其他类型的查找略为复杂一点。 以下是一些注意事项：

> 在方法查找过程中，如果某方法抛出一个异常，除非该异常有一个 `silent_variable_failure` 属性并且值为 `True` ，否则的话它将被传播。如果异常被传播，模板里的指定变量会被置为空字符串，比如:

```python
t = Template("My name is {{ person.first_name }}.")
>>> class PersonClass3:
...     def first_name(self):
...         raise AssertionError, "foo"
>>> p = PersonClass3()
>>> t.render(Context({"person": p}))
Traceback (most recent call last):
...
AssertionError: foo

>>> class SilentAssertionError(AssertionError):
...     silent_variable_failure = True
>>> class PersonClass4:
...     def first_name(self):
...         raise SilentAssertionError
>>> p = PersonClass4()
>>> t.render(Context({"person": p}))
u'My name is .'
```

仅在方法无需传入参数时，其调用才有效。 否则，系统将会转移到下一个查找类型（列表索引查找）。

显然，有些方法是有副作用的，好的情况下允许模板系统访问它们可能只是干件蠢事，坏的情况下甚至会引发安全漏洞。

例如，你的一个 `BankAccount` 对象有一个 `delete()` 方法。 如果某个模板中包含了像 `{{ account.delete }}`这样的标签，其中`` account`` 又是`BankAccount` 的一个实例，请注意在这个模板载入时，account对象将被删除。

要防止这样的事情发生，必须设置该方法的 `alters_data` 函数属性：

```python
def delete(self):
    # Delete the account
delete.alters_data = True
```

模板系统不会执行任何以该方式进行标记的方法。 接上面的例子，如果模板文件里包含了 `{{ account.delete }}` ，对象又具有 `delete()`方法，而且`delete()` 有`alters_data=True`这个属性，那么在模板载入时， `delete()`方法将不会被执行。 它将静静地错误退出。

### 如何处理无效变量

默认情况下，如果一个变量不存在，模板系统会把它展示为空字符串，不做任何事情来表示失败。 例如：

```python
>>> from django.template import Template, Context
>>> t = Template('Your name is {{ name }}.')
>>> t.render(Context())
u'Your name is .'
>>> t.render(Context({'var': 'hello'}))
u'Your name is .'
>>> t.render(Context({'NAME': 'hello'}))
u'Your name is .'
>>> t.render(Context({'Name': 'hello'}))
u'Your name is .'
```

### 上下文(context)对象

多数时间，你可以通过传递一个完全填充(full populated)的字典给 `Context()` 来初始化 `上下文(Context)` 。 但是初始化以后，你也可以使用标准的Python字典语法(syntax)向``上下文(Context)`` 对象添加或者删除条目:

```python
>>> from django.template import Context
>>> c = Context({"foo": "bar"})
>>> c['foo']
'bar'
>>> del c['foo']
>>> c['foo']
Traceback (most recent call last):
  ...
KeyError: 'foo'
>>> c['newvariable'] = 'hello'
>>> c['newvariable']
'hello'
```

### 基本的模板标签和过滤器

**if /else**

`{% if %}` 标签检查(evaluate)一个变量，如果这个变量为真（即，变量存在，非空，不是布尔值假），系统会显示在 `{% if %}` 和 `{% endif %}` 之间的任何内容，例如：

```django
{% if today_is_weekend %}
    <p>Welcome to the weekend!</p>
{% else %}
    <p>Get back to work.</p>
{% endif %}
```

`{% if %}` 标签接受 `and` ， `or` 或者 `not` 关键字来对多个变量做判断 ，或者对变量取反（ `not` )，例如：

```django
{% if athlete_list and coach_list %}
    Both athletes and coaches are available.
{% endif %}

{% if not athlete_list %}
    There are no athletes.
{% endif %}

{% if athlete_list or coach_list %}
    There are some athletes or some coaches.
{% endif %}
```

**`{% if %}` 标签不允许在同一个标签中同时使用 `and` 和 `or` ，因为逻辑上可能模糊的，例如，**

```django
{% if athlete_list and coach_list or cheerleader_list %}
```

系统**不支持用圆括号**来组合比较操作。 如果你确实需要用到圆括号来组合表达你的逻辑式，考虑将它移到模板之外处理，然后以模板变量的形式传入结果吧。

并没有 `{% elif %}` 标签， 请使用嵌套的`` {% if %}`` 标签来达成同样的效果



**for**

`{% for %}` 允许我们在一个序列上迭代。 与Python的 `for` 语句的情形类似，循环语法是 `for X in Y` ，Y是要迭代的序列而X是在每一个特定的循环中使用的变量名称。 每一次循环中，模板系统会渲染在 `{% for %}` 和 `{% endfor %}` 之间的所有内容。

```django
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% endfor %}
</ul>
```

给标签增加一个 `reversed` 使得该列表被反向迭代：

```django
{% for athlete in athlete_list reversed %}
...
{% endfor %}
```

有一个{% empty %}标签来判断for循环是否一开始就是空的，这种场景是比较普遍的

```django
{% for athlete in athlete_list %}
    <p>{{ athlete.name }}</p>
{% empty %}
    <p>There are no athletes. Only computer programmers.</p>
{% endfor %}
```

Django不支持退出循环操作。 如果我们想退出循环，可以改变正在迭代的变量，让其仅仅包含需要迭代的项目。 同理，Django也不支持continue语句，我们无法让当前迭代操作跳回到循环头部。

**forloop**

在每个`` {% for %}``循环里有一个称为`` forloop`` 的模板变量。这个变量有一些提示循环进度信息的属性。

`forloop.counter` 总是一个表示当前循环的执行次数的整数计数器。 这个计数器是从1开始的，所以在第一次循环时 `forloop.counter` 将会被设置为1。`forloop.counter0`是从0开始计数的。

```django
{% for item in todo_list %}
    <p>{{ forloop.counter }}: {{ item }}</p>
{% endfor %}
```

`forloop.revcounter` 是表示循环中剩余项的整型变量。 在循环初次执行时 `forloop.revcounter` 将被设置为序列中项的总数。 最后一次循环执行中，这个变量将被置1。同样有个`forloop.revcounter0` 

`forloop.first` 是一个布尔值，如果该迭代是第一次执行，那么它被置为`True`

`forloop.last` 是一个布尔值；在最后一次执行循环时被置为True。 一个常见的用法是在一系列的链接之间放置管道符（|）

```django
{% for link in links %}{{ link }}{% if not forloop.last %} | {% endif %}{% endfor %}

Link1 | Link2 | Link3 | Link4
```

`forloop.parentloop` 是一个指向当前循环的上一级循环的 `forloop` 对象的引用（在嵌套循环的情况下）。

```django
{% for country in countries %}
    <table>
    {% for city in country.city_list %}
        <tr>
        <td>Country #{{ forloop.parentloop.counter }}</td>
        <td>City #{{ forloop.counter }}</td>
        <td>{{ city }}</td>
        </tr>
    {% endfor %}
    </table>
{% endfor %}
```

`forloop` 变量仅仅能够在循环中使用。 在模板解析器碰到`{% endfor %}`标签后，`forloop`就不可访问了。



`{% ifequal %}` 标签比较两个值，当他们相等时，显示在 `{% ifequal %}` 和 `{% endifequal %}` 之中所有的值。

```django
{% ifequal user currentuser %}
    <h1>Welcome!</h1>
{% endifequal %}
```

和 `{% if %}` 类似， `{% ifequal %}` 支持可选的 `{% else%}` 标签：

```django
{% ifequal section 'sitenews' %}
    <h1>Site News</h1>
{% else %}
    <h1>No News Here</h1>
{% endifequal %}
```

只有模板变量，字符串，整数和小数可以作为 `{% ifequal %}` 标签的参数。下面是合法参数的例子：

```django
{% ifequal variable 1 %}
{% ifequal variable 1.23 %}
{% ifequal variable 'foo' %}
{% ifequal variable "foo" %}
```



注释

就像HTML或者Python，Django模板语言同样提供代码注释。 注释使用 `{# #}` ：

```django
{# This is a comment #}
```



过滤器

就象本章前面提到的一样，模板过滤器是在变量被显示前修改它的值的一个简单方法。 过滤器使用管道字符，如下所示：

```django
{{ name|lower }}
```

显示的内容是变量 `{{ name }}` 被过滤器 `lower` 处理后的结果，它功能是转换文本为小写。

过滤管道可以被* 套接* ，既是说，一个过滤器管道的输出又可以作为下一个管道的输入，如此下去。 下面的例子实现查找列表的第一个元素并将其转化为大写。

```django
{{ my_list|first|upper }}
```

有些过滤器有参数。 过滤器的参数跟随冒号之后并且总是以双引号包含。 例如：

```django
{{ bio|truncatewords:"30" }}
```



include

 `{% include %}` 。该标签允许在（模板中）包含其它的模板的内容。 标签的参数是所要包含的模板名称，可以是一个变量，也可以是用单/双引号硬编码的字符串。 每当在多个模板中出现相同的代码时，就应该考虑是否要使用 `{% include %}` 来减少重复。但是感觉用的多的使模板继承。

```
# mypage.html

<html>
<body>
{% include "includes/nav.html" %}
<h1>{{ title }}</h1>
</body>
</html>

# includes/nav.html

<div id="nav">
    You are in: {{ current_section }}
</div>
```



模板继承

模板继承就是先构造一个基础框架模板，而后在其子模板中对它所包含站点公用部分和定义块进行重载。

先看那个返回时间的模板

```django
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
<html lang="en">
<head>
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    <h1>My helpful timestamp site</h1>
    {% block content %}{% endblock %}
    {% block footer %}
    <hr>
    <p>Thanks for visiting my site.</p>
    {% endblock %}
</body>
</html>
```

这个叫做 `base.html` 的模板定义了一个简单的 HTML 框架文档，我们将在本站点的所有页面中使用。 子模板的作用就是重载、添加或保留那些块的内容。 所有的 `{% block %}` 标签告诉模板引擎，子模板可以重载这些部分。 每个`{% block %}`标签所要做的是告诉模板引擎，该模板下的这一块内容将有可能被子模板覆盖。换句话说，没有被覆盖的就原样使用。

子模板1

```django
{% extends "base.html" %}

{% block title %}The current time{% endblock %}

{% block content %}
<p>It is now {{ current_date }}.</p>
{% endblock %}
```

子模版2

```django
{% extends "base.html" %}

{% block title %}Future time{% endblock %}

{% block content %}
<p>In {{ hour_offset }} hour(s), it will be {{ next_time }}.</p>
{% endblock %}
```

使用继承的一种常见方式是下面的三层法：

1. 创建 `base.html` 模板，在其中定义站点的主要外观感受。 这些都是不常修改甚至从不修改的部分。
2. 为网站的每个区域创建 `base_SECTION.html` 模板(例如, `base_photos.html` 和 `base_forum.html` )。这些模板对 `base.html` 进行拓展，并包含区域特定的风格与设计。
3. 为每种类型的页面创建独立的模板，例如论坛页面或者图片库。 这些模板拓展相应的区域模板。

这个方法可最大限度地重用代码，并使得向公共区域（如区域级的导航）添加内容成为一件轻松的工作。

以下是使用模板继承的一些诀窍：

- 如果在模板中使用 `{% extends %}` ，必须保证其为模板中的第一个模板标记。 否则，模板继承将不起作用。

- 一般来说，基础模板中的 `{% block %}` 标签越多越好。 记住，子模板不必定义父模板中所有的代码块，因此你可以用合理的缺省值对一些代码块进行填充，然后只对子模板所需的代码块进行（重）定义。 俗话说，钩子越多越好。

- 如果发觉自己在多个模板之间拷贝代码，你应该考虑将该代码段放置到父模板的某个 `{% block %}` 中。

- 如果你需要访问父模板中的块的内容，使用 `{{ block.super }}`这个标签吧，这一个魔法变量将会表现出父模板中的内容。 如果只想在上级代码块基础上添加内容，而不是全部重载，该变量就显得非常有用了。

- 不允许在同一个模板中定义多个同名的 `{% block %}` 。 存在这样的限制是因为block 标签的工作方式是双向的。 也就是说，block 标签不仅挖了一个要填的坑，也定义了在*父*模板中这个坑所填充的内容。如果模板中出现了两个相同名称的 *{% block %}* 标签，父模板将无从得知要使用哪个块的内容。

- `{% extends %}` 对所传入模板名称使用的加载方法和 `get_template()` 相同。 也就是说，会将模板名称被添加到 `TEMPLATE_DIRS` 设置之后。

- 多数情况下， `{% extends %}` 的参数应该是字符串，但是如果直到运行时方能确定父模板名，这个参数也可以是个变量。 这使得你能够实现一些很酷的动态功能。





