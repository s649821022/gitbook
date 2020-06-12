# 文件上传功能

### 前端HTML代码说明

```python
# form标签属性enctype="multipart/form=data"和文件对象myFile

<form enctype="multipart/form-data" action="" method="post">
    {% csrf_token %}
    <input type="file" name="myfile">
    <br>
    <input type="submit" value="上传文件">
</form>
```

文件myfile提供了以下属性来获取文件信息

- myFile.name: 获取上传文件的文件名，包含文件后缀名
- myFile.size: 获取文件的大小
- myFIle.content_type: 获取文件类型，通过后续名判断文件类型
- myFIle.read(): 从文件对象里读取整个文件上传的数据，整个方法只适合小文件
- myFile.chunks(): 按流式响应方式读取文件，在for循环中迭代，将大文件分块写入服务器所指定的保存位置
- myFile.multiple_chunks():判断文件对象的文件大小，返回True或False，当文件大于2.5MB（默认值为2.5MB）时，该方法返回True，否则返回False，因此，可以根据该方法来选择选用read方法读取还是采用chunks方法

```python
def save_file(file):
    with open(os.path.join(r'D:\\upload', file.name), 'wb+') as f:
        for chunk in file.chunks():
            f.write(chunk)

def upload(request):
    # 请求方法为post才进行上传
    if request.method == 'POST':
    # 获取上传的文件，如果没有文件，就默认为None
        myFile = request.FILES.get('myfile', None)
        if not myFile:
            return HttpResponse('no files for upload!')
        save_file(myFile)
        return HttpResponse('upload over!')
    else:
        return render(request, 'upload.html')
```