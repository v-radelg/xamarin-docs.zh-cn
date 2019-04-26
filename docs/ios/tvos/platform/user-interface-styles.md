---
title: tvOS 在 Xamarin 中的用户界面样式
description: 这篇文章介绍光线和深色 UI 主题，Apple 已添加到 tvOS 10 以及如何在 Xamarin.tvOS 应用中实现它们。
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270830"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>tvOS 在 Xamarin 中的用户界面样式

_这篇文章介绍光线和深色 UI 主题，Apple 已添加到 tvOS 10 以及如何在 Xamarin.tvOS 应用中实现它们。_

tvOS 10 现在支持深色和浅色用户界面主题，所有内置的 UIKit 控件将自动适应，基于用户的首选项。 此外，开发人员可以手动调整基于用户选定的主题的 UI 元素，并且可重写给定的主题。

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>有关新的用户界面样式

如上面所述，tvOS 10 现在支持深色和浅色用户界面主题，所有内置的 UIKit 控件将自动适应，基于用户的首选项。

用户可以通过转到切换此主题**设置** > **常规** > **外观**之间进行切换和**光**并**深色**:

[![](user-interface-styles-images/theme01.png "设置应用程序")](user-interface-styles-images/theme01.png#lightbox)

当**深色**选择主题中的所有用户界面元素将切换到在深色背景上浅色文本：

[![](user-interface-styles-images/theme02.png "深色主题")](user-interface-styles-images/theme02.png#lightbox)

用户可以选择随时切换主题，并因此基于 Apple TV 所在的位置的当前活动或一天的时间可能会执行。

Light UI 主题是默认主题和任何现有的 tvOS 应用仍将使用浅色主题，而不考虑用户的首选项，除非为 tvOS 10，以充分利用深色主题进行修改。 TvOS 10 应用还具有重写当前的主题并始终使用 Light 或深色主题的某些或全部的用户界面的功能。

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>采用浅色和深色主题

若要支持此功能，Apple 已添加到一个新的 API`UITraitCollection`类和 tvOS 应用必须参加以支持深色外观 (通过中的设置其`Info.plist`文件)。

若要选择加入到浅色和深色主题支持，请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 选择**源**视图 （在编辑器的底部）。
3. 添加新的密钥，调用它`UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "UIUserInterfaceStyle 密钥")](user-interface-styles-images/theme03.png#lightbox)
4. 将类型设置为保留`String`和输入的值为`Automatic`: 

    [![](user-interface-styles-images/theme04.png "输入自动")](user-interface-styles-images/theme04.png#lightbox)
5. 保存对文件所做的更改。

有三个可能值为`UIUserInterfaceStyle`密钥：

- **光**-强制 tvOS 应用的 UI，以始终使用浅色主题。
- **深色**-强制 tvOS 应用的 UI，以始终使用深色主题。
- **自动**-浅色和深色主题之间切换根据用户的首选项中设置。 这是首选的设置。

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>UIKit 主题支持

如果 tvOS 应用使用标准的内置`UIView`基于控件，它们将自动响应而无需任何开发人员干预的 UI 主题。

此外，`UILabel`和`UITextView`会自动将更改其颜色基于选择的 UI 主题：

- 文本将显示为黑色浅色主题中。
- 该文本则为白色深色主题中。

如果开发人员更改文本颜色手动 （无论在情节提要或代码中），它们将负责处理基于 UI 的主题颜色更改。

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>新的模糊效果

TvOS 10 应用中支持浅色和深色主题，Apple 已添加两个新的模糊效果。 这些新效果将自动调整模糊基于用户选择，如下所示的 UI 主题：

- `UIBlurEffectStyleRegular` -使用光模糊浅色主题和深色模糊深色主题中。
- `UIBlurEffectStyleProminent` -使用浅色主题中的 extra-light 模糊和深色主题中的超深色模糊。

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>使用特征集合

新`UserInterfaceStyle`的属性`UITraitCollection`类可以用来获取当前所选的用户界面主题，以及将`UIUserInterfaceStyle`枚举的以下值之一：

- **光**-Light UI 主题处于选中状态。
- **深色**-深色 UI 主题处于选中状态。
- **未指定**-查看尚未显示屏幕，因此当前的 UI 主题是未知。

此外，特征集合 tvOS 10 中具有以下功能：
 
- 可以根据自定义外观代理`UserInterfaceStyle`的给定`UITraitCollection`更改等映像或项基于主题的颜色。
- TvOS 应用可以处理特征集合更改通过重写`TraitCollectionDidChange`方法`UIView`或`UIViewController`类。

> [!IMPORTANT]
> TvOS 10 Xamarin.tvOS 早期预览版不完全支持`UIUserInterfaceStyle`为`UITraitCollection`尚未。 将在未来的版本中添加完整的支持。




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>自定义基于主题的外观

对于支持外观代理的用户界面元素，其外观可以调整根据其特性集合的用户界面主题。 因此，对于给定的 UI 元素，开发人员可以指定一种颜色，浅色主题和深色主题的另一种颜色。

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> 遗憾的是，适用于 tvOS 10 Xamarin.tvOS 预览版不完全支持`UIUserInterfaceStyle`为`UITraitCollection`，因此，此类型的自定义尚不可用。 将在未来的版本中添加完整的支持。

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>直接响应主题更改

在开发人员需要对 UI 元素的外观进行更深入地控制基于所选的用户界面主题，它们可以重写`TraitCollectionDidChange`方法`UIView`或`UIViewController`类。

例如：

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>重写特性集合

根据 tvOS 应用的设计，可能有开发人员需要重写给定的用户界面元素的特性集合，并将其始终使用特定的 UI 主题的时间。

这可以使用`SetOverrideTraitCollection`方法`UIViewController`类。 例如：

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

有关详细信息，请参阅[特征](~/ios/user-interface/storyboards/unified-storyboards.md)并[重写特征](~/ios/user-interface/storyboards/unified-storyboards.md)的部分我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)文档。

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>特征集合和情节提要

TvOS 10 中可以设置应用的情节提要来响应特征集合和很多 UI 元素可以意识到浅色和深色主题。 适用于 tvOS 10 当前 Xamarin.tvOS 早期预览版尚不支持此功能在接口设计器中，因此情节提要需要 Xcode 的 Interface Builder 作为一种解决方法中进行编辑。

若要启用特征集合支持，请执行以下操作：

1. 右键单击情节提要文件**解决方案资源管理器**，然后选择**打开** > **Xcode Interface Builder**: 

    [![](user-interface-styles-images/theme05.png "使用 Xcode 接口生成器打开")](user-interface-styles-images/theme05.png#lightbox) 
2. 若要启用特征集合支持，请切换到**文件检查器**，并检查**使用特征变体**中的属性**界面生成器文档**部分： 

    [![](user-interface-styles-images/theme06.png "启用特征集合支持")](user-interface-styles-images/theme06.png#lightbox)
3. 确认使用特征变体的更改： 

    [![](user-interface-styles-images/theme07.png "使用特征变体警报")](user-interface-styles-images/theme07.png#lightbox)
4. 将所做的更改保存到情节提要文件。

编辑中 Interface Builder 的 tvOS 情节提要时，Apple 添加了以下功能：

* 开发人员可以指定基于 UI 主题中的用户界面元素的不同变体**属性检查器**:
    
    * 现在有多个属性 + 旁边的可单击要添加的用户界面主题特定版本：  

        [![](user-interface-styles-images/theme08.png "添加 UI 主题特定版本")](user-interface-styles-images/theme08.png#lightbox) 
    
    * 开发人员可以指定一个新的属性，或单击**x**按钮将其删除： 

        [![](user-interface-styles-images/theme09.png "指定新属性，或单击 x 按钮以将其删除")](user-interface-styles-images/theme09.png#lightbox)
* 开发人员可以预览中的 Light 或深色主题和 Interface Builder 中的 UI 设计：
    
    * 在设计图面底部允许开发人员若要切换当前的 UI 主题： 

        [![](user-interface-styles-images/theme10.png "设计图面底部")](user-interface-styles-images/theme10.png#lightbox)
        
    * Interface Builder 中将显示新的主题并将显示任何特征集合特定的调整： 

        [![](user-interface-styles-images/theme11.png "Interface Builder 中显示主题")](user-interface-styles-images/theme11.png#lightbox)

此外，tvOS 模拟器现在具有的键盘快捷方式，使开发人员能够快速调试 tvOS 应用时在浅色和深色主题之间切换。 使用**命令 Shift D**键盘序列浅色和深色之间进行切换。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了光线和深色 UI 主题，Apple 已添加到 tvOS 10 以及如何在 Xamarin.tvOS 应用中实现它们。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
