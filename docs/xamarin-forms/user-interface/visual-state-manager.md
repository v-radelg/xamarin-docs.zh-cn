---
title: Xamarin. Forms 视觉对象状态管理器
description: 使用可视状态管理器根据代码中的可视状态集对 XAML 元素进行更改。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656060"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin. Forms 视觉对象状态管理器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_使用可视状态管理器根据代码中的可视状态集对 XAML 元素进行更改。_

视觉状态管理器（VSM）是 Xamarin. Forms 3.0 中的新增项。 VSM 提供了一种结构化的方法，可以从代码对用户界面进行可视更改。 在大多数情况下，应用程序的用户界面是在 XAML 中定义的，此 XAML 包含描述视觉状态管理器如何影响用户界面视觉对象的标记。

VSM 引入了_可视状态_的概念。 @No__t_0 的 Xamarin 视图可以有多个不同的视觉外观，具体取决于其基本状态 &mdash; 是已禁用、已按下还是有输入焦点。 这些是按钮的状态。

可视状态在_视觉状态组_中收集。 可视状态组中的所有可视状态都是互斥的。 可视状态和视觉状态组均由简单文本字符串标识。

Xamarin. Forms 视觉对象状态管理器定义了一个名为 "CommonStates" 的可视化状态组，其中包含三种可视状态：

- "Normal"
- Disabled
- 侧重于

派生自[`VisualElement`](xref:Xamarin.Forms.VisualElement)的所有类都支持此视觉对象状态组，这是[`View`](xref:Xamarin.Forms.View)和[`Page`](xref:Xamarin.Forms.Page)的基类。 

你还可以定义自己的视觉状态组和视觉状态，如本文所示。

> [!NOTE]
> Xamarin：熟悉[触发器](~/xamarin-forms/app-fundamentals/triggers.md)的窗体开发人员可以知道，触发器还可以根据视图属性中的更改或事件激发来更改用户界面中的视觉对象。 然而，使用触发器处理这些更改的各种组合可能会令人感到困惑。 从历史角度来看，可视状态管理器是在基于 Windows XAML 的环境中引入的，用于缓解由于视觉状态组合而产生的混淆。 使用 VSM，可视状态组中的可视状态始终是互斥的。 在任何时候，每个组中只有一个状态是当前状态。

## <a name="the-common-states"></a>常见状态

视觉状态管理器允许您在 XAML 文件中包含部分，如果视图正常、处于禁用状态或具有输入焦点，则可以更改视图的可视外观。 这些_状态称为公共状态_。

例如，假设页面上有一个 `Entry` 视图，并且希望 `Entry` 的视觉外观按以下方式变化：

- 当 `Entry` 处于禁用状态时，`Entry` 应会有粉红色的背景。
- @No__t_0 的背景通常为酸橙色。
- 当该 `Entry` 有输入焦点时，应扩展为其正常高度的两倍。

可以将 VSM 标记附加到单个视图，也可以在样式中定义它（如果它应用于多个视图）。 接下来的两部分介绍了这些方法。

### <a name="vsm-markup-on-a-view"></a>视图上的 VSM 标记

若要将 VSM 标记附加到 `Entry` 视图，请先将 `Entry` 分离到开始标记和结束标记中：

```xaml
<Entry FontSize="18">

</Entry>
```

由于其中一种状态将使用 `FontSize` 属性为 `Entry` 中的文本大小加倍，因此提供了一个显式字体大小。

