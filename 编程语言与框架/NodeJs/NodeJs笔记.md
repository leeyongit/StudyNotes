## 使用yum安装Node.js和npm

在撰写本文时，CentOS 8存储库中包含的Node.js版本是`10.19.0`，它是先前的TLS版本。 安装非常简单。 运行以下yum命令即可安装Node.js和npm：

```bash
yum -y install nodejs npm
```

Copy

安装完成后，通过运行以下nod命令验证安装：

```bash
node --version
```

