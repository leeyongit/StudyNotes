# linux系统设置密钥登录，禁用root帐号密码登录

1. 生成密钥
使用 ssh-keygen 时，请先进入到 ~/.ssh 目录，不存在的话，请先创建
```sh
ssh-keygen -t rsa -C "我的SSH密钥"
           ~~文件名   ~~~~ 备注
使用 ssh-kengen 会在~/.ssh/目录下生成两个文件，不指定文件名和密钥类型的时候，默认生成的两个文件是：
id_rsa
id_rsa.pub
第一个是私钥文件，第二个是公钥文件。
```

2. 在root用户家目录下创建隐藏目录.ssh，并在此目录下创建以authorized_key文件，并将事先生成的公钥放入authorized_key中。
```sh
    mkdir -v ~/.ssh
    vim ~/.ssh/authorized_keys
```

3. 修改权限
```sh
    chmod 600 ~/.ssh/authorized_keys
    chmod 700 .ssh/
```

4. 清空防火墙规则和关闭selinux
```sh
    iptables -F
    setenforce 0
```

5. 修改ssh配置文件,去掉注释，使其支持密钥认证。Vim /etc/ssh/sshd_config
```vim
    RSAAuthentication yes
    PubkeyAuthentication yes
```

6. 重启sshd服务
```sh
    /etc/init.d/sshd restart
    systemctl start sshd.service
```
新建会话 填写名称跟主机，确定登录