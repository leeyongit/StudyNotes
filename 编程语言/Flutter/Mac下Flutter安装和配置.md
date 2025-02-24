在 macOS 15 系统中，默认的 Shell 是 `zsh`，因此在安装 Flutter 时，环境变量的配置需要针对 `zsh` 进行调整。以下是基于 `zsh` 的 Flutter 安装和配置步骤：

---

### 1. **系统要求**
- 操作系统：macOS（64 位）
- 磁盘空间：至少 2.8 GB（不包括 IDE/工具的磁盘空间）
- 工具：需要安装 `bash`、`curl`、`git`、`mkdir`、`rm`、`unzip`、`which` 等基础工具。

---

### 2. **安装 Flutter SDK**
#### 方法一：通过官方下载
1. 访问 [Flutter 官网](https://flutter.dev/docs/get-started/install/macos) 下载 Flutter SDK 的稳定版。
2. 解压下载的压缩包到目标目录，例如：
   ```bash
   cd ~/Documents
   unzip ~/Downloads/flutter_macos_3.3.10-stable.zip
   ```
3. 配置环境变量：
   - 打开终端，编辑 `~/.zshrc` 文件（`zsh` 的配置文件）：
     ```bash
     vim ~/.zshrc
     ```
   - 添加以下内容（根据实际路径修改）：
     ```bash
     export PATH="$PATH:`pwd`/flutter/bin"
     export PUB_HOSTED_URL=https://pub.flutter-io.cn
     export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
     ```
   - 保存并退出（按 `Esc`，然后输入 `:wq`）。
   - 刷新配置文件：
     ```bash
     source ~/.zshrc
     ```
   - 验证是否成功：
     ```bash
     echo $PATH
     which flutter
     ```

#### 方法二：通过 Homebrew 安装
1. 安装 Homebrew（如果尚未安装）：
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
2. 通过 Homebrew 安装 Flutter：
   ```bash
   brew install --cask flutter
   ```
3. 验证安装：
   ```bash
   flutter --version
   ```

---

### 3. **运行 `flutter doctor`**
1. 在终端运行以下命令，检查环境配置：
   ```bash
   flutter doctor
   ```
2. 根据 `flutter doctor` 的输出，安装缺失的依赖项。例如：
   - 安装 Xcode 命令行工具：
     ```bash
     xcode-select --install
     ```
   - 安装 Rosetta 2（适用于 Apple Silicon Mac）：
     ```bash
     sudo softwareupdate --install-rosetta --agree-to-license
     ```
   - 安装 Android Studio 并配置 Android SDK。

---

### 4. **安装 IDE 插件**
1. 打开 Android Studio 或 VS Code。
2. 安装 Flutter 和 Dart 插件：
   - 在 Android Studio 中，进入 `Preferences > Plugins`，搜索并安装 Flutter 和 Dart 插件。
   - 在 VS Code 中，进入扩展市场，搜索并安装 Flutter 和 Dart 插件。

---

### 5. **创建并运行 Flutter 项目**
1. 在 Android Studio 中，选择 `File > New > New Flutter Project`，创建一个新的 Flutter 项目。
2. 运行项目：
   - 连接设备（物理设备或模拟器）。
   - 在终端运行：
     ```bash
     flutter run
     ```

---

### 6. **常见问题**
- **网络问题**：如果下载 Flutter SDK 或依赖项时遇到网络问题，可以使用国内镜像（如 `pub.flutter-io.cn` 和 `storage.flutter-io.cn`）。
- **Apple Silicon 支持**：确保安装 Rosetta 2 以兼容部分工具。

---

### 7. **针对 `zsh` 的注意事项**
- macOS 15 默认使用 `zsh`，因此所有环境变量配置都需要写入 `~/.zshrc`，而不是 `~/.bash_profile` 或 `~/.bashrc`。
- 如果之前配置过 `~/.bash_profile`，可以将其内容迁移到 `~/.zshrc` 中。

---

通过以上步骤，您可以在 macOS 15 系统上成功安装并配置 Flutter 开发环境。如果遇到问题，可以参考 `flutter doctor` 的输出或相关文档进行调整。