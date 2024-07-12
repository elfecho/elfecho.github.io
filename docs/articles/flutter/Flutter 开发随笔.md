### Flutter 布局

Flex布局

## 查看 SpUtil 的存储文件

### 对于Android设备：

SharedPreferences的数据通常保存在以下路径下的一个.xml文件中：


`/data/data/<包名>/shared_prefs/`

要查看这个路径下的内容，需要root权限，或者使用Android Studio的Device File Explorer（设备文件浏览器）。但是，如果你在开发时使用的是模拟器，就可以直接通过Android Studio中的Device File Explorer查看这个路径下的文件内容了。SharedPreferences的文件通常以你的Preferences名称命名，或者是你的应用包名后跟`_preferences.xml`。

### 对于iOS设备：

在iOS上，SharedPreferences（NSUserDefaults）的数据被保存在应用沙盒内的`Library/Preferences`目录下。文件名通常是以应用的包名为前缀的`.plist`文件。

要查看这些文件，你可以使用Xcode的Devices and Simulators窗口。选择你的设备，找到你的应用，然后通过下载应用的沙盒内容来查看这些文件。同样的，如果你使用的是iOS模拟器，也可以通过相应的文件系统路径直接访问。

### 注意：

1. 直接访问这些文件主要用于调试目的。
2. 实际开发中，不推荐直接操作这些文件，而是应该通过SharedPreferences的API来访问这些数据。
3. 当需要在物理设备上调试时，如果没有root（对于Android）或没有特定的权限（对于iOS），查看这些文件可能会比较困难。

如果你需要检查`SpUtil`（或任何SharedPreferences的封装）存储的具体值，建议在应用内部提供一个调试页面，或通过日志输出等方式来查看和调试数据。


## 处理滑动不能实时触发事件

`TabController`的`addListener`方法监听的是索引的改变，而不是直接监听滑动手势。当你用手指滑动`TabBarView`时，`TabController`的索引只有在滑动结束且新页面完全到位时才会更新。这就是为什么你可能会觉得监听有“延迟性”的原因——实际上这不是延迟，而是等待滑动动作完成的结果。

如果你需要更即时的响应，想要在用户滑动时立刻得到反馈，那么你需要另外一种方式来监听滑动事件。这可以通过监听`TabBarView`的`PageView`滑动事件来实现，因为`TabBarView`内部实际上是使用`PageView`来实现滑动效果的。

你可以使用`PageController`来代替`TabController`来监听滑动，或者同时使用两者但通过`PageController`来获取滑动事件。这样做可以让你在滑动过程中就能获取到事件，而不是等到滑动完全结束。

下面是如何使用`PageController`来监听滑动事件的示例：
```dart
class _MyHomePageState extends State<MyHomePage> with SingleTickerProviderStateMixin {
  late PageController _pageController;
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 3, vsync: this);
    _pageController = PageController();

    _pageController.addListener(() {
      // 监听滑动事件
      print('Page is scrolling: ${_pageController.page}');

      // 调整TabController的索引以保持同步
      // round()是因为_pageController.page是一个double类型的值，可以四舍五入到最接近的整数
      _tabController.animateTo(_pageController.page!.round());
    });

    _tabController.addListener(() {
      // 当点击TabBar时，也要同步PageController的页面
      if (_tabController.indexIsChanging) {
        _pageController.animateToPage(_tabController.index,
            duration: Duration(milliseconds: 300), curve: Curves.ease);
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        bottom: TabBar(
          controller: _tabController,
          tabs: [
            Tab(text: 'Tab 1'),
            Tab(text: 'Tab 2'),
            Tab(text: 'Tab 3'),
          ],
        ),
      ),
      body: PageView(
        controller: _pageController,
        children: [
          Center(child: Text('Page 1')),
          Center(child: Text('Page 2')),
          Center(child: Text('Page 3')),
        ],
      ),
    );
  }

  @override
  void dispose() {
    _pageController.dispose();
    _tabController.dispose();
    super.dispose();
  }
}

```
在这个例子中，我们使用`PageController`来监听用户的滑动操作，并通过`addListener`方法实时获取滑动事件。同时，我们也需要在用户点击`TabBar`时更新`PageView`的页面，这通过监听`TabController`并调用`_pageController.animateToPage`方法来实现。

