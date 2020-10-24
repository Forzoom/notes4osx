### osx如何注册Service Provider

osx上的App并非一定要启动GUI界面才可以为用户提供服务，也可以使用`Service Provider`的方式，来提供自己的功能，这样做之后，可以做到在其他App中使用该App的功能。例如，在Finder中的文件选中后，右键点击可以看到“添加到xx笔记”这样的功能。

#### 提供功能代码

新建一个类，并且实现一个提供服务功能的函数。

```swift
class Service: NSObject {
    public func test(pboard: NSPasteboard) {
        // 示例
        print("this is test function")
    }
}
```

#### 在AppDelegate中注册Service Provider对象

```swift
@NSApplicationMain
class AppDelegate: NSObject, NSApplicationDelegate {

    func applicationDidFinishLaunching(_ aNotification: Notification) {
        NSApp.servicesProvider = Service();
    }
}
```

#### 在plist文件中向系统注册服务

在`info.plist`文件中，可以向系统注册自己的`Service Provider`，plist文件中所需要的property在文档中有比较详细的说明。但是根据现在xcode中的自动提示，当前的property的显示名字和过去有所不同（虽然本质上仍旧相同），并且过去的部分property好像被删除了。

Apple相关文档: https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/SysServices/introduction.html#//apple_ref/doc/uid/10000101-SW1

#### 将生成的app文件放入Application文件夹

只有将生成app文件或者service文件放入指定的文件夹才可以生效。
