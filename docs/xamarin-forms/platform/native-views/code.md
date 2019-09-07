---
title: 在 C# 中的本机视图
description: 从 iOS、 Android 和 UWP 的本机视图，可以从使用 C# 创建的 Xamarin.Forms 页面直接引用。 本文演示如何将本机视图添加到使用 C# 中，创建的 Xamarin.Forms 布局以及如何重写自定义视图来更正其度量 API 使用情况的布局。
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: a759569f9faf7c8e13768a68b203a8221d3b31cb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770719"
---
# <a name="native-views-in-c"></a>C 中的本机视图\#

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_从 iOS、 Android 和 UWP 的本机视图，可以从使用 C# 创建的 Xamarin.Forms 页面直接引用。本文演示如何将本机视图添加到使用 C# 中，创建的 Xamarin.Forms 布局以及如何重写自定义视图来更正其度量 API 使用情况的布局。_

## <a name="overview"></a>概述

允许任何 Xamarin.Forms 控件`Content`设置，或具有`Children`集合中，可以添加特定于平台的视图。 例如，iOS`UILabel`可以直接添加到[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)属性，或设置为[ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children)集合。 但请注意，此功能需要使用`#if`Xamarin.Forms 共享项目解决方案中定义，不提供从 Xamarin.Forms.NET Standard 库解决方案。

下面的屏幕截图演示了特定于平台的视图具有已添加到 Xamarin.Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "包含特定于平台的视图的 StackLayout")](code-images/screenshots.png#lightbox "StackLayout 包含特定于平台的视图")

能够将特定于平台的视图添加到 Xamarin.Forms 布局被启用的每个平台上的两个扩展方法：

- `Add` – 将添加到的特定于平台的视图[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)布局的集合。
- `ToView` – 采用特定于平台的视图，并将其包装为 Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View)可以将设置为`Content`控件的属性。

在 Xamarin.Forms 共享项目中使用这些方法需要导入相应的特定于平台的 Xamarin.Forms 命名空间：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>添加每个平台上的特定于平台的视图

以下各节演示如何将特定于平台的视图添加到每个平台上的 Xamarin.Forms 布局。

### <a name="ios"></a>iOS

下面的代码示例演示如何添加`UILabel`到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)和一个[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

该示例假定`stackLayout`和`contentView`具有以前在 XAML 或 C# 中创建实例。

### <a name="android"></a>Android

下面的代码示例演示如何添加`TextView`到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)和一个[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

该示例假定`stackLayout`和`contentView`具有以前在 XAML 或 C# 中创建实例。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下面的代码示例演示如何添加`TextBlock`到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)和一个[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

该示例假定`stackLayout`和`contentView`具有以前在 XAML 或 C# 中创建实例。

## <a name="overriding-platform-measurements-for-custom-views"></a>重写自定义视图的平台度量

每个平台上的自定义视图通常只正确地实现设计它们的布局方案用于度量值。 例如，自定义视图可能旨在仅占用设备的可用宽度的一半。 但是，与其他用户共享之后, 自定义视图可能需要占用的设备的完整可用宽度。 因此，可能需要时重新使用 Xamarin.Forms 布局中重写自定义视图度量实现。 因此，`Add`和`ToView`扩展方法提供允许度量委托来指定，它时将其添加到 Xamarin.Forms 布局可以重写自定义视图布局的替代。

以下各节演示如何重写自定义视图，以更正其度量 API 使用情况的布局。

### <a name="ios"></a>iOS

下面的代码示例演示`CustomControl`类，该类继承自`UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

此视图的实例添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如以下代码示例所示：

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

但是，因为`CustomControl.SizeThatFits`重写将始终返回高度为 150，视图将显示为具有空白区域的上方和下方，如以下屏幕截图中所示：

![](code-images/ios-bad-measurement.png "iOS 使用错误 SizeThatFits 实现 CustomControl")

此问题的解决方案是提供`GetDesiredSizeDelegate`实现，如下面的代码示例中所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

此方法使用提供的宽度`CustomControl.SizeThatFits`方法，但将替换为 70%高度为 150 的高度。 当`CustomControl`实例添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，则`FixSize`方法可以指定为`GetDesiredSizeDelegate`若要修复错误所提供的度量`CustomControl`类：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

这会导致自定义视图显示正确，而无需空白区域的上方和下方，如以下屏幕截图中所示：

![](code-images/ios-good-measurement.png "iOS CustomControl GetDesiredSize 替代方法")

### <a name="android"></a>Android

下面的代码示例演示`CustomControl`类，该类继承自`TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

此视图的实例添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如以下代码示例所示：

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

但是，因为`CustomControl.OnMeasure`重写将始终返回请求的宽度的一半，则视图将显示占用只完成了一半的可用宽度的设备，如以下屏幕截图中所示：

![](code-images/android-bad-measurement.png "与错误 OnMeasure 实现 android CustomControl")

此问题的解决方案是提供`GetDesiredSizeDelegate`实现，如下面的代码示例中所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

此方法使用提供的宽度`CustomControl.OnMeasure`方法，但将其乘以 2。 当`CustomControl`实例添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，则`FixSize`方法可以指定为`GetDesiredSizeDelegate`若要修复错误所提供的度量`CustomControl`类：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

这会导致自定义视图所显示正确，占用的宽度的设备，如以下屏幕截图中所示：

![](code-images/android-good-measurement.png "与自定义 GetDesiredSize 委托 android CustomControl")

### <a name="universal-windows-platform"></a>通用 Windows 平台

下面的代码示例演示`CustomControl`类，该类继承自`Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

此视图的实例添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如以下代码示例所示：

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

但是，因为`CustomControl.ArrangeOverride`重写将始终返回请求的宽度的一半，将为可用宽度的一半的设备，剪辑视图，如以下屏幕截图中所示：

![](code-images/winrt-bad-measurement.png "与错误 ArrangeOverride 实现 UWP CustomControl")

此问题的解决方案是提供`ArrangeOverrideDelegate`实现中，添加到视图时[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如以下代码示例所示：

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

此方法使用提供的宽度`CustomControl.ArrangeOverride`方法，但将其乘以 2。 这会导致自定义视图所显示正确，占用的宽度的设备，如以下屏幕截图中所示：

![](code-images/winrt-good-measurement.png "UWP CustomControl 与 ArrangeOverride 委托")

## <a name="summary"></a>总结

本文介绍如何将本机视图添加到使用 C# 中，创建的 Xamarin.Forms 布局以及如何重写自定义视图来更正其度量 API 使用情况的布局。

## <a name="related-links"></a>相关链接

- [NativeEmbedding （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [本机窗体](~/xamarin-forms/platform/native-forms.md)
