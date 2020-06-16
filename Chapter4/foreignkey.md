### 外键和表关系

外键删除操作

如果一个模型使用了外键。那么在对方那个模型被删掉后，该进行什么样的操作。可以通过`on_delete`来指定。可以指定的类型如下：

1. `CASCADE`：级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除。
2. `PROTECT`：受保护。即只要这条数据引用了外键的那条数据，那么就不能删除外键的那条数据。
3. `SET_NULL`：设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。如果设置这个选项，前提是要指定这个字段可以为空。
4. `SET_DEFAULT`：设置默认值。如果外键的那条数据被删除了，那么本条数据上就将这个字段设置为默认值。如果设置这个选项，前提是要指定这个字段一个默认值。
5. `SET()`：如果外键的那条数据被删除了。那么将会获取`SET`函数中的值来作为这个外键的值。`SET`函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去。
6. `DO_NOTHING`：不采取任何行为。一切全看数据库级别的约束。

**以上这些选项只是Django级别的，数据级别依旧是RESTRICT！**



### 表关系笔记：

#### 一对多：
1. 应用场景：比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系。
2. 实现方式：一对多或者多对一，都是通过`ForeignKey`来实现的。还是以文章和作者的案例进行讲解。

```python
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)

class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey("User",on_delete=models.CASCADE)
```

那么以后在给`Article`对象指定`author`，就可以使用以下代码来完成：

```python
article = Article(title='abc',content='123')
author = User(username='zhiliao',password='111111')
# 要先保存到数据库中
author.save()
article.author = author
article.save()
```

并且以后如果想要获取某个用户下所有的文章，可以通过`article_set`来实现。示例代码如下：

```python
user = User.objects.first()
# 获取第一个用户写的所有文章
articles = user.article_set.all()
for article in articles:
    print(article)
```

并且如果想要将文章添加到某个分类中。可以使用一下的方式：
```python
category = Category.objects.first()

article = Article(title='bbb',content='vvv')
article.author = FrontUser.objects.first()

category.article_set.add(article,bulk=False)
```
* 使用`bulk=False`，那么Django会自动的保存article，而不需要在添加到category之前先保存article。
* 或者是另外一种解决方式是，在添加到`category.article_set`中之前，先将`article`保存到数据库中。但是如果`article.category`不能为空，那么就产生一种死循环了，article没有`category`不能保存，而将article添加到`cateogry.artile_set`中，又需要article之前是已经存储到数据库中的。
* 如果是上面的那种需求，建议使用`bulk=False`的解决方案。

#### 一对一：
1. 在Django中一对一是通过`models.OnetToOneField`来实现的。这个`OneToOneField`其实本质上就是一个外键，只不过这个外键有一个`唯一约束（unique key）`，来实现一对一。
2. 以后如果想要反向引用，那么是通过引用的模型的名字转换为小写的形式进行访问。比如以下模型：
    ```python
    class FrontUser(models.Model):
        username = models.CharField(max_length=200)

    class UserExtension(models.Model):
        school = models.CharField(max_length=100)
        user = models.OneToOneField("FrontUser",on_delete=models.CASCADE)

    # 通过userextension来访问UserExtension对象
    user = FrontUser.objects.first()
    print(user.userextension)
    ```
    `UserExtension`的对象，可以通过`user`来访问到对应的user对象。并且`FrontUser`对象可以使用`userextension`来访问对应的`UserExtension`对象。
    如果不想使用Django默认的引用属性名字。那么可以在`OneToOneField`中添加一个`related_name`参数。示例代码如下：
    ```python
    class FrontUser(models.Model):
        username = models.CharField(max_length=200)

    class UserExtension(models.Model):
        school = models.CharField(max_length=100)
        user = models.OneToOneField("FrontUser",on_delete=models.CASCADE,related_name='extension')

    # 通过extension来访问到UserExtension对象
    user = FrontUser.objects.first()
    print(user.extension)
    ```
    那么以后就`FrontUser`的对象就可以通过`extension`属性来访问到对应的`UserExtension`对象。

#### 多对多：
1. 应用场景：比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。

2. 实现方式：`Django`为这种多对多的实现提供了专门的`Field`。叫做`ManyToManyField`。还是拿文章和标签为例进行讲解。示例代码如下：

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    tags = models.ManyToManyField("Tag",related_name="articles")

class Tag(models.Model):
    name = models.CharField(max_length=50)
```

在数据库层面，实际上`Django`是为这种多对多的关系建立了一个中间表。这个中间表分别定义了两个外键，引用到`article`和`tag`两张表的主键。



### related_name和related_query_name：

#### related_name：

还是以`User`和`Article`为例来进行说明。如果一个`article`想要访问对应的作者，那么可以通过`author`来进行访问。但是如果有一个`user`对象，想要通过这个`user`对象获取所有的文章，该如何做呢？这时候可以通过`user.article_set`来访问，这个名字的规律是`模型名字小写_set`。示例代码如下：

```python
user = User.objects.get(name='张三')
user.article_set.all()
```

如果不想使用`模型名字小写_set`的方式，想要使用其他的名字，那么可以在定义模型的时候指定`related_name`。示例代码如下：

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # 传递related_name参数，以后在方向引用的时候使用articles进行访问
    author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles')
```

以后在方向引用的时候。使用`articles`可以访问到这个作者的文章模型。示例代码如下：

```python
user = User.objects.get(name='张三')
user.articles.all()
```

如果不想使用反向引用，那么可以指定`related_name='+'`。示例代码如下：

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # 传递related_name参数，以后在方向引用的时候使用articles进行访问
    author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='+')
```

以后将不能通过`user.article_set`来访问文章模型了。

#### related_query_name：

在查找数据的时候，可以使用`filter`进行过滤。使用`filter`过滤的时候，不仅仅可以指定本模型上的某个属性要满足什么条件，还可以指定相关联的模型满足什么属性。比如现在想要获取写过标题为`abc`的所有用户，那么可以这样写：

```python
users = User.objects.filter(article__title='abc')
```

如果你设置了`related_name`为`articles`，因为反转的过滤器的名字将使用`related_name`的名字，那么上例代码将改成如下：

```python
users = User.objects.filter(articles__title='abc')
```

可以通过`related_query_name`将查询的反转名字修改成其他的名字。比如`article`。示例代码如下：

```python
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    # 传递related_name参数，以后在方向引用的时候使用articles进行访问
    author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles',related_query_name='article')
```

那么在做反向过滤查找的时候就可以使用以下代码：

```python
users = User.objects.filter(article__title='abc')
```