<p align="center">
<img src="https://raw.githubusercontent.com/rakuyoMo/JSONPreview/master/Images/logo.png" alt="JSONPreview" title="JSONPreview" width="1000"/>
</p>

<p align="center">
<a><img src="https://img.shields.io/badge/language-swift-ffac45.svg"></a>
<a href="https://github.com/rakuyoMo/JSONPreview/releases"><img src="https://img.shields.io/cocoapods/v/JSONPreview.svg"></a>
<a href="https://github.com/rakuyoMo/JSONPreview/blob/master/LICENSE"><img src="https://img.shields.io/cocoapods/l/JSONPreview.svg?style=flat"></a>
</p>

`JSONPreview` 继承自 `UIView`，并基于 `UITextView` 实现功能。您可以通过它来**格式化**您的 JSON 数据，并**高亮**展示。

同时 `JSONPreview` 还提供**折叠与展开**功能，您可以折叠那些您暂时不关注的节点，并在任意时刻重新展示它。

`JSONPreview` 的全部功能都使用**原生方法**编写，这意味着您可以在 Apple 平台获得较好的使用体验。

## 预览

下面是一个大约25秒的gif（**大约2.5M**），它展示了使用本库预览 JSON 时的效果。

![image](https://github.com/rakuyoMo/JSONPreview/blob/master/Images/screenshot.gif)

## 基本要求

- 运行 **iOS 10** 及以上版本的设备。
- 使用 **Xcode 10** 及以上版本编译。
- **Swift 5.0** 以及以上版本。

## 安装

### CocoaPods

```ruby
pod 'JSONPreview'
```

### Swift Package Manager

- 依次选择 File > Swift Packages > Add Package Dependency
- 输入 https://github.com/rakuyoMo/JSONPreview.git
- 选择 "Up to Next Major" 并填入 "1.3.2"

或者将下面的内容添加到 `Package.swift` 文件中：

```swift
dependencies: [
  .package(url: "https://github.com/rakuyoMo/JSONPreview.git", from: "1.3.2")
]
```

## 功能

> 在 `1.3.0` 版本中，我们删除了斜向滑动的功能。
> 现在如果JSON一行展示不开，那么它将折行展示，而不是超出屏幕。如果您希望使用该功能，请使用 [1.2.3](https://github.com/rakuyoMo/JSONPreview/releases/tag/1.2.3) 版本

- [x] 支持**格式化**显示 JSON 数据。
- [x] 支持**高亮** JSON 数据，提供多种颜色与字体配置选项。
- [x] 针对 `Array` 与 `Object` 提供**折叠**与**展开**功能。
- [x] 基于 `UITextView` 实现功能。意味着您可以复制 `JSONPreview` 中的任意内容。

- `JSONPreview` 提供有限的，不完整的格式检查功能，故该功能不作为主要功能提供。详情可以参考：[格式检查](#格式检查)

## 使用

> 下载项目后，[`ViewController.swift`](https://github.com/rakuyoMo/JSONPreview/blob/master/JSONPreview/JSONPreview/Other/ViewController.swift) 文件中包含部分测试代码，运行项目即可查看对应的效果。

1. 首先创建 `JSONPreview` 对象，并添加到界面上：

```swift
let previewView = JSONPreview()

view.addSubview(previewView)
```

2. 调用 `preview(_:style:)` 方法，以默认样式预览数据：

```swift
let json = "{\"key\":\"value\"}"

previewView.preview(json)
```

3. 如果您想要自定义高亮样式，可通过 `HighlightStyle` 与 `HighlightColor` 类型进行设置：

> 其中，[`ConvertibleToColor`](https://github.com/rakuyoMo/JSONPreview/blob/master/JSONPreview/JSONPreview/Core/HighlightColor.swift#L119) 是一个用于提供颜色的协议。通过该协议，您可以直接使用 `UIColor` 对象，或轻松的将诸如 `0xffffff`、`#FF7F20` 以及  `[0.72, 0.18, 0.13]` 转换为 `UIColor` 对象。

```swift
let highlightColor = HighlightColor(
    keyWord: ConvertibleToColor,
    key: ConvertibleToColor,
    link: ConvertibleToColor,
    string: ConvertibleToColor,
    number: ConvertibleToColor,
    boolean: ConvertibleToColor,
    null: ConvertibleToColor,
    unknownText: ConvertibleToColor,
    unknownBackground: ConvertibleToColor,
    jsonBackground: ConvertibleToColor,
    lineBackground: ConvertibleToColor,
    lineText: ConvertibleToColor
)

let style = HighlightStyle(
    expandIcon: UIImage?,
    foldIcon: UIImage?,
    color: highlightColor,
    lineFont: UIFont?,
    jsonFont: UIFont?,
    lineHeight: CGFloat
)

previewView.preview(json, style: style)
```

## 格式检查

### 渲染

对于渲染，`JSONPreview` 只进行**有限**的格式检查，包括：

> 以下所提到的 “上一个节点” 均不包括 `空格`、`\t` 以及 `\n`。

- 所要预览的JSON必须以 `{` 或 `[` 开头。
- `:` 的上一个节点必须是 `.string`。
- `,` 的上一个节点只能是 `.null`、`.link`、`.string`、`.number`、`.boolean`、`}` 以及 `]` 中的一个。
- `{` 必须存在上一个节点，同时上一个节点不能为 `{`。
- `}` 必须与 `{` 成对出现。
- `[` 必须存在上一个节点，同时上一个节点不能为 `]`。
- `]` 必须与 `[` 成对出现。
- `"` 必须成对出现。
- `"` 的上一个节点只能是 `{`、`[`、`,` 以及 `:`  中的一个。
- 针对 `null`、`true` 以及 `false` 的拼写检查。

除此之外的语法错误均不会触发渲染错误。

### 链接

*1.2.0* 版本增加了对链接（`.link`）的渲染功能。在渲染的同时，`JSONPreview` 会进行有限的**去转义**操作。

不同版本支持的去转义操作如下：

> 如无特殊说明，以下功能均为累加。

- 1.2.0：支持将 `"\\/"` 替换为 `"/"`。

## DFD

![image](https://github.com/rakuyoMo/JSONPreview/blob/master/Images/DFD.png)

## TODO

- [ ] 支持 `Carthage`。
- [ ] 支持 intel 版本的 macOS。

## 致谢

感谢 [Awhisper](https://github.com/Awhisper) 在 `JSONPreview` 的开发过程中提出的宝贵意见。

## License

`License` 在 **MIT** 许可下可用。 有关更多信息，请参见 [LICENSE](https://github.com/rakuyoMo/License/blob/master/LICENSE) 文件。
