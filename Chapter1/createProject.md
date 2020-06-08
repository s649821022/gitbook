# 创建一个django项目

#### 环境准备

- python3.6/3.7环境
- virtualenvwrapper（为了管理django创建的虚拟环境）
- pycharm专业版
- mysql5.7（windows版本[下载链接](https://dev.mysql.com/downloads/windows/installer/5.7.html)，其他操作系统[下载链接](https://dev.mysql.com/downloads/mysql/)）



#### 用pycharm创建django项目

![](http://m.qpic.cn/psc?/V12zIqyN1nULTY/IuPP.iXwoDzL23w4nRtQckEN8C2jXbn5cfFjyQzLfydIdepGjA3KuRXTYsRpB7vfUmeG8aXZ6zILqYN1*9XTQA!!/b&bo=3QNtAgAAAAADB5M!&rf=viewer_4)



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

![](http://m.qpic.cn/psc?/V12zIqyN1nULTY/Wikhxl.6Q5GRLBXYyjrKSolR0H0*Onm7CJxUdduA6sJJuc*fn6pibFbWh7b1wMEs38TFW7WaBpAzIBvVWgthEUtjt9NM2x4nSG8ea5X4pds!/b&bo=CQOIAQAAAAADF7E!&rf=viewer_4)

> 以后就不用再命令行里使用`python manage.py + 命令`
>
> 直接在pycharm自带的工具中输入命令

![](http://m.qpic.cn/psc?/V12zIqyN1nULTY/Wikhxl.6Q5GRLBXYyjrKSnzXyVVNPO7wkk7Pq75NuOg6dFLbppRDpj*AAeR3*axpqlDmoAJ2rtle1J4JhG5fInZ8cn4FqqI1nRY*X86k2PY!/b&bo=cwMRAQAAAAADF1I!&rf=viewer_4)