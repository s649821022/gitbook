#### 1. INSTALLED_APPS中自带的app

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

- admin：内置的后台管理系统
- auth：内置的用户认证系统
- contenttypes：记录项目中所有model元数据（Django的ORM框架）
- sessions：Session会话功能，用于标识当前访问网站的用户身份，记录相关用户信息
- messages：消息提示功能
- staticfiles：查找静态资源路径



#### 2.资源集合-------STATICFILES_DIRS

由于STATIC_URL的特殊性，在开发中会造成诸多不便，比如将静态文件夹存放在项目的根目录以及多个静态文件夹，那么可以在setting.py文件中添加STATICFILES_DIRS属性。该属性以列表形式表示

```python
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static'),
                    os.path.join(BASE_DIR, '对应的其他存储static的路径')]
```

#### 3.资源部署-------STATIC_ROOT

静态资源配置还有STATIC_ROOT，其作用是在服务器上部署项目，实现服务器和项目之间的映射。STATIC_ROOT主要收集整个项目的静态资源并存放在一个新的文件夹

```python
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

当项目属性DEBUG设为True时，Django会自动提供静态文件代理服务，无须使用STATIC_ROOT

当项目属性DEBUG设为FALSE时，Django不再提供静态文件代理服务，此时需要在项目中配置STATIC_ROOT

#### 4.媒体资源-------MEDIA

media资源类似于static静态文件，只不过对于一些经常变动的文件，通常将其存放在媒体资源文件夹，然后在配置文件setting.py里设置MEDIA_ROOT和MEDIA_URL，MEDIA_URL用于设置媒体资源的路由地址，MEDIA_ROOT用于获取media文件夹在计算机系统的完整路径

```python
# 设置媒体路由地址信息
MEDIA_URL = '/media/'
# 获取media文件夹的完整路径信息
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

配置好后，还需要将media文件夹注册到Django中，让Django知道如何找到媒体文件，否则无法在浏览器上访问该文件夹的文件信息

```
# 首先需要导入包，配置媒体文件夹
from django.views.static import serve
from django.conf import settings
# 配置媒体文件的路由地址
re_path('media/(?P<path>.*)', serve,
            {'document_root': settings.MEDIA_ROOT}, name='media'),
```

#### 5.模板配置

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- BACKEND：定义模板引擎，用于识别模板里面的变量和指令。内置的模板引擎有DjangoTemplates和jinjia2.jinjia2，每个模板引擎都有自己的变量和指令语法
- DIRS：设置模板路径，默认为放在templates文件夹中
- APP_DIRS：是否在App里查找模板文件，默认为True
- OPTIONS：用于填充在RequestContext的上下文（模板里面的变量和指令），一般情况下不做任何修改

#### 6.中间件

**<u>每个中间件的顺序是固定的，不能随意变换中间件，否则容易导致程序异常</u>**

```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    # 添加中间件LocaleMiddleware,django内置的功能支持中文显示
    'django.middleware.locale.LocaleMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

- SecurityMiddleware：内置的安全机制，保护用户与网站的通信安全
- SessionMiddleware：会话session功能
- LocaleMiddleware：国际化和本地化功能
- CommonMiddleware：处理请求信息，规范化请求内容
- CsrfViewMiddleware：开启CSRF防护功能
- AuthenticationMiddleware：开启内置的用户认证系统
- MessageMiddleware：开启内置的信息提示功能
- XFrameOptionsMiddleware：防止恶意程序单击劫持