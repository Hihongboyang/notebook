# Django Admin

**管理界面** 是基础设施中非常重要的一部分。 这是以网页和有限的可信任管理者为基础的界面，它可以让你添加，编辑和删除网站内容。 

管理界面的编写大同小异，大多是重复的工作。所以Django提供了一个已经编辑好的admin模块以供使用。它读取你模式中的元数据，然后提供给你一个强大而且可以使用的界面，网站管理者可以用它立即工作。# 

## 将models加入到Admin管理中

将自己的模块加入管理工具中，这样我们就能够通过这个界面添加、修改和删除数据库中的对象了。

```python
@admin.register(Book)  
class BookAdmin(admin.ModelAdmin):
    list_display = ['title', 'publisher', 'publication_date']
    list_filter = ['publication_date',]
    date_hierarchy = 'publication_date'
    ordering = ['-publication_date',]
    fields = ['title', 'authors', 'publisher', ]
    filter_horizontal = ['authors', ]
```

通过装饰器来注册管理类，通过继承获得需要的功能方法。



## 自定义管理页

```python
@admin.register(Book)  
class BookAdmin(admin.ModelAdmin):
    list_display = ['title', 'publisher', 'publication_date']  # 管理页面要战术的字段
    list_filter = ['publication_date',]  # 指定用什么进行筛选
    date_hierarchy = 'publication_date'  # 过滤日期
    ordering = ['-publication_date',]  # 用什么排序
    fields = ['title', 'authors', 'publisher', ]  # 指定那些字段可以修改
    filter_horizontal = ['authors', ]  # 可以多选的框，适合多对多的字段
    search_fields = ['title', 'authors',]  # 指定哪几个字段可以用来检索
    raw_id_fields = ["publisher",]  # 用来处理外键，展示成一个文本框，而不是下拉框
```

