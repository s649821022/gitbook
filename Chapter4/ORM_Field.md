### 模型常用属性

在源码目录`from django.db.models import fields`的`__init.py__`中找到哦啊各种模型字段，说明如下：

| 字段                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| AutoField                 | 一般不需要使用这个类型，自增长类型，数据表的字段类型为整数，长度为11位 |
| BigAutoField              | 自增长类型，数据表的字段类型为bigint，长度为20位             |
| BooleanField              | 在模型层面接收的是`True/False`。在数据库层面是`tinyint`类型。如果没有指定默认值，默认值是`None` |
| CharField                 | 字符类型，必须要传递`max_length`这个关键字参数               |
| DateField                 | 日期类型。<br />在`Python`中是`datetime.date`类型，可以记录年月日。在映射到数据库中也是`date`类型。使用这个`Field`可以传递以下几个参数：<br />`auto_now`：在每次这个数据保存的时候，都使用当前的时间。比如作为一个记录修改日期的字段，可以将这个属性设置为`True`。 <br />`auto_now_add`：在每次数据第一次被添加进去的时候，都使用当前的时间。比如作为一个记录第一次入库的字段，可以将这个属性设置为`True`。 |
| DateTimeField             | 日期时间类型，类似于`DateField`。不仅仅可以存储日期，还可以存储时间。映射到数据库中是`datetime`类型。这个`Field`也可以使用`auto_now`和`auto_now_add`两个属性。 |
| TimeField                 | 时间类型。在数据库中是`time`类型。在`Python`中是`datetime.time`类型。 |
| EmailField                | 类似于`CharField`。在数据库底层也是一个`varchar`类型。最大长度是254个字符。 |
| FileField                 | 用来存储文件的                                               |
| ImageField                | 用来存储图片文件的                                           |
| FloatField                | 浮点类型。映射到数据库中是`float`类型                        |
| IntegerField              | 整形。值的区间是`-2147483648——2147483647`。                  |
| BigIntegerField           | 大整形。值的区间是`-9223372036854775808——9223372036854775807`。 |
| PositiveIntegerField      | 正整形。值的区间是`0——2147483647`。                          |
| SmallIntegerField         | 小整形。值的区间是`-32768——32767`。                          |
| PositiveSmallIntegerField | 正小整形。值的区间是`0——32767`。                             |
| TextField                 | 大量的文本类型。映射到数据库中是longtext类型。               |
| UUIDField                 | 只能存储`uuid`格式的字符串。`uuid`是一个32位的全球唯一的字符串，一般用来作为主键。 |
| URLField                  | 类似于`CharField`，只不过只能用来存储`url`格式的字符串。并且默认的`max_length`是200。 |



## Field的常用参数

每个模型的字段都允许设置参数，这些参数来自己父类Field

| 字段        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| null        | 如果设置为`True`，`Django`将会在映射表的时候指定是否为空。默认是为`False`。在使用字符串相关的`Field`（CharField/TextField）的时候，官方推荐尽量不要使用这个参数，也就是保持默认值`False`。因为`Django`在处理字符串相关的`Field`的时候，即使这个`Field`的`null=False`，如果你没有给这个`Field`传递任何值，那么`Django`也会使用一个空的字符串`""`来作为默认值存储进去。因此如果再使用`null=True`，`Django`会产生两种空值的情形（NULL或者空字符串）。如果想要在表单验证的时候允许这个字符串为空，那么建议使用`blank=True`。如果你的`Field`是`BooleanField`，那么对应的可空的字段则为`NullBooleanField`。 |
| blank       | 标识这个字段在表单验证的时候是否可以为空。默认是`False`。<br/>这个和`null`是有区别的，`null`是一个纯数据库级别的。而`blank`是表单验证级别的。 |
| db_column   | 这个字段在数据库中的名字。如果没有设置这个参数，那么将会使用模型中属性的名字。 |
| default     | 默认值。可以为一个值，或者是一个函数，但是不支持`lambda`表达式。并且不支持列表/字典/集合等可变的数据结构。 |
| primary_key | 是否为主键。默认是`False`。                                  |
| unique      | 在表中这个字段的值是否唯一。一般是设置手机号码/邮箱等。      |

> 在定义模型时，一般情况下会重写函数`__str__`，这是设置模型的返回值，默认情况下，返回值为模型名+主键
>
> 需要注意的是，函数`__str__`只允许返回字符类型的字段，如果字段是整形或者日期类型的，必须使用str()函数进行强转



#### 模型中Meta选项

| 字段         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| abstract     | 若为True，则改模型为抽象模型，不会在数据库里创建数据表       |
| db_table     | 设置模型所对应的数据表名称，若未指定，则默认使用模型名来作为数据库表名 |
| managed      | 默认值为True，支持Django命令执行数据迁移，若为False，则不支持数据迁移功能 |
| ordering     | 属性值为列表，将模型数据以某个字段进行排序                   |
| verbose_name | 属性值为字符串，设置模型直观可读的名称并以复数形式表示       |

<u>关于Django时间的补充</u>

**什么是navie时间？什么是aware时间？**

1.navie时间：不知道自己的时间表示哪个时区。也就是不知道自己几斤几两，比较幼稚

2.aware时间：知道自己的时间表示的是哪个时区的。也就是比较清醒。

#### pytz库

> 专门用来处理时区的库，这个库会经常更新一些时区的数据，不需要我们担心，并且这个库在安装Django的时候回默认的安装。如果没有安装，那么可以通过`pip install pytz`的方式进行安装



#### astimezone方法

将一个时区的时间转换为另一个时区的时间。这个方法只能被aware类型的时间调用。不能被navie类型的时间调用。示例代码如下：

```python
import pytz
from datetime import datetime
now = datetime.now() # 这是一个navie类型的时间
utc_timezone = pytz.timezone("UTC") # 定义UTC的时区对象
utc_now = now.astimezone(utc_timezone) # 将当前的时间转换为UTC时区的时间
>> ValueError: astimezone() cannot be applied to a naive datetime # 会抛出一个异常，原因就是因为navie类型的时间不能调用astimezone方法


now = now.replace(tzinfo=pytz.timezone('Asia/Shanghai'))
utc_now = now.astimezone(utc_timezone)
# 这时候就可以正确的转换。
```

以上操作皆在linux上进行，windows上不会出现此问题，因为windows调用的时间API和linux上不同

#### django.utils.timezone.now方法：

会根据`settings.py`文件中是否设置了`USE_TZ=True`获取当前的时间。如果为Ture，那么就获取一个aware类型的UTC时间，如果为False，那么就获取一个navie类型的时间

#### django.utils.timezone.localtime方法

会根据`setting.py`中的`TIME_ZONE`来将一个aware类型的时间转换为`TIME_ZONE`指定时区的时间

