---
title: Xamarin 中的 watchOS 复杂性
description: 本文档介绍如何在 Xamarin 中使用 watchOS 复杂性。 本文介绍如何添加复杂的、编写复杂的模板，并提供示例代码。
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/03/2017
ms.openlocfilehash: 5aea7ae094e0b79831a5fb84397108ca09e18360
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028307"
---
# <a name="watchos-complications-in-xamarin"></a>Xamarin 中的 watchOS 复杂性

_watchOS 允许开发人员为观看面部编写自定义的复杂问题_

此页说明了不同类型的可用的复杂程度，以及如何将问题添加到 watchOS 3 应用程序中。

请注意，每个 watchOS 应用程序只能有一个复杂的。

首先阅读[Apple 的文档](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)，以确定你的应用是否适合于复杂性。 有5个 `CLKComplicationFamily` 类型的显示可供选择：

[![](complications-images/all-complications-sml.png "The 5 CLKComplicationFamily types available: Circular Small, Modular Small, Modular Large, Utilitarian Small, Utilitarian Large")](complications-images/all-complications.png#lightbox)

应用只能实现一种样式，也可以仅实现五种类型，具体取决于所显示的数据。
你还可以支持时间段，并在用户打开 Digital Crown 的情况下为过去和/或未来的时间提供值。

<a name="adding" />

## <a name="adding-a-complication"></a>添加复杂

### <a name="configuration"></a>配置

可以在创建期间将复杂性添加到 watch 应用，也可以手动添加到现有解决方案。

### <a name="add-new-project"></a>添加新项目 。

"**添加新项目 ...** " 向导包含一个复选框，该复选框将自动创建一个复杂的控制器类并配置**info.plist**文件：

![](complications-images/file-new-project-sml.png "The Include Complication checkbox")

### <a name="existing-projects"></a>现有项目

若要向现有项目添加复杂化：

1. 创建新的**ComplicationController.cs**类文件并实现 `CLKComplicationDataSource`。
2. 配置应用的**info.plist**以揭示复杂的，并识别支持哪些复杂的系列。

下面更详细地介绍了这些步骤。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 类

以下C#模板包含实现`CLKComplicationDataSource`所需的最低方法。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

请按照[编写复杂](#writing)说明向此类中添加代码。

### <a name="infoplist"></a>Info.plist

监视扩展的**info.plist**文件应指定 `CLKComplicationDataSource` 的名称，以及要支持的复杂系列：

[![](complications-images/complications-config-sml.png "The complication family types")](complications-images/complications-config.png#lightbox)

**数据源类**条目列表将显示子类 `CLKComplicationDataSource` 包含您的复杂逻辑的子类的类名称。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

所有复杂的功能都在一个类中实现，该方法从 `CLKComplicationDataSource` 抽象类（实现 `ICLKComplicationDataSource` 接口）中重写方法。

### <a name="required-methods"></a>必需的方法

您必须实现以下方法，以使运行复杂化：

- `GetPlaceholderTemplate`-返回在配置期间或应用无法提供值时使用的静态显示。
- `GetCurrentTimelineEntry`-在运行复杂化时计算正确的显示。
- `GetSupportedTimeTravelDirections`-从 `CLKComplicationTimeTravelDirections` （如 `None`、`Forward`、`Backward`或 `Forward | Backward`）返回选项。

### <a name="privacy"></a>隐私

显示个人数据的复杂情况

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果此方法返回 `HideOnLockScreen` 则当监视锁定时，将显示一个图标或应用程序名称（而不是任何数据）。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate`-返回操作系统下次应在应用中查询更新的复杂显示数据的时间。

你还可以强制执行 iOS 应用的更新。

### <a name="supporting-time-travel"></a>支持旅行

行程支持是可选的，由 `GetSupportedTimeTravelDirections` 方法控制。 如果它返回 `Forward`、`Backward`或 `Forward | Backward`，则必须实现以下方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>编写复杂

复杂范围从简单的数据显示到复杂的图像，以及带有时间行程支持的数据呈现。 下面的代码演示如何构建简单的单模板。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>代码示例

此示例仅支持 `UtilitarianLarge` 模板，因此只能在支持该类型的复杂的特定监视面上选择。 在监视上*选择*"复杂" 时，它会显示 "**我**的问题"，并在*运行*时显示文本 "**分钟 _" （_** 包含部分时间）。

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```

<a name="templates" />

## <a name="complication-templates"></a>复杂模板

有多种不同的模板可用于每种复杂样式。
**环形**模板使你可以在复杂的周围显示进度样式环形，这种方式可用于以图形方式显示进度或其他某个值。

[Apple 的 CLKComplicationTemplate 文档](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>圆形小

这些模板类名称全部都带有 `CLKComplicationTemplateCircularSmall`前缀：

- **RingImage** -显示单个图像，周围有一个进度环。
- **RingText** -显示单行文本，其中包含进度圆圈。
- **SimpleImage** -只显示小型单一图像。
- **SimpleText** -只显示小文本片段。
- **StackImage** -显示一个图像和一个文本行，其中一个文本在另一个上方
- **StackText** -显示两行文本。

### <a name="modular-small"></a>小型模块

这些模板类名称全部都带有 `CLKComplicationTemplateModularSmall`前缀：

- **ColumnsText** -显示文本值的小网格（2行和2列）。
- **RingImage** -显示单个图像，周围有一个进度环。
- **RingText** -显示单行文本，其中包含进度圆圈。
- **SimpleImage** -只显示小型单一图像。
- **SimpleText** -只显示小文本片段。
- **StackImage** -显示一个图像和一个文本行，其中一个文本在另一个上方
- **StackText** -显示两行文本。

### <a name="modular-large"></a>大模块

这些模板类名称全部都带有 `CLKComplicationTemplateModularLarge`前缀：

- **列**-显示包含2列的3行网格，还可以选择在每行左侧包含一个图像。
- **StandardBody** -显示带有两行纯文本的粗体标头字符串。 标题可以选择在左侧显示图像。
- **表**-显示一个粗体标头字符串，其中包含文本的2x2 网格。 标题可以选择在左侧显示图像。
- **TallBody** -显示一个粗体标头字符串，其下有一个较大的字体。

### <a name="utilitarian-small"></a>小型实用

这些模板类名称全部都带有 `CLKComplicationTemplateUtilitarianSmall`前缀：

- **平面**-在单个行上显示图像和某些文本（文本应为 short）。
- **RingImage** -显示单个图像，周围有一个进度环。
- **RingText** -显示单行文本，其中包含进度圆圈。
- **方形**-显示正方形图像（40px 或44px 正方形分别用于38mm 或 42mm Apple Watch）。

### <a name="utilitarian-large"></a>实用大型

对于此复杂样式，只有一个模板： `CLKComplicationTemplateUtilitarianLargeFlat`。
它在一行上显示单个图像和一些文本。

## <a name="related-links"></a>相关链接

- [Apple 文档](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 视频](https://developer.apple.com/videos/play/wwdc2015-209/)
