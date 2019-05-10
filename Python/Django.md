Django
======
你能知道 Django 已被安装，且安装的是哪个版本，通过在命令提示行输入命令
~ python -m django --version

## 创建项目
~ django-admin startproject mysite

## 用于开发的简易服务器
~ python manage.py runserver

## 通过运行 makemigrations 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次 迁移。
～ python3 manage.py  makemigrations polls
## 返回对应sql
～ python manage.py sqlmigrate polls 0001
## 运行 migrate 命令，在数据库里创建新定义的模型的数据表：
～ python3 manage.py  migrate
## 超级用户初始化方法
~ python3 manage.py createsuperuser
> admin 123456

## 创建一个应用
～ python manage.py startapp polls