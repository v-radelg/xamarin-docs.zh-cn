---
title: 使用 tvOS 在 Xamarin 中的分段控件
description: 本文档介绍如何使用 tvOS 分段的控件中使用 Xamarin 生成的应用。 它讨论了段图标和文本、 事件，修改控件的外观和的详细信息。
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 98a770d05014e0498b805ed9ffa0c84314efc765
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374953"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>使用 tvOS 在 Xamarin 中的分段控件

分段控件提供了一组线性元素，其中每个图标或文本，可以包含，用于向用户提供的一组相关的选项。

[![](segmented-controls-images/segment01.png "示例段控件")](segmented-controls-images/segment01.png#lightbox)

Apple 具有以下建议以获得使用分段控件：

- **提供足够的空间**-注意应为要提供足够的空间，其他之间花费的时间[可获得焦点项](~/ios/tvos/app-fundamentals/navigation-focus.md)的分段控件。 （不是在单击时） 已处于焦点并且用户可以在实际想要选择当前段上的另一个可获得焦点项时意外更改段时，将被选中的各个段。
- **用于拆分视图内容筛选**的分段控件不可以合理地选择内容的内容和筛选器之间轻松导航设计拆分视图进行筛选。
- **七个段最大限制**-应尝试保留以下八个段的最大数目 (8) 作为这是更轻松地在同一房间的沙发上更轻松地导航从分析。
- **使用段内容大小保持一致**-所有段都有相同的宽度和，如果可能，应尝试保留内容，每个段中相同的大小。 这不但使段控件更视觉效果很好，而且它可以更轻松地快速读取。
- **避免混合使用图标和文本**-一个图标或文本，但不是能同时包含每个单独的段。 可以混合使用图标和相同的分段控件中的文本时，应避免此操作。

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>有关段图标

Apple 建议段图标，如搜索一个放大镜中使用简单、 可识别的映像。 过于复杂图标很难在室内识别电视屏幕上，因此最好限制您的图标为简单的表示形式。

不能混合文本和给定段上的图标和应避免同时包含图标和单个分段控件中的文本。 它应为所有图标或所有文本。

<a name="Summary" />

## <a name="segment-text"></a>段的文本

Apple 可以处理段文本的以下建议：

- **使用短，有意义的名词**-段标题应明确声明的用户在选择给定的段时应期待的内容类型。 例如：音乐或视频。
- **使用词首字母大写的大小写作**-段标题的每个词应采用大写形式除文章、 连词和介词少于四 （4） 个字符。
- **使用短，已设定焦点的标题**-保留标题，简短并专注于的内容时选择段的预期行为的类型。

同样，不能混合文本和给定段上的图标，并且应避免同时包含图标和单个分段控件中的文本。

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>段控件和情节提要

使用段控件 Xamarin.tvOS 应用中的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**段控件**从**工具箱**并将其放在视图上： 

    [![](segmented-controls-images/segment02.png "分段控件")](segmented-controls-images/segment02.png#lightbox)
1. 在**小组件选项卡**的**属性填充**，您可以调整的段控制多个属性，如其**样式**并**状态**: 

    [![](segmented-controls-images/segment03.png "小组件选项卡")](segmented-controls-images/segment03.png#lightbox)
1. 使用**段**字段来控制在控制器的段数。
1. 选择从给定的段**段下拉列表中**来调整其各个属性如**标题**或**图像**并控制如果给定的段**已启用**或**选定**时显示该控件。
1. 最后，将分配**名称**的控件，以便可以响应在C#代码。 例如： 

    [![](segmented-controls-images/segment04.png "指定一个名称")](segmented-controls-images/segment04.png#lightbox)
1. 保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**段控件**从**工具箱**并将其放在视图上： 

    [![](segmented-controls-images/segment02-vs.png "分段控件")](segmented-controls-images/segment02-vs.png#lightbox)
1. 在中**小组件选项卡**的**属性资源管理器**，您可以调整的段控制多个属性，如其**样式**和**状态**: 

    [![](segmented-controls-images/segment03-vs.png "小组件选项卡")](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用**段**字段来控制在控制器的段数。
1. 选择从给定的段**段下拉列表中**来调整其各个属性如**标题**或**图像**并控制如果给定的段**已启用**或**选定**时显示该控件。
1. 最后，将分配**名称**的控件，以便可以响应在C#代码。 例如： 

    [![](segmented-controls-images/segment04-vs.png "指定一个名称")](segmented-controls-images/segment04-vs.png#lightbox)
1. 保存更改。
    
-----

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>中的分段控件

如上所述，s 分段控件提供了一套线性元素，其中每个可以包含图标或文本，并用于向用户提供的一组相关的选项。

有多种不同的方式，您可以使用分段控件 Xamarin.tvOS 应用程序中。

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>公开为的名称和事件

如果在接口设计器中创建段控件和公开为一个名为控件和事件可以使用以下代码以响应段更改：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

在上面的示例中，段控件已作为公开`PlayerCount`名称和一个`PlayerCountChanged`事件的操作。 有关如何使用操作和输出口的详细信息，请参阅[编写的代码具有输出口和操作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)一部分我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

`SelectedSegment`属性获取或设置基于为零 (0) 的当前所选的段索引。 因此，如果五 （5） 段，第一个段必须零 (0) 和最后一个的索引的索引四 （4)。

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>修改段

在任何时候您可以修改数和分段控件的内容。 使用以下代码插入一个新图标段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二个参数定义段将在其中插入使用 (零 0) 开始的索引。 如果最后一个参数是`true`插入进行动画处理。

若要删除给定的段，使用以下命令：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

或以下内容，以删除所有段：

```csharp
SegmentedControl.RemoveAllSegments();
```

同样，如果最后一个参数是`true`，删除将进行动画处理。 使用`NumberOfSegments`属性以返回当前段的数。

若要获取**标题**或**图标**对于给定的段，请使用以下：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

并更改**标题**或**图标**，使用以下内容：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看给定的段是否**已启用**，使用以下内容：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

进出**启用/禁用**给定段，使用以下：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>修改分段的控件的外观

下面的代码可用于将给定的段的背景更改为图像：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中`UIControlState`指定要设置此图像可查看为该控件的状态：

- 普通
- 突出显示
- 已禁用
- 已选定
- 已设定焦点

和`UIBarMetrics`指定要用作的度量值：

- 默认
- 压缩
- DefaultPrompt
- CompactPrompt

此外，可以设置使用的段之间的分隔线：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中第一个`UIControlState`指定左侧的分隔线和第二个段的状态`UIControlState`指定状态的向右段。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和使用在 Xamarin.tvOS 应用内的分段控件。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
