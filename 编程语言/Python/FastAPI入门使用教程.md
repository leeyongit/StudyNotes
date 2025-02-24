## FastAPI 入门使用教程

下面是 FastAPI 的快速入门使用教程:

1. **安装 FastAPI**:
```
pip install fastapi
```
FastAPI 还需要 Uvicorn 作为服务器,也需要安装:
```
pip install uvicorn
```

2. **创建一个 FastAPI 应用程序**:
在一个新的 Python 文件中,例如 `main.py`，添加以下代码:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```
这个示例创建了一个 FastAPI 应用程序,并定义了两个 API 端点:
- `GET /`: 返回一个简单的 JSON 响应
- `GET /items/{item_id}`: 接受一个 `item_id` 参数并返回一个包含 `item_id` 和可选 `q` 参数的 JSON 响应。

3. **运行 FastAPI 应用程序**:
在终端中运行以下命令:
```
uvicorn main:app --reload
```
这将启动 Uvicorn 服务器,并运行 FastAPI 应用程序。
- `main:app` 指定应用程序的入口点。`main` 是文件名(不带 `.py` 扩展名),`app` 是创建的 FastAPI 实例。
- `--reload` 选项使 Uvicorn 在代码更改时自动重新加载应用程序。

4. **测试 API 端点**:
在浏览器中访问 `http://127.0.0.1:8000/` 和 `http://127.0.0.1:8000/items/5?q=somequery`，你应该能看到相应的 JSON 响应。

5. **添加数据模型**:
FastAPI 支持基于 Pydantic 的数据模型。让我们创建一个简单的 Item 模型:
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

@app.post("/items/")
def create_item(item: Item):
    return item
```
现在，你可以发送一个 POST 请求到 `/items/` 端点,并在请求体中包含一个 JSON 格式的 Item 对象。FastAPI 会自动解析请求体,并将其转换为 Item 模型实例。

6. **文档和交互式 API 浏览器**:
FastAPI 会自动生成一个交互式 API 文档,你可以在 `http://127.0.0.1:8000/docs` 访问。这个文档允许你测试 API 端点,并提供了对 API 的详细说明。

这个入门教程展示了 FastAPI 的一些基本功能。实际应用中,你可以进一步了解 FastAPI 的其他特性,如路由参数、请求验证、数据库集成、中间件等。FastAPI 的文档提供了更多详细的信息和示例,可以帮助你更好地掌握和使用这个强大的 Python Web 框架。