---
title: Xamarin.Forms 复选框
description: Xamarin.Forms 复选框是一种按钮可以进行检查或为空。 选中一个复选框后，它已被视为不上。 如果一个复选框为空，它被视为处于关闭状态。
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517916"
---
# <a name="xamarinforms-checkbox"></a>Xamarin.Forms 复选框

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

Xamarin.Forms`CheckBox`的一种可能的按钮为选中或为空。 选中一个复选框后，它已被视为不上。 如果一个复选框为空，它被视为处于关闭状态。

`CheckBox` 定义`bool`名为属性`IsChecked`，指示是否`CheckBox`检查。 此属性还受[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着它可以设置样式，并将数据绑定的目标。

> [!NOTE]
> `IsChecked`可绑定属性设置了默认绑定模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)。

`CheckBox` 定义`CheckedChanged`已经激发时`IsChecked`属性更改，可通过用户操作，或当应用程序设置`IsChecked`属性。 `CheckedChangedEventArgs`对象，它附带`CheckedChanged`事件都有一个名为的单个属性`Value`，类型的`bool`。 激发事件时，值`Value`属性设置为的新值`IsChecked`属性。

## <a name="create-a-checkbox"></a>创建一个复选框

下面的示例演示如何实例化`CheckBox`在 XAML 中：

```xaml
<CheckBox />
```

此 XAML 会导致下面的屏幕截图中所示的外观：

![空复选框，在 iOS 和 Android 上的屏幕截图](checkbox-images/checkbox-empty.png "空的复选框")

默认情况下，`CheckBox`为空。 `CheckBox`用户操作，或通过设置可以检查`IsChecked`属性设置为`true`:

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 会导致下面的屏幕截图中所示的外观：

![选中的复选框，在 iOS 和 Android 上的屏幕截图](checkbox-images/checkbox-checked.png "选中复选框")

或者，`CheckBox`可以在代码中创建：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>响应更改状态的复选框

当`IsChecked`属性更改，可通过用户操作，或当应用程序设置`IsChecked`属性，`CheckedChanged`事件触发。 若要对更改做出响应，可以注册此事件的事件处理程序：

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

代码隐藏文件包含的处理程序`CheckedChanged`事件：

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

`sender`自变量是`CheckBox`负责此事件。 可以使用此访问`CheckBox`对象，或以区分多个`CheckBox`对象共享相同`CheckedChanged`事件。

或者，事件处理程序`CheckedChanged`可以在代码中注册事件：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>将数据绑定的复选框

`CheckedChanged`事件处理程序可通过使用数据绑定和触发器以响应来消除`CheckBox`被选中或为空：

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

在此示例中， [ `Label` ](xref:Xamarin.Forms.Label)在数据触发器中使用绑定表达式来监视`IsChecked`属性`CheckBox`。 当此属性变为`true`，则`FontAttributes`并`FontSize`的属性`Label`更改。 当`IsChecked`属性将返回到`false`，则`FontAttributes`并`FontSize`的属性`Label`重置为其初始状态。

IOS 的屏幕截图中的以下屏幕截图，显示[ `Label` ](xref:Xamarin.Forms.Label)格式设置时`CheckBox`为空，而 Android 屏幕截图显示了`Label`格式设置时`CheckBox`检查：

[![数据的屏幕截图绑定复选框，在 iOS 和 Android](checkbox-images/checkbox-databinding.png "数据绑定的复选框")](checkbox-images/checkbox-databinding-large.png#lightbox "数据绑定的复选框")

有关触发器的详细信息，请参阅[Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-checkbox"></a>禁用一个复选框

有时应用程序进入状态其中`CheckBox`正在检查不是有效的操作。 在这种情况下，`CheckBox`可以禁用通过设置其`IsEnabled`属性设置为`false`。

## <a name="checkbox-appearance"></a>复选框的外观

除了属性外，`CheckBox`继承自[ `View` ](xref:Xamarin.Forms.View)类`CheckBox`还定义了`Color`属性，可将其颜色设置为[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

以下屏幕截图显示了一系列选中`CheckBox`对象，其中每个对象都有其`Color`属性设置为另一种[ `Color` ](xref:Xamarin.Forms.Color):

![彩色复选框，在 iOS 和 Android 上的屏幕截图](checkbox-images/checkbox-colors.png "彩色的复选框")

## <a name="checkbox-visual-states"></a>复选框的视觉状态

`CheckBox` 具有`IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState)可用于启动到可视更改`CheckBox`时它将变为选中状态。

下面的 XAML 示例演示如何定义的可视状态`IsChecked`状态：

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

在此示例中， `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState)指定当`CheckBox`处于选中状态，其`Color`属性将设置为绿色。 `Normal` `VisualState`指定当`CheckBox`处于正常状态，其`Color`属性将设置为红色。 因此，总体效果是，`CheckBox`为红色，当它为空，且绿色时对其进行检查。

可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [复选框演示 （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