通过这种方式，你可以实时地获取到滑动事件，而不会有“延迟性”的感觉。这种方法结合了`TabController`和`PageController`的优势，既保持了`TabBar`和`TabBarView`的联动，也能够实时监听滑动事件。

## flutter 如何设置软件icon

在Flutter中设置应用图标是一个重要的步骤，因为它帮助用户在他们的设备上快速识别你的应用。以下是如何为Android和iOS平台设置软件图标的步骤：

### 1. 准备你的图标文件
首先，你需要为你的应用准备图标。通常，你需要多种尺寸的图标适应不同设备的屏幕。建议使用一个高分辨率的母图（如512x512像素）来生成各种尺寸的图标。

### 2. 使用 `flutter_launcher_icons` 包
`flutter_launcher_icons` 是一个在Flutter项目中自动化配置启动图标的流行方式。通过这个包，你可以简单地设定一张图片，这个工具将会帮你生成所有必需的图标尺寸，并自动配置它们。

首先，将 `flutter_launcher_icons` 添加到你的 `pubspec.yaml` 文件中的 `dev_dependencies` 部分，并在 `flutter` 部分配置图标文件：

```yaml
dev_dependencies:
  flutter_launcher_icons: ^0.9.2

flutter_icons:
  android: true
  ios: true
  image_path: "assets/icon/app_icon.png"
```

这里 `"assets/icon/app_icon.png"` 是你的图标文件相对于项目根目录的路径。确保你已经将图标文件放到正确的位置。

### 3. 运行包
完成 `pubspec.yaml` 配置后，在终端运行以下命令来生成图标并应用到你的Flutter项目中：

```bash
flutter pub get
flutter pub run flutter_launcher_icons:main
```

这些命令会根据你的配置生成所需的图标并替换掉iOS和Android的默认图标。

### 4. 确认图标已经设置
为了确认图标已经被正确设置，你可以运行你的应用到模拟器或真实设备上并检查图标。

### 5. 手动设置（可选）
如果你不想使用 `flutter_launcher_icons` 包，你也可以手动设置图标：

- **对于Android：**
  - 将不同尺寸的图标放入 `android/app/src/main/res` 目录下的相应 `mipmap-` 文件夹中。
  
- **对于iOS：**
  - 打开你的iOS项目（使用Xcode），然后在Xcode中的`Assets.xcassets`文件夹中找到`AppIcon`。这里你可以拖放你的图标到对应的槽中。

### 结论
使用 `flutter_launcher_icons` 包是设置Flutter应用图标的最简单方法，它自动处理大多数的细节和配置。然而，如果需要更详细的控制，你也可以选择手动方式进行配置。不管哪种方法，确保图标质量符合设计标准，并且在所有设备上看起来都很完美。


## 要使用 Flutter 对接 `android.car.jar` 并实现驾驶模式，你需要按照以下步骤进行操作：

### 步骤 1: 获取 `android.car.jar`

首先，你需要从你的 Android SDK 中获取 `android.car.jar` 文件。根据你提供的文档，这个文件通常位于 Android SDK 的特定目录下。例如：

```groovy
def sdkDir = project.android.sdkDirectory.canonicalPath
def androidCarJar = "$sdkDir/platforms/android-31/optional/android.car.jar"
```

### 步骤 2: 在 `build.gradle` 中配置依赖

在你的 Flutter 插件项目的 `android/build.gradle` 文件中添加对 `android.car.jar` 的依赖：

```groovy
dependencies {
    compileOnly files(androidCarJar)
}
```

### 步骤 3: 在 `AndroidManifest.xml` 中声明驾驶模式

在你的 Flutter 插件项目的 `android/src/main/AndroidManifest.xml` 文件中，为每个需要支持驾驶模式的 Activity 添加 `meta-data` 标签：

