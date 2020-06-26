# 使用Docker搭建git服务Gogs

## 步骤

1. 首先拉取gogs镜像

   ```sh
   sudo docker pull gogs/gogs
   ```

2. 创建本地目录存放数据

    ```sh
    sudo mkdir -p /volume1/fshd/gogs
    ```

3. 使用run命令绑定端口和一些配置文件

   ```sh
   sudo docker run -d --name=fshd_gogs -p 1022:22 -p 1080:3000 -v /volume1/fshd/gogs:/data gogs/gogs
   ```

