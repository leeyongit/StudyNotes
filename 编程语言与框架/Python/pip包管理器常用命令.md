## pip 包管理器的常用命令和功能：

1. 基础安装命令
```bash
# 安装包
pip install package_name

# 安装指定版本
pip install package_name==1.2.3

# 安装最低版本
pip install package_name>=1.2.3

# 从 requirements.txt 安装
pip install -r requirements.txt
```

2. 查看和搜索命令
```bash
# 查看已安装包信息
pip show package_name

# 查看所有已安装的包
pip list

# 查看可升级的包
pip list --outdated

# 搜索包
pip search package_name  # 注：某些新版本可能已禁用此功能
```

3. 更新和卸载命令
```bash
# 更新包
pip install --upgrade package_name

# 更新 pip 自身
pip install --upgrade pip

# 卸载包
pip uninstall package_name
```

4. 导出和环境管理
```bash
# 导出当前环境的所有包
pip freeze > requirements.txt
# 结合 --exclude-editable 选项来避免生成本地路径的依赖项
pip freeze --exclude-editable > requirements.txt

# 指定源安装
pip install package_name -i https://pypi.tuna.tsinghua.edu.cn/simple
```

5. 缓存管理
```bash
# 查看缓存
pip cache list

# 清理缓存
pip cache purge
```

6. 版本控制语法
```bash
package_name==1.2.3    # 指定版本
package_name>=1.2.3    # 最小版本
package_name<=1.2.3    # 最大版本
package_name~=1.2.3    # 兼容版本
package_name!=1.2.3    # 排除版本
```

7. 常用选项
```bash
-v, --verbose          # 显示详细信息
-q, --quiet           # 最小输出
--no-cache-dir        # 禁用缓存
--user                # 仅为当前用户安装
-U, --upgrade         # 升级包
```

8. 虚拟环境相关
```bash
# 创建虚拟环境
python -m venv myenv

# 激活虚拟环境
# Windows:
myenv\Scripts\activate
# Linux/Mac:
source myenv/bin/activate

# 退出虚拟环境
deactivate
```

注意事项：
1. 建议总是在虚拟环境中安装包，避免污染系统环境
2. 在国内使用时，可以考虑使用国内镜像源加速下载
3. 在生产环境中，最好使用 requirements.txt 固定依赖版本
4. 安装包之前先检查其是否兼容当前 Python 版本
5. 某些包可能需要系统级依赖，可能需要先安装对应的系统包

