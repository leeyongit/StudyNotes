# 使用vscode开发flutter 如何连接android studio 模拟器 

在Visual Studio Code (VSCode) 中开发Flutter并连接到Android Studio模拟器需要一些配置。以下是一些建议的步骤：

1. **安装VSCode插件**: 在VSCode中，你需要安装Flutter和Dart插件。打开VSCode，点击左侧的扩展图标（Extensions），搜索并安装"Flutter"和"Dart"插件。
2. **安装Flutter SDK**: 确保你已经在你的计算机上安装了Flutter SDK。你可以从[Flutter官网](https://flutter.dev/docs/get-started/install)获取安装说明。
3. **设置Flutter路径**: 打开VSCode，按`Ctrl + ,`（Windows/Linux）或者`Cmd + ,`（Mac）打开设置。搜索"Flutter SDK Path"并设置为你Flutter SDK的路径。
4. **启动Android Studio模拟器**: 在Android Studio中启动模拟器。确保你的模拟器正在运行。
5. **连接VSCode到模拟器**: 打开VSCode，按`Ctrl + Shift + P`（Windows/Linux）或者`Cmd + Shift + P`（Mac）打开命令面板。输入"Flutter: Launch Emulator"，选择要连接的模拟器。
6. **运行Flutter应用**: 在VSCode中打开你的Flutter项目。按`F5`或者选择“Run”来运行你的应用。VSCode应该会连接到Android Studio模拟器，并在模拟器上启动你的Flutter应用。