### Flutter 布局

Flex布局

## 查看 SpUtil 的存储文件

### 对于Android设备：

SharedPreferences的数据通常保存在以下路径下的一个.xml文件中：

复制

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