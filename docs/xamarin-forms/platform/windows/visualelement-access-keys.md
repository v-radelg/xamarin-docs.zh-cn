---
title: 在 Windows 上的 VisualElement 访问密钥
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Windows 特定于平台的 VisualElement 为指定的访问密钥。
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c480f398c37ce43b634e0ec1c955b965466757f1
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926850"
---
# <a name="visualelement-access-keys-on-windows"></a>在 Windows 上的 VisualElement 访问密钥

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

访问键都是通过为用户可以快速导航并进行交互以直观的方式提供通过触摸键盘而不是通过应用程序的可见 UI 改进的可用性和可访问性的应用通用 Windows 平台 (UWP) 的键盘快捷方式或鼠标。 它们是 Alt 键和一个或多个字母数字，通常按下键按顺序的组合。 使用单个字母数字字符的访问密钥会自动支持键盘快捷方式。

访问键提示浮动徽章控件，其中包含访问密钥旁边显示。 每个访问键提示包含激活关联的控件的字母数字键。 当用户按下 Alt 键时，显示访问键提示。

此平台特定于 UWP 的用来指定的访问密钥[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 设置使用在 XAML [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty)为字母数字值，并根据需要设置附加属性[ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty)附加属性设置为值为[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)枚举[ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty)附加属性设置为`double`，并[ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty)附加属性设置为`double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

`VisualElement.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，用于设置的访问密钥值`VisualElement`。 [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement))方法中，（可选） 指定的位置，用于访问键提示，显示与[ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement)枚举提供以下可能值：

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – 指示访问键提示放置将由操作系统。
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) – 指示访问键提示将出现上面的上边缘`VisualElement`。
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) – 指示访问键提示将显示下面的下边缘`VisualElement`。
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) – 指示访问键提示将显示右侧的右边缘的`VisualElement`。
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – 指示访问键提示将显示的左边缘的左侧`VisualElement`。
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) – 指示，访问键提示将显示为叠加的中心`VisualElement`。

> [!NOTE]
> 通常情况下， [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto)键提示放置已足够，其中包括对自适应用户界面的支持。

[ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))并[ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double))方法可用于访问键提示位置的更精细的控制。 参数`SetAccessKeyHorizontalOffset`方法指示如何得移动访问键提示向左或向右、 和的参数`SetAccessKeyVerticalOffset`方法指示如何得来向上或向下移动访问键提示。

>[!NOTE]
> 访问密钥放置设置时，不能设置访问键提示的偏移量`Auto`。

此外， [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))， [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))，并[ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))可以使用方法若要检索所需的访问密钥值和它的位置。

结果是访问键提示，可以显示任何旁边[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)实例定义的访问密钥，通过按 Alt 键：

![VisualElement 访问密钥特定于平台的](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement 访问密钥特定于平台的")

当用户激活通过按 Alt 键，然后按访问访问密钥，密钥的默认操作为`VisualElement`将执行。 例如，当用户激活的访问密钥上[ `Switch` ](xref:Xamarin.Forms.Switch)，则`Switch`处于切换状态。 当用户在激活的访问密钥[ `Entry` ](xref:Xamarin.Forms.Entry)，则`Entry`获得焦点。 当用户在激活的访问密钥[ `Button` ](xref:Xamarin.Forms.Button)，事件处理程序[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)执行事件。

有关访问密钥的详细信息，请参阅[访问密钥](/windows/uwp/design/input/access-keys#key-tip-positioning)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
