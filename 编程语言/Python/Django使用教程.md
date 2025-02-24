## Django使用教程

下面是Django框架的基本使用教程:

1. **安装 Django**:
```
pip install django
```

2. **创建一个新的 Django 项目**:
```
django-admin startproject myproject
```
这将创建一个名为 `myproject` 的新 Django 项目。

3. **启动开发服务器**:
```
cd myproject
python manage.py runserver
```
这将启动 Django 开发服务器,可以在浏览器中访问 `http://127.0.0.1:8000/` 查看默认页面。

4. **创建一个应用程序**:
```
python manage.py startapp myapp
```
这将创建一个名为 `myapp` 的新 Django 应用程序。

5. **定义模型**:
在 `myapp/models.py` 文件中添加以下代码:
```python
from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```
这定义了一个名为 `MyModel` 的数据模型,包含 `name`、`description` 和 `created_at` 三个字段。

6. **注册应用程序**:
在 `myproject/settings.py` 文件的 `INSTALLED_APPS` 列表中添加 `'myapp'`。

7. **创建数据库迁移**:
```
python manage.py makemigrations myapp
python manage.py migrate
```
这将为 `MyModel` 创建数据库迁移,并应用到数据库中。

8. **创建视图和 URL 路由**:
在 `myapp/views.py` 文件中添加以下代码:
```python
from django.shortcuts import render
from .models import MyModel

def index(request):
    objects = MyModel.objects.all()
    return render(request, 'myapp/index.html', {'objects': objects})
```
在 `myapp/urls.py` 文件中添加以下代码:
```python
from django.urls import path
from . import views

app_name = 'myapp'
urlpatterns = [
    path('', views.index, name='index'),
]
```
这定义了一个名为 `index` 的视图函数,并将其映射到 `/` 的 URL 路由。

9. **创建模板**:
在 `myapp/templates/myapp/` 目录下创建 `index.html` 文件,并添加以下内容:
```html
{% if objects %}
    <ul>
    {% for obj in objects %}
        <li>{{ obj.name }} - {{ obj.description }}</li>
    {% endfor %}
    </ul>
{% else %}
    <p>No objects found.</p>
{% endif %}
```
这是一个简单的模板,用于显示 `MyModel` 实例的列表。

10. **运行开发服务器**:
```
python manage.py runserver
```
现在,你可以在浏览器中访问 `http://127.0.0.1:8000/myapp/` 查看应用程序的运行情况。

这只是 Django 的一个基本入门教程,实际使用中可以根据需求继续探索更多的功能和特性。Django 提供了丰富的文档和资源,可以帮助你更好地掌握和使用这个强大的 Python Web 框架。

11. **创建超级用户**

> Django 自带了一个功能强大的后台管理系统,只要你创建了一个超级用户,就可以直接访问后台管理界面进行登录。

创建超级用户:

```
python manage.py createsuperuser
```

按照提示输入用户名、电子邮件和密码即可。

- 访问后台管理界面:
   在浏览器中访问 `http://127.0.0.1:8000/admin/`。你应该会看到 Django 默认的后台管理登录页面。

- 使用刚刚创建的超级用户账号登录:
   输入用户名和密码,点击"Log in"即可进入后台管理界面。

