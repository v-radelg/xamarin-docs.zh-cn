---
title: Xamarin.Forms 视觉状态管理器
description: 使用可视状态管理器对 XAML 元素按从代码中设置的视觉状态进行更改。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656060"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin.Forms 视觉状态管理器

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_使用可视状态管理器对 XAML 元素按从代码中设置的视觉状态进行更改。_

视觉状态管理器 (VSM) 是 Xamarin.Forms 3.0 中的新增功能。 VSM 提供结构化的方式来从代码对用户界面进行可视更改。 在大多数情况下，应用程序的用户界面中 XAML，定义和此 XAML 包含描述可视状态管理器如何影响用户界面的视觉对象的标记。

VSM 介绍的概念_可视状态_。 如 Xamarin.Forms 视图`Button`可以有根据其基础状态的多个不同的视觉外观&mdash;是处于禁用状态，或按下，还是具有输入焦点。 这些是按钮的状态。

视觉状态中收集_可视状态组_。 可视状态组中的所有可视状态互相排斥。 可视状态和可视状态组由简单的文本字符串标识。

Xamarin.Forms 视觉状态管理器定义一个可视状态组名"CommonStates"为具有三种可视状态：

- "Normal"
- "已禁用"
- "已设定焦点"

此可视状态组支持的所有类的派生[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，这是类的基类[ `View` ](xref:Xamarin.Forms.View)并[ `Page` ](xref:Xamarin.Forms.Page)。 

此外可以定义自己的可视状态组和视觉状态，为这篇文章将演示。

> [!NOTE]
> Xamarin.Forms 开发人员熟悉[触发器](~/xamarin-forms/app-fundamentals/triggers.md)可识别触发器还可以对基于视图的属性或事件触发中的更改的用户界面中的视觉对象进行更改。 但是，使用触发器来处理这些更改的各种组合可能会变得非常令人困惑。 从历史上看，基于 Windows XAML 的环境，若要缓解的可视状态的组合导致混乱中引入了视觉状态管理器。 利用 VSM，可视状态组中的视觉状态始终是互相排斥的。 在任何时候，每个组中的只有一个状态为当前状态。

## <a name="the-common-states"></a>常见的状态

视觉状态管理器，可在你可以更改视图的可视外观，如果视图是正常的或已禁用，或者具有输入的焦点的 XAML 文件中包含部分。 这些参数称为_常用状态_。

例如，假设您有`Entry`在页中，视图和所需的可视外观`Entry`按以下方式更改：

- `Entry`应有粉红色背景时`Entry`被禁用。
- `Entry`通常应具有酸橙色背景。
- `Entry`它具有输入焦点时应扩展到正常高度的两倍。

可以将 VSM 标记附加到一个单独的视图，或如果它适用于多个视图可以定义其样式中。 接下来的两部分介绍了这些方法。

### <a name="vsm-markup-on-a-view"></a>在视图上的 VSM 标记

若要附加到 VSM 标记`Entry`视图中，第一次单独的`Entry`到开始和结束标记：

```xaml
<Entry FontSize="18">

</Entry>
```

因为将使用一种状态，提供显式的字号`FontSize`属性中的文本大小加倍`Entry`。

接下来，插入`VisualStateManager.VisualStateGroups`标记这些标记之间：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) 是定义的一个附加可绑定属性[ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager)类。 (可绑定的附加属性的详细信息，请参阅文章[附加属性](~/xamarin-forms/xaml/attached-properties.md)。)这是如何`VisualStateGroups`属性将附加到`Entry`对象。

`VisualStateGroups`属性属于类型[ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList)，这是一系列[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)对象。 内`VisualStateManager.VisualStateGroups`标记，插入一对`VisualStateGroup`标记为要包含的可视状态的每个组：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

请注意，`VisualStateGroup`标记具有`x:Name`属性，指示组的名称。 `VisualStateGroup`类定义`Name`可以改为使用的属性：

```xaml
<VisualStateGroup Name="CommonStates">
```

你可以使用`x:Name`或`Name`但不是能同时在同一个元素。

`VisualStateGroup`类定义一个名为属性[ `States` ](xref:Xamarin.Forms.VisualStateGroup.States)，这是一系列[ `VisualState` ](xref:Xamarin.Forms.VisualState)对象。 `States` 是_内容属性_的`VisualStateGroups`因此你可以将包含`VisualState`标记之间直接`VisualStateGroup`标记。 (内容属性将在本文中讨论[基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)。)

下一步是要包含在该组中的一对每个可视状态的标记。 此外可以识别使用`x:Name`或`Name`:

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

`VisualState` 定义一个名为属性[ `Setters` ](xref:Xamarin.Forms.VisualState.Setters)，这是一系列[ `Setter` ](xref:Xamarin.Forms.Setter)对象。 它们是相同`Setter`中使用对象[ `Style` ](xref:Xamarin.Forms.Style)对象。

