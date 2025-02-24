`pipreqs` 是一个非常有用的 Python 包,用于自动生成项目的 `requirements.txt` 文件。它可以分析您的 Python 项目并列出所有已使用的依赖包,然后将它们保存到 `requirements.txt` 文件中。这对于项目迁移或者在不同环境中安装项目依赖非常方便。

> 使用pipreqs一键安装Python项目所有依赖包

```python
# 安装pipreqs工具
pip install pipreqs 
# 在项目根目录下执行  生成所需依赖包名、版本
pipreqs ./
# 安装所需依赖
pip install -r requirements.txt
```



以下是 `pipreqs` 的一些常见用法:

1. **生成 requirements.txt 文件**:
   ```
   pipreqs /path/to/project/directory
   ```
   这会在项目目录下生成一个 `requirements.txt` 文件,其中列出了所有项目依赖的 Python 库。

2. **指定输出文件名**:
   ```
   pipreqs --force --output=project_requirements.txt /path/to/project/directory
   ```
   这会将依赖包列表保存到 `project_requirements.txt` 文件中。`--force` 参数确保即使 `requirements.txt` 文件已存在,也会覆盖它。

3. **忽略某些包**:
   ```
   pipreqs --ignore mysql-connector,scrapy /path/to/project/directory
   ```
   这会在生成 `requirements.txt` 文件时忽略 `mysql-connector` 和 `scrapy` 两个包。

4. **只包含直接依赖**:
   ```
   pipreqs --no-pin /path/to/project/directory
   ```
   这会只列出项目直接依赖的包,而不包括这些包的依赖。默认情况下,`pipreqs` 会列出所有直接和间接依赖。

5. **指定 Python 解释器版本**:
   ```
   pipreqs --python-version 3.9 /path/to/project/directory
   ```
   这会根据 Python 3.9 版本生成 `requirements.txt` 文件。

总的来说,`pipreqs` 是一个非常方便的工具,可以帮助您轻松管理 Python 项目的依赖关系。它可以节省大量手动维护 `requirements.txt` 文件的时间和精力。