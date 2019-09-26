---
title: 将设计时数据与 XAML 预览器结合使用
description: 本文介绍如何使用设计时数据来显示 XAML 预览器中的数据密集型布局，而不运行您的应用程序。
ms.prod: xamarin
ms.assetid: 0F608019-5951-4BE6-80E0-9EEE1733D642
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: a6a34615adc9cf290ff6bf9dd344487e5f29cfa2
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69887853"
---
# <a name="use-design-time-data-with-the-xaml-previewer"></a>将设计时数据与 XAML 预览器结合使用

_某些布局难以直观显示，无需数据。使用这些提示可充分利用 XAML 预览器中的数据密集型页面。_

## <a name="design-time-data-basics"></a>设计时数据基础

设计时数据是您设置的虚假数据，以使您的控件更易于在 XAML 预览程序中可视化。 若要开始，请将以下代码行添加到 XAML 页面的标头：

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

添加命名空间后，可以将其`d:`置于任何属性或控件的前面，以便在 XAML 预览器中显示它。 运行时`d:`未显示的元素。

例如，可以将文本添加到通常具有绑定数据的标签。

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![使用标签中的文本设计时间数据](xaml-previewer-images/designtimedata-label-sm.png "使用文本标记设计时间数据")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

在此示例中， `d:Text`如果没有，XAML 预览程序将不会显示标签。 相反，它会显示 "Name！" 其中，标签在运行时将包含实际数据。

可以将与`d:` Xamarin 控件的任何特性一起使用，如颜色、字号和间距。 甚至可以将其添加到控件本身：

```xaml
<d:Button Text="Design Time Button" />
```

[![使用按钮控件设计时间数据](xaml-previewer-images/designtimedata-controls-sm.png "使用按钮控件设计时间数据")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

在此示例中，按钮仅在设计时出现。 使用此方法可为[XAML 预览器不支持的自定义控件](render-custom-controls.md)放置中的占位符。

## <a name="preview-images-at-design-time"></a>在设计时预览图像

您可以为绑定到页面或动态加载的图像设置设计时间源。 在 Android 项目中，将你想要在 XAML 预览器中显示的图像添加到 **> 可绘制**文件夹的资源。 在 iOS 项目中，将图像添加到**Resources**文件夹。 然后，你可以在设计时在 XAML 预览器中显示该图像：

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![用图像设计时间数据](xaml-previewer-images/designtimedata-image-sm.png "用配置映像设计时间数据")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Listview 的设计时数据

Listview 是在移动应用中显示数据的一种常用方式。 但是，如果没有实际数据，就难以直观显示。 若要将设计时数据与它们一起使用，必须创建一个用作 System.windows.controls.itemscontrol.itemssource 的设计时数组。 XAML 预览器会在设计时显示 ListView 中该数组的内容。

```xaml
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

[![使用 ListView 设计时间数据](xaml-previewer-images/designtimedata-itemssource-sm.png "使用 ListView 设计时间数据")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

此示例将在 XAML 预览器中显示三个 TextCells 的 ListView。 您可以更改`x:String`为项目中现有的数据模型。

有关更复杂的示例，请参阅[James Montemagno 的 Hanselman 应用程序。](https://github.com/jamesmontemagno/Hanselman.Forms/blob/vnext/src/Hanselman/Views/Podcasts/PodcastDetailsPage.xaml#L26-L47)

## <a name="alternative-hardcode-a-static-viewmodel"></a>其他硬编码静态 ViewModel

如果不想将设计时数据添加到各个控件，可以设置模拟数据存储以绑定到页面。 请参阅 James Montemagno 的[博客文章添加设计时数据](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)，了解如何绑定到 XAML 中的静态 ViewModel。

## <a name="troubleshooting"></a>疑难解答

### <a name="requirements"></a>要求

设计时数据需要最少的 Xamarin 版本。窗体3.6。

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense 在我的设计时数据下显示波浪线

这是一个已知问题，将在即将发布的 Visual Studio 版本中得到解决。 项目仍将生成，但不会发生错误。

### <a name="the-xaml-previewer-stopped-working"></a>XAML 预览器已停止工作

尝试关闭并重新打开 XAML 文件，并清除和重新生成项目。
