# Django  URLs

记录URLs从最简单的逻辑实现出发，慢慢的变为使用更多封装，和更多功能的模块。

## 第一步：固定`url`

```python
from django.conf.urls import path

urlpatterns = [
    path(''),
]
```

指定了空的地址。

当以使用视图中的逻辑的时候，我们需要将他绑定到一个`url`上。

```python
from django.conf.urls import path, re_path
from mysite.view import hello  # view函数

urlpatterns = [
    path('hello/', hello),
    re_path('^hello/$', hello),
]
```

re_path和path的区别之后补充。

如上，到使用对应的URL时，就会调用相应的view函数。



## 第二步：可变的URL

如果有很多类似的URL地址需要编写，比如

```
hello/zhao/
hello/li/
hello/sun/
···
```

我们不可能穷举他，但是我们可以使用正则表达式来匹配它。这可以表示我们可以不断的加入对应的数据，而不用修改URL地址（针对不同的URL我们可以返回不同的值，也可以返回相同的值）。

```python
urlpatterns = [
    re_path('^hello/\w+/$', hello),
]
```

上面的模式就可以匹配之前的模式（规则同正则表达式吧，这个自己查吧）。

如果想把\w中的内容当作参数传给hello函数，只需要加上圆括号，Django会自动将参数（字符串）传递过去（以位置参数的方式）。

```python
urlpatterns = [
    re_path('^hello/(\w+)/$', hello),  # hello怎么处理，在viwe中做
]
```













