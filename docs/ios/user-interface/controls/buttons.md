---
title: Xamarin 中的按钮
description: UIButton 类用于表示 iOS 屏幕中各种不同的按钮样式。 本指南介绍了在 iOS 中使用按钮的不同选项。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2018
ms.openlocfilehash: ce0c4579f13311811106a00390f95a20a0abf979
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768452"
---
# <a name="buttons-in-xamarinios"></a>Xamarin 中的按钮

在 iOS 中， `UIButton`类表示按钮控件。

可以通过编程方式或使用 iOS 设计器的**Properties Pad**修改按钮的属性：

![IOS 设计器的 Properties Pad](buttons-images/properties.png "IOS 设计器的 Properties Pad")

## <a name="creating-a-button-programmatically"></a>以编程方式创建按钮

`UIButton`只能用几行代码创建。

- 实例化一个按钮并指定其类型：

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  按钮的类型由`UIButtonType`指定：

  - `UIButtonType.System`-通用按钮
  - `UIButtonType.DetailDisclosure`-指示详细信息的可用性，通常是有关表中特定项的信息
  - `UIButtonType.InfoDark`-指示配置信息的可用性;深色
  - `UIButtonType.InfoLight`-指示配置信息的可用性;浅色
  - `UIButtonType..AddContact`-表示可添加联系人
  - `UIButtonType.Custom`-可自定义按钮

  有关不同按钮类型的详细信息，请查看：
  
  - 本文档的 "[自定义按钮类型](#custom-button-types)" 部分
  - [按钮类型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)食谱
  - Apple 的[IOS 人体学接口指导原则](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)。

- 定义按钮的大小和位置：

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- 设置按钮的文本。 使用需要文本`UIControlState`和值的方法：`SetTitle`

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  有关设置按钮样式并设置其文本的详细信息，请参阅：

  - 本文档的 "[设置按钮样式](#styling-a-button)" 部分
  - "[设置" 按钮文本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text)食谱。

## <a name="handling-a-button-tap"></a>处理按钮点击

若要响应按钮点击，请提供按钮`TouchUpInside`事件的处理程序：

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside`不是唯一可用的按钮事件。 `UIButton`是的`UIControl`一个子类，它定义[多个不同的事件](xref:UIKit.UIControlEvent)。

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>使用 iOS 设计器指定按钮事件处理程序

使用**Properties Pad**的 "**事件**" 选项卡指定按钮的各种事件的事件处理程序。

对于相应的事件，请键入新事件处理程序的名称，或从列表中选择一个事件处理程序。 执行此操作将会在代码中为按钮的视图控制器创建一个事件处理程序。

![Properties Pad 的 "事件" 选项卡](buttons-images/image1.png "Properties Pad 的 \"事件\" 选项卡")

## <a name="styling-a-button"></a>设置按钮样式

`UIButton`控件可以存在于多个不同状态中，每个状态都`UIControlState`由一个`Normal`值`Disabled`指定： `Highlighted`、、 `Focused`、等。可以通过编程方式或使用 iOS 设计器为每种状态指定唯一样式。

> [!NOTE]
> 若要查看所有`UIControlState`值的完整列表，请查看[`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> 关于.

例如，要为`UIControlState.Normal`以下内容设置标题颜色和阴影颜色：

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

下面的代码将按钮标题设置为和`UIControlState.Normal` `UIControlState.Highlighted`的特性化（风格化）字符串：

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自定义按钮类型

具有`UIButtonType`的的`Custom`按钮没有默认样式。 但是，可以通过设置图像的不同状态来配置按钮的外观：

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

根据用户是否接触按钮，该按钮将呈现为以下图像之一（`UIControlState.Normal` `UIControlState.Highlighted`和`UIControlState.Selected`状态）：

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted")
![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

有关使用自定义按钮的详细信息，请参阅为[按钮食谱使用图像](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button)。
