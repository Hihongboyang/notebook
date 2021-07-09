# Django  View

记录View视图从最简单的逻辑实现出发，慢慢的变为使用更多封装，和更多功能的模块。

## 第一步：简单的输出

```python
from django.http import HttpResponse

def hello(request):
    return HttpResponse("Hello world")
```

很简单，只是单纯的返回一个`http`的response就行了。这个函数中用到了什么？他又返回了什么？

使用一个`request`实例做参数：request包含了很多`http`请求的信息，他以类似字典的方式取用数据。他必须是试图函数的第一个参数。

返回了一个`HttpResponse`实例：对我们要返回的内容进行了`http`的封装。

要使一个函数成为视图函数，需要满足上面这两个条件。但是也不是必须的。



## 第二步：根据数据返回Response

```python
from django.http import HttpResponse
import datetime

def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>" % now  # 构造了一段html的响应
    return HttpResponse(html)
```

上面这个`view`函数就将返回纯文字的内容转变成了返回带有`html`标记的东西了。

那每次都这样手写`html`标记？肯定是不合适的。

## 第三步：使用单个函数

```python
def current_datatime(request):
    """展示当前时间"""
    now = datetime.datetime.now()
    fp = open('/home/djangouser/template/mytemplate.html')
    t = template.Template(fp.read())
    fp.close()
    html = t.render(template.Context({'current_date': now}))
    return HttpResponse(html)
```

使用open打开文件然后再渲染显得有些low，而且这些代码是重复的，可以对它进行封装。

Django当然是提供了相应的`API`的，但是首先要告诉程序我们的模板放在哪。`setting.py`中的`TEMPLATE_DIRS`正是这个功能。`TEMPLATE_DIRS`接受一个元组。其中存放模板位置的地址。

模板

```django
<html><body>It is now {{ current_date }}.</body></html>
```

当编写好模板代码，并且设定好路径后，就可以将view函数进行改造了

```python
from django.template.loader import get_template
from django.template import Context
from django.http import HttpResponse
import datetime

def current_datetime(request):
    now = datetime.datetime.now()
    t = get_template('current_datetime.html')
    html = t.render(Context({'current_date': now}))  # 填充，渲染一起完成
    return HttpResponse(html)
```

get_template返回的是Template对象。我们简化了代码，但是还不够简洁，因为还是分了三步。使用一个函数来完成所有事情是最简单的。

隆重推出 render_to_response

```python
from django.shortcuts import render_to_response
import datetime

def current_datetime(request):
    now = datetime.datetime.now()
    return render_to_response('current_datetime.html', {'current_date': now})
```

render_to_response第一个参数是模板文件，第二个参数是要渲染的数据。

当然，当然如果你连字典也不想写，可以这样

```python
def current_datetime(request):
    current_date = datetime.datetime.now()
    return render_to_response('current_datetime.html', locals())
```



