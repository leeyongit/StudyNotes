## CentOS安装Python3.8

由于 CentOS 的默认仓库不包含 Python 3.8，我们需要从源码编译安装。以下是详细步骤：

1. 首先安装必要的编译工具和依赖：
```bash
sudo yum groupinstall "Development Tools" -y
sudo yum install openssl-devel bzip2-devel libffi-devel xz-devel wget -y
```

2. 下载和解压 Python 3.8 源码：
```bash
cd /opt
sudo wget https://www.python.org/ftp/python/3.8.16/Python-3.8.16.tgz
sudo tar xzf Python-3.8.16.tgz
```

3. 配置和编译安装：
```bash
cd Python-3.8.16
sudo ./configure --enable-optimizations
sudo make altinstall
```
注意：使用 `make altinstall` 而不是 `make install`，这样可以避免覆盖系统默认的 python3 命令。

4. 验证安装：
```bash
python3.8 --version
```

5. 创建虚拟环境（推荐）：
```bash
python3.8 -m venv myenv
source myenv/bin/activate
```

6. 升级 pip：
```bash
pip install --upgrade pip
```

7. 如果需要设置 Python 3.8 为默认版本，可以创建软链接：
```bash
sudo ln -sf /usr/local/bin/python3.8 /usr/bin/python3
sudo ln -sf /usr/local/bin/pip3.8 /usr/bin/pip3
```

注意事项：
1. 确保有足够的磁盘空间（至少 500MB）
2. 编译过程可能需要一些时间
3. 不要删除系统默认的 Python 版本，因为很多系统工具依赖它
4. 最好在虚拟环境中工作，避免影响系统 Python



## 安装pip3.8

安装完 Python 3.8 后如何安装 pip。有两种方法：

方法1：使用 get-pip.py 脚本安装
```bash
# 1. 下载 get-pip.py 脚本
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py

# 2. 使用 Python 3.8 运行脚本安装 pip
python3.8 get-pip.py

# 3. 验证安装
pip3.8 --version
```

方法2：使用 ensurepip 模块安装
```bash
# 1. 使用 Python 的内置 ensurepip 模块
python3.8 -m ensurepip --upgrade

# 2. 升级到最新版本
python3.8 -m pip install --upgrade pip
```

如果遇到权限问题，可以添加 `sudo`：
```bash
sudo python3.8 -m pip install --upgrade pip
```

验证安装是否成功：
```bash
# 检查 pip 版本
pip3.8 --version

# 或者
python3.8 -m pip --version
```

如果需要将 pip3.8 设置为默认的 pip3，可以创建软链接：
```bash
sudo ln -sf /usr/local/bin/pip3.8 /usr/bin/pip3
```

注意事项：
1. 确保先安装好 Python 3.8
2. 如果出现 SSL 错误，可能需要安装 openssl-devel：
```bash
sudo yum install openssl-devel
```
