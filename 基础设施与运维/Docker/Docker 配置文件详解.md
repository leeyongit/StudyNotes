Docker 配置文件（`daemon.json`）用于配置 Docker 引擎的行为。该文件通常位于 `/etc/docker/daemon.json`（Linux）或 `%programdata%\docker\config\daemon.json`（Windows）路径下，用于存储 Docker Daemon（守护进程）启动时的设置。

### 常见的 Docker 配置项及详解：

#### 1. **`registry-mirrors`**

- **描述**：配置 Docker 使用的镜像加速器。通常用于提高拉取镜像的速度，尤其是在访问 Docker Hub 较慢的地区。

- 示例：

  ```json
  {
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn", "https://hub-mirror.c.163.com"]
  }
  ```

- **用途**：将镜像仓库的地址列出，Docker 会优先从这些加速器拉取镜像。

#### 2. **`insecure-registries`**

- **描述**：指定不使用 HTTPS（即不安全连接）的 Docker 镜像仓库的地址。通常用于企业内部或私有仓库，并且这些仓库没有启用 TLS 加密。

- 示例：

  ```json
  {
    "insecure-registries": ["10.0.0.12:5000"]
  }
  ```

- **用途**：允许 Docker 访问未启用 SSL/TLS 的仓库。默认情况下，Docker 只允许通过 HTTPS 协议访问镜像仓库。

#### 3. **`bip`**

- **描述**：设置 Docker 默认网络桥接（bridge）的 IP 地址。`bip` 代表 Docker 的默认桥接网络（`bridge`）的 IP 地址。通常在需要设置静态 IP 地址时使用。

- 示例：

  ```json
  {
    "bip": "192.168.1.5/24"
  }
  ```

- **用途**：通过此配置项，可以设置 Docker 桥接网络的 IP 地址段。

#### 4. **`default-runtime`**

- **描述**：指定 Docker 容器使用的默认运行时（runtime）。Docker 默认使用 `runc`，但可以配置为其他容器运行时（如 `containerd` 或 `kata-runtime`）。

- 示例：

  ```json
  {
    "default-runtime": "runc"
  }
  ```

- **用途**：指定容器运行时的默认选择。

#### 5. **`data-root`**

- **描述**：指定 Docker 存储数据的根目录。通常默认路径为 `/var/lib/docker`，但可以配置为其他路径。

- 示例：

  ```json
  {
    "data-root": "/mnt/docker-data"
  }
  ```

- **用途**：改变 Docker 存储容器数据和镜像的默认路径。

#### 6. **`debug`**

- **描述**：启用或禁用 Docker 守护进程的调试模式。启用调试模式会增加日志记录并帮助开发人员在排查问题时获得更多信息。

- 示例：

  ```json
  {
    "debug": true
  }
  ```

- **用途**：如果你遇到问题并需要更详细的日志输出，可以启用调试模式。

#### 7. **`log-driver`**

- **描述**：指定容器的日志驱动程序。可以使用不同的日志系统，如 `json-file`、`syslog`、`journald` 等。

- 示例：

  ```json
  {
    "log-driver": "json-file"
  }
  ```

- **用途**：选择日志的记录方式和存储位置。

#### 8. **`log-opts`**

- **描述**：为指定的日志驱动程序提供额外的配置选项。

- 示例：

  ```json
  {
    "log-opts": {
      "max-size": "10m",
      "max-file": "3"
    }
  }
  ```

- **用途**：与 `log-driver` 配合使用，设置日志文件的最大大小和数量等。

#### 9. **`storage-driver`**

- **描述**：指定容器存储驱动程序。Docker 支持多种存储驱动，例如 `overlay2`、`aufs`、`btrfs` 等。

- 示例：

  ```json
  {
    "storage-driver": "overlay2"
  }
  ```

- **用途**：指定 Docker 使用的存储驱动。`overlay2` 是推荐使用的驱动，通常适用于大多数 Linux 系统。

#### 10. **`max-concurrent-downloads`**

- **描述**：指定 Docker 同时从镜像仓库下载镜像的最大并发数。

- 示例：

  ```json
  {
    "max-concurrent-downloads": 3
  }
  ```

- **用途**：优化 Docker 镜像下载时的性能。特别是在带宽有限的情况下，调整并发数可以提高下载效率。

#### 11. **`max-concurrent-uploads`**

- **描述**：指定 Docker 同时上传镜像到仓库的最大并发数。

- 示例：

  ```json
  {
    "max-concurrent-uploads": 1
  }
  ```

- **用途**：优化 Docker 上传镜像时的性能，避免高并发上传导致网络拥堵。

#### 12. **`oom-kill-disable`**

- **描述**：是否禁用内存溢出时杀死容器的功能。如果启用，Docker 在容器超出内存限制时不会杀死该容器。

- 示例：

  ```json
  {
    "oom-kill-disable": true
  }
  ```

- **用途**：对于一些需要长时间运行的容器，可能希望禁用 OOM（内存不足）杀死容器的机制。

#### 13. **`ipv6`**

- **描述**：启用或禁用 Docker 容器的 IPv6 支持。

- 示例：

  ```json
  {
    "ipv6": true
  }
  ```

- **用途**：启用容器的 IPv6 地址支持，适用于需要支持 IPv6 网络的场景。

#### 14. **`dns`**

- **描述**：指定容器的 DNS 服务器。如果不设置，Docker 会使用默认的 DNS 配置。

- 示例：

  ```json
  {
    "dns": ["8.8.8.8", "8.8.4.4"]
  }
  ```

- **用途**：为容器指定 DNS 服务器，通常用于确保容器可以访问外部网络，特别是在企业网络环境中。

### 15. **`no-new-privileges`**

- **描述**：启用此配置后，Docker 容器将无法获得新的权限。通常用于提升容器安全性，防止容器中的进程提升特权。

- 示例：

  ```json
  {
    "no-new-privileges": true
  }
  ```

- **用途**：增强安全性，防止容器进程获得额外的权限。

### 示例 `daemon.json` 文件

这是一个典型的 `daemon.json` 配置文件示例，结合了多个常见配置项：

```json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn", "https://hub-mirror.c.163.com"],
  "insecure-registries": ["10.0.0.12:5000"],
  "bip": "192.168.1.5/24",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "max-concurrent-downloads": 3,
  "debug": true,
  "oom-kill-disable": true,
  "dns": ["8.8.8.8", "8.8.4.4"]
}
```

### 总结：

Docker 的 `daemon.json` 配置文件可以极大地影响 Docker 的行为和性能。通过正确配置，你可以优化 Docker 的网络连接、存储驱动、日志管理、以及安全性等方面。根据具体需求调整这些配置，确保 Docker 引擎的性能和安全性。