```xml
<application>
    <activity android:name=".YourActivityA">
        <meta-data android:name="distractionOptimized" android:value="true" />
    </activity>
    <!-- 其他 Activity 同样添加 meta-data 标签 -->
</application>
```

### 步骤 4: 在原生代码中实现驾驶模式逻辑

在你的 Flutter 插件项目的原生 Android 代码中，实现驾驶模式的逻辑。这通常涉及到获取 `CarUxRestrictionsManager` 实例并监听驾驶模式的变化：

```kotlin
private lateinit var mCarUxRestrictionsManager: CarUxRestrictionsManager?

private fun carUxRestrictions(connectedCar: Car): Int {
    try {
        mCarUxRestrictionsManager = connectedCar.getCarManager(Car.CAR_UX_RESTRICTION_SERVICE) as CarUxRestrictionsManager?
        mCarUxRestrictionsManager?.registerListener { carUxRestrictions ->
            handleUxRestrictionsChanged(carUxRestrictions)
        }
    } catch (e: Exception) {
        Logger.warning("carUxRestrictions fail ${e.message}")
    }
    return mCarUxRestrictionsManager?.currentCarUxRestrictions?.activeRestrictions ?: 0
}

private fun handleUxRestrictionsChanged(carUxRestrictions: CarUxRestrictions) {
    Logger.info("handleUxRestrictionsChanged value ${carUxRestrictions.activeRestrictions}")
    // TODO: 根据驾驶模式调整应用行为
}
```

### 步骤 5: 在 Flutter 代码中调用原生方法

在你的 Flutter 代码中，你需要调用原生代码来获取驾驶模式的状态，并根据这些状态调整你的应用行为。你可以使用 `MethodChannel` 来实现这一点：

```dart
import 'package:flutter/services.dart';

class CarModeService {
  static const MethodChannel _channel =
      MethodChannel('car_mode_channel');

  Future<int> getCarMode() async {
    final int mode = await _channel.invokeMethod('getCarMode');
    return mode;
  }
}
```

在原生代码中，你需要实现 `MethodChannel` 的对应方法：

```kotlin
class CarModeMethodCallHandler(private val activity: Activity) : MethodChannel.MethodCallHandler {
    override fun onMethodCall(call: MethodCall, result: Result) {
        when (call.method) {
            "getCarMode" -> {
                val mode = carUxRestrictions(activity)
                result.success(mode)
            }
            else -> result.notImplemented()
        }
    }
}
```

### 步骤 6: 测试和验证

确保在实际的车辆环境中测试你的应用，以验证驾驶模式是否正确工作，并且应用在驾驶模式下的行为符合预期。

### 注意事项

- 确保你有权访问 `android.car.jar` 并了解如何在你的应用程序中正确地集成和使用它。
- 在将 `android.car.jar` 集成到你的项目中后，进行彻底的测试，以确保你的应用在所有目标设备上都能正常工作。
- 遵循 Android Automotive OS 的 API 使用规则和权限要求。

请记住，`android.car.jar` 是 Android Automotive OS 的一部分，它可能不是公开可用的，或者可能需要特定的权限和配置才能使用。在尝试使用此类库之前，请确保你有权访问它，并且了解如何在你的应用程序中正确地集成和使用它。


## 接入media

