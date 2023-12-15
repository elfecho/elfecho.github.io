### 布局方式及差异

- 两种不同的编程方式
- 命令式UI:UI界面由一个个命令操作组合而成
- 声明式UI:将想要的UI描述出来即可

声明式UI优点：
- 更适合做`多设备适配`
- UI布局和控制逻辑通过reactive方式`实现数据绑定`
- 更好实现UI`局部刷新机制`，只刷新更新的部分

**Widget是不可变的**
如果需要改变属性需要使用可变的State

### 生命周期
1. createState: 创建State 当StatefulWidget被调用时立即执行
2. initState:State初始化方法，需调用super重写父类的方法
3. didChangeDependencies:该函数是在该组件依赖的State发生变化时调用，例如语言或者主题等
4. **Build** : 返回需要染的Widget( 重要)
5. Reassemble:开发阶段使用，在debug模式下，每次热重载都会调用该函数
6. didUpdateWidget:主要是在组件重新构建时调用，比如说热重载或父组件发生build
7. Deactivate: 在组件被移除节点后会被调用，如果该组件被移除节点，然后未被插入到其他节点时，则会继续调用dispose永久移除
8. Dispose: 永久移除组件，并释放组件资源。