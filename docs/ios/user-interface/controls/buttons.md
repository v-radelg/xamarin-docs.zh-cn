---
title: 在 Xamarin.iOS 中的按钮
description: UIButton 类用于表示在 iOS 屏幕的按钮的各种不同的样式。 本指南介绍使用按钮在 iOS 中的不同选项。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2018
ms.openlocfilehash: a98ddc2622682f2c105a6aff32e94bd92a5b11f2
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233245"
---
# <a name="buttons-in-xamarinios"></a>在 Xamarin.iOS 中的按钮

在 iOS 中，`UIButton`类表示一个按钮控件。

可以修改按钮的属性，以编程方式或使用**Properties Pad**的 iOS 设计器：

![IOS 设计器的属性面板](buttons-images/properties.png "iOS 设计器的 Properties Pad")

## <a name="creating-a-button-programmatically"></a>以编程方式创建按钮

一个`UIButton`可以创建仅几行代码。

- 实例化一个按钮，并指定其类型：

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  按钮的类型由指定`UIButtonType`:

  - `UIButtonType.System` -常规用途的按钮
  - `UIButtonType.DetailDisclosure` -表示详细信息，通常是关于表中特定项的可用性
  - `UIButtonType.InfoDark` -指示; 的配置信息的可用性深色的
  - `UIButtonType.InfoLight` -指示; 的配置信息的可用性浅色
  - `UIButtonType..AddContact` -指示可以添加联系人
  - `UIButtonType.Custom` 自定义按钮

  有关不同的按钮类型的详细信息，看看：
  
  - [自定义按钮类型](#custom-button-types)本文档的部分
  - [按钮类型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)方案
  - Apple [iOS 人机接口指南 》](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)。

- 定义按钮的大小和位置：

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- 设置按钮的文本。 使用`SetTitle`方法，需要对文本和一个`UIControlState`值：

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  有关设置按钮的样式和设置其文本的详细信息，请参阅：

  - [样式按钮](#styling-a-button)本文档的部分
  - [设置按钮文本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text)方案。

## <a name="handling-a-button-tap"></a>处理点击按钮

若要响应按钮点击，提供处理程序，为按钮的`TouchUpInside`事件：

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` 不是唯一可用的按钮事件。 `UIButton` 是的一个子类`UIControl`，用于定义[多个不同事件](xref:UIKit.UIControlEvent)。

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>使用 iOS 设计器指定按钮事件处理程序

使用**事件**选项卡**Properties Pad**若要指定的按钮的各种事件在事件处理程序。

对于相应的事件中，键入新的事件处理程序的名称，或从列表中选择一个。 执行此操作将为按钮的视图控制器在代码中创建一个事件处理程序。

![事件选项卡的属性面板](buttons-images/image1.png "事件选项卡的属性面板")

## <a name="styling-a-button"></a>设置按钮样式

`UIButton` 每个控件可以存在于多个不同的状态，指定由`UIControlState`值 – `Normal`， `Disabled`， `Focused`， `Highlighted`，等等。每种状态，可以提供以编程方式或使用 iOS 设计器指定一个唯一样式。

> [!NOTE]
> 有关完整列表的所有`UIControlState`值，请参阅 [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> 文档。

例如，若要设置标题颜色和阴影颜色`UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

下面的代码设置为特性化 （风格） 字符串的按钮标题`UIControlState.Normal`和`UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自定义按钮类型

按钮`UIButtonType`的`Custom`不有任何默认样式。 但是，就可以通过设置不同状态下的映像来配置该按钮的外观：

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

具体取决于是否用户接触到按钮，或不，它将呈现为以下映像之一 (`UIControlState.Normal`，`UIControlState.Highlighted`和`UIControlState.Selected`指出，分别):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted")
![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

有关如何使用自定义按钮的详细信息，请参阅[使用图像作为按钮](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button)方案。

