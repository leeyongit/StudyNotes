### **Kubernetes 包管理器 Helm 入门指南**

Helm 是 Kubernetes 的包管理器，用于简化应用程序的定义、安装和管理。它通过打包 YAML 文件成为 Chart，提供了高效的应用部署、更新和版本回滚功能。

------

### **一、Helm 的核心概念**

1. **Chart**
   - Chart 是 Helm 包的基本单位，包含应用的 Kubernetes 配置模板。
   - 一个 Chart 可以用于部署一个简单的应用或复杂的服务。
2. **Release**
   - Release 是某个 Chart 部署后的运行实例。
   - 例如：通过 Helm 部署一个 Nginx Chart 会生成一个 Nginx Release。
3. **Repository**
   - 存放 Charts 的集合，可以是官方仓库或自定义仓库。

------

### **二、安装 Helm**

#### 1. **在 Linux/macOS/Windows 安装**

- 使用脚本安装：

  ```bash
  curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
  ```

- 验证安装：

  ```bash
  helm version
  ```

#### 2. **通过包管理器安装**

- macOS (Homebrew)：

  ```bash
  brew install helm
  ```

- Ubuntu (Snap)：

  ```bash
  sudo snap install helm --classic
  ```

------

### **三、Helm 常用命令**

#### 1. **初始化并添加仓库**

- 查看默认仓库：

  ```bash
  helm repo list
  ```

- 添加官方稳定仓库：

  ```bash
  helm repo add stable https://charts.helm.sh/stable
  ```

- 更新仓库：

  ```bash
  helm repo update
  ```

#### 2. **搜索和查看 Charts**

- 搜索 Chart：

  ```bash
  helm search repo <chart-name>
  ```

- 查看 Chart 详情：

  ```bash
  helm show chart <repo/chart-name>
  ```

- 查看 Chart 的 YAML 配置：

  ```bash
  helm show values <repo/chart-name>
  ```

#### 3. **安装应用**

- 基本安装：

  ```bash
  helm install <release-name> <repo/chart-name>
  ```

  例如：

  ```bash
  helm install my-nginx stable/nginx
  ```

- 指定自定义参数：

  ```bash
  helm install <release-name> <repo/chart-name> --set <key=value>
  ```

- 使用自定义配置文件：

  ```bash
  helm install <release-name> <repo/chart-name> -f values.yaml
  ```

#### 4. **查看和管理 Release**

- 查看所有 Release：

  ```bash
  helm list
  ```

- 查看指定 Release 的状态：

  ```bash
  helm status <release-name>
  ```

- 卸载 Release：

  ```bash
  helm uninstall <release-name>
  ```

#### 5. **升级和回滚 Release**

- 升级 Release：

  ```bash
  helm upgrade <release-name> <repo/chart-name> --set <key=value>
  ```

- 查看历史版本：

  ```bash
  helm history <release-name>
  ```

- 回滚到之前的版本：

  ```bash
  helm rollback <release-name> <revision>
  ```

------

### **四、创建和发布 Chart**

#### 1. **创建自定义 Chart**

- 创建 Chart 目录：

  ```bash
  helm create <chart-name>
  ```

  生成的目录结构：

  ```
  <chart-name>/
  ├── Chart.yaml         # Chart 元信息
  ├── values.yaml        # 默认值文件
  ├── templates/         # K8s 配置模板
  ```

#### 2. **打包 Chart**

- 将 Chart 打包为 

  ```
  .tgz
  ```

   文件：

  ```bash
  helm package <chart-directory>
  ```

#### 3. **上传 Chart 到仓库**

- 自建 Chart 仓库工具：
  - 使用 [ChartMuseum](https://github.com/helm/chartmuseum) 管理私有仓库。
  - 或将 `.tgz` 文件上传到对象存储（如 S3 或 GCS）。

------

### **五、Helm 的模板引擎**

Helm 使用 [Go 模板](https://pkg.go.dev/text/template) 生成 Kubernetes 配置文件。

#### 1. **动态值插入**

在模板中插入变量：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-config
data:
  key: {{ .Values.someKey }}
```

#### 2. **控制语句**

- 条件语句：

  ```yaml
  {{- if .Values.enableFeature }}
  ...
  {{- end }}
  ```

- 循环语句：

  ```yaml
  {{- range $key, $value := .Values.mapKey }}
  ...
  {{- end }}
  ```

#### 3. **调试模板**

- 验证模板生成的 YAML：

  ```bash
  helm template <release-name> <chart-name>
  ```

------

### **六、常见 Helm 使用场景**

1. **部署常用应用**
   - NGINX、MySQL、Redis 等开源软件通常都有官方或社区维护的 Helm Chart，极大简化了部署工作。
2. **应用管理**
   - 使用 Helm 管理多实例部署和参数化部署。
3. **CI/CD 集成**
   - 将 Helm 部署集成到 CI/CD 工具（如 Jenkins、GitLab CI）。
4. **多环境支持**
   - 使用不同的 `values.yaml` 文件，实现开发、测试、生产环境的配置分离。

------

### **七、Helm 的优缺点**

#### 优点：

- 简化 Kubernetes 应用的部署和管理。
- 通过模板支持灵活的配置。
- 管理版本历史，可回滚。
- 丰富的社区和官方 Chart 支持。

#### 缺点：

- 模板的复杂性可能导致调试困难。
- 需要学习 Go 模板语法。

