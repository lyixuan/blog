react native

1、按照react native 搭建开发环境步骤搭建

安装依赖：Node、Watchman 和 React Native 命令行工具以及 Xcode。
brew install node
brew install watchman
npm install -g react-native-cli
安装 Xcode，并在Xcode | Preferences | Locations菜单中检查一下是否装有某个版本的Command Line Tools。Xcode 的命令行工具中包含一些必须的工具，比如git等。

至此，开发环境已经准备完成。
接下来创建一个demo项目并启动起来。

2、创建一个名为"AwesomeProject"的新项目
react-native init AwesomeProject
cd AwesomeProject
react-native run-ios
提示：如果 run-ios 无法正常运行，请使用 Xcode 运行来查看具体错误（run-ios 的报错没有任何具体信息）。

现在你已经成功运行了项目

3、修改项目
* 使用你喜欢的编辑器打开App.js并随便改上几行。
* 在 iOS 模拟器中按下⌘-R就可以刷新 APP 并看到你的最新修改！（如果没有反应，请检查模拟器的 Hardware 菜单中，connect hardware keyboard 选项是否选中开启）
完成了！
恭喜！你已经成功运行并修改了你的第一个 React Native 应用。

接下来？
试着在开发者菜单中打开Live Reload，现在你只要一保存代码应用就会自动完整刷新。  
==============调试===========
1、开启调试的快捷键
务必确保simulator模拟器的 Hardware 菜单中，Keyboard 选项下的"Connect Hardware Keyboard"处于开启状态，

2、访问 App 内的开发菜单
你可以通过摇晃设备或是选择 iOS 模拟器的"Hardware"菜单中的"Shake Gesture"选项来打开开发菜单。还可以按下Command⌘ + D 快捷键
提示：在发布（production）版本中开发者菜单将无法使用。

刷新 JavaScript
* iOS 模拟器中按下Command⌘ + R
* 开发菜单中的"Enable Live Reload"可以开启自动刷新
需开启开发菜单中的Hot Reloading选项。
某些情况下 hot reload 并不能顺利实施。如果碰到任何界面刷新上的问题，请尝试手动完全刷新。
但有些时候你必须要重新编译应用才能使修改生效：
* 增加了新的资源(比如给 iOS 的Images.xcassets或是 Andorid 的res/drawable文件夹添加了图片)
* 更改了任何的原生代码（objective-c/swift/java

3、Chrome 开发者工具
菜单中选择"Debug JS Remotely"选项，即可以开始在 Chrome 中调试

注意：Chrome 中并不能直接看到 App 的用户界面，而只能提供 console 的输出，以及在 sources 项中断点调试 js 脚本。
你可以安装独立（非插件）版本的 React Developer Tools 来辅助查看界面布局，下文会讲述具体安装方法。

注意：使用 Chrome 调试目前无法观测到 React Native 中的网络请求，你可以使用功能更强大的第三方的react-native-debugger来进行观测。



