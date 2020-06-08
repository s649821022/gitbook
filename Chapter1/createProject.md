# 创建一个django项目

#### 环境准备

- python3.6/3.7环境
- virtualenvwrapper（为了管理django创建的虚拟环境）
- pycharm专业版
- mysql5.7（windows版本[下载链接](https://dev.mysql.com/downloads/windows/installer/5.7.html)，其他操作系统[下载链接](https://dev.mysql.com/downloads/mysql/)）



#### 用pycharm创建django项目

![](http://i1.fuimg.com/720825/236bcdbb9c295e27.png)



#### django内置的操作命令

可以在命令行输入`python manage.py help`并按回车键，即可查看

> 下面列举一些常见的命令

| 指令            | 说明                                                    |
| :-------------- | ------------------------------------------------------- |
| createsuperuser | 为内置用户表创建超级管理员账号                          |
| flush           | 清空数据表的信息                                        |
| makemigrations  | 从模型对象创建数据迁移文件并保存在App的migrations文件夹 |
| migrate         | 根据迁移文件内容，在数据库中生成相对应的数据表          |
| shell           | 进入django的Shell模式，用于调试项目功能                 |
| startapp        | 创建项目应用APP                                         |
| startproject    | 创建新的django项目                                      |
| runserver       | 在本地计算机上启动Django项目                            |

#### 分享一个pycharm自带的manage.py工具

![](http://i1.fuimg.com/720825/b4b3893bbbd04ab5.png)

> 以后就不用再命令行里使用`python manage.py + 命令`
>
> 直接在pycharm自带的工具中输入命令

![](http://i1.fuimg.com/720825/2af09249314b14c4.png)