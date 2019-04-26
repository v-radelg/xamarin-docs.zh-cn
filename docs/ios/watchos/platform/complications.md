---
title: watchOS 在 Xamarin 中的并发数据
description: 本文档介绍如何使用在 Xamarin 中的 watchOS 复杂情况。 它讨论了如何将添加一个问题，编写复杂，模板，并提供示例代码。
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61225518"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS 在 Xamarin 中的并发数据

_watchOS 允许开发人员能够编写自定义复杂情况用于监视的人脸_

此页介绍了不同类型的复杂情况，以及如何将一个问题添加到你的 watchOS 3 应用。

请注意，每个 watchOS 应用程序只能有一个存在问题。

首先阅读[Apple 的 docs](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html)以确定您的应用程序是否适用于一个问题。 有 5`CLKComplicationFamily`类型的显示可供选择：

[![](complications-images/all-complications-sml.png "5 CLKComplicationFamily 可用的类型：小圆形，模块化，小、 大、 最有效小模块、 实用的大型")](complications-images/all-complications.png#lightbox)

应用程序可以实现只是一种样式，或者所有五个，具体取决于要显示的数据。
您还可以支持按时间顺序查看，提供在用户打开数字 Crown 过去和/或将来的某个时间值。

<a name="adding" />

## <a name="adding-a-complication"></a>添加了问题

### <a name="configuration"></a>配置

可以在创建期间，添加到监视应用程序或手动添加到现有解决方案复杂情况。

### <a name="add-new-project"></a>添加新项目...

**添加新项目...** 向导包括一个复选框，将自动创建难点控制器类和配置**Info.plist**文件：

![](complications-images/file-new-project-sml.png "包括功能栏复选框")

### <a name="existing-projects"></a>现有项目

若要添加到现有项目的一个问题：

1. 创建一个新**ComplicationController.cs**类文件，并实现`CLKComplicationDataSource`。
2. 将应用配置**Info.plist**公开的复杂性和标识支持的复杂性系列的类别。

下面更详细地介绍这些步骤。

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource 类

以下C#模板包括所需最小的方法来实现`CLKComplicationDataSource`。

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

请按照[编写复杂](#writing)将代码添加到此类的说明。

### <a name="infoplist"></a>Info.plist

监视扩展**Info.plist**文件应指定的名称`CLKComplicationDataSource`和你想要支持的复杂性系列的类别：

[![](complications-images/complications-config-sml.png "复杂类型系列")](complications-images/complications-config.png#lightbox)

**数据源类**条目列表将显示类名该子类`CLKComplicationDataSource`包括复杂逻辑的子类。

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

单个类，重写从方法中实现所有复杂功能`CLKComplicationDataSource`抽象类 (该类实现`ICLKComplicationDataSource`接口)。

### <a name="required-methods"></a>所需的方法

必须实现以下用于运行复杂的方法：

- `GetPlaceholderTemplate` -返回静态显示在配置期间或在应用程序不能提供的值时使用。
- `GetCurrentTimelineEntry` -运行复杂性时计算正确的显示。
- `GetSupportedTimeTravelDirections` -返回从选项`CLKComplicationTimeTravelDirections`如`None`， `Forward`， `Backward`，或`Forward | Backward`。

### <a name="privacy"></a>隐私

显示个人数据的复杂性

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` 或 `HideOnLockScreen`

如果此方法返回`HideOnLockScreen`出现的错误将会显示一个图标或应用程序名称 （和没有任何数据） 锁定监视时。

### <a name="updates"></a>更新

- `GetNextRequestedUpdateDate` 返回操作系统在接下来查询适用于应用一次更新的功能栏显示数据。

此外可以从 iOS 应用程序强制更新。

### <a name="supporting-time-travel"></a>支持按时间顺序查看

时间的差旅支持是可选的并由控制`GetSupportedTimeTravelDirections`方法。 如果它返回`Forward`， `Backward`，或`Forward | Backward`然后，必须实现以下方法

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>编写一个问题

复杂性范围从简单的数据显示为复杂的图像和按时间顺序查看支持的数据呈现。 下面的代码演示如何生成简单的、 单模板的复杂性。

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>代码示例

此示例中仅支持`UtilitarianLarge`模板，因此只能在特定的表盘支持该类型的复杂的选择。 时*选择*复杂性上监视，它将显示**我复杂性**以及何时*运行*其显示文本**分钟_小时_**  （与时间的部分）。

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

## <a name="complication-templates"></a>问题模板

有多种不同的模板可用于每个复杂样式。
**环**模板，可以显示周围复杂性，可用于以图形方式显示进度或其他某个值的进度样式环。

[Apple 的 CLKComplicationTemplate docs](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>小圆形

这些模板类名称的前缀为`CLKComplicationTemplateCircularSmall`:

- **RingImage** -显示包含在其周围采用进度环的单一映像。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **SimpleImage** -只是显示一个小的单个图像。
- **SimpleText** -只显示文本的一小段代码。
- **StackImage** -显示图像和文本，逐一行
- **StackText** -显示两行文本。

### <a name="modular-small"></a>小模块

这些模板类名称的前缀为`CLKComplicationTemplateModularSmall`:

- **ColumnsText** -显示的文本值 （2 行和 2 列） 的一个小网格。
- **RingImage** -显示包含在其周围采用进度环的单一映像。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **SimpleImage** -只是显示一个小的单个图像。
- **SimpleText** -只显示文本的一小段代码。
- **StackImage** -显示图像和文本，逐一行
- **StackText** -显示两行文本。

### <a name="modular-large"></a>大模块

这些模板类名称的前缀为`CLKComplicationTemplateModularLarge`:

- **列**-显示包含 2 列，也可以选择包含每个行的左侧图像 3 行的网格。
- **StandardBody** -显示具有两个行的纯文本的加粗标题字符串。 标头 （可选） 可以在左侧显示的图像。
- **表**-显示的加粗标题的字符串，其下的文本的一个 2 x 2 网格。 标头 （可选） 可以在左侧显示的图像。
- **TallBody** -显示具有更大字体单个文本行下方的加粗标题字符串。

### <a name="utilitarian-small"></a>实用小

这些模板类名称的前缀为`CLKComplicationTemplateUtilitarianSmall`:

- **平面**-（文本应该更短） 在单个行上显示图像和一些文本。
- **RingImage** -显示包含在其周围采用进度环的单一映像。
- **RingText** -显示单行文本，与在其周围采用进度环。
- **正方形**-显示正方形的图像 （40px 或 44px 38 mm 或 42 mm Apple Watch 分别为正方形）。

### <a name="utilitarian-large"></a>实用的大型

没有此功能栏样式只能有一个模板： `CLKComplicationTemplateUtilitarianLargeFlat`。
它显示单个图像和一些文本，在单个行。



## <a name="related-links"></a>相关链接

- [Apple 的文档](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC 视频](https://developer.apple.com/videos/play/wwdc2015-209/)
