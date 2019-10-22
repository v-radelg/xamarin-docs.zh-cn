---
title: Xamarin 复选框
description: Xamarin 复选框是一种可以选中或空的按钮类型。 选中复选框后，它将被视为已启用。 如果复选框为空，则将其视为关闭。
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739149"
---
# <a name="xamarinforms-checkbox"></a>Xamarin 复选框

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin `CheckBox` 是一种可以选中或空的按钮类型。 选中复选框后，它将被视为已启用。 如果复选框为空，则将其视为关闭。

`CheckBox` 定义了一个名为 `IsChecked` `bool` 属性，该属性指示是否选中 `CheckBox`。 此属性也是[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它可以进行样式化，也可以是数据绑定的目标。

> [!NOTE]
> @No__t_0 可绑定属性的默认绑定模式为[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)。

`CheckBox` 定义一个 `CheckedChanged` 事件，该事件在 `IsChecked` 属性发生更改时通过用户操作或在应用程序设置 `IsChecked` 属性时激发。 @No__t_1 事件附带的 `CheckedChangedEventArgs` 对象具有名为 `Value` 的单个属性，类型 `bool`。 触发事件时，`Value` 属性的值将设置为 `IsChecked` 属性的新值。

## <a name="create-a-checkbox"></a>创建复选框

下面的示例演示如何在 XAML 中实例化 `CheckBox`：

```xaml
<CheckBox />
```

此 XAML 将显示以下屏幕截图所示的外观：

![IOS 和 Android 上的空复选框的屏幕截图](checkbox-images/checkbox-empty.png "空复选框")

默认情况下，`CheckBox` 为空。 @No__t_0 可以通过用户操作来检查，也可以通过将 `IsChecked` 属性设置为 `true` 来进行检查：

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 将显示以下屏幕截图所示的外观：

![IOS 和 Android 上选中的复选框的屏幕截图](checkbox-images/checkbox-checked.png "选中的复选框")

或者，可以在代码中创建 `CheckBox`：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>响应更改状态的复选框

当 `IsChecked` 属性更改时，无论是通过用户操作更改，还是在应用程序设置 `IsChecked` 属性时，将激发 `CheckedChanged` 事件。 可注册此事件的事件处理程序以响应更改：

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

代码隐藏文件包含 `CheckedChanged` 事件的处理程序：

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

@No__t_0 参数是负责此事件的 `CheckBox`。 您可以使用此访问 `CheckBox` 对象，或区分共享同一 `CheckedChanged` 事件的多个 `CheckBox` 对象。

或者，可以在代码中注册 `CheckedChanged` 事件的事件处理程序：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>数据绑定复选框

通过使用数据绑定和触发器来响应要检查的 `CheckBox` 或为空，可以消除 `CheckedChanged` 事件处理程序：

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此示例中， [`Label`](xref:Xamarin.Forms.Label)在数据触发器中使用绑定表达式来监视 `CheckBox` 的 `IsChecked` 属性。 当此属性 `true` 时，`Label` 的 `FontAttributes` 和 `FontSize` 属性会更改。 当 `IsChecked` 属性返回到 `false` 时，`Label` 的 `FontAttributes` 和 `FontSize` 属性将重置为它们的初始状态。

在下面的屏幕截图中，当 `CheckBox` 为空时，iOS 屏幕截图将显示[`Label`](xref:Xamarin.Forms.Label)格式，而 Android 屏幕快照会在检查 `CheckBox` 时显示 `Label` 格式：

[![IOS 和 Android 上的数据绑定复选框的屏幕截图](checkbox-images/checkbox-databinding.png ""数据绑定" 复选框")](checkbox-images/checkbox-databinding-large.png#lightbox ""数据绑定" 复选框")

有关触发器的详细信息，请参阅[Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-checkbox"></a>禁用复选框

有时，应用程序进入状态，在该状态下，要检查的 `CheckBox` 不是有效操作。 在这种情况下，可以通过将 `CheckBox` 的 `IsEnabled` 属性设置为 `false` 来禁用。

## <a name="checkbox-appearance"></a>CheckBox 外观

除了 `CheckBox` 继承自[`View`](xref:Xamarin.Forms.View)类的属性以外，`CheckBox` 还定义 `Color` 属性，该属性将其颜色设置为[`Color`](xref:Xamarin.Forms.Color)：

```xaml
<CheckBox Color="Red" />
```

下面的屏幕截图显示一系列经过检查的 `CheckBox` 对象，其中每个对象的 `Color` 属性设置为不同的[`Color`](xref:Xamarin.Forms.Color)：

![IOS 和 Android 上的彩色复选框的屏幕截图](checkbox-images/checkbox-colors.png "彩色复选框")

## <a name="checkbox-visual-states"></a>复选框可视状态

`CheckBox` 具有一个 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于在其被选中时启动对 `CheckBox` 的视觉更改。

下面的 XAML 示例演示如何为 `IsChecked` 状态定义可视状态：

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

在此示例中，`IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState)指定在检查 `CheckBox` 时，其 `Color` 属性将设置为绿色。 @No__t_0 `VisualState` 指定 `CheckBox` 处于正常状态时，其 `Color` 属性将设置为红色。 因此，整体效果是 `CheckBox` 在它为空时为红色，选中时为绿色。

有关可视状态的详细信息，请参阅[Xamarin。窗体可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [复选框演示（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. Forms 视觉对象状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)
