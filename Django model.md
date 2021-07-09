# Django model

记录model从最简单的逻辑实现出发，慢慢的变为使用更多封装，和更多功能的模块。

如果我们要从数据库中拿取数据怎么办？使用pymysql等引擎去拿取。如下

```python
from django.shortcuts import render_to_response
import MySQLdb

def book_list(request):
    db = MySQLdb.connect(user='me', db='mydb', passwd='secret', host='localhost')
    cursor = db.cursor()
    cursor.execute('SELECT name FROM books ORDER BY name')
    names = [row[0] for row in cursor.fetchall()]
    db.close()
    return render_to_response('book_list.html', {'names': names})
```

这会有一些问题， 1）代码中链接数据库的代码会是重复的。2）与mysql绑定了，换了数据库就要改代码。

当然，Django一定是提供了数据库API的。下来就来看看。

------------------------------------------------------数据库设置之后补上------------------------------------------------------

## 第一步：定义数据库字段

```python
from django.db import models

class Publisher(models.Model):
    name = models.CharField(max_length=30)
    address = models.CharField(max_length=50)
    city = models.CharField(max_length=60)
    state_province = models.CharField(max_length=30)
    country = models.CharField(max_length=50)
    website = models.URLField()

class Author(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=40)
    email = models.EmailField()

class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher)
    publication_date = models.DateField()
```

每个数据模型都是 `django.db.models.Model` 的子类。它的父类 Model 包含了所有必要的和数据库交互的方法，并提供了一个简洁漂亮的定义数据库字段的语法。每个模型相当于单个数据库表，每个属性也是这个表中的一个字段。 属性名就是字段名，它的类型（例如 `CharField` ）相当于数据库的字段类型 （例如 `varchar` ）。

除非你单独指明主键，否则Django会自动为每个模型生成一个自增长的整数主键字段每个Django模型都要求有单独的主键`id`。django为主键单独起了个名字`pk`，调用它就可以找到主键。



## 第二步：设置字符串表示

```python
class Publisher(models.Model):
    name = models.CharField(max_length=30)
    address = models.CharField(max_length=50)
    city = models.CharField(max_length=60)
    state_province = models.CharField(max_length=30)
    country = models.CharField(max_length=50)
    website = models.URLField()
    
    def __unicode__(self):
        return self.name

class Author(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=40)
    email = models.EmailField(verbose_name="e-mail")

    def __unicode__(self):
        return u'%s %s' % (self.first_name, self.last_name)
    def __str__(self):
        return u'%s %s' % (self.first_name, self.last_name)

class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    publication_date = models.DateField(blank=True, null=True)

    def __unicode__(self):
        return self.title
```

如同python中一样，只要指定了dunder方法，就可以控制实例的显示方式。

Author 中Email字段中的verbose_name指定在控制界面这个字段的别名显示是什么。如果不指定，默认使用变量名。



## 第三步：对字段的限制

```python
class Author(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=40)
    email = models.EmailField(blank=True, verbose_name="e-mail")

    def __unicode__(self):
        return u'%s %s' % (self.first_name, self.last_name)
    def __str__(self):
        return u'%s %s' % (self.first_name, self.last_name)
```

默认情况下，所有字段都是需要填写的，即 NOT NULL，但是当你指定了blank这个参数的时候，这个字段就可以不用赋值，Django对字符型的字段会插入一个空字符串“占位”。空字符串和None还有NULL是不同的。我们也可以指定当不填入值是自动补充NULL值。如下：

```python
class Author(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=40)
    email = models.EmailField(blank=True, null=True, verbose_name="e-mail")
```

不过一定要注意，**指定null之后，其中不会再出现空字符串了，这两个是互不兼容的**。

为什么会有null，因为有些时候你不知道填充什么好，所以就填个null。比如日期，填什么都不是很合适，那就用null。

**总结**：当允许某个字段为空时，1）指定blank，自动填入一个占位值。2）指定null，直接插入NULL表示字段为空。二取其一。





## 如何使用

### 插入数据

向数据库中插入数据，只需要实例化一个数据库表的对象即可。真正插入数据库，需要执行特定的函数，在此之前数据是没有正真插入的。

```python
p = Publisher(
    name='Apress', 
    address='2855 Telegraph Ave.', 
    city='Berkeley', 
    state_province='CA', 
    country='U.S.A.', 
    website='http://www.apress.com/')  # 实例化对象
p.save()  # 插入数据
```



### 查询数据

查询是使用最多而且也是方法最多的。需要各种各样的过滤。先从查询所有数据开始，之后再探讨各种过滤方式。

```python
Publisher.objects.all()
```

`objects`属性。 它被称为管理器，管理着所有针对数据包含、还有最重要的数据查询的表格级操作。所有的模型都自动拥有一个 `objects` 管理器；你可以在想要查找数据时使用它。

#### 数据过滤

```python
Publisher.objects.filter(name='Apress', city='U.S')
Publisher.objects.filter(name__contains="press")  # 使用双下划线代表魔术方法，contains表示LIKE

Publisher.objects.get(name="Apress")  # 获取单个对象

Publisher.objects.order_by("name"， "-city")  # 排序。 指定多个字段，-代表反序
```

#### 限制返回的数据

类似于切片，但是不支持负索引。

```python
Publisher.objects.order_by('name')[0]
Publisher.objects.order_by('name')[1:3]
```



### 更新数据

尽量使用update来更新数据，因为这样更高效。

```python
Publisher.objects.filter(id=52).update(name='Apress Publishing')
```



### 删除数据

删除数据，只要调用delete函数即可。

```python
p = Publisher.objects.get(name="O'Reilly")
p.delete()
```

如果要删除所有数据需要显示的调用all()方法，这是一种防范机制

```python
Publisher.objects.filter(country='USA').delete()
Publisher.objects.all().delete()
```