```kotlin
package com.example.vehicle

import android.car.Car
import android.car.drivingstate.CarUxRestrictionsManager
import android.content.ComponentName
import android.content.IntentFilter
import android.os.Build
import android.os.Bundle
import android.os.IBinder
import android.util.Log
import androidx.media.session.MediaSessionCompat
import io.flutter.embedding.android.FlutterActivity
import io.flutter.plugin.common.EventChannel
import io.flutter.plugin.common.MethodChannel

class MainActivity : FlutterActivity() {
    private val CHANNEL = "media_control_channel"
    private val EVENT_CHANNEL = "car_ux_restrictions_event"
    private var mCar: Car? = null
    private var carUxRestrictionsManager: CarUxRestrictionsManager? = null
    private lateinit var mediaSession: MediaSessionCompat

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        initialCar()
        initSession()
        setupMediaControlChannel()
        registerMediaButtonReceiver()
    }

    private fun setupMediaControlChannel() {
        MethodChannel(flutterEngine!!.dartExecutor.binaryMessenger, CHANNEL).setMethodCallHandler { call, result ->
            when (call.method) {
                "play" -> {
                    mediaSession.controller.transportControls.play()
                    result.success(null)
                }
                "pause" -> {
                    mediaSession.controller.transportControls.pause()
                    result.success(null)
                }
                "next" -> {
                    mediaSession.controller.transportControls.skipToNext()
                    result.success(null)
                }
                "previous" -> {
                    mediaSession.controller.transportControls.skipToPrevious()
                    result.success(null)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        EventChannel(flutterEngine!!.dartExecutor.binaryMessenger, EVENT_CHANNEL).setStreamHandler(
            object : EventChannel.StreamHandler {
                override fun onListen(arguments: Any?, events: EventChannel.EventSink?) {
                    carUxRestrictionsManager?.registerListener {
                        Log.d("MainActivity", "restriction changed ${carUxRestrictionsManager?.currentCarUxRestrictions}")
                        events?.success(carUxRestrictionsManager?.currentCarUxRestrictions?.isRequiresDistractionOptimization ?: false)
                    }
                }

                override fun onCancel(arguments: Any?) {
                    carUxRestrictionsManager?.unregisterListener()
                }
            }
        )
    }

    private fun initialCar() {
        if (Build.VERSION.SDK_INT < 30) {
            Log.d("MainActivity", "Android SDK < 30")
            mCar = Car.createCar(applicationContext, object : ServiceConnection {
                override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
                    service?.let {
                        Log.d("MainActivity", "obtain car success $service")
                        registerRestrictionListener(mCar!!)
                    }
                }
                override fun onServiceDisconnected(name: ComponentName?) {
                    mCar = null
                }
            })
            try {
                mCar?.connect()
            } catch (e: Exception) {
                e.printStackTrace()
            }
        } else {
            mCar = Car.createCar(
                applicationContext, null, 0
            ) { car, ready ->
                mCar = if (ready) {
                    registerRestrictionListener(car)
                    car
                } else {
                    null
                }
            }
        }
    }

    private fun initSession() {
        mediaSession = MediaSessionCompat(this, "session tag").apply {
            setCallback(MyMediaSessionCallback())
            isActive = true
        }
        Log.d("MainActivity", "sessionToken: ${mediaSession.sessionToken}")
    }

    private fun registerRestrictionListener(car: Car) {
        Log.d("MainActivity", "registerRestrictionListener")
        val uxRestrictionsManager =
            car.getCarManager(Car.CAR_UX_RESTRICTION_SERVICE) as CarUxRestrictionsManager
        Log.d("MainActivity", "current restriction ${uxRestrictionsManager.currentCarUxRestrictions}")
        carUxRestrictionsManager = uxRestrictionsManager
    }

    private fun getCarUxRestrictions(): Boolean {
        return carUxRestrictionsManager?.currentCarUxRestrictions?.isRequiresDistractionOptimization ?: false
    }

    private fun registerMediaButtonReceiver() {
        val filter = IntentFilter(Intent.ACTION_MEDIA_BUTTON)
        val buttonReceiver = MediaButtonReceiver()
        registerReceiver(buttonReceiver, filter)
    }

    override fun onDestroy() {
        mCar?.disconnect()
        mediaSession.release()
        super.onDestroy()
    }

    private inner class MyMediaSessionCallback : MediaSessionCompat.Callback() {
        override fun onPlay() {
            // Handle play
        }

        override fun onPause() {
            // Handle pause
        }

        override fun onSkipToNext() {
            // Handle next track
        }

        override fun onSkipToPrevious() {
            // Handle previous track
        }
    }
}

```