### 检测拖拽的文件路径

拖拽是常见的交互形式，在图形化界面中拖拽操作更加直观。

#### 实现NSDraggingDestination

拖拽后的检测主要由NSDraggingDestination实现，NSView已经实现protocol。

1. 调用`registerForDraggedTypes`注册需要监视的类型，如果是拖拽的文件的话，使用`.fileURL`。
2. 实现`draggingEntered`函数，因为第三步中的函数依赖于该函数返回一个可使用的值。
3. 重新定义`prepareForDragOperation`，在该函数中，可以获取到文件信息。

#### 示例代码

```swift
class DraggableView: NSView {
    override init(frame frameRect: NSRect) {
        super.init(frame: frameRect)
        
        self.registerForDraggedTypes([.fileURL])
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        
        self.registerForDraggedTypes([.fileURL])
    }

    override func draggingEntered(_ sender: NSDraggingInfo) -> NSDragOperation {
        print("dragging enter")
        return .generic
    }

    // 确认是否接受drag
    override func prepareForDragOperation(_ sender: NSDraggingInfo) -> Bool {
        print("prepareForDragOperation")
        let pboard = sender.draggingPasteboard

        if pboard.pasteboardItems != nil && pboard.pasteboardItems!.count <= 1 {
            // 获得的fileURL大致为 file:///.file/id=123.456
            guard let fileURL = pboard.pasteboardItems?[0].string(forType: .fileURL) else {
                return
            }
            print("fileURL:", fileURL)
            let url = URL.init(string: fileURL)
            // 获得的url大致为 file:///Users/xxx/Downloads/test.txt
            // 如果是是文件夹，大致为 file:///Users/xxx/Downloads/test/
            print("url:", url)
        }

        return true
    }
}
```

#### 在SwiftUI中实现类似效果

SwiftUI中的实现和上面基本相似，只是利用的是`NSItemProvider`来传递数据，承载回调函数内容的是`DropDelegate`protocol。

```swift
struct MyExampleView: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 0) {
            Button("测试") {
                print("on click test button")
            }
        }.padding(EdgeInsets(top: 40, leading: 0, bottom: 40, trailing: 0))
        .onDrop(of: [NSPasteboard.PasteboardType.fileURL.rawValue], delegate: MyDropDelegate())
    }
}
```

```swift
struct MyDropDelegate: DropDelegate {
    func dropEntered(info: DropInfo) {
        print("drop entered")
    }
    
    func dropUpdated(info: DropInfo) -> DropProposal? {
        print("drop updated")
        return DropProposal(operation: .move)
    }
    
    func performDrop(info: DropInfo) -> Bool {
        print("perform drop")
        
        let items = info.itemProviders(for: [NSPasteboard.PasteboardType.fileURL.rawValue])
        
        if items.count > 0 {
            print(items[0].loadItem(forTypeIdentifier: NSPasteboard.PasteboardType.fileURL.rawValue, options: nil, completionHandler: { (item, error) in
                print("result is", NSString(data: item as! Data, encoding: String.Encoding.utf8.rawValue))
            }))
        }
        return true
    }
}
```
