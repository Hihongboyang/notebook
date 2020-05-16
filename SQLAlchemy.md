# SQLAlchemy

sqlalchemy有两种使用方式 Core 和 ORM。这两种方式可以单独使用，也可以混合使用。他们之间最大的区别是把数据看作**模式**还是**业务对象**。

所谓ORM（Object Relational Mapping），就是建立其由Python类到数据库表的映射关系：一个Python实例(*instance*)对应数据库中的一行(*row*)。这种映射包含两层含义，一是实现对象和与之关联的的行的状态同步，二是将涉及数据库的查询操作，表达为Python类的相互关系。引用自https://www.jianshu.com/p/0d234e14b5d3



## 安装

使用pip

```shell
pip install sqlalchemy
```

安装数据库驱动

```shell
psycopg2 针对postgreSQL
pymysql  针对mysql
```



## 使用

连接到数据库需要先创建一个SQLALchemy引擎。

引擎由一个函数创建，函数接收一个连接字符串：

- 数据库类型（postgres，mysql）
- 各数据库类型默认之外的数据库驱动(psycopg2，pymysql)
- 可选的认证细节（用户名和密码）
- 数据库位置（数据库服务的文件或者主机名）
- 数据库服务端口（可选）
- 数据库名（可选）

```python
from sqlalchemy import create_engine
engine = create_engine("sqlite:///cookies.db")
engine1 = create_engine("postgresql+psycopg2://username:password@localhost5432/mydb")
engine2 = create_engine("mysql+pymysql://cookiemonster:chocolatechip@mysql01.monster.internal/cookies",pool_recycle=3600, echo=True)  # 3600秒后断开连接，默认-1不超时
```

> 连接字符串的格式：**`数据库类型+数据库驱动名称://用户名:口令@机器地址:端口号/数据库名`**
>

连接数据库

```python
connection = engine.connect()
```

> 当第一次使用`engine.execute()`或者`engine.connect()`方法被调用时，engine才会建立真正连接，连接到数据库。**一般不直接使用engine**。

当我们使用ORM的时候，其配置过程主要分为两个部分：一是描述我们要处理的数据库表的信息，二是将我们的Python类映射到这些表上。这两个过程在SQLAlchemy中是一起完成的，我们将这个过程称之为**Declarative**。使用Declarative参与ORM映射的类需要被定义成为一个指定基类的子类，这个基类应当含有ORM映射中相关的类和表的信息。这样的基类我们称之为**declarative base class**。在我们的应用中，我们一般只需要一个这样的基类。这个基类我们可以通过`declarative_base`来创建。（引用自https://www.jianshu.com/p/0d234e14b5d3）

