---
title: Xamarin.Forms Shell 简介
description: Xamarin.Forms Shell 提供了大多数应用程序所需的基本功能，包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f6662f20485c6671edcb5a1654569cdd8498607e
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005206"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能，包括：

- 用于描述应用程序的视觉层次结构的单个位置。
- 常见的导航用户体验。
- 允许导航到应用程序中的任何页面的基于 URI 的导航方案。
- 集成的搜索处理程序。

此外，Shell 应用程序还因提高了呈现速度和减少了内存消耗而受益。

> [!IMPORTANT]
> Xamarin.Forms Shell 仅适用于 iOS 和 Android。 现有的 iOS 和 Android 应用程序可以采用 Shell，立即获益于导航、性能和扩展性方面的改进。

## <a name="shell-navigation-experience"></a>Shell 导航体验

Shell 提供了基于浮出控件和选项卡的固定导航体验。 Shell 应用程序中的最顶级导航是一个浮出控件：

[![iOS 和 Android 上的 Shell 浮出控件的屏幕截图](introduction-images/flyout.png "Shell 浮出控件")](introduction-images/flyout-large.png#lightbox "Shell 浮出控件")

选择浮出控件项导致选中并显示表示项的底部选项卡：

[![iOS 和 Android 上的 Shell 底部选项卡的屏幕截图](introduction-images/monkeys.png "Shell 底部选项卡")](introduction-images/monkeys-large.png#lightbox "Shell 底部选项卡")

> [!NOTE]
> 浮出控件未打开时，系统将底部选项卡栏视作应用程序中的顶级导航。

每个选项卡都会显示 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 但是，如果底部选项卡包含多个页面，则页面可通过顶部选项卡栏进行导航：

[![iOS 和 Android 上的 Shell 顶部选项卡的屏幕截图](introduction-images/cats.png "Shell 顶部选项卡")](introduction-images/cats-large.png#lightbox "Shell 顶部选项卡")

在每个选项卡中，可以导航到其他 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

[![iOS 和 Android 上的 Shell 页面导航的屏幕截图](introduction-images/cat-details.png "Shell 应用导航")](introduction-images/cat-details-large.png#lightbox "Shell 应用导航")

## <a name="subclassing-the-shell-class"></a>创建 Shell 类的子类

子类 `Shell` 对象描述 Shell 应用程序的视觉层次结构，并且由以下三个主要的层次结构对象组成：

- `FlyoutItem`，表示浮出控件中的一个或多个项。 每个 `FlyoutItem` 对象都是 `Shell` 对象的子对象。
- `Tab`，表示分组内容，可通过底部选项卡导航。 每个 `Tab` 对象都是 `FlyoutItem` 对象的子对象。
- `ShellContent`，表示应用程序中的 `ContentPage` 对象。 每个 `ShellContent` 对象都是 `Tab` 对象的子对象。 当 `Tab` 中存在多个 `ShellContent` 对象时，这些对象将可通过顶部选项卡导航。

上述对象均不表示任何用户界面，而表示应用程序视觉层次结构的组织。 Shell 使用这些对象，生成内容的导航用户界面。

> [!NOTE]
> `FlyoutItem` 类是 `ShellItem` 类的别名，而 `Tab` 类是 `ShellSection` 类的别名。 这些别名旨在使 API 更便于使用。

以下 XAML 演示子类 `Shell` 对象的一个示例：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

运行时，此 XAML 显示 `CatsPage`，因为它是子类 `Shell` 对象中声明的第一个内容项：

[![iOS 和 Android 上的 Shell 应用的屏幕截图](introduction-images/cats.png "Shell 应用")](introduction-images/cats-large.png#lightbox "Shell 应用")

按汉堡图标或从左侧轻扫会显示以下浮出控件：

[![iOS 和 Android 上的 Shell 浮出控件的屏幕截图](introduction-images/flyout-reduced.png "Shell 浮出控件")](introduction-images/flyout-reduced-large.png#lightbox "Shell 浮出控件")

> [!IMPORTANT]
> 在 Shell 应用程序中，作为 `ShellContent` 对象子对象的每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在应用程序启动期间创建的。 使用该方法添加其他 `ShellContent` 对象将导致在应用程序启动期间创建额外页面，这可能导致糟糕的启动体验。 不过，Shell 还能够根据需要创建页面，以响应导航。 有关详细信息，请参阅 [Xamarin.Forms Shell 选项卡](tabs.md)指南中的[高效页面加载](tabs.md#efficient-page-loading)。

## <a name="bootstrapping-a-shell-application"></a>启动 Shell 应用程序

将 `App` 类的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 属性设置为子类 `Shell` 对象，即可启动 Shell 应用程序：

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

在此示例中，`AppShell` 类是派生自 `Shell` 类的 XAML 文件，该类描述应用程序的视觉层次结构。

## <a name="shell-appearance"></a>Shell 外观

`Shell` 类定义以下控制 Shell 应用程序外观的属性：

- `BackgroundColor` 是 `Color` 类型的附加属性，用于定义 Shell chrome 的背景色。 不会在 Shell 内容后面填充颜色。
- `DisabledColor` 是 `Color` 类型的附加属性，用于定义要对处于禁用状态的文本和图标进行阴影处理的颜色。
- `ForegroundColor` 是 `Color` 类型的附加属性，用于定义要对文本和图标进行阴影处理的颜色。
- `TitleColor` 是 `Color` 类型的附加属性，用于定义当前页面标题的颜色。
- `UnselectedColor` 是 `Color` 类型的附加属性，用于定义 Shell chrome 中未选文本和图标的颜色。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

此外，还可以使用级联样式表 (CSS) 设置这些属性。 有关详细信息，请参阅 [Xamarin.Forms Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="shell-content-layout"></a>Shell 内容布局

`Shell` 类定义以下影响 Shell 应用程序内容布局的属性：

- `NavBarIsVisible` 是 `boolean` 类型的附加属性，用于定义导航栏在显示页面时是否应可见。 应在页面上设置此属性，并且其默认值为 `true`。
- `TabBarIsVisible` 是 `bool` 类型的附加属性，用于定义选项卡栏在显示页面时是否应可见。 应在页面上设置此属性，并且其默认值为 `true`。
- `TitleView` 是 `View` 类型的附加属性，用于定义页面的 `TitleView`。 应在页面上设置此属性。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着这些属性可以作为数据绑定的目标。

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Xamarin.Forms Shell 特定属性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
