# urls模块化：
如果项目变得越来越大。那么url会变得越来越多。如果都放在主`urls.py`文件中，那么将不太好管理。因此我们可以将每个app自己的urls放到自己的app中进行管理。一般我们会在app中新建一个urls.py文件用来存储所有和这个app相关的子url。
需要注意的地方：
1. 应该使用`include`函数包含子`urls.py`，并且这个`urls.py`的路径是相对于项目的路径。示例代码如下：
    ```python
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book',include('book.urls'))
    ]
    ```
2. 在`app`的`urls.py`中，所有的url匹配也要放在一个叫做`urlpatterns`的变量中，否则找不到。
3. `url`是会根据主`urls.py`和app中的`urls.py`进行拼接的，因此注意不要多加斜杠。



# include函数的用法：

1. include(module,namespace=None)：
    * module：子url的模块字符串。
    * namespace：实例命名空间。这个地方需要注意一点。如果指定实例命名空间，那么前提必须要先指定应用命名空间。也就是在子`urls.py`中添加`app_name`变量。
2. include((pattern_list, app_namespace), namespace=None)：`include`函数的第一个参数既可以为一个字符串，也可以为一个元组，如果是元组，那么元组的第一个参数是子`urls.py`模块的字符串，元组的第二个参数是应用命名空间。也就是说，应用命名空间既可以在子`urls.py`中通过`app_name`指定，也可以在`include`函数中指定。
3. include(pattern_list)：`pattern_list`是一个列表。这个列表中装的是`path`或者`re_path`函数。实例代码如下：
    ```pyhon
    path('movie/',include([
        path('',views.movie),
        path('list/',views.movie_list),
    ]))
    ```