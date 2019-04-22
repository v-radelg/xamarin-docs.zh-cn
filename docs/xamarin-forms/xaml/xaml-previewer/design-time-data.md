---
title: XAML 预览程序中使用设计时数据
description: 本文介绍如何使用设计时数据而无需运行您的应用程序在 XAML 预览程序中显示大量数据的布局。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 0ff9f8b5ee6f9468650b6535745706bee8f96536
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58866353"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>XAML 预览程序中使用设计时数据

_某些布局很难直观显示不包含数据。使用这些提示可以充分利用预览 XAML 预览程序中大量数据的页面。_

## <a name="design-time-data-basics"></a>设计时间数据基础知识

设计时数据是假设置以使您的控件更容易可视化 XAML 预览程序中的数据。 若要开始，到 XAML 页面的标头中添加以下代码行：

```csharp
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

添加命名空间之后, 您可以将`d:`任何属性或控件以显示 XAML 预览程序中的前面。 元素与`d:`不会显示在运行时。

例如，可以将文本添加到通常具有数据绑定到它的标签。

```csharp
<Label Text={Binding Name} d:Text="Name" />
```

[![设计时数据与在标签中的文本](xaml-previewer-images/designtimedata-label-sm.png "设计时间数据使用文本标签")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

 在此示例中，而无需`d:Text`，XAML 预览程序不会显示任何标签。 相反，它将显示"名称"标签将包含实际数据的情况在运行时。

可以使用`d:`使用 Xamarin.Forms 控件，如颜色、 字体大小和间距的任何属性。 您甚至可以将其添加到控件本身：

```csharp
<d:Button Text="Design Time Button" />
```

[![设计时数据与按钮控件](xaml-previewer-images/designtimedata-controls-sm.png "设计时数据与按钮控件")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此示例中，才会显示按钮在设计时。 使用此方法将在占位符[XAML 预览程序不支持的自定义控件](render-custom-controls.md)。

## <a name="preview-images-at-design-time"></a>在设计时预览图像

可以设置与页面绑定或动态加载中的图像的设计时间源。 在 Android 项目中，添加你想要为 XAML 预览程序中显示的图像**资源 > Drawable**文件夹。 在 iOS 项目中，将映像添加到**资源**文件夹。 然后可以在设计时在 XAML 预览程序中显示该图像：

```csharp
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```
[![设计时数据与图像](xaml-previewer-images/designtimedata-image-sm.png "设计时数据与配置映像")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的设计时数据

Listview 是一种流行方式移动应用中显示数据。 但是，也很难直观显示不包含实际数据。 若要使用与它们的设计时数据，您必须创建要用作 ItemsSource 的设计时间数组。 XAML 预览程序显示什么是在设计时在 ListView 中该数组中。

```csharp
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![设计时数据与 ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "设计时数据与 ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

此示例将 XAML 预览程序中显示三个 TextCells 的 ListView。 您可以更改`x:String`到你的项目中的现有数据模型。

请参阅[James Montemagno 的 Hanselman.Forms 应用](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L36-L57)有关更复杂的示例。


## <a name="alternative-hardcode-a-static-viewmodel"></a>备选过程：进行硬编码静态 ViewModel

如果不想要为单个控件添加设计时数据，您可以设置模拟数据存储区将绑定到您的页面。 请参阅 James Montemagno[博客文章上添加设计时数据](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)若要了解如何将绑定到 XAML 中静态 ViewModel。

## <a name="troubleshooting"></a>疑难解答

### <a name="requirements"></a>要求

设计时数据要求的最低版本为 Xamarin.Forms 3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 会显示在我的设计时数据下的波浪线

这是一个已知的问题，将在将来的 Visual Studio 版本中修复。 该项目仍将生成未出现错误。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 预览程序停止工作

请尝试关闭并重新打开 XAML 文件中，并清除和重新生成项目。