接下来，在这些标记之间插入 `VisualStateManager.VisualStateGroups` 标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)是由[`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager)类定义的附加可绑定属性。 （有关附加的可绑定属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)一文。）这就是 `VisualStateGroups` 属性附加到 `Entry` 对象的方式。

@No__t_0 属性的类型为[`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList)，它是[`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup)对象的集合。 在 `VisualStateManager.VisualStateGroups` 标记中，为想要包括的每个可视状态组插入一对 `VisualStateGroup` 标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

请注意，`VisualStateGroup` 标记具有指示组名称的 `x:Name` 属性。 @No__t_0 类定义可改用的 `Name` 属性：

```xaml
<VisualStateGroup Name="CommonStates">
```

可以在同一元素中使用 `x:Name` 或 `Name`，但不能同时使用两者。

@No__t_0 类定义名为[`States`](xref:Xamarin.Forms.VisualStateGroup.States)的属性，该属性是[`VisualState`](xref:Xamarin.Forms.VisualState)对象的集合。 `States` 是 `VisualStateGroups` 的_content 属性_，因此可以在 `VisualStateGroup` 标记之间直接包含 `VisualState` 标记。 （有关内容属性的介绍，请参见[基本的 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)一文。）

下一步是为该组中的每个可视状态包含一对标记。 还可以使用 `x:Name` 或 `Name` 来识别这些内容：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` 定义名为[`Setters`](xref:Xamarin.Forms.VisualState.Setters)的属性，该属性是[`Setter`](xref:Xamarin.Forms.Setter)对象的集合。 它们 `Setter` 在[`Style`](xref:Xamarin.Forms.Style)对象中使用的对象。

`Setters`_不_是 `VisualState` 的 content 属性，因此需要包含 `Setters` 属性的属性元素标记：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

你现在可以在每对 `Setters` 标记之间插入一个或多个 `Setter` 对象。 定义前面所述的视觉对象状态的 `Setter` 对象如下：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

每个 `Setter` 标记指示特定属性在该状态为当前状态时的值。 @No__t_0 对象引用的任何属性都必须由可绑定的属性支持。

与此类似的标记是 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例程序中 "**查看**" 页的基础。 此页包含三个 `Entry` 视图，但只有第二个视图附加了 VSM 标记：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

请注意，第二个 `Entry` 的 `Trigger` 集合中还包含一个 `DataTrigger`。 这会导致在第三个 `Entry` 中键入内容之前禁用 `Entry`。 在 iOS、Android 和通用 Windows 平台（UWP）上运行时，会显示以下页面：

[![视图上的 VSM：已禁用](vsm-images/VsmOnViewDisabled.png "视图上的 VSM-已禁用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

当前视觉状态为 "已禁用"，因此第二个 `Entry` 的背景在 iOS 和 Android 屏幕上为粉红色。 @No__t_0 的 UWP 实现不允许在禁用 `Entry` 时设置背景色。 

在第三个 `Entry` 中输入一些文本后，第二个 `Entry` 会切换为 "正常" 状态，并且背景现在为酸橙色：

[![视图上的 VSM：正常](vsm-images/VsmOnViewNormal.png "视图上的 VSM-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

当你触摸第二个 `Entry` 时，它将获取输入焦点。 它将切换到 "重点" 状态，并扩展到其高度的两倍：

[![视图上的 VSM：聚焦](vsm-images/VsmOnViewFocused.png "基于视图的 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

请注意，当获得输入焦点时，`Entry` 不会保留酸橙色背景。 当可视状态管理器在可视状态之间切换时，先前状态设置的属性将设置为 "未设置"。 请记住，可视状态是互斥的。 "正常" 状态并不意味着只启用 `Entry`。 这意味着 `Entry` 已启用，并且没有输入焦点。 

如果希望 `Entry` 在 "重点" 状态下具有酸橙色背景，请将另一个 `Setter` 添加到该视觉对象状态：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

为了使这些 `Setter` 对象正常工作，`VisualStateGroup` 必须包含该组中所有状态的 `VisualState` 对象。 如果存在没有任何 `Setter` 对象的视觉状态，请将其包含为空标记：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>样式中的可视状态管理器标记

通常需要在两个或更多视图中共享同一可视状态管理器标记。 在这种情况下，需要将标记放在 `Style` 定义中。

下面是 "查看" "查看" 页面**上**`Entry` 元素的现有隐式 `Style`：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

添加 `VisualStateManager.VisualStateGroups` 附加的可绑定属性的 `Setter` 标记：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

@No__t_0 的 content 属性是 `Value` 的，因此可以在这些标记中直接指定 `Value` 属性的值。 该属性的类型为 `VisualStateGroupList`：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

在这些标记中，可以包含一个或多个 `VisualStateGroup` 对象：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

VSM 标记的剩余部分与之前相同。

下面是 "**样式**" 页中显示完整的 vsm 标记的 vsm：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

现在，此页上的所有 `Entry` 视图都以相同的方式对其视觉对象状态做出响应。 另请注意，"重点" 状态现在包含第二个 `Setter`，当每个 `Entry` 具有输入焦点时，还会使每个具有酸橙色背景：

[![VSM 样式](vsm-images/VsmInStyle.png "VSM 样式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定义自己的视觉状态

派生自 `VisualElement` 的每个类都支持 "Normal"、"聚焦" 和 "Disabled" 三种常见状态。 在内部， [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)类将在它处于启用或禁用状态、焦点或失去焦点并调用静态[`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String))方法时进行检测：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

这是你将在 `VisualElement` 类中找到的唯一视觉状态管理器代码。 由于每个对象都基于派生自 `VisualElement` 的每个类调用 `GoToState`，因此您可以将视觉状态管理器与任何 `VisualElement` 对象一起使用来响应这些更改。

有趣的是，在 `VisualElement` 中未显式引用视觉状态组 "CommonStates" 的名称。 组名不是可视状态管理器的 API 的一部分。 到目前为止，您可以将组的名称从 "CommonStates" 更改为其他任何程序，程序仍将运行。 组名称只是该组中状态的一般说明。 它被隐式理解，任何组中的可视状态都是互斥的：一个状态，并且在任何时候都只有一个状态是最新的。

如果要实现自己的视觉状态，需要从代码调用 `VisualStateManager.GoToState`。 大多数情况下，你将从页面类的代码隐藏文件进行此调用。

**[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例中的 " **VSM 验证**" 页显示了如何在连接到输入验证时使用视觉状态管理器。 XAML 文件包含两个 `Label` 元素、一个 `Entry` 和 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 标记附加到第二个 `Label` （名为 `helpLabel`）和 `Button` （名为 `submitButton`）。 有两个互相排斥的状态，分别名为 "有效" 和 "无效"。 请注意，这两个 "ValidationState" 组中的每一个都包含 "有效" 和 "无效" 的 `VisualState` 标记，尽管其中一组在每种情况下都为空。 

如果 `Entry` 不包含有效的电话号码，则当前状态为 "无效"，因此第二个 `Label` 可见，`Button` 处于禁用状态：

[![VSM 验证：无效状态](vsm-images/VsmValidationInvalid.png "VSM 验证-无效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

输入有效的电话号码时，当前状态将变为 "有效"。 第二个 `Entry` 随即消失，此时将启用 `Button`：

[![VSM 验证：有效状态](vsm-images/VsmValidationValid.png "VSM 验证-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

代码隐藏文件负责用于处理 `Entry` 中的 `TextChanged` 事件。 处理程序使用正则表达式来确定输入字符串是否有效。 名为 `GoToState` 的代码隐藏文件中的方法为 `helpLabel` 和 `submitButton` 调用静态 `VisualStateManager.GoToState` 方法：

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

另请注意，从构造函数调用 `GoToState` 方法以初始化状态。 应始终具有当前状态。 但在代码中不存在任何引用视觉状态组的名称，但在 XAML 中将其作为 "ValidationStates" 引用是为了清楚起见。 

请注意，代码隐藏文件必须考虑页面上受这些视觉状态影响的每个对象，并对每个对象调用 `VisualStateManager.GoToState`。 在此示例中，它只是两个对象（`Label` 和 `Button`），但也可能更多。

您可能想知道：代码隐藏文件必须引用页面上受这些视觉状态影响的每个对象，为什么代码隐藏文件无法直接访问对象？ 当然可以。 不过，使用 VSM 的优点是，你可以控制视觉对象如何完全在 XAML 中响应不同状态，这会将所有 UI 设计保存在一个位置。 这可以避免通过直接从代码隐藏访问视觉对象来设置视觉外观。

考虑从 `Entry` 中派生一个类可能会很有吸引力，并且可能会定义一个可设置为外部验证函数的属性。 派生自 `Entry` 的类随后可以调用 `VisualStateManager.GoToState` 方法。 此方案可以正常工作，但仅当 `Entry` 是受不同可视状态影响的唯一对象时才适用。 在此示例中，还会影响 `Label` 和 `Button`。 对于附加到 `Entry` 的 VSM 标记，无法控制页面上的其他对象，也无法将附加到这些其他对象的 VSM 标记从另一个对象引用为可视状态的更改。

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>使用视觉对象状态管理器进行自适应布局

通常，在手机上运行的 Xamarin 应用程序应用程序通常可以在纵向或横向纵横比上查看，并且可以调整桌面上运行的 Xamarin 程序的大小，以采用许多不同的大小和纵横比。 设计良好的应用程序可能会以不同的方式显示这些各种页面或窗口外形因素的内容。 

此方法有时称为_自适应布局_。 因为自适应布局只涉及程序的视觉对象，所以它是可视状态管理器的理想应用。

一个简单的示例是应用程序，它显示了影响应用程序内容的小按钮集合。 在纵向模式下，可能会在页面顶部的水平行中显示这些按钮：

[![VSM 自适应布局：纵向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 自适应布局-纵向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在横向模式下，按钮数组可以移到一边，并显示在列中：

[![VSM 自适应布局：横向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自适应布局-横向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

从上到下，程序正在通用 Windows 平台、Android 和 iOS 上运行。

[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)示例中的 " **VSM 自适应布局**" 页面定义名为 "OrientationStates" 的组，其中包含两个名为 "纵向" 和 "横向" 的可视状态。 （更复杂的方法可能基于几个不同的页面或窗口宽度。） 

VSM 标记出现在 XAML 文件中的四个位置。 名为 `mainStack` 的 `StackLayout` 包含菜单和内容，这是 `Image` 元素。 此 `StackLayout` 在纵向模式下应具有垂直方向，在横向模式下为水平方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

名为 `menuScroll` 的内部 `ScrollView` 和名为 `menuStack` 的 `StackLayout` 实现按钮菜单。 这些布局的方向相对 `mainStack`。 菜单应在纵向模式下水平滚动，在横向模式下垂直。

VSM 标记的第四部分是按钮本身的隐式样式。 此标记设置特定于 portait 和横向方向 `VerticalOptions`、`HorizontalOptions` 和 `Margin` 属性。

代码隐藏文件设置 `menuStack` 的 `BindingContext` 属性以实现 `Button` 命令，同时将处理程序附加到页面的 `SizeChanged` 事件：

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

@No__t_0 处理程序对两个 `StackLayout` 和 `ScrollView` 元素调用 `VisualStateManager.GoToState`，然后遍历 `menuStack` 的子级，以调用 `VisualStateManager.GoToState` 元素的 `Button`。

这似乎是代码隐藏文件可以通过设置 XAML 文件中元素的属性来更直接地处理方向更改，但视觉对象状态管理器是一种结构化方法。 所有视觉对象都保存在 XAML 文件中，在此文件中，它们会更易于检查、维护和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>具有 Xamarin 大学的视觉对象状态管理器

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin 3.0 视觉对象状态管理器视频**

## <a name="related-links"></a>相关链接

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
