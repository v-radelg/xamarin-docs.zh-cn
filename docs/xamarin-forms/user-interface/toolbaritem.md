---
title: Xamarin. Forms ToolbarItem
description: ToolbarItem 类是应用程序导航栏中使用的一种特殊类型的按钮。
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: e6ce55f25b130c64b72a5a7e2ec2ec9a00291d14
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976346"
---
# <a name="xamarinforms-toolbaritem"></a>Xamarin. Forms ToolbarItem

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

Xamarin [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)类是可以添加`Page`到对象的`ToolbarItems`集合中的一种特殊类型的按钮。 每`ToolbarItem`个对象都将作为一个按钮显示在应用程序的导航栏中。 一个`ToolbarItem`实例可以有一个图标, 并显示为主菜单项或辅助菜单项。 类继承自[`MenuItem`。](xref:Xamarin.Forms.MenuItem) `ToolbarItem`

以下屏幕截图显示`ToolbarItem` iOS 和 Android 上导航栏中的对象:

!["Android 和 iOS 上的 ToolbarItem 演示屏幕截图"](toolbaritem-images/toolbaritem-device-screenshot.png "Android 和 iOS 上的 ToolbarItem 演示屏幕截图")

`ToolbarItem`类定义以下属性:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)是一个`ToolbarItemOrder`枚举值, 它确定`ToolbarItem`实例是在主菜单还是辅助菜单中显示。
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)`Page`确定对象的`ToolbarItems`集合中项的显示顺序的值。`integer`

类继承`MenuItem`类中通常使用的以下属性: `ToolbarItem`

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是一个`ICommand` , 它允许将用户操作 (如指指单击或单击) 绑定到在 viewmodel 上定义的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)指定应传递到的`Command`参数的。 `object`
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)确定对象上显示图标的值。`ImageSource` `ToolbarItem`
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string`确定对象`ToolbarItem`上显示文本的。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的`ToolbarItem` , 因此, 实例可以是数据绑定的目标。

> [!NOTE]
> 从[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)对象创建工具栏的另一种方法是[`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)将附加属性设置为包含多个视图的布局类。 有关详细信息, 请参阅[在导航栏中显示视图](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar)。

## <a name="create-a-toolbaritem"></a>创建 ToolbarItem

`ToolbarItem`对象可以在 XAML 中进行实例化。 `Text` 和`IconImageSource`属性可以设置为确定按钮在导航栏中的显示方式。 下面的示例演示如何`ToolbarItem`使用一些公共属性集来实例化:

```xaml
<ToolbarItem Text="Example Item"
             IconImageSource="example_icon.png"
             Order="Primary"
             Priority="0" />
```

此示例将生成一个`ToolbarItem`对象, 该对象具有文本、图标并首先出现在主导航栏区域中。 也可以在代码中创建, 并将其添加`ToolbarItems`到集合中: `ToolbarItem`

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

由表示`string`的文件 ( `IconImageSource`作为属性提供) 必须存在于每个平台项目中。

> [!NOTE]
> 每个平台上的图像资产的处理方式不同。 `ImageSource`可以来自源, 包括本地文件或嵌入的资源、URI 或流。 有关设置 xamarin 中的`IconImageSource`属性和图像的详细信息, 请参阅[xamarin 中的图像](~/xamarin-forms/user-interface/images.md)。

## <a name="define-button-behavior"></a>定义按钮行为

`ToolbarItem`类`Clicked`从类`MenuItem`继承事件。 可以将事件处理程序附加到`Clicked`事件, 以响应在 XAML 中按`ToolbarItem`下或单击实例:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

事件处理程序也可以在代码中附加:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

前面的示例引用`OnItemClicked`了事件处理程序。 下面的代码演示实现示例:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`对象还可以使用`Command`和`CommandParameter`属性来响应用户输入, 而无需使用事件处理程序。 有关`ICommand` interface 和 MVVM 数据绑定的详细信息, 请参阅[Xamarin。窗体 MenuItem MVVM 行为](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm)。

## <a name="primary-and-secondary-menus"></a>主菜单和辅助菜单

枚举具有`Default` 、`Primary`和值`Secondary` 。 `ToolbarItemOrder`

当属性设置为`Primary`时, 该`ToolbarItem`对象将显示在所有平台上的主导航栏中。 `Order` `ToolbarItem`对象优先于页面标题, 这将被截断以为项目腾出空间。 以下屏幕截图显示`ToolbarItem` iOS 和 Android 上主菜单中的对象:

!["ToolbarItem 主菜单屏幕快照 Android 和 iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Android 和 iOS 上的 ToolbarItem 主菜单屏幕截图")

当属性设置为`Secondary`时, 行为因平台而异。 `Order` 在 UWP 和 Android 上, `Secondary` "项" 菜单显示为三个点, 可以点击或单击它们以显示垂直列表中的项。 在 iOS 上, `Secondary` "项目" 菜单显示在导航栏下面作为水平列表。 以下屏幕截图显示了 iOS 和 Android 上的辅助菜单:

!["ToolbarItem 辅助菜单屏幕快照 Android 和 iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Android 和 iOS 上的 ToolbarItem 辅助菜单屏幕截图")

> [!WARNING]
> `Order`属性设置为`ToolbarItem` 的对象中的图标行为在平台`Secondary`上不一致。 避免在辅助`IconImageSource`菜单中显示的项上设置属性。

## <a name="related-links"></a>相关链接

* [ToolbarItem 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Xamarin 中的图像](~/xamarin-forms/user-interface/images.md)
* [Xamarin。窗体 MenuItem](~/xamarin-forms/user-interface/menuitem.md)