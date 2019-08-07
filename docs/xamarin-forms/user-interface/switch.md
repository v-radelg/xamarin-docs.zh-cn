---
title: Xamarin Forms 开关
description: Xamarin 开关是一种按钮类型, 用户可以对其进行操作以在开启和关闭状态之间切换。 本文介绍如何使用 Switch 类显示切换 UI 元素。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 58755c54ce2afe80a8bf43adc25a0cf2d90a0bb5
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739458"
---
# <a name="xamarinforms-switch"></a>Xamarin Forms 开关

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Xamarin [`Switch`](xref:Xamarin.Forms.Switch)是一个水平切换按钮, 用户可以对其进行操作, 以在打开和关闭状态之间切换, 这些状态由一个`boolean`值表示。 类继承自[`View`。](xref:Xamarin.Forms.View) `Switch`

下面的屏幕截图显示`Switch`了在 iOS和 Android上的开关状态下的控件:

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-default.png "IOS 和 Android 上的交换机")

`Switch`控件定义了两个属性:

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)是一个`Color` , 它会影响`Switch`在切换或**开启**状态下呈现的方式。
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)指示是否**打开**的`Switch`值。 `boolean`

[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持这些属性, 这`Switch`意味着可以对其进行样式化并成为数据绑定的目标。

控件定义一个`Toggled`事件, 该事件在`IsToggled`属性更改时通过`IsToggled`用户操作或应用程序设置属性时引发。 `Switch` 事件附带的`Value`对象具有`bool`名为的单个属性, 类型为。 `ToggledEventArgs` `Toggled` 触发事件时, `Value`属性的值将反映`IsToggled`属性的新值。

## <a name="create-a-switch"></a>创建开关

`Switch`可以在 XAML 中实例化。 可以`IsToggled`设置其属性以`Switch`切换。 默认情况下, `IsToggled`属性为`false`。 下面的示例演示如何使用可选`Switch` `IsToggled`的属性集在 XAML 中实例化:

```xaml
<Switch IsToggled="true"/>
```

也`Switch`可以在代码中创建:

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>切换样式属性

属性可以设置为在其处于**开启**状态时`Switch`定义颜色。 `OnColor` 下面的示例演示如何`Switch` `OnColor`使用属性集在 XAML 中实例化:

```xaml
<Switch OnColor="Orange" />
```

`Switch`在`OnColor`代码中创建时, 还可以设置属性:

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

下面的屏幕截图显示`Switch`了在 iOS 和 Android 上的`OnColor`属性设置为`Color.Orange`时, 其**打开**和**关闭**切换状态:

![IOS 和 Android 上的开启和关闭状态的开关屏幕截图](switch-images/switch-states-oncolor.png "IOS 和 Android 上的交换机")

## <a name="respond-to-a-switch-state-change"></a>响应交换机状态更改

当属性更改时, 无论是通过用户操作, 还是在应用程序`IsToggled`设置属性时`Toggled` , 都将激发该事件。 `IsToggled` 可注册此事件的事件处理程序以响应更改:

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

事件`sender`处理程序`Switch`中的参数负责激发此事件。 您可以使用`sender`属性访问该`Switch`对象, 或区分共享同一`Toggled`事件处理程序`Switch`的多个对象。

还可以在代码中指定事件处理程序:`Toggled`

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>数据绑定开关

通过使用数据绑定和触发器来响应更改的`Switch`切换状态, 可以消除事件处理程序。`Toggled`

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

在此示例中, [`Label`](xref:Xamarin.Forms.Label)使用`DataTrigger`中的绑定表达式来监视`IsToggled`指定`styleSwitch`的`Switch`的属性。 当此属性变为`true`时`FontAttributes` , 将`FontSize`更改的`Label`和属性。 `false` `FontAttributes` `FontSize`当属性返回到时,的`Label`和属性将重置为它们的初始状态。 `IsToggled`

有关触发器的信息, 请参阅[Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-switch"></a>禁用交换机

应用程序可能进入正在切换的`Switch`状态, 这是无效操作。 在这种情况下`Switch` , 可以通过将其`IsEnabled`属性设置为`false`来禁用。 这会阻止用户操作`Switch`。

## <a name="related-links"></a>相关链接

* [切换演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin。窗体触发器](~/xamarin-forms/app-fundamentals/triggers.md)
