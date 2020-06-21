# 自定义过滤器笔记：

1. 首先在某个app中，创建一个python包，叫做`templatetags`，注意，这个包的名字必须为`templatetags`，不然就找不到。

2. 在这个`templatetags`包下面，创建一个python文件用来存储过滤器。

3. 在新建的python文件中，定义过滤器（也就是函数），这个函数的第一个参数永远是被过滤的那个值，并且如果在使用过滤器的时候传递参数，那么还可以定义另外一个参数。但是过滤器最多只能有2个参数。

4. 在写完过滤器（函数）后，要使用`django.template.Library.filter`进行注册。

5. 还要把这个过滤器所在的这个app添加到`settings.INSTALLED_APS`中，不然Django也找不到这个过滤器。

6. 在模板中使用`load`标签加载过滤器所在的python包。

7. 可以使用过滤器了。

8. `django.template.Library.filter`还可以当作装饰器来使用。如果`filter`函数没有传递任何参数，那么将会使用这个函数的名字来作为过滤器的名字。当然如果你不想使用函数的名字来作为过滤器的名字，也可以传递一个`name`参数。示例代码如下：
   
```python
   @register.filter('my_greet')
   def greet(value,word):
       return value + word
   ```

9.时间过滤器模板

```python
@register.filter
def time_since(value):
    """
       time距离现在的时间间隔
       1. 如果时间间隔小于1分钟以内，那么就显示“刚刚”
       2. 如果是大于1分钟小于1小时，那么就显示“xx分钟前”
       3. 如果是大于1小时小于24小时，那么就显示“xx小时前”
       4. 如果是大于24小时小于30天以内，那么就显示“xx天前”
       5. 否则就是显示具体的时间 2017/10/20 16:15
       """
    if isinstance(value, datetime):
        now = datetime.now()
        timestamp = (now - value).total_seconds()
        if timestamp < 60:
            return "刚刚"
        elif timestamp >= 60 and timestamp < 60 * 60:
            minutes = int(timestamp /60)
            return f"{minutes}分钟前"
        elif timestamp >= 60 * 60 and timestamp < 60 * 60 *24:
            hours = int(timestamp / (60 * 60))
            return f"{hours}小时前"
        elif timestamp >= 60 * 60 * 24 and timestamp < 60 * 60 * 24 * 30:
            days = int(timestamp / (60 * 60 * 24))
            return f"{days}天前"
        else:
            return value.strftime("%Y/%m/%d %H:%M")
    else:
        return value
```