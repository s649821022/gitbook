#### 基础视图`TemplateView`

视图类`TemplateView`是所有视图类里最基础的应用视图类，开发者可以直接调用应用视图类，它继承多个父类：`TemplateResponseMixin`，`ContextMixin`，`View`，源码如下：

``````python
class TemplateView(TemplateResponseMixin, ContextMixin, View):
    """
    Render a template. Pass keyword arguments from the URLconf to the context.
    """
    def get(self, request, *args, **kwargs):
        context = self.get_context_data(**kwargs)
        return self.render_to_response(context)
``````

视图类`TemplateViewd`的get()所调用的函数说明如下：

- 视图类`ContextMixin`的`get_context_data()`方法用于获取模板上下文内容，模板上下文是将视图里的数据传递到模板文件，再由模板引擎将数据转换成HTML网页数据
- 视图类`TemplateResponseMixin`的`render_to_response()`用于实现响应处理，由响应类`TemplateResponse`完成

我们可以在视图类`TemplateView`的源码文件里找到视图类`TemplateResponseMixin`的定义过程，该类设置了4个属性和两个类方法，这些属性和类方法说明如下：

- template_name：设置模板文件的文件名
- template_engine：设置解析模板文件的引擎
- response__class：设置HTTP请求的响应类，默认值为响应类`TemplateResponse`
- content_type：设置响应内容的数据格式，一般情况下使用默认值即可
- render_to_response()：实现响应处理，由响应类T`emplateResponse`完成
- get_template_names()：获取属性template_name的值



#### 列表视图`ListView`

在网站开发中，经常会出现需要列出某个表中的一些数据作为列表展示出来。比如文章列表，图书列表等等。在`Django`中可以使用`ListView`来帮我们快速实现这种需求。示例代码如下：

```python
class ArticleListView(ListView):
    model = Article
    template_name = 'article_list.html'
    paginate_by = 10
    context_object_name = 'articles'
    ordering = 'create_time'
    page_kwarg = 'page'

    def get_context_data(self, **kwargs):
        context = super(ArticleListView, self).get_context_data(**kwargs)
        print(context)
        return context

    def get_queryset(self):
        return Article.objects.filter(id__lte=89)
```

对以上代码进行解释：

1. 首先`ArticleListView`是继承自`ListView`。
2. `model`：重写`model`类属性，指定这个列表是给哪个模型的。
3. `template_name`：指定这个列表的模板。
4. `paginate_by`：指定这个列表一页中展示多少条数据。
5. `context_object_name`：指定这个列表模型在模板中的参数名称。
6. `ordering`：指定这个列表的排序方式。
7. `page_kwarg`：获取第几页的数据的参数名称。默认是`page`。
8. `get_context_data`：获取上下文的数据。
9. `get_queryset`：如果你提取数据的时候，并不是要把所有数据都返回，那么你可以重写这个方法。将一些不需要展示的数据给过滤掉。

#### Paginator和Page类：

`Paginator`和`Page`类都是用来做分页的。他们在`Django`中的路径为`django.core.paginator.Paginator`和`django.core.paginator.Page`。以下对这两个类的常用属性和方法做解释：

#### Paginator常用属性和方法：

1. `count`：总共有多少条数据。
2. `num_pages`：总共有多少页。
3. `page_range`：页面的区间。比如有三页，那么就`range(1,4)`。

#### Page常用属性和方法：

1. `has_next()`：是否还有下一页。
2. `has_previous()`：是否还有上一页。
3. `next_page_number()`：下一页的页码。
4. `previous_page_number()`：上一页的页码。
5. `number`：当前页。
6. `start_index()`：当前这一页的第一条数据的索引值。
7. `end_index()`：当前这一页的最后一条数据的索引值。



#### 给类视图添加装饰器

```python
from django.utils.decorators import method_decorator
'''自定义登录装饰器'''
def login_required(func):
    def wrapper(request,*args,**kwargs):
        if request.GET.get("username"):
            return func(request,*args,**kwargs)
        else:
            return redirect(reverse('login'))
    return wrapper

@method_decorator(login_required,name='dispatch')
class IndexView(View):
    def get(self,request,*args,**kwargs):
        return HttpResponse("index")
```

