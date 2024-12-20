要在 macOS 上安装 Maven

1. **使用 Homebrew 安装 Maven**（推荐）： 如果您已经安装了 Homebrew，可以通过 Homebrew 来安装 Maven。在终端中执行以下命令：

   ```bash
   brew install maven
   ```

   这将自动下载并安装最新版本的 Maven。

1. **手动安装 Maven**： 如果您希望手动安装 Maven，则可以按照以下步骤操作：

   - 前往 Maven 官方网站（https://maven.apache.org/download.cgi）下载 Maven 的二进制文件。

   - 解压下载的文件到您选择的位置。例如，您可以将其解压到 `/usr/local` 目录下。

   - 配置环境变量： 在终端中打开 `~/.bash_profile` 或 `~/.bashrc` 文件，并添加以下行：

     ```bash
     export M2_HOME=/usr/local/apache-maven-x.y.z  # 将 x.y.z 替换为您下载的 Maven 版本号
     export PATH=$M2_HOME/bin:$PATH
     ```

     然后执行以下命令使配置生效：

     ```
     source ~/.bash_profile  # 或 source ~/.bashrc
     ```

   - 验证安装： 在终端中执行以下命令来验证 Maven 是否安装成功：

     ```bash
     mvn -v
     ```

     如果一切正常，您应该看到 Maven 的版本信息。

### 在 Visual Studio Code 中创建和管理 Java 项目

在 Visual Studio Code 中创建和管理 Java 项目非常简单。下面是一些基本步骤：

1. **安装 Java Extension Pack**：
   打开 Visual Studio Code，并在扩展商店搜索并安装 Java Extension Pack。这个扩展包提供了 Java 开发所需的一切工具和功能，包括语法高亮、代码提示、调试支持等等。

2. **创建新项目**：
   在 Visual Studio Code 中，您可以选择使用 Maven 或 Gradle 等构建工具来管理 Java 项目。如果您选择使用 Maven，可以按照以下步骤创建新项目：
   
   - 打开 Visual Studio Code，并选择一个文件夹作为您的项目根目录。

   - 在终端中执行以下命令来创建一个 Maven 项目：
   
     ```
     mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
     ```

     这会在您选择的文件夹中创建一个名为 `my-app` 的 Maven 项目。
     
     > 使用 `mvn archetype:generate` 命令生成的项目是一个简单的 Maven 项目，使用的是 Maven 的快速启动原型（maven-archetype-quickstart）。
   
3. **打开项目**：
   在 Visual Studio Code 中打开您刚刚创建的项目文件夹。

4. **编写代码**：
   使用 Visual Studio Code 的编辑器编写 Java 源代码。您可以在 `src/main/java` 目录中创建您的 Java 源文件。

5. **运行程序**：
   您可以使用 Maven 插件或直接在终端中执行 Maven 命令来构建和运行项目。例如，要运行 Maven 项目，您可以在终端中执行以下命令：

   ```
   mvn clean package
   java -cp target/my-app-1.0-SNAPSHOT.jar com.example.App
   ```

   这将编译并运行项目中的主类 `App`。

6. **调试程序**：
   您可以使用 Visual Studio Code 的调试功能来调试 Java 代码。请确保您的项目中有一个适当的调试配置，并且您已经安装了 Java 调试器扩展。

