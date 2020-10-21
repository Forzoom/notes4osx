### 检测拖拽的文件信息

拖拽是常见的交互形式，在图形化界面中拖拽操作更加直观。

#### 实现NSDraggingDestination

1. 调用`registerForDraggedTypes`注册需要监视的类型，如果是拖拽的文件的话，使用`.fileURL`。
2. 实现`draggingEntered`函数，因为第三步中的函数依赖于该函数返回一个可使用的值。
3. 拖拽后的检测主要由`NSDraggingDestination`实现，NSView已经实现，使用时只需要重新定义`prepareForDragOperation`即可，在该函数中，可以获取到文件信息。

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
            print("url:", url)
        }

        return true
    }
}
```