> When using the ORM, the configurational process starts by describing the database tables we’ll be dealing with, and then by defining our own classes which will be mapped to those tables. In modern SQLAlchemy, these two tasks are usually performed together, using a system known as [Declarative](https://docs.sqlalchemy.org/en/13/orm/extensions/declarative/index.html), which allows us to create classes that include directives to describe the actual database table they will be mapped to.
>
> Classes mapped using the Declarative system are defined in terms of a base class which maintains a catalog of classes and tables relative to that base - this is known as the **declarative base class**.  Our application will usually have just one instance of this base in a commonly imported module. We create the base class using the [`declarative_base()`](https://docs.sqlalchemy.org/en/13/orm/extensions/declarative/api.html#sqlalchemy.ext.declarative.declarative_base) function, as follows:
>
> 来自官方文档  https://docs.sqlalchemy.org/en/13/orm/tutorial.html#working-with-related-objects

这就是下面例子第6行中这样使用的原因。

使用流程

```python
# 这个例子是ORM的形式写的
from sqlalchemy import Column, String, create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()  # 创建对象的基类

class User(Base):
    __tablename__ = "user"  # 表的名字
    
    id = Column(String(20), primary_key=True)  # 表的结构
    name = Column(String(20))

class School(Base):
    __tablename__ = "school"
    id = Column(String(50), primary_key=True)
    name = Column(String(20))

engine = create_engine("mysql+pymysql://root:pass@localhost:3306/test")  # 初始化数据库连接
DBSession = sessionmaker(bind=engine)  #创建会话对象

session = DBSession()  # 创建一个session对象
new_user = User(id='5',name='Bob')  # 创建一个User对象
session.add(new_user)  # 添加到session
session.commit()  # 提交保存到数据库
session.close()  # 关闭session

# 此例来自https://www.liaoxuefeng.com/wiki/1016959663602400/1017803857459008
```





## 下面是使用SQLAlchemy Core的方式处理数据



## 第一章 模式和类型

数据表存储什么数据？数据之间如何关联？数据上有什么约束？

为了访问数据库需要一个东西代表**数据表**。有三种方式：

- 用户定义的Table对象
- 使用代表数据表的声明式类
- 从数据库中推断

先看第一种方式。

### 类型

类型指的是数据的类型，比如字符，日期，数字等

- 通用类型
- SQL标准类型
- 厂商自定义类型
- 用户自定义类型

这些类型可以从`sqlalchemy.types`中导入，也可以从sqlalchemy中直接导入。下面看看各种类型在数据库、python和数据库中对应的类型。下面的类型是通用数据类型，非常的重要

| SQLAlchemy    | Python               | SQL                                |
| ------------- | -------------------- | ---------------------------------- |
| `BigInteger`  | `int`                | `BIGINT`                           |
| `Boolean`     | `bool`               | `BOOLEAN` 或 `SMALLINT`            |
| `Date`        | `datetime.date`      | `DATE(SQLite:STRING)`              |
| `DateTime`    | `datetime.datetime`  | `DATETIME (SQLite: STRING)`        |
| `Enum`        | `str`                | `ENUM` 或 `VARCHAR`                |
| `Float`       | `float` 或 `Decimal` | `FLOAT` 或 `REAL`                  |
| `Integer`     | `int`                | `INTEGER`                          |
| `Interval`    | `datetime.timedelta` | `INTERVAL` 或 `DATE`（从纪元开始） |
| `LargeBinary` | `byte`               | `BLOB` 或 `BYTEA`                  |
| `Numeric`     | `decimal.Decimal`    | `NUMERIC` 或 `DECIMAL`             |
| `Unicode`     | `unicode`            | `UNICODE` 或 `VARCHAR`             |
| `Text`        | `str`                | `CLOB` 或 `TEXT`                   |
| `Time`        | `datetime.time`      | `DATETIME`                         |
|               |                      |                                    |



### 元数据

元数据用来把数据库结合在一起，以便在SQLAlchemy中快速访问它。可以把元数据看作一种Table对象目录，其中包含与引擎和连接有关的信息。这些表可以通过字典`MetaData.tables`访问。

```python
from sqlalchemy import MetaData
metadata = MetaData()
```



### 表

在SQLalchemy core中，通过调用Table构造函数初始化Table对象。要在构造函数中提供**MetaData对象（元数据）**和**表名**。当然还有一些其他参数。

列是通过Column()函数创建的，

```python
from sqlalchemy import Table, Column, Integer, Numeric, String, ForeignKey
cookies = Table('cookies', metadata,
               Column("cookie_id", Integer(),primary_key = True),
               Column("cookie_name", String(50),index=True),  # 创建cookie名称索引
               Column("cookie_recipe_url",String(255)),
               Column("unit_cost",Numeric(12,2))
               )
```



### 列

定义数据表中的字段。其中可以定义

数据长度，数字的精度，是否是唯一值，是否是必选值，设置默认值，记录更新时的更改列值。



### 键和约束

保证数据在存储到数据库之前满足某些要求。三种约束

```python
from sqlalchemy import PrimaryKeyConstraint, UniqueConstraint, CheckConstraint
```

主键约束（PrimaryKeyConstraint），定义主键，确保不同表中两个相关数据之间的关系正确。

唯一性约束（UniqueConstraint），确保字段中存在重复值。

检查约束（CheckConstraint），确保列数据和一组由用户定义的标准相匹配。

```
CheckConstraint("unit_cost>=0.00", name="unit_cost_positive")
```



### 索引

加快字段值的查找速度

```python
from sqlalchemy import Index
Index("ix_cookies_cookie_name", "cookie_name")
```



### 关联关系和外键约束

如何在表之间创建关系。

![img](http://www.ituring.com.cn/figures/2019/EssentialSQLAlchemy2nd/003.png)

从图中，定义line_items表中的order_id和cookie_id为两个外键，当一个表中有多个外键的时候，这个表可能就是**关联表**。

sqlalchemy使用`ForeignKeyConstraint`来建立两个表之间的关系。

```python
from sqlalchemy import ForeignKey
orders = Table('orders', metadata,
    Column('order_id', Integer(), primary_key=True),
    Column('user_id', ForeignKey('users.user_id')), ➊
    Column('shipped', Boolean(), default=False)
)

line_items = Table('line_items', metadata,
    Column('line_items_id', Integer(), primary_key=True),
    Column('order_id', ForeignKey('orders.order_id')),
    Column('cookie_id', ForeignKey('cookies.cookie_id')),  # 关联到两个表
    Column('quantity', Integer()),
    Column('extended_cost', Numeric(12, 2))
)
```

使用字符串而非实际列，这允许我们跨多个模块分离表定义，而且不必担心表的加载顺序。这是因为 SQLAlchemy 只会在第一次访问表名和列时对该字符串执行解析。如果在 `ForeignKey` 定义中使用了硬引用（hardreference），比如 `cookies.c.cookie_id`，那它会在模块初始化期间执行解析，并且有可能失败，这取决于表的加载顺序。

下面的代码演示了如何为 `line_items` 和 `orders` 表之间的 `order_id` 字段创建 `ForeignKeyConstraint`:

```python
ForeignKeyConstraint(["order_id"],["orders.order_id"])
```



### 表的持久化

所有表和模式定义都与 `metadata` 的实例有关。要想将模式持久化到数据库中，只需调用 `metadata` 实例的 `create_all()` 方法，并提供创建表的引擎即可：

```python
metadata.create_all(engine)
```



先列一个完成代码

```python
from datetime import datetime
from sqlalchemy import MetaData, Table, Column, Integer, Boolean, Numeric, String, DateTime, ForeignKey, create_engine

metadata = MetaData()

cookies = Table("cookies", metadata,
                Column("cookie_id", Integer(), primary_key=True),
                Column("cookie_name", String(50), index=True),
                Column("cookie_recipe_url", String(255)),
                Column("cookie_sku", String(55)),
                Column("quantity", Integer()),
                Column("unit_cost", Numeric(12, 2))
                )

users = Table("users", metadata,
              Column("user_id", Integer(), primary_key=True),
              Column("customer_number", Integer(), autoincrement=True),
              Column("username", String(15), nullable=False, unique=True),
              Column("email_address", String(255), nullable=False),
              Column("phone", String(20), nullable=False),
              Column("password", String(25), nullable=False),
              Column("created_on", DateTime(), default=datetime.now),
              Column("updated_on", DateTime(), default=datetime.now, onupdate=datetime.now)
              )

orders = Table("orders", metadata,
               Column("order_id", Integer(), primary_key=True),
               Column("user_id", ForeignKey("users.user_id")),
               Column("shipped", Boolean(), default=False),
               )

line_items = Table("line_items", metadata,
                   Column("line_items_id", Integer(), primary_key=True),
                   Column("order_id", ForeignKey('orders.order_id')),
                   Column("cookie_id", ForeignKey("cookies.cookie_id")),
                   Column("quantity", Integer()),
                   Column("extended_cost", Numeric(12, 2))
                   )

engine = create_engine("sqlite:///:memory:")
metadata.create_all(engine)

```





## 第二章 使用SQLAlchemy Core处理数据

数据库中已经创建了数据表，现在就可以进行数据操作了。

### 插入数据

使用insert语句进行插入，

```python
ins = cookies.insert().values(cookie_name="chocolate chip", 
                              cookie_recipe_url="http:/sssssssssss",
                              cookie_sku="CC01",
                              quantity="12",
                              unit_cost="0.50"
                             )

print(str(ins))  # 可以展示真实的sql语句
```

ins 对象的compile()方法返回一个SQLComplier对象，该对象允许我们通过params属性访问随机查询一起发送的实际参数。

```python
ins.compile().params
```

添加号数据之后，需要再将命令提交到数据库

```python
result = connection.execute(ins)
result.inserted_primary_key  # 获取刚刚插入的数据的id
```

插入语句作为独立的函数实现，这样的实现，可以很好的服用代码，可以根据需要调整它的参数。

```python
from sqlalchemy import insert
ins = insert(cookies).values(cookie_name="chocolate chip", 
                             cookie_recipe_url="http://sssssssssss", 
                             cookie_sku="CC01", 
                             quantity="12", 
                             unit_cost="0.50"
                            )
```

也可以将要插入的值，放在execute语句当中，更改上面的例子如下

```python
ins = cookies.insert()
result = connection.execute(ins, cookie_name="dark chocolate chip",
                            cookie_recipe_url="http://sssssssssssssss",
                            cookie_sku="CC02",
                            quantity="1",
                            unit_cost="0.75"
                            )
result.inserted_primary_key
```

通过字典列表一次性添加多条语句

```python
inventory_list = [{"cookie_name":"peanut butter", "cookie_recipe_url":"http://sssssss", "cookie_sku":"PB01", "quantity":"24", "unit_cost":"0.25"},
                 {"cookie_name":"oatmeal raisin", "cookie_recipe_url":"http://sssssssss", "cookie_sku":"EWW01", "quantity":"100", "unit_cost":"1.00"}]

result = connection.execute(ins, inventory_list)
```



### 查询数据

查询用select函数。

```python
from sqlalchemy.sql import select
s = select([cookies])  # 给的是数据库表的列表
rp = connection.execute(s)
result = rp.fetchall()  # 返回所有行

"""还有一种简单的方式"""
s = cookies.select()
rp = connection.executes(s)
results = rp.fetchall()

```

##### ResultProxy 对象

ResultProxy 是DBAPI游标对象的包装器，其主要目标是让语句返回的结果更容易使用和操作。比如，ResultProxy 允许使用索引、名称或者Column对象进行访问。

```python
first_row = result[0]  # 获取ResultProxy的第一行
first_row[1]
first_row.cookie_name
first_row[cookies.c.cookie_name]
```

ResultProxy 对象是可迭代的，可以容易的对每条记录进行操作。

```python
rp = connection.execute(s)
for record in rp:
    print(record.cookie_name)
```

ResultProxy 对象还有其他快捷的获取记录的方法

- first() 返回第一个记录并关闭连接
- fetchone() 返回一行，并保持光标的打开状态
- scalar()  如果查询结果是包含一个列的单条记录，则返回单个值
- keys()  获取列名列表。

注意事项：

> 获取单条记录用first，尽量不用fetchone和scalar
>
> 使用可迭代的ResultProxy对象，而不是fetchall和fetchone。迭代的节省内存
>
> fetchone使数据库练级处于打开状态
>
> 使用scalar返回多条记录时，会引发错误



控制查询中的列数

```python
s = select([cookies.c.cookie_name, cookies.c.quantity])  # 为什么是.c 。。先组织查询的语句
rp = connection.execute(s)  # 再提交执行
print(rp.keys())
result = rp.first()
```

排序

```python
s = select([cookies.c.cookie_name, cookies.c.quantity])
s = s.order_by(cookies.c.quantity)
rp = connection.execute(s)
for cookie in rp:
    print("{}-{}".format(cookie.quantity, cookie.cookie_name))
    
# ----------
from sqlalchemy import desc
s = select([cookies.c.cookie_name, cookies.c.quantity])
s = s.order_by(desc(cookie.c.quantity))  # 使用desc函数进行反序操作
```

限制返回条数

```python
s = select([cookies.c.cookie_name, cookies.c.quantity])
s = s.order_by(cookies.c.quantity)
s = s.limit(2)  # 限制只返回两条数据
```



为查询到的数据设置标签

SQLAlchemy 可以利用后端数据库的SQL函数，比如`sum`和`count`。使用这些函数需要调用sqlalchemy.sql.func模块

```python
from sqlalchemy.sql import func
s = select([func.sum(cookies.c.quantity)])
rp = connection.execute(s)
print(rp.scalar())  # scalar返回第一个记录最左边的列
#-------------
s = select([func.count(cookies.c.cookie_name)])
rp = connection.execute(s)
record = rp.first()
print(record.keys())  # 随机给出一个列名字
print(record.count_1)  # 总过几行
```

进行重命名

```python
s = select([func.count(cookies.c.cookie_name).label("inventory_count")])  # 给一个名字
rp = connection.execute(s)
record = rp.first()
print(record.keys())
print(record.inventory_count)
```



过滤 where语句

典型的where()子句包含一个列、一个运算符和一个值或列。可以把多个where子句接在一起使用如and一样

```python
s = select([cookies]).where(cookies.c.cookie_name=="chocolate chip")  # where筛选
s = select([cookies]).where(cookies.c.cookie_name.like("%chocolate%")) # like 方法
rp = connection.execute(s)
record = rp.first()
print(record.items())
```

在上面的例子中where语句中，cookies.c.cookie_name列用作一种`ClauseElement`来过滤结果。

`ClauseElement`是在子句中使用的实体，一般是表中列。但是`ClauseElement`拥有许多额外的功能

| 方法                   | 用途                               |
| ---------------------- | ---------------------------------- |
| between(cleft, cright) | 查找cleft和cright之间的值          |
| concat(column_two)     | 连接到                             |
| distinct()             | 查找列的唯一值                     |
| in_([list])            | 查找列在列表中的位置               |
| is_(None)              | 查找列None的位置（通常找null和None |
| contains(string)       | 查找包含string的列（区分大小写）   |
| endswith(string)       | 查找以string结尾的列（区分大小写） |
| like(string)           | 查找string匹配的列（区分大小写）   |
| startswith(string)     | 查找string开头的列（区分大小写）   |
| ilike(string)          | 查找string匹配的列（不区分大小写） |

还可以用运算符（==、！=、<、>、<=、>=）



布尔运算符

AND、OR、NOT。可以使用(&，|，~)。最好还是使用括号包裹。

连接词

`and_(), or_(), not_()`

可以把多个where()句子连接在一起，也可以使用连接词来实现，而且使用连接词更好，功能更强。

```python
from sqlalchemy import and_, or_, not_
s = select([cookies]).where(and_(cookies.c.quantity>23, cookies.c.unit_cost<0.40))
for row in connection.execute(s):
    print(row.cookie_name)
```



### 更新数据

update()，配套where语句，否则全被更新了。

```python
from sqlalchemy import update
u = update(cookies).where(cookies.c.cookie_name == "chocolate chip")
u = u.values(quantity = (cookies.c.quantity + 120))  # 生成方法构建语句
result = connection.execute(u)
print(result.rowcount)  # 更新的行数
s = select([cookies]).where(cookies.c.cookie_name == "chocolate chip")
result = connection.execute(s).first()
for key in result.keys():
    print("{:>20}:{}".format(key, result[key]))
```



### 删除数据

delete()，既是函数，也可以使用表的delete()方法。delete不接收值参数，只接收一个可选的where句子，用来指定删除范围。

```python
from sqlalchemy import delete
u = delete(cookies).where(cookies.c.cookie_name == "dark chocolate chip")
result = connection.execute(u)
print(result.rowcount)
s = select([cookies]).where(cookies.c.cookie_name == "dark chocolate chip")
result = connection.execute(s).fetchall()
print(len(result))
```



### 连接

使用join()和outerjoin()方法查询相关数据。使用连接查询表

```python
columns = [orders.c.order_id, users.c.username, users.c.phone, 
           cookies.c.cookie_name, line_items.c.quantity, line_items.c.extended_cost]
cookiemon_orders = select(columns)
cookiemon_orders = cookiemon_orders.select_from(orders.join(users).join(line_items).join(
    cookies)).where(users.c.username == "cookiemon")  # 关系连接用作from子句
result = connection.execute(cookiemon_orders).fetchall()
for row in result:
    print(row)
```

可以将上面的函数转为SQL语句

```sql
SELECT orders.order_id, users.username, users.phone, cookies.cookie_name, line_items.quantity, line_items.extended_cost 
FROM users 
JOIN users ON users.user_id = orders.user_id 
JOIN line_items ON orders.order_id = line_items.order_id
JOIN cookies ON cookies.cookie_id = line_items.cookie_id
WHERE users.username = :username_1
```



外连接查询多个表

```python
columns  = [users.c.username, func.count(orders.c.order_id)]
all_orders = select(columns)
all_orders = all_orders.i(users.outerjoin(orders))
all_orders = all_orders.group_by(users.c.username)
result = connection.execute(all_orders).fetchall()
for row in result:
    print(row)
```



### 别名

假设有表

```python
employee_table = Table("employee", metadata, 
                       Column("id", Integer, primary_key = True),
                       Column("manager", None, ForeignKey("employee.id")), 
                       Column("name", String(255))
                      )
```

想要从中选择数据， 使用的SQL语句为

```sql
SELECT employee.name 
FROM employee, employee AS manager
WHERE employee.manager_id = manager.id
AND manager.name = "Fred"
```

这需要使用别名来处理

```python
manager = employee_table.alias("mgr")
stmt = select([employee_table.c.name], and_(employee_table.c.manager_id == manager.c.id,
                                            manager.c.name == "Fred"))
print(stmt)
```



### 分组

```python
columns = [users.c.username, func.count(orders.c.order_id)]
all_orders = select(columns)
all_orders = all_orders.select_from(users.outerjoin(orders))
all_orders = all_orders.group_by(users.c.username)  # 根据用户进行分组
result = connection.execute(all_orders).fetchall()
for row in result:
    print(row)
```



### 链式调用

构建查询时，如果想把逻辑清晰地表示出来，那使用链式调用会有用。

获取订单数据，如下

```python
def get_orders_by_customer(cust_name):
    columns = [orders.c.order_id, users.c.username, users.c.phone, cookies.c.cookie_name, 
               line_items.c.quantity, line_items.c.extended_cost]
    cust_orders = select(columns)
    cust_orders = cust_orders.select_from(usrs.join(orders).join(line_items).join(cookies))
    cust_orders = cust_orders.where(users.c.username == cust_name)
    result = connection.execute(cust_orders).fetchall()
    return result
get_orders_by_customer("cakeeater")
```

如果还要包含其他过滤条件，如下所示

```python
def get_orders_by_customer(cust_name, shipped=None, details=False):
    columns = [orders.c.order_id, users.c.username, users.c.phone]
    joins = users.join(orders)
    if details:
        column.extend([cookies.c.cookie_name, line_items.c.quantity, line_items.c.extended_cost])
        joins = joins.join(line_items).join(cookies)
        
    cust_orders = select(columns)
    cust_orders = cust_orders.select_from(joins)
    cust_orders = cust_orders.where(users.c.username == cust_name)
    if shipped is not None:
        cust_orders = cust_orders.where(orders.c.shipped == shipped)
        
    result = connection.execute(cust_orders).fetchall()
    return result
get_orders_by_customer("cakeeater")  # 获取所有订单
get_orders_by_customer("cakeeater", details=True)  # 获取订单的细节
get_orders_by_customer("cakeeater", shipped=True)  # 获取已发货订单
get_orders_by_customer("cakeeater", shipped=False)  # 获取没发货订单
get_orders_by_customer("cakeeater", shipped=False, details=True)  # 获取没发货订单的细节
```



### 原始查询

可以在SQLAlchemyCore中使用SQL中的原始语句。他们返回的都是ResultProxy，可以继续和他交互。

```python
result = connection.execute("select * from orders").fetchall()
print(result)

from sqlalchemy import text
stmt = select([users]).where(text("username='cookiemon'"))
print(connection.execute(stmt).fetchall())
```





## 第三章 异常和事务

在使用SQL语句的时候，经常会产生错误，比如查询了不存在的字段，使用了重复的主键。

那么遇到错误应该怎么办? 使用事务进行处理，将错误执行的线管语句全部回滚。

### 事务

使用事务可以防止程序的崩溃，防止错误处理导致数据库的数据与实际不符。

```python
from sqlalchmey.exc import IntegrityError  # 引入异常
def ship_it(order_id):
    s = select([line_items.c.cookie_id, line_items.c.quantity])
    s = s.where(line_items.c.order_id == order_id)
    
    transaction = connection.begin()  # 开始事务
    
    cookies_to_ship = connection.execute(s).fetchall()
    try:
        for cookie in cookies_to_ship:
            u = update(cookies).where(cookies.c.cookie_id == cookie.cookie_id)
            u = u.value(quantity = cookies.c.quantity-cookie.quantity)
            result = connection.execute(u)
        u = update(orders).where(orders.c.order_id == order_id)
        u = u.values(shipped = True)
        result = connection.execute(u)
        print("Shipped order ID:{}".format(order_id))
        
        transaction.commit()  # 没有错误，提交事务
    except IntegrityError as error:
        transaction.rollback()  # 有错误，回滚错误
        print(error)
```



## 第四章 测试

书中，使用DataAccessLayer类建立数据库。使用这个数据访问类来初始化数据库模式，并随时连接到引擎。

```python
from datetime import datetime
from sqlalchemy import (MetaData, Table, Column, Integer, Numeric, String, DateTime, ForeignKey, Boolean, create_engine)

class DataAccessLayer:
    connection = None
    engine = None
    conn_string = None
    metadata = MetaData()
    # 声明表模式
    cookies = Table("cookies", metadata, 
                    Column("cookie_id", Integer(), primary_key = True), 
                    Column("cookie_name", String(50), index=True),
                    Column("cookie_recipe_url", String(255)),
                    Column("cookie_sku", String(55)),
                    Column("quantity", Integer()),
                    Column("unit_cost", Numeric(12,2))
                   )
    
    users = Table("users", metadata,
                  Column("user_id", Integer(), primary_key=True),
                  Column("customer_number", Integer(), autoincrement=True),
                  Column("username", String(15), nullable=False, unique=True),
                  Column("email_address", String(255), nullable=False),
                  Column("phone", String(20), nullable=False),
                  Column("password", String(25), nullable=False),
                  Column("created_on", DateTime(), default=datetime.now),
                  Column("updated_on", DateTime(), default=datetime.now, onupdate=datetime.now)
                 )

    orders = Table("orders", metadata,
                   Column("order_id", String(50), primary_key=True),  #这里原来有错误，应改成字符
                   Column("user_id", ForeignKey("users.user_id")),
                   Column("shipped", Boolean(), default=False),
                   )

    line_items = Table("line_items", metadata,
                       Column("line_items_id", Integer(), primary_key=True),
                       Column("order_id", ForeignKey('orders.order_id')),
                       Column("cookie_id", ForeignKey("cookies.cookie_id")),
                       Column("quantity", Integer()),
                       Column("extended_cost", Numeric(12, 2))
                       )
    
    
    def db_init(self, conn_string):  # 工厂函数
        self.engine = create_engine(conn_string or self.conn_string)
        self.metadata.create_all(self.engine)
        self.connection = self.engine.connect()
        
dal = DataAccessLayer()  # 创建一个实例，在应用程序中导入它


def prep_db():
    ins = dal.cookies.insert()
    dal.connection.execute(ins, cookie_name='dark chocolate chip',
            cookie_recipe_url='http://some.aweso.me/cookie/recipe_dark.html',
            cookie_sku='CC02',
            quantity='1',
            unit_cost='0.75')
    inventory_list = [
        {
            'cookie_name': 'peanut butter',
            'cookie_recipe_url': 'http://some.aweso.me/cookie/peanut.html',
            'cookie_sku': 'PB01',
            'quantity': '24',
            'unit_cost': '0.25'
        },
        {
            'cookie_name': 'oatmeal raisin',
            'cookie_recipe_url': 'http://some.okay.me/cookie/raisin.html',
            'cookie_sku': 'EWW01',
            'quantity': '100',
            'unit_cost': '1.00'
        }
    ]
    dal.connection.execute(ins, inventory_list)

    customer_list = [
        {
            'username': "cookiemon",
            'email_address': "mon@cookie.com",
            'phone': "111-111-1111",
            'password': "password"
        },
        {
            'username': "cakeeater",
            'email_address': "cakeeater@cake.com",
            'phone': "222-222-2222",
            'password': "password"
        },
        {
            'username': "pieguy",
            'email_address': "guy@pie.com",
            'phone': "333-333-3333",
            'password': "password"
        }
    ]
    ins = dal.users.insert()
    dal.connection.execute(ins, customer_list)
    ins = insert(dal.orders).values(user_id=1, order_id='wlk001')
    dal.connection.execute(ins)
    ins = insert(dal.line_items)
    order_items = [
        {
            'order_id': 'wlk001',
            'cookie_id': 1,
            'quantity': 2,
            'extended_cost': 1.00
        },
        {
            'order_id': 'wlk001',
            'cookie_id': 3,
            'quantity': 12,
            'extended_cost': 3.00
        }
    ]
    dal.connection.execute(ins, order_items)
    ins = insert(dal.orders).values(user_id=2, order_id='ol001')
    dal.connection.execute(ins)
    ins = insert(dal.line_items)
    order_items = [
        {
            'order_id': 'ol001',
            'cookie_id': 1,
            'quantity': 24,
            'extended_cost': 12.00
        },
        {
            'order_id': 'ol001',
            'cookie_id': 4,
            'quantity': 6,
            'extended_cost': 6.00
        }
    ]
    dal.connection.execute(ins, order_items)

    
```



等待测试的函数

```python
from db import dal
from sqlalchemy.sql import select
def get_orders_by_customer(cust_name, shipped = None, details = False):
    columns = [dal.orders.c.order_id, dal.user.c.username, dal.users.c.phone]
    joins = dal.users.join(dal.orders)
    if details:
        columns.extend([dal.cookies.c.cookie_name, dal.line_items.c.quantity, dal.line_items.c.extended_cost])
        
    cust_orders = select(columns)
    cust_orders = cust_orders.select_from(joins).where(dal.users.c.username == cust_name)
    
    if shipped is not None:
        cust_orders = cust_orders.where(dal.orders.c.shipped == shipped)
    result = dal.connection.execute(cust_orders).fetchall()
    return result
```

> 不要测试SQLAlchemy的基本功能，比如insert、select、delete、update等，因为这些代码已经经过了测试。我们应该测试操作这些函数的代码。

get_orders_by_customer有三个参数，他们分别的取值是

- cust_name  1.空值。2.有效的字符串。3.无效的字符串。
- shipped  1.None。2.True。3.False。
- details 1.True。 2.True。

这三个参数有`3*3*2=18`种可能组合。下面的例子使用了18个测试函数。这样真的好吗?

下面编写测试代码

```python
class TestApp(unittest.TestCase):  # 单元测试需要继承unittest.TestCase
    cookie_orders = [(u'wlk001', u'cookiemon', u'111-111-1111')]
    # 书中给的是如下一个列表，实际上返回的不是下面的列表。 所有后面几个判断会出错。换成正确的例子就没问题
    cookie_details = [(u'wlk001', u'cookiemon', u'111-111-1111', u'dark chocolate chip', 2, Decimal('1.00')), 
                      (u'wlk001', u'cookiemon', u'111-111-1111', u'oatmeal raisin', 12, Decimal('3.00'))]

    @classmethod
    def setUpClass(cls):  # 只调用一次
        dal.db_init("sqlite:///:memory:")  # 内存数据库，数据都是在内存种的。
        prep_db()

    def test_orders_by_customer_blank(self):
        results = get_orders_by_customer("")
        self.assertEqual(results, [])

    def test_orders_by_customer_blank_shipped(self):
        result = get_orders_by_customer("", True)
        self.assertEqual(result, [])

    def test_orders_by_customer_blank_notshipped(self):
        result = get_orders_by_customer("", False)
        self.assertEqual(result, [])

    def test_orders_by_customer_blank_details(self):
        result = get_orders_by_customer("", details=True)
        self.assertEqual(result, [])

    def test_orders_by_customer_blank_shipped_detail(self):
        result = get_orders_by_customer("", True, True)
        self.assertEqual(result, [])

    def test_orders_by_customer_blank_notshipped_details(self):
        result = get_orders_by_customer("", False, True)
        self.assertEqual(result, [])

    # -------------------------------------------------------------
    def test_orders_by_customer_bad_cust(self):
        results = get_orders_by_customer('bad name')
        self.assertEqual(results, [])

    def test_orders_by_customer_bad_cust_shipped(self):
        results = get_orders_by_customer('bad name', True)
        self.assertEqual(results, [])

    def test_orders_by_customer_bad_cust_notshipped(self):
        results = get_orders_by_customer('bad name', False)
        self.assertEqual(results, [])

    def test_orders_by_customer_bad_cust_details(self):
        results = get_orders_by_customer('bad name', details=True)
        self.assertEqual(results, [])

    def test_orders_by_customer_bad_cust_shipped_details(self):
        results = get_orders_by_customer('bad name', True, True)
        self.assertEqual(results, [])

    def test_orders_by_customer_bad_cust_notshipped_details(self):
        results = get_orders_by_customer('bad name', False, True)
        self.assertEqual(results, [])

    # ------------------------------------------------------------
    def test_orders_by_customer(self):
        results = get_orders_by_customer('cookiemon')
        self.assertEqual(results, self.cookie_orders)

    def test_orders_by_customer_shipped_only(self):
        results = get_orders_by_customer('cookiemon', True)
        self.assertEqual(results, [])

    def test_orders_by_customer_unshipped_only(self):
        results = get_orders_by_customer('cookiemon', False)
        self.assertEqual(results, self.cookie_orders)

    def test_orders_by_customer_with_details(self):
        results = get_orders_by_customer('cookiemon', details=True)
        self.assertEqual(results, self.cookie_details)  # 这个会报错

    def test_orders_by_customer_shipped_only_with_details(self):
        results = get_orders_by_customer('cookiemon', True, True)
        self.assertEqual(results, [])

    def test_orders_by_customer_unshipped_only_details(self):
        results = get_orders_by_customer('cookiemon', False, True)
        self.assertEqual(results, self.cookie_details)  # 这个会报错

```



### 使用mock

如果有大量逻辑需要对查询结果进行操作，那么模拟SQLAlchemy代码返回你想要的值，会很有用，这样只需测试逻辑代码。换句话说就是，使用mock模拟从SQL种获得的数据。

mock使用模拟对象替换系统中的部分，并对他们已使用的方式进行断言。单元测试应该只针对当前单元进行测试, 所有的内部或外部的依赖应该是稳定的, 已经在别处进行测试过的.使用mock 就可以对外部依赖组件实现进行模拟并且替换掉, 从而使得单元测试将焦点只放在当前的单元功能。

mock在python3种已经集成在unittest种，python2需要单独导入。

```python
import unittest
from decimal import Decimal
from db import dal, prep_db
from app import get_orders_by_customer
class TestApp(unittest.TestCase):
    cookie_orders = [(u'wlk001', u'cookiemon', u'111-111-1111')]
    cookie_details = [(u'wlk001', u'cookiemon', u'111-111-1111', u'dark chocolate chip', 2, Decimal('1.00')),  (u'wlk001', u'cookiemon', u'111-111-1111', u'oatmeal raisin', 12, Decimal('3.00'))]

    
    @unittest.mock.patch("app.dal.connection")  # 使用mock包装app中的dal.connection
    def test_orders_by_customer(self, mock_conn):  # mock以mock_conn形式传入测试函数
        mock_conn.execute.return_value.fetchall.return_value = self.cookie_orders  # 把execute方法的返回值设置成fetchall方法的返回值，并将fetchall方法的返回值设置为self.cookie_order
        results = get_orders_by_customer("cookiemon")  # 调用测试函数，模拟dal.connection，并返回上一步设置的返回值
        self.assertEqual(result, self.cookie_orders)
```

使用mock.patch装饰器，并模拟app模块中的select对象。

```python
@unittest.mock.patch("app.select")  # 连接时，模拟select方法
@unittest.mock.patch("app.dal.connection")
def test_orders_by_customer_blank(self, mock_conn, mock_select):  # 把装饰器传递到函数中，按顺序传入
    mock_select.return_value.select_from.return_value.where.return_value = ""  # 必须为链式查询的所有部分模拟返回值。
    mock_conn.execute.return_value.fetchall.return_value = []  # 仍然需要模拟连接，否则app模块的SQLAlchemy代码将尝试进行查询
    result = get_order_by_customer("")
    self.asserEqual(result, [])
```

> 这个mock部分还有待深入，之后再做深入理解，主要是为什么要将调用路径上的所有函数都替换掉。搜索一下mock的用法





## 第五章 反射

反射就是自动生成已存在数据表的模式。

反射单个表

```python
from sqlalchemy import MetaData, create_engine
metadata = MetaData()
engine = create_engine("sqlite:///Chinook_Sqlite.sqlite")
# -----先生成反射需要的数据

from sqlalchemy import Table
artlist = Table("Artist", metadata, autoload=True, autoload_with=engine)  # 使用autoload和autoload_with即可完成映射，下来就是可以使用artlist引用的表了

metadata.tables["artlist"]  # 查看元数据，可以看表的属性，每个列的属性
```

反射整个数据库

```python
metadata.reflect(bind=engine)
metadata.tables.key()  # 返回表名列表
```

使用反射对象构建查询

```python
playlist = metadata.tables["Playlist"]
from sqlalchemy import select
s = select([playlist]).limit(10)
engine.execute(s).fetchall()
```





## SQLAlchemy ORM

## 第六章 使用SQLAlchemy ORM定义

在SQLAlchemy ORM中，我们会先创建一个元数据容器，然后声明一个与该元数据相关联的Table对象。而在SQLAlchemy ORM中，我们会定义一个类，它继承自一个名为declarative_base的特殊基类。declarative_base把元数据容器和映射器（把类映射到数据表）结合在一起。

### 使用ORM类定义表

ORM使用的类应该满足一下要求

- 继承自declarative_base对象
- 包含`__tablename__`，这是数据库使用的表名。
- 包含一个或者多个属性，它们都是Column对象。
- 确保一个或多个属性组成主键。

先看一个表的例子，总体是和Core方法相似

```python
from sqlalchemy import Table, Column, Integer, Numeric, String
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()
class Cookie(Base):
    __tablename__ = "cookies"  # 表名
    cookie_id = Column(Integer(), primary_key=True)  # 列是一个Column对象
    cookie_name = Column(String(50), index=True)
    cookie_recipe_url = Column(String(255))
    cookie_sku = Column(String(55))
    quantity = Column(Integer())
    unit_cost = Column(Numeric(12, 2))
    
    
# SQLAlchemy Core的方法定义
cookies = Table("cookies", metadata, 
                Column("cookie_id", Integer(), primary_key = True), 
                Column("cookie_name", String(50), index=True),
                Column("cookie_recipe_url", String(255)),
                Column("cookie_sku", String(55)),
                Column("quantity", Integer()),
                Column("unit_cost", Numeric(12,2))
               )
```

可以通过`Cookie.__table__`看cookie的属性。再看一个表

```python
    from datetime import datetime
    from sqlalchemy import DateTime
    class User(Base):
        __tablename__ = "users"
        user_id = Column(Integer(), primary_key=True)
        username = Column(String(15), nullable=False, unique=True)
        email_address = Column(String(255), nullable=False)
        phone = Column(String(20), nullable=False),
        password = Column(String(25), nullable=False),
        created_on = Column(DateTime(), default=datetime.now),
        updated_on = Column(DateTime(), default=datetime.now, onupdate=datetime.now)
        
```



### 键、约束、索引

除了在列本身中定义键和约束外，还可以在表构造函数中定义键和约束。但是ORM中是构建类，不是函数，所以可以通过类的`__table_args__`属性添加，`__table_args__`接收一个元组。

```python
class SomeDataClass(Base):
    __tablename__ = "somedatatable"
    __table_args__ = (ForeignKeyConstraint(["id"], ["other_table.id"]), CheckConstraint(unit_cost>=0.00, name="unit_cost_positive"))

```

这里的语法与Table()构造函数中相同



### 关系

SQLAlchemy Core与ORM的表和对象的关联也不同。ORM使用类似ForeignKey列来约束和链接对象。还能使用relationship指令来提供一个属性，该属性可用于访问相关对象。

带关系的表

```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship, backref  # 注意导入的方式
class Order(Base):
    __tablename__ = "orders"
    order_id = Column(Integer(), primary_key=True)
    user_id = Column(Integer(), ForeignKey("users.user_id"))
    shipped = Column(Boolean(), default = False)
    user = relationship("User", backref = backref("orders", order_by = id)  # 建立一对多的关系
```

可以通过访问user属性来获得与此Order相关的User。这种关系还通过backref关键字参数在User类上建立orders属性，backref参数按照order_id排序。

```python
class Lineitems(Base):
    __tablename__ = "line_items"
    line_items_id = Column(Integer(), primary_key = True)
    order_id = Column(Integer(), ForeignKey("orders.order_id"))
    cookie_id = Column(Integer(), ForeignKey("cookies.cookie_id"))
    quantity = Column(Integer())
    extended_cost = Column(Numeric(12,2))
    order = relationship("Order", backref=backref("line_items", order_by=line_items_id))
    cookie = relationship("Cookie", uselist = False)  # 一对一关系
```

### 持久化

```python
from sqlchemy import create_engine
engine = create_engine("sqlite:///:memory:")
Base.metadata.create_all(engine)
```





## 第七章 ORM处理数据

### 会话

会话是ORM和数据库交互的方式，包装了引擎和数据库链接，会话包含一个事务，这个事务一直打开，直到提交会话或者回滚。会话为加载的和关联的对象，提供标识映射，标识映射是一种类似于缓存的数据结构，它包含由对象表和主键确定的唯一的对象列表？？？？？？

会话是通过sessionmaker类创建的。

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
engine = create_engine("sqlite:///:memory:")
Session = sessionmaker(bind=engine)
session = Session()
```

举一个具体的例子

```python
from datetime import datetime
from sqlalchemy import Table, Clumn, Integer, Numeric, String, DateTime, ForeignKey
from sqlalchemy.ext declarative import declarative_base
from sqlalchemy.orm import relationship, backref

Base = declarative_base()
class Cookie(Base):
    __tablename__ = "cookies"  # 表名
    cookie_id = Column(Integer(), primary_key=True)  # 列是一个Column对象
    cookie_name = Column(String(50), index=True)
    cookie_recipe_url = Column(String(255))
    cookie_sku = Column(String(55))
    quantity = Column(Integer())
    unit_cost = Column(Numeric(12, 2))
    def __repr__(self):
        return "Cookie(cookie_name='{self.cookie_name}',"\ 
                "cookie_recipe_url='{self.cookie_recipe_url}')".format(self=self)
        
Base.metadata.create_all(engine)
```

上面创建好了数据表，下面就是增删改查了



### 插入数据

插入数据，先实例化一个表的对象

```python
cc_cookie= Cookie(cookie_name="chocolate chip", cookie_recipe_url="http://some.aweso.me/sssss", 
                  cookie_sku="CC01", quantity=12, unit_cost=0.50)
session.add(cc_cookie)  # 添加到会话中，并未写入数据库
session.commit()  # 提交会话，写入到数据库，关闭事务，但是没断开连接，你还可以再进行操作
```

插入多条记录

```python
dcc = Cookie(cookie_name="dark chocolate chip", cookie_recipe_url="http://some.aweso.me/sssss", 
             cookie_sku="CC02", quantity=1, unit_cost=0.75)
mol = Cookie(cookie_name="molasses", cookie_recipe_url="http://some.aweso.me/sssss", 
             cookie_sku="MOL01", quantity=1, unit_cost=0.80)

session.add(dcc)
session.add(mol)  # 添加到会话
session.flush()  # 刷新会话，将数据写入数据库，但是不断开链接，不结束事务，还可以在做其他操作。

```

与会话无关的插入

```python
c1 = Cookie(cookie_name="peanut butter", cookie_recipe_url="http://some.aweso.me/sssss", 
            cookie_sku="PB01", quantity=22, unit_cost=0.25)
c2 = Cookie(cookie_name="oatmeal raisin", cookie_recipe_url="http://some.aweso.me/sssss", 
            cookie_sku="EWW01", quantity=100, unit_cost=1.00)
session.bulk_save_objects([c1, c2])
session.commit()
```

使用bulk_save_objects方法保存的会直接插入到数据库，不启用会话，不产生其他输出。但是速度更快。丧失的性能：

- 关系设置和操作得不到遵守和触发
- 对象没有链接到会话
- 默认情况下，不获取主键。。     不获取主键会怎么样？？
- 不触发任何事件



### 查询数据

```python
cookies = session.query(Cookie).all()
```

获得cookie中的所有记录。这返回的是Cookie对象的列表。可以对这些对象进行操作。

迭代操作。

```python
for cookie in session.query(Cookie):
    print(cookie)
```

其他方法：

- first()  返回第一个对象
- one()  查询所有行，如果返回的不是单个结果，则抛出异常
- scalar()  返回第一个结果的第一个元素，没有则为None，若结果多于一个，则引发错误

### 限制查询的列数

```python
session.query(Cookie.cookie_name, Cookie.quantity).first()
```



### 排序

```python
for cookie in session.query(Cookie).order_by(Cookie.quantity):
    print("{:3}-{}".format(cookie.quantity, cookie.cookie_name))
    
from sqlalchemy import desc
for cookie in session.query(Cookie).order_by(desc(Cookie.quantity)):
    print("{:3}-{}".format(cookie.quantity, cookie.cookie_name))
```



### 限制返回条数

```python
query = session.query(Cookie).order_by(Cookie.quntity)[:2]   # 返回切片，这种方式不可取，效率低下
print([result.cookie_name for result in query])
```



```python
query = session.query(Cookie).order_by(Cookie.quantity).limit(2)
print([result.cookie_name for result in query])
```



### 内置函数和标签

```python
from sqlalchemy import func
inv_count = session.query(func.sum(Cookie.quantity)).scalar()  # 获得cookie的总数
print(inv_count)

rec_count = session.query(func.count(Cookie.cookie_name)).first()  # 统计有几种cookie
```

标签

```python
rec_count = session.query(func.count(Cookie.cookie_name).label("inventory_count")).first()
print(rec_count.keys())
print(rec_count.inventory_count)
```



### 过滤

过滤使用filter()进行过滤。典型的filter()子句包含一个列、一个运算符和一个值或列。

````python
record = session.query(Cookie).filter(Cookie.cookie_name == "chocolate chip").first()
print(record)
````

还有一个filter_by()方法，他没有明确要求把类作为过滤器表达式的一部分，而是使用属性关键字表达式，这些表达式来自于查询的主实体或最后一个连接到语句的实体。

```python
record = session.query(Cookie).filter_by(cookie_name="chocolate chip").first()  # 这里是一个等号
```

下面过滤语句中，Cookie.cookie_name作为ClauseElement来过滤结果，具体可以参照之前的内容

```python
query = session.query(Cookie).filter(Cookie.cookie_name.like("%chocolate%"))
for record in query:
    print(record.cookie_name)
```



### 运算符

目前为止，过滤方法有两种，一是 列是否等于某个值，二是 使用ClauseElement的方法。

基本上和python的运算符相同

```python
results = session.query(Cookie.cookie_name, "SKU-" + Cookie.cookie_sku).all()
for row in results:
    print(row)
```

```python
from sqlalchemy import cast
query = session.query(Cookie.cookie_name, 
                      cast((Cookie.quantity * Cookie.unit_cost), Numeric(12,2)).label("inv_cost"))
# cast 是做类型转换
for result in query:
    print("{}-{}".format(result.cookie_name, result.inv_cost))
    
```



### 连接词

把多个filter()子句连接在一起，可以用连接词实现。`and_()  or_() not_()`

```python
from sqlalchemy import and_, or_, not_
query = session.query(Cookie).filter(and_(Cookie.quantity>23, Cookie.unit_cost<0.40))
for result in query:
    print(result.cookie_name)
    
    
query = session.query(Cookie).filter(or_(Cookie.quantity.between(10,50), Cookie.cookie_name.contains("chip")))
for result in query:
    print(result.cookie_name)
```



### 更新数据

```python
query = session.query(Cookie)
cc_cookie = query.filter(Cookie.cookie_name == "chocolate chip").first()  # 先查询
cc_cookie.quantity = cc_cookie.quantity + 120  # 再更新
session.commit()  # 再提交
print(cc_cookie.quantity)
```



```python
query = session.query(Cookie)
query = query.filter(Cookie.cookie_name == "chocolate chip")
query.update({Cookie.quantity: Cookie.quantity - 20})
cc_cookie = query.first()
print(cc_cookie.quantity)
```



### 删除数据

```python
query = session.query(Cookie)
query = query.filter(Cookie.cookie_name == "dark chocolate chip")
dcc_cookie = query.one()
session.delete(dcc_cookie)  # 删除
session.commit()
dcc_cookie = query.first()
print(dcc_cookie)
```

就地删除

```python
query = session.query(Cookie)
query = query.filter(Cookie.cookie_name == "molasses")
query.delete()  # 没有获取对象
mol_cookie = query.first()
print(mol_cookie)
```