`Setters` 是_不_的内容属性的`VisualState`，因此必须包括为属性元素标记`Setters`属性：

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

你现在可以将插入一个或多个`Setter`对象之间的每个对`Setters`标记。 这些是`Setter`对象以定义前面所述的可视状态：

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

每个`Setter`标记指示特定属性的值时该状态是最新。 引用的任何属性`Setter`对象必须由可绑定的属性。

标记类似于以下是的基础 **视图上的 VSM** 页面 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例程序。 此页包含三个`Entry`视图中，但是仅第二个具有 VSM 标记附加到它：

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

请注意，第二个`Entry`还有`DataTrigger`作为的一部分其`Trigger`集合。 这将导致`Entry`之前的内容键入到第三个要禁用`Entry`。 以下是在启动 iOS、 Android 和通用 Windows 平台 (UWP) 上运行时的页：

[![视图上的 VSM:已]禁用(vsm-images/VsmOnViewDisabled.png "视图唤醒-已禁用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

当前的可视状态为"Disabled"，因此第二个后台`Entry`为粉红色 iOS 和 Android 屏幕上。 UWP 实现`Entry`不允许设置背景颜色时`Entry`被禁用。 

到第三个输入一些文本时`Entry`，第二个`Entry`切换到"正常"状态，并在后台现酸橙色：

[![视图上的 VSM:](vsm-images/VsmOnViewNormal.png "查看时的普通 VSM-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

当您触摸第二个`Entry`，获取输入的焦点。 它将切换到"已设定焦点"状态，并扩展到高度的两倍：

[![视图上的 VSM:专注]于(vsm-images/VsmOnViewFocused.png "视图的")注重](vsm-images/VsmOnViewFocused-Large.png#lightbox)

请注意，`Entry`获得输入的焦点时不会保留酸橙色背景。 可视状态之间切换视觉状态管理器，以前的状态设置的属性是取消设置。 请注意，视觉状态互相排斥。 "正常"状态并不意味着仅`Entry`已启用。 这意味着`Entry`已启用且不具有输入的焦点。 

如果你想`Entry`若要为浅背景"焦点"状态中，添加另一个`Setter`该可视状态：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

为了使这些`Setter`对象才能正常工作，`VisualStateGroup`必须包含`VisualState`该组中的所有状态的对象。 如果没有任何可视状态`Setter`对象，仍然为空标记包括：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>在样式中的视觉状态管理器标记

它通常是共享相同的视觉状态管理器标记在两个或多个视图之间所需的。 在这种情况下，你将想要将标记放入`Style`定义。

下面是现有隐式`Style`有关`Entry`中的元素**VSM 在视图**页：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

添加`Setter`标记`VisualStateManager.VisualStateGroups`附加可绑定属性：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

内容属性`Setter`是`Value`，因此值`Value`可以直接在这些标记中指定属性。 属性属于类型`VisualStateGroupList`:

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

这些标记中可以包含一个或多`VisualStateGroup`对象：

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

VSM 标记的其余部分是与之前相同。

下面是**VSM 在样式中的**页面，其中显示完整的 VSM 标记：

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

现在所有`Entry`此页上的视图响应其可视状态的相同方法。 "焦点"状态现在包括第二个另请注意`Setter`，它给出每个`Entry`酸橙色还后台时，它具有输入焦点：

[![在样式中的 VSM](vsm-images/VsmInStyle.png "VSM 在样式中")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定义你自己的可视状态

每个类都派生自`VisualElement`支持三个常用状态"正常"、"中心"和"已禁用"。 在内部， [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)类将检测时它正在成为已启用或禁用，或已设定焦点或失去焦点，并调用静态[ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String))方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

这是您会发现中的唯一视觉状态管理器代码`VisualElement`类。 因为`GoToState`为基于每个类都派生自每个对象调用`VisualElement`，可以使用可视状态管理器以及任何`VisualElement`对象的这些更改进行响应。

有趣的是，可视状态组"CommonStates"的名称未显式引用中`VisualElement`。 组名称不是可视状态管理器的 API 的一部分。 中到目前为止所示的两个示例程序之一，你可以为任何其他内容，从"CommonStates"组的名称和程序仍将起作用。 组名称是仅仅是该组中的状态的一般说明。 它被隐式理解, 任何组中的可视状态都是互斥的:一种状态, 并且每次只有一个状态为最新状态。

如果你想要实现您自己的视觉状态，你将需要调用`VisualStateManager.GoToState`从代码。 通常，你将使此调用的页类代码隐藏文件中。

          **VSM 验证** 页面 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 示例演示如何使用输入验证与可视状态管理器。 XAML 文件包含两个`Label`元素， `Entry`，和`Button`:

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

VSM 标记附加到第二个`Label`(名为`helpLabel`) 和`Button`(名为`submitButton`)。 有两个互斥的状态，名为"Valid"和"无效"。 请注意，两个"ValidationState"组的每个包含`VisualState`标记为"Valid"和"无效"，虽然其中一种是在每种情况下为空。 

如果`Entry`不包含有效的电话号码，则当前状态为"无效"，因此第二个`Label`可见和`Button`已禁用：

[![VSM 验证:无效的]状态(vsm-images/VsmValidationInvalid.png "VSM 验证-无效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

输入有效的电话号码，然后当前状态将变为"Valid"。 第二个`Entry`消失和`Button`现已启用：

[![VSM 验证:有效的]状态(vsm-images/VsmValidationValid.png "VSM 验证-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

代码隐藏文件负责处理`TextChanged`从事件`Entry`。 该处理程序使用的正则表达式来确定输入的字符串是否有效。 名为的代码隐藏文件中的方法`GoToState`调用静态`VisualStateManager.GoToState`两个方法`helpLabel`和`submitButton`:

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

另请注意`GoToState`从初始化状态的构造函数中调用方法。 始终应为当前状态。 但不是在代码中有任何引用的可视状态组中，名称虽然它在 XAML 中的引用作为"ValidationStates"为了清晰起见。 

请注意，代码隐藏文件必须采取的每个对象的帐户受影响的页面上，通过这些视觉状态，并调用`VisualStateManager.GoToState`为每个对象。 在此示例中，它是只有两个对象 (`Label`和`Button`)，但它可能是几个更多。

您可能想知道:如果代码隐藏文件必须引用页面上受这些视觉状态影响的每个对象, 则为什么代码隐藏文件无法直接访问对象呢？ 当然可以。 但是，使用 VSM 的优点是可以控制如何将视觉元素，对完全在 XAML，将所有 UI 设计保留在一个位置中的不同状态做出反应。 这直接从代码隐藏中访问的可视元素，因此可避免设置可视外观。

它很可能，请考虑派生的类从`Entry`和可能定义一个属性，可以设置为外部验证函数。 派生的类`Entry`然后可以调用`VisualStateManager.GoToState`方法。 此方案将能正常运行，但仅当`Entry`已受不同的可视状态的唯一对象。 在此示例中，`Label`和一个`Button`也会受到影响。 没有任何方法为 VSM 标记附加到`Entry`来控制在页面中，没有方法上的其他对象的 VSM 标记附加到这些其他对象，若要从另一个对象引用中的可视状态的更改。

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>使用可视状态管理器进行自适应的布局

可以调整大小在手机上运行的应用程序通常可以查看在纵向或横向纵横比，并且在桌面上运行的 Xamarin.Forms 程序 Xamarin.Forms 采用许多不同的大小和纵横比。 设计良好的应用程序可能会显示不同的方式针对这些不同的页或窗口外观造型其内容。 

此方法有时称为_自适应布局_。 由于自适应布局仅涉及程序的视觉对象，它是理想的可视状态管理器应用程序。

一个简单的示例是显示小的会影响应用程序的内容的按钮集合的应用程序。 在纵向模式下，可能会在页面顶部水平行中显示这些按钮：

[![VSM 自适应布局:纵向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 自适应布局-纵向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在横向模式下，可能会移动到一个一侧，和在列中显示的按钮数组：

[![VSM 自适应布局:横向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自适应布局-横向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

从上到下，通用 Windows 平台、 Android 和 iOS 上运行该程序。

**VSM 自适应布局**页面[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)示例定义名为"纵向"和"横向"的两个可视状态与名为"OrientationStates"的组。 （更复杂的方法可能会基于多个不同的页或窗口宽度。） 

VSM 标记出现在 XAML 文件中的四个位置。 `StackLayout`名为`mainStack`包含的菜单和内容，这是`Image`元素。 这`StackLayout`应具有垂直方向为纵向模式和横向模式下表中的水平方向：

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

内部`ScrollView`名为`menuScroll`并`StackLayout`名为`menuStack`实现按钮的菜单。 这些布局的方向是相反的`mainStack`。 在纵向模式下水平和垂直在横向模式下，应为菜单。

中的按钮本身的隐式样式是 VSM 标记的第四个部分。 此标记将设置`VerticalOptions`， `HorizontalOptions`，和`Margin`特定于 portait 和横向方向的属性。

代码隐藏文件集`BindingContext`的属性`menuStack`实现`Button`命令，并还会将附加到一个处理程序`SizeChanged`页面的事件：

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

`SizeChanged`处理程序调用`VisualStateManager.GoToState`的两个`StackLayout`并`ScrollView`元素，然后循环访问的子级`menuStack`调用`VisualStateManager.GoToState`为`Button`元素。

这可能看起来像代码隐藏文件可以处理更直接地通过在 XAML 文件中，设置元素的属性的方向更改，但视觉状态管理器肯定是一种更加结构化的方法。 所有视觉对象将保留在 XAML 文件，其中它们变得更轻松地检查，维护和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>使用 Xamarin.University 视觉状态管理器

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin 3.0 视觉对象状态管理器视频**

## <a name="related-links"></a>相关链接

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
