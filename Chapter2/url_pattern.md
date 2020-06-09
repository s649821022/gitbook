# url传递参数：

## url映射：
1. 为什么会去urls.py文件中寻找映射呢？
是因为在`settings.py`文件中配置了`ROOT_URLCONF`为`urls.py`。所有django会去`urls.py`中寻找。
2. 在`urls.py`中我们所有的映射，都应该放在`urlpatterns`这个变量中。
3. 所有的映射不是随便写的，而是使用`path`函数或者是`re_path`函数进行包装的。



## url传参数：

1. 采用在url中使用变量的方式：在path的第一个参数中，使用`<参数名>`的方式可以传递参数。然后在视图函数中也要写一个参数，视图函数中的参数必须和url中的参数名称保持一致，不然就找不到这个参数。另外，url中可以传递多个参数。
2. 采用查询字符串的方式：在url中，不需要单独的匹配查询字符串的部分。只需要在视图函数中使用`request.GET.get('参数名称')`的方式来获取。示例代码如下：
    ```python
    def author_detail(request):
        author_id = request.GET['id']
        text = '作者的id是：%s' % author_id
        return HttpResponse(text)
    ```
    因为查询字符串使用的是`GET`请求，所以我们通过`request.GET`来获取参数。并且因为`GET`是一个类似于字典的数据类型，所有获取值跟字典的方式都是一样的。



## url参数的转换器：
1. str：除了斜杠`/`以外所有的字符都是可以的。
2. int：只有是一个或者多个的阿拉伯数字。
3. path：所有的字符都是满足的。
4. uuid：只有满足`uuid.uuid4()`这个函数返回的字符串的格式。
5. slug：英文中的横杆或者英文字符或者阿拉伯数字或者下划线才满足。



### 自定义URL（PATH）转换器笔记

实现一个获取文章列表的demo，用户可以根据`/articles/文章分类/`的方式来获取文章。其中文章分类采用的是`分类1+分类2+分类3...`的方式拼接的，并且如果只有一个分类，那就不需要加号。示例如下：
```
# 1. 第一种：获取python分类下的文章
/articles/python/
# 2. 第二种：获取python和django分类下的文章
/articles/python+django/
# 3. 第三种：获取python和django和flask分类下的文章
/articles/python+django+flask/
以此类推...
```

在“文章分类”参数传到视图函数之前要把这些分类分开来存储到列表中。
比如参数是`python+django`，那么传到视图函数的时候就要变成`['python','django']`。

以后在使用reverse反转的时候，限制传递“文章分类”的参数应该是一个列表，并且要将这个列表变成`python+django`的形式。




## 自定义URL转换器：
之前已经学到过一些django内置的url转换器，包括有int、uuid等。有时候这些内置的url转换器并不能满足我们的需求，因此django给我们提供了一个接口可以让我们自己定义自己的url转换器。

自定义url转换器按照以下五个步骤来走就可以了： 
1. 定义一个类，直接继承自object就可以了。 
2. 在类中定义一个属性regex，这个属性是用来限制url转换器规则的正则表达式。 
3. 实现to_python(self,value)方法，这个方法是将url中的值转换一下，然后传给视图函数的。 
4. 实现to_url(self,value)方法，这个方法是在做url反转的时候，将传进来的参数转换后拼接成一个正确的url。 
5. 将定义好的转换器，使用`django.urls.converters.register_converter`方法注册到django中。
6. **<u>上述步骤完成后，一定要在该app的`init.py`文件中导入自定义的转换器`from . import converters`</u>**

示例代码如下：
```python
from django.urls import register_converter

class CategoryConverter(object):
    regex = r'\w+|(\w+\+\w+)+'

    def to_python(self,value):
        # python+django+flask
        # ['python','django','flask']
        result = value.split("+")
        return result

    def to_url(self,value):
        # value：['python','django','flask']
        # python+django+flask
        if isinstance(value,list):
            result = "+".join(value)
            return result
        else:
            raise RuntimeError("转换url的时候，分类参数必须为列表！")

register_converter(CategoryConverter,'cate')
```