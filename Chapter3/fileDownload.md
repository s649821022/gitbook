# 文件下载功能

- HttpResponse是所有响应过程的核心类，它的底层功能类是HttpResponseBase
- StreamHttpResponse是在HttpResponseBase的基础上进行继承与重写的，它实现流式响应输出（流式响应输出是使用python的迭代器将数据进行分段处理并传输的），适用于大规模数据响应和文件 传输响应
- FileResponse是在StreamHttpResponse的基础上进行继承与重写的，它实现文件的流式响应输出，只适用于文件传输响应



**HttpResponse、StreamingHttpresponse和FileResponse都能实现文件下载功能，但三者存在一定差异，说明如下：**

- HttpResponse实现文件下载存在很大的弊端，其工作原理是将文件读取并载入内存中，然后输出到浏览器中实现下载功能，但是如果下载的文件很大，该方法就会占用很多内存，对于下载大文件，Django推荐使用StreamingHttpResponse和FileResponse方法，这两个方法将下载的文件分批写入服务器的本地磁盘，而不再将文件载入服务器的内存
- StreamingHttpResponse和FileResponse的实现原理是相同的，两者都是将下载文件分批写入本地磁盘，实现文件的流式响应输出
- 从适用范围来讲，StreamingHttpResponse的适用范围更为广泛，可支持大规模数据或文件输出，而FileResponse只支持文件输出
- 从使用方式来说，由于StreamingHttpResponse支持数据或文件输出，因此在使用时需要设置响应输出类型和方式，而FileResponse只需设置3个参数即可实现文件下载功能

```python
def download1(request):
    file_path = 'D:\cat.jpg'
    try:
        response = HttpResponse(open(file_path, 'rb'))
        response["content-type"] = 'application/octet-stream'
        response["content-Disposition"] = 'attachment;filename=cat.jpg'
        return response
    except Exception:
        raise Http404('Download error')

def download2(request):
    file_path = 'D:\duck.jpg'
    try:
        response = StreamingHttpResponse(open(file_path, 'rb'))
        response["content-type"] = 'application/octet-stream'
        response["content-Disposition"] = 'attachment;filename=duck.jpg'
        return response
    except Exception:
        raise Http404('Download error')

def download3(request):
    file_path = 'D:\dog.jpg'
    try:
        f = open(file_path, 'rb')
        response = FileResponse(f, as_attachment=True, filename='dog.jpg')
        return response
    except Exception:
        raise Http404('Download error')
```