# Electron是什么？

Electron是一个使用 JavaScript、HTML 和 CSS 构建桌面应用程序的框架。 嵌入 [Chromium](https://www.chromium.org/) 和 [Node.js](https://nodejs.org/) 到 二进制的 Electron 允许您保持一个 JavaScript 代码代码库并创建 在Windows上运行的跨平台应用 macOS和Linux——不需要本地开发 经验。



## 市面上常见的跨平台应用程序框架

Electron, Flutter 和 React Native 都是用于构建跨平台应用程序的流行框架，但它们在许多方面有所不同。



1. Electron: Electron 是一个使用 JavaScript, HTML 和 CSS 构建桌面应用程序的开源框架。它允许你使用 web 技术构建跨 Windows, MacOS 和 Linux 的应用程序。Electron 的一个主要优点是它的社区支持和丰富的生态系统（例：electron + vue、electron + react、webpack、vite......），但它的主要缺点是生成的应用程序通常比使用原生技术构建的应用程序大且消耗更多资源。
2. Flutter: Flutter 是 Google 开发的一个开源 UI 工具包，用于从单一代码库构建跨 Android, iOS, Web 和（实验性的）桌面应用程序。Flutter 使用 Dart 语言，它的主要优点是其高性能和美观的 UI，但是 Dart 不如 JavaScript 流行，因此可能需要额外的学习时间。
3. React Native: React Native 是 Facebook 开发的一个框架，用于使用 JavaScript 和 React 构建原生移动应用程序。它的优点是可以使用 web 技术（和可重用的 React 组件）来构建性能接近原生应用程序的应用程序，但它的主要缺点是其社区和生态系统不如 Electron 或 Flutter 成熟。



### 如何将一个react网页项目构建成应用程序

##### 安装 `electron`

`npm i electron --save`

##### 修改工程

修改package.json文件

```js
//	1、在package.json 中添加 homepage和main字段
  "main": "main.js",
  "homepage": ".",
   
//	2、添加script脚本启动命令
	"electron-dev": "electron . dev",
  "electron": "electron .",
/*
  这里一个是使用electron运行开发时候的脚本加了 dev的参数，后面会根据传入的这个参数，在主进程中做一层判断
  如果是开发环境的话，那么主进程loadURL就会加载 http://localhost:3000/地址
  如果是打包之后的环境就会加载打包文件夹的地址（这里打包是放在build文件夹下，所以会加载’./build/index.html’地址）
*/
```

##### 添加`main.js`文件

需要在工程目录下添加[main.js](https://github.com/electron/electron-quick-start)文件，即electron的主进程文件，新建一个窗体应用，在electron应用启动之后根据(开发/生产)环境去加载页面。

主要修改如下：

```js
// 引入node的 path 和url模块
const path = require('path')
const url = require('url');
// 获取在 package.json 中的命令脚本传入的参数，来判断是开发还是生产环境
const mode = process.argv[2];
```

在createWindow函数中，创建了主窗体之后根据参数去加载不同的url到electron中：

```js
//判断是否是开发模式 
  if(mode === 'dev') { 
    mainWindow.loadURL("http://localhost:3000/")
  } else { 
    mainWindow.loadURL(url.format({
      pathname:path.join(__dirname, './build/index.html'), 
      protocol:'file:', 
      slashes:true 
    }))
  }
```

##### 添加`preload.js`文件

```js
window.addEventListener('DOMContentLoaded', () => {
    const replaceText = (selector, text) => {
      const element = document.getElementById(selector)
      if (element) element.innerText = text
    }
  
    for (const type of ['chrome', 'node', 'electron']) {
      replaceText(`${type}-version`, process.versions[type])
    }
  })
```

##### 热模块更新

到这里之后基本就将electron 和 react 框架结合在一起了

1. 打开一个终端命令行输入 `npm run start` 启动react。(默认是在 http://localhost:3000/)
2. 再打开一个命令行输入 `npm run electron-dev` 启动electron应用，这时候electron应用就会加载 http://localhost:3000/ 并且在代码中修改也会热更新到electron应用中的主窗体
3. 如果react打包之后，可以运行 `npm run electron` 这个时候electron 加载的就是 `build/index.html` 文件



## 原生支持

例：当实现调用相机功能时

​	`electron:`

Electron 本身并没有直接提供访问设备相机和相册的API，但是，由于 Electron 允许你在应用中使用 Node.js，因此你可以使用各种` Node.js` 库来实现这些功能。例如，你可以使用 `node-webcam` 库来访问摄像头，使用 `fs`（文件系统）模块来访问和操作设备的文件系统，包括相册中的图片。需要注意的是，这种方法可能需要处理一些权限和兼容性问题。

```js
//	首先，需要安装 node-webcam，可以通过 npm 进行安装：
npm install node-webcam
//	然后，在 Electron 的主进程或渲染进程中，可以使用以下代码来捕获图像：
const webcam = require('node-webcam');

// 默认选项
let opts = {
    width: 1280,
    height: 720,
    quality: 100,
    delay: 0,
    saveShots: true,
    output: "jpeg",
    device: false,
    callbackReturn: "location",
    verbose: false
};

let Webcam = webcam.create( opts );

// 捕获图像并将其保存为 `photo.jpg`
Webcam.capture( "photo", function( err, data ) {
    console.log( data );
});
```



​		`flutter:`

Flutter本身访问设备的相机和相册，可以使用像`camera`和`image_picker`这样的插件来实现这些功能。`camera`插件可以在Flutter应用中直接访问设备的相机，而`image_picker`插件则可以从设备的相册中选择图片。

```js
//	首先，在 pubspec.yaml 文件中添加 camera 依赖：
dependencies:
  flutter:
    sdk: flutter
  camera: ^0.8.1
//	然后，运行 flutter pub get 来获取依赖。
//	在你的 Flutter 代码中，初始化相机：
import 'package:camera/camera.dart';

List<CameraDescription> cameras;

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  cameras = await availableCameras();
  runApp(MyApp());
}
//	最后，在 widget 中创建一个 CameraController 并显示相机预览：
class CameraExampleHome extends StatefulWidget {
  @override
  _CameraExampleHomeState createState() => _CameraExampleHomeState();
}

class _CameraExampleHomeState extends State<CameraExampleHome> {
  CameraController controller;

  @override
  void initState() {
    super.initState();
    controller = CameraController(cameras[0], ResolutionPreset.medium);
    controller.initialize().then((_) {
      if (!mounted) {
        return;
      }
      setState(() {});
    });
  }

  @override
  Widget build(BuildContext context) {
    if (!controller.value.isInitialized) {
      return Container();
    }
    return AspectRatio(
      aspectRatio: controller.value.aspectRatio,
      child: CameraPreview(controller),
    );
  }

  @override
  void dispose() {
    controller?.dispose();
    super.dispose();
  }
}
```



​		`React Native:`

React Native可以通过一些组件库来实现访问设备相机的功能。如`react-native-camera`和`react-native-image-picker`这样的库来实现这些功能。

```js
//	首先，需要安装 react-native-camera，可以通过 npm 进行安装：
npm install react-native-camera
//	然后，在需要使用的位置调用
import { RNCamera } from 'react-native-camera';

<RNCamera
  ref={ref => {
    this.camera = ref;
  }}
  style={{
    flex: 1,
    width: '100%',
  }}
/>
```







## 打包应用程序

### 使用 Electron Forge

Electron 的核心模块中没有捆绑任何用于打包或分发文件的工具。 如果您在开发模式下完成了一个 Electron 应用，需要使用额外的工具来打包应用程序 (也称为**可分发文件**) 并分发给用户 。 可分发文件可以是安装程序 (例如 Windows 上的 MSI) 或者绿色软件 (例如 macOS 上的 `.app` 文件)。

Electron Forge 是一个处理 Electron 应用程序打包与分发的一体化工具。 在工具底层，它将许多现有的 Electron 工具 (例如 [`electron-packager`](https://github.com/electron/electron-packager)、 [`@electron/osx-sign`](https://github.com/electron/osx-sign)、[`electron-winstaller`](https://github.com/electron/windows-installer) 等) 组合到一起，因此您不必费心处理不同系统的打包工作。



##### 导入你的项目到 Forge

将 Electron Forge 的 CLI 工具包安装到项目的 `devDependencies` 依赖中，然后使用现成的转化脚本将项目导入至 Electron Forge。

```bash
npm install --save-dev @electron-forge/cli
  npx electron-forge import
```

转换脚本完成后，Forge 会将一些脚本添加到您的 `package.json` 文件中。

```json
  //...
  "scripts": {
    "start": "electron-forge start",
    "package": "electron-forge package",
    "make": "electron-forge make"
  },
  //...
```



##### 创建一个可分发版本

要创建可分发文件，请使用项目中的 `make` 脚本，该脚本最终运行了 `electron-forge make` 命令。

```bash
npm run make
```

该 `make` 命令包含两步：

1. 它将首先运行 `electron-forge package` ，把您的应用程序 代码与 Electron 二进制包结合起来。 完成打包的代码将会被生成到一个特定的文件夹中。
2. 然后它将使用这个文件夹为每个 maker 配置生成一个可分发文件。

在脚本运行后，您应该看到一个 `out` 文件夹，其中包括可分发文件与一个包含其源码的文件夹。

```
out/
├── out/make/zip/darwin/x64/my-electron-app-darwin-x64-1.0.0.zip
├── ...
└── out/my-electron-app-darwin-x64/my-electron-app.app/Contents/MacOS/my-electron-app
```

