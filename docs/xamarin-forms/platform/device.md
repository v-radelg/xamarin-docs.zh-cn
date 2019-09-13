---
title: Xamarin.Forms 设备类
description: 本文介绍如何使用 Xamarin.Forms Device 类，实现对功能和布局的细粒度控制，根据每个平台。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 77cc414cd9b15f99f95d4a54f7af5ce6f028c41a
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887444"
---
# <a name="xamarinforms-device-class"></a>Xamarin.Forms 设备类

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[ `Device` ](xref:Xamarin.Forms.Device)类包含大量属性和方法，以帮助开发人员自定义布局和根据每个平台的功能。

除了使用特定硬件类型和大小的代码的方法和属性以外， `Device`类还包括可用于与后台线程中的 UI 控件交互的方法。 有关详细信息，请参阅[从后台线程与 UI 交互](#interact-with-the-ui-from-background-threads)。

## <a name="providing-platform-specific-values"></a>提供特定于平台的值

在 Xamarin.Forms 2.3.4 之前, 的平台运行应用程序无法获取通过检查[ `Device.OS` ](xref:Xamarin.Forms.Device.OS)属性并将对其进行比较[ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS)， [`TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android)， [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone)，并且[ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows)枚举值。 同样，之一[ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))重载可用于提供对控件的特定于平台的值。

但是，因为 Xamarin.Forms 2.3.4 这些 Api 已弃用并替换。 [ `Device` ](xref:Xamarin.Forms.Device)类现在包含的标识平台 – 公共字符串常量[ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS)， [ `Device.Android` ](xref:Xamarin.Forms.Device.Android)， `Device.WinPhone`(不推荐使用）， `Device.WinRT` （已弃用） [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP)，并[ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS)。 同样， [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))已替换重载[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)并[ `On` ](xref:Xamarin.Forms.On) Api。

在C#，可以通过创建提供特定于平台的值`switch`语句[ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform)属性，并提供`case`语句所需的平台：

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)并[ `On` ](xref:Xamarin.Forms.On)类提供 XAML 中的相同功能：

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)类是必须使用实例化一个泛型类`x:TypeArguments`匹配目标类型的属性。 在中[ `On` ](xref:Xamarin.Forms.On)类， [ `Platform` ](xref:Xamarin.Forms.On.Platform)属性可以接受单个`string`值或以逗号分隔的多个`string`值。

> [!IMPORTANT]
> 提供不正确`Platform`属性中的值`On`类不会导致错误。 相反，该代码将执行且不应用任何特定于平台的值。

或者，`OnPlatform`标记扩展可用于在 XAML 中自定义根据每个平台的 UI 外观。 有关详细信息，请参阅[OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom`属性可用于更改布局或功能，具体取决于设备应用程序上运行。 [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom)枚举包含的以下值：

- **Phone** – iPhone、 iPod touch 和 Android 设备窄于 600 dip ^
- **平板电脑**– iPad，Windows 设备和 Android 设备宽于 600 dip ^
- **桌面**– 仅在返回[UWP 应用](~/xamarin-forms/platform/windows/installation/index.md)Windows 10 桌面计算机上 (返回`Phone`移动 Windows 设备，包括连续体方案中)
- **电视**– Tizen TV 设备
- **观看**– Tizen 监视设备
- **不支持**– 未使用

*^ dip 不一定是物理像素计数*

`Idiom`属性是用于构建充分利用较大的屏幕，此类的布局特别有用：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1)类提供在 XAML 中的相同功能：

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

[ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1)类是必须使用实例化一个泛型类`x:TypeArguments`匹配目标类型的属性。

或者，`OnIdiom`标记扩展可用于在 XAML 中自定义 UI 外观基于的设备运行应用程序的惯用语法。 有关详细信息，请参阅[OnIdiom 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom)。

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)值检索[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)枚举值，该值表示当前正由设备的流方向。 流方向是指页面 UI 元素的浏览方向。 枚举值为：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中， [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)值可以通过使用`x:Static`标记扩展：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

中的等效代码C#是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

有关流方向的详细信息，请参阅[从右到左本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>Device.Styles

[ `Styles`属性](~/xamarin-forms/user-interface/styles/index.md)包含可应用于某些控件的内置样式定义 (如`Label`)`Style`属性。 可用的样式包括：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` 设置时，可以使用[ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md)在C#代码：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="deviceopenuri"></a>Device.OpenUri

`OpenUri`方法可用于触发对基础平台，如本机 web 浏览器中打开一个 URL 的操作 (**Safari**在 iOS 上或**Internet**在 Android 上)。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[WebView 示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包括的示例使用`OpenUri`若要打开的 Url 和也会触发电话呼叫。

[地图示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)还使用`Device.OpenUri`以显示地图和方向使用本机**映射**iOS 和 Android 上的应用。

## <a name="devicestarttimer"></a>Device.StartTimer

`Device`类还具有`StartTimer`方法提供的适用于 Xamarin.Forms 的常见代码，包括.NET Standard 库的触发时间相关的任务的简单方法。 传递`TimeSpan`若要设置的间隔，并返回`true`保持运行的计时器或`false`当前调用后将其停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果内部计时器的代码与用户界面进行交互 (例如，设置的文本`Label`，或显示的警报) 应在内部完成`BeginInvokeOnMainThread`表达式 （见下文）。

## <a name="interact-with-the-ui-from-background-threads"></a>从后台线程与 UI 交互

大多数操作系统（包括 iOS、Android 和通用 Windows 平台）都将单线程模型用于涉及用户界面的代码。 此线程通常称为 "*主线程*" 或 " *UI 线程*"。 此模型的结果是，所有访问用户界面元素的代码都必须在应用程序的主线程上运行。

应用程序有时会使用后台线程来执行可能长时间运行的操作，例如从 web 服务检索数据。 如果后台线程上运行的代码需要访问用户界面元素，则必须在主线程上运行该代码。

此`Device`类包括以下`static`方法，可用于与背景线程中的用户界面元素进行交互：

| 方法 | 自变量 | 返回 | 用途 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | `Action`在主线程上调用，而不会等待它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主线程上调用 `Func<T>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主线程上调用 `Action`，并等待其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主线程上调用 `Func<Task<T>>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主线程上调用 `Func<Task>`，并等待其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 返回主线程的 `SynchronizationContext`。 |

下面的代码显示使用`BeginInvokeOnMainThread`方法的示例：

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>相关链接

- [设备示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [样式示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [设备](xref:Xamarin.Forms.Device)
