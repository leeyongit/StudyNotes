## Django REST framework (DRF) 的基础使用教程：

### 快速入门

1. 安装 Django 和 Django REST Framework

```bash
pip install django
pip install djangorestframework
```

2. 创建 Django 项目

```bash
django-admin startproject django_rest
```

3. 创建 Django 应用

```
python manage.py startapp api
```

4. 在 Django 项目的 settings.py 中添加 DRF和api应用：

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'api',
]
```

5. 使用替代的 MySQL 客户端库

可以考虑使用 `PyMySQL` 作为 MySQL 的客户端库，它是纯 Python 实现的，不需要编译 C 扩展：

```
pip install PyMySQL
```

然后在 Django 的 `settings.py` 中添加以下内容：

```bash
import pymysql
pymysql.install_as_MySQLdb()
```

这样就可以使用 `PyMySQL` 代替 `mysqlclient` 连接 MySQL 数据库。

6. 配置 Django 使用 MySQL

在你的 Django 项目的 `settings.py` 文件中配置数据库连接。假设你已经安装了 MySQL，并且已经有了一个数据库（例如：`mydatabase`），你可以在 `DATABASES` 配置中这样设置：

```bash
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 使用 MySQL 数据库
        'NAME': 'mydatabase',  # 数据库名
        'USER': 'yourusername',  # MySQL 用户名
        'PASSWORD': 'yourpassword',  # MySQL 密码
        'HOST': 'localhost',  # 数据库主机，通常是 localhost
        'PORT': '3306',  # 默认 MySQL 端口
    }
}
```

现在第一次同步你的数据库：

```
python manage.py migrate
```

我们还要创建一个名为 `admin` 的初始用户，密码为 `password123`。我们稍后将在该示例中验证该用户。

```bash
python manage.py createsuperuser
```

7. 创建一个简单的模型(models.py):

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=5, decimal_places=2)
    published_date = models.DateField()

    def __str__(self):
        return self.title
```

运行 makemigrations 和 migrate 命令来创建相应的数据库表

```
python manage.py makemigrations # 创建新的迁移文件 
python manage.py migrate # 应用所有未应用的迁移
```



8. 创建序列化器(serializers.py):

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'price', 'published_date']
```

9. 创建视图(views.py):

```python
from rest_framework import viewsets
from .models import Book
from .serializers import BookSerializer

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

10. 配置 URL 路由(urls.py):

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BookViewSet

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]
```

这样就完成了一个基本的 REST API。现在你可以：

- GET /api/books/ - 获取所有图书列表
- POST /api/books/ - 创建新图书
- GET /api/books/{id}/ - 获取特定图书
- PUT /api/books/{id}/ - 更新特定图书
- DELETE /api/books/{id}/ - 删除特定图书

### 进阶用法

1. 添加过滤功能：

```python
from django_filters.rest_framework import DjangoFilterBackend

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['author', 'published_date']
```

2. 添加分页：

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}
```

3. 添加认证：

```python
# views.py
from rest_framework.permissions import IsAuthenticated

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsAuthenticated]
```

4. 自定义序列化：

```python
class BookSerializer(serializers.ModelSerializer):
    author_name = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'author', 'author_name', 'price']

    def get_author_name(self, obj):
        return f"作者: {obj.author}"
```

5. 添加搜索功能：

```python
from rest_framework.filters import SearchFilter

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    filter_backends = [SearchFilter]
    search_fields = ['title', 'author']
```

一些实用的建议：

1. 使用 `django-cors-headers` 处理跨域请求
2. 使用 `drf-yasg` 生成 API 文档
3. 使用 `django-filter` 进行高级过滤
4. 使用 `djangorestframework-simplejwt` 实现 JWT 认证

需要注意的是：

1. 在生产环境中要确保开启认证和权限控制
2. 合理使用缓存提高性能
3. 适当处理异常并返回合适的状态码
4. 注意 API 版本控制

要测试 API，你可以：

1. 使用 DRF 提供的可浏览 API 界面
2. 使用 Postman 或 curl 进行测试
3. 编写单元测试：

```python
from rest_framework.test import APITestCase

class BookTests(APITestCase):
    def test_create_book(self):
        data = {
            "title": "测试图书",
            "author": "测试作者",
            "price": "99.99",
            "published_date": "2024-01-01"
        }
        response = self.client.post('/api/books/', data, format='json')
        self.assertEqual(response.status_code, 201)
```

如果你需要更深入了解某个特定功能，请随时告诉我！