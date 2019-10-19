---
title: Xamarin Forms 设备类
description: 本文介绍如何使用 Xamarin Forms 设备类对每个平台的功能和布局进行精细控制。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: e77dc003e3786767f873e71514ebb3b7aef8c8d6
ms.sourcegitcommit: 8f78139333aa54d9247710008bcfae15f7fd5392
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72583552"
---
# <a name="xamarinforms-device-class"></a>Xamarin Forms 设备类

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[@No__t_1](xref:Xamarin.Forms.Device)类包含多个属性和方法，可帮助开发人员基于每个平台自定义布局和功能。

除了用于特定硬件类型和大小的代码的方法和属性以外，`Device` 类还包括可用于与后台线程中的 UI 控件交互的方法。 有关详细信息，请参阅[从后台线程与 UI 交互](#interact-with-the-ui-from-background-threads)。

## <a name="providing-platform-specific-values"></a>提供特定于平台的值

在2.3.4 之前运行应用程序的平台，可以通过检查[`Device.OS`](xref:Xamarin.Forms.Device.OS)属性，并将其与[`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)、 [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android)、 [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)和[`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)枚举进行比较来获得。分隔. 同样，其中一个[`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))重载可用于向控件提供平台特定的值。

不过，由于 Xamarin. Forms 2.3.4，这些 Api 已弃用并已替换。 [@No__t_1](xref:Xamarin.Forms.Device)类现在包含用于标识平台的公共字符串常量（ [`Device.iOS`](xref:Xamarin.Forms.Device.iOS)、 [`Device.Android`](xref:Xamarin.Forms.Device.Android)、`Device.WinPhone` （弃用）、`Device.WinRT` （不推荐使用）、 [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)和[`Device`1](xref:Xamarin.Forms.Device.macOS)。 同样， [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))重载已替换为[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On) api。

在C#中，可以通过在[`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform)属性上创建 `switch` 语句，并为所需平台提供 `case` 语句，来提供特定于平台的值：

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

[@No__t_1](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)类在 XAML 中提供了相同的功能：

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

[@No__t_1](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用与目标类型匹配的 `x:TypeArguments` 属性对其进行实例化。 在[`On`](xref:Xamarin.Forms.On)类中， [`Platform`](xref:Xamarin.Forms.On.Platform)属性可以接受单个 `string` 值或多个以逗号分隔的 `string` 值。

> [!IMPORTANT]
> 在 `On` 类中提供不正确的 `Platform` 属性值不会导致错误。 相反，代码将在不应用特定于平台的值的情况下执行。

另外，还可以在 XAML 中使用 `OnPlatform` 标记扩展，根据每个平台自定义 UI 外观。 有关详细信息，请参阅[OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="deviceidiom"></a>Device。

@No__t_0 属性可用于根据应用程序运行的设备更改布局或功能。 [@No__t_1](xref:Xamarin.Forms.TargetIdiom)枚举包含以下值：

- **手机**– IPhone、iPod Touch 和 Android 设备的范围低于600
- **Tablet** -IPad、Windows 设备和 Android 设备，宽于600
- **桌面**–仅在 Windows 10 台式计算机上的[UWP 应用](~/xamarin-forms/platform/windows/installation/index.md)中返回（在移动 Windows 设备上返回 `Phone`，包括在 Continuum 方案中）
- **电视**– TIZEN 的电视设备
- **手表**– Tizen Watch 设备
- **不支持**–未使用

*^ dip 不一定是物理像素计数*

@No__t_0 属性对于构建利用较大屏幕的布局特别有用，如下所示：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[@No__t_1](xref:Xamarin.Forms.OnIdiom`1)类在 XAML 中提供了相同的功能：

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

[@No__t_1](xref:Xamarin.Forms.OnPlatform`1)类是一个泛型类，必须使用与目标类型匹配的 `x:TypeArguments` 属性对其进行实例化。

另外，还可以在 XAML 中使用 `OnIdiom` 标记扩展，根据应用程序运行所在的设备的用法来自定义 UI 外观。 有关详细信息，请参阅[OnIdiom 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom)。

## <a name="deviceflowdirection"></a>System.windows.flowdirection>

[@No__t_1](xref:Xamarin.Forms.VisualElement.FlowDirection)值将检索表示设备当前正在使用的流动方向的[`FlowDirection`](xref:Xamarin.Forms.FlowDirection)枚举值。 流方向是指页面 UI 元素的浏览方向。 枚举值为：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中，可以通过使用 `x:Static` 标记扩展来检索[`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)值：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

中C#的等效代码是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

有关流方向的详细信息，请参阅[从右到左的本地化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>设备样式

[@No__t_1 属性](~/xamarin-forms/user-interface/styles/index.md)包含可应用于某些控件（如 `Label`） `Style` 属性的内置样式定义。 可用样式包括：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>GetNamedSize

在代码中C#设置[`FontSize`](~/xamarin-forms/user-interface/text/fonts.md)时可以使用 `GetNamedSize`：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="deviceopenuri"></a>OpenUri

@No__t_0 方法可用于触发基础平台上的操作，例如在本机 web 浏览器中打开 URL （iOS 上的**Safari**或 Android 上的**Internet** ）。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[Web 视图示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包含一个示例，该示例使用 `OpenUri` 打开 url，同时还会触发电话呼叫。

[Map 示例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)还使用 `Device.OpenUri` 在 IOS 和 Android 上使用本机**地图**应用显示地图和方向。

## <a name="devicestarttimer"></a>StartTimer

@No__t_0 类还具有一个 `StartTimer` 方法，该方法提供了一种简单的方法来触发 Xamarin 中的依赖于时间的任务 .NET Standard。 传递 `TimeSpan` 以设置时间间隔并返回 `true`，以使计时器运行或 `false` 在当前调用之后将其停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果计时器内的代码与用户界面交互（例如设置 `Label` 的文本或显示警报），则应在 `BeginInvokeOnMainThread` 表达式中完成此操作（见下文）。

> [!NOTE]
> @No__t_0 和 `System.Threading.Timer` 类是使用 `Device.StartTimer` 方法的 .NET Standard 替代项。

## <a name="interact-with-the-ui-from-background-threads"></a>从后台线程与 UI 交互

大多数操作系统（包括 iOS、Android 和通用 Windows 平台）都将单线程模型用于涉及用户界面的代码。 此线程通常称为 "*主线程*" 或 " *UI 线程*"。 此模型的结果是，所有访问用户界面元素的代码都必须在应用程序的主线程上运行。

应用程序有时会使用后台线程来执行可能长时间运行的操作，例如从 web 服务检索数据。 如果后台线程上运行的代码需要访问用户界面元素，则必须在主线程上运行该代码。

@No__t_0 类包含以下 `static` 方法，这些方法可用于与背景线程中的用户界面元素进行交互：

| 方法 | 自变量 | 返回 | 目标 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | 在主线程上调用 `Action`，而不会等待它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主线程上调用 `Func<T>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主线程上调用 `Action`，并等待其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主线程上调用 `Func<Task<T>>`，并等待其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主线程上调用 `Func<Task>`，并等待其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 返回主线程的 `SynchronizationContext`。 |

下面的代码演示了使用 `BeginInvokeOnMainThread` 方法的示例：

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
