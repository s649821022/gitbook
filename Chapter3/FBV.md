## 返回响应内容

| 响应类型                            | 说明                                  |
| ----------------------------------- | ------------------------------------- |
| HttpResponse("Hello World")         | 状态码200，请求已成功被服务器接收     |
| HttpResponseRedirect('/')           | 状态码302，重定向首页地址             |
| HttpResponsePeermanentRedirect('/') | 状态码301，永久重定向首页地址         |
| HttpResponseBadRequest('400')       | 状态码400，访问的页面不存在或请求错误 |
| HttpResponseNotFound('404')         | 状态码404，网页不存在或网页的URL失效  |
| HttpResponseForbidden('403')        | 状态码403，没有访问权限               |
| HttpResponseNotAllowed('405')       | 状态码405，不允许使用该请求方式       |
| HttpResponseServerError('500')      | 状态码500，服务器内容错误             |
| JsonResponse({'foo':'bar})          | 默认状态码200，响应内容为JSON数据     |
| StreamingHttpresponse()             | 默认状态码200，响应内容以流式输出     |



### render函数源码解析

```python
def render(request, template_name, context=None, content_type=None, status=None, using=None):
    """
    Return a HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)
```

render函数中参数request和template_name是必需参数，其余的参数都是可选参数，各个参数说明如下：

- request：浏览器向服务器发送的请求对象，包含用户信息，请求内容和请求方式等
- template_name：模板名称，用于生成网页内容
- context：对模板上下文（模板变量）赋值，以字典格式表示，默认情况下是一个空字典
- content_type：响应内容的数据格式，一般情况下使用默认值即可

- status： HTTP状态码，默认为200
- using： 设置模板引擎，用于解析模板文件，生成网页内容

**在实际开发过程中：如果视图传递的变量过多，在设置参数context时就显得非常冗余，因此可以使用python内置语法locals()取代context**



