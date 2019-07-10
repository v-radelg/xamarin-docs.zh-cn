---
title: Xamarin.Forms Switch
description: Xamarin.Forms 开关是按钮的一种可以由用户打开和关闭状态之间切换操作。 此文章介绍了如何使用开关类来显示切换的 UI 元素。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 22a17f9a916d94a3a0f44a451512de43c943e95a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675032"
---
# <a name="xamarinforms-switch"></a>Xamarin.Forms Switch

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch)是一个水平切换按钮，可以由用户之间进行切换打开和关闭状态，由表示操作`boolean`值。 `Switch`类继承自[ `View` ](xref:Xamarin.Forms.View)。

以下屏幕截图显示`Switch`控制在其**上**并**关闭**切换 iOS 和 Android 上的状态：

![屏幕截图的交换机中打开和关闭状态，在 iOS 和 Android](switch-images/switch-states-default.png "iOS 和 Android 上切换")

`Switch`控件定义了两个属性：

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) 是`Color`，可影响如何`Switch`是呈现中切换，或**上**，状态。
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 是`boolean`值，该值指示是否`Switch`是**上**。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着`Switch`可以设置的样式和作为目标的数据绑定。

`Switch`控件定义`Toggled`时引发的事件`IsToggled`属性更改，可通过用户操作，或当应用程序设置`IsToggled`属性。 `ToggledEventArgs`对象，它附带`Toggled`事件都有一个名为的单个属性`Value`，类型的`bool`。 激发事件时，值`Value`属性会反映出的新值`IsToggled`属性。

## <a name="create-a-switch"></a>创建交换机

一个`Switch`可以在 XAML 中实例化。 其`IsToggled`属性可以设置切换`Switch`。 默认情况下`IsToggled`属性是`false`。 下面的示例演示如何实例化`Switch`中使用可选的 XAML`IsToggled`属性集：

```xaml
<Switch IsToggled="true"/>
```

一个`Switch`还可以在代码中创建：

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>切换样式属性

`OnColor`可以设置属性来定义`Switch`颜色时它会切换到其**上**状态。 下面的示例演示如何实例化`Switch`中使用的 XAML`OnColor`属性集：

```xaml
<Switch OnColor="Orange" />
```

`OnColor`创建时，还可以设置属性`Switch`在代码中：

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

以下屏幕截图显示`Switch`在其**上**和**关闭**切换状态，与`OnColor`属性设置为`Color.Orange`iOS 和 Android 上：

![屏幕截图的交换机中打开和关闭状态，在 iOS 和 Android](switch-images/switch-states-oncolor.png "iOS 和 Android 上切换")

## <a name="respond-to-a-switch-state-change"></a>响应的开关状态更改

当`IsToggled`属性更改，可通过用户操作，或当应用程序设置`IsToggled`属性，`Toggled`事件触发。 若要对更改做出响应，可以注册此事件的事件处理程序：

```xaml
<Switch Toggled="OnToggled" />
```

代码隐藏文件包含的处理程序`Toggled`事件：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

`sender`事件处理程序中的自变量是`Switch`负责触发此事件。 可以使用`sender`属性来访问`Switch`对象，或以区分多个`Switch`对象共享相同`Toggled`事件处理程序。

`Toggled`还可以在代码中分配事件处理程序：

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>将数据绑定的交换机

`Toggled`事件处理程序可通过使用数据绑定和触发器以响应来消除`Switch`更改切换状态。

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此示例中， [ `Label` ](xref:Xamarin.Forms.Label)使用中的绑定表达式`DataTrigger`来监视`IsToggled`属性的`Switch`名为`styleSwitch`。 当此属性变为`true`，则`FontAttributes`并`FontSize`的属性`Label`发生更改。 当`IsToggled`属性将返回到`false`，则`FontAttributes`并`FontSize`的属性`Label`重置为其初始状态。

有关触发器的信息，请参阅[Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-switch"></a>禁用开关

应用程序可能会进入状态其中`Switch`要切换不是有效的操作。 在这种情况下，`Switch`可以禁用通过设置其`IsEnabled`属性设置为`false`。 这将阻止用户成为知道如何处理`Switch`。

## <a name="related-links"></a>相关链接

* [切换演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Xamarin.Forms 触发器](~/xamarin-forms/app-fundamentals/triggers.md)
