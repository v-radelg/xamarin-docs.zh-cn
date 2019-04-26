---
title: Xamarin.Forms 浅色主题
description: 本文介绍如何使用 Xamarin.Forms 浅色主题的应用程序中。
ms.prod: xamarin
ms.assetid: D5D16AE3-F51F-4359-B37A-E1087ECE512B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 7f40e375d653acec60f8848627234ab46fcce8de
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896250"
---
# <a name="xamarinforms-light-theme"></a>Xamarin.Forms 浅色主题

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!NOTE]
> 主题需要 Xamarin.Forms 2.3 预览版本。 检查[故障排除提示](~/xamarin-forms/user-interface/themes/index.md)如果出现错误。

若要使用浅色主题：

## <a name="1-add-nuget-packages"></a>1.添加 Nuget 包

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Light

## <a name="2-add-to-the-resource-dictionary"></a>2.将添加到资源字典

在中**App.xaml**文件并添加新的自定义`xmlns`主题，然后确保与应用程序的资源字典合并主题的资源。
XAML 文件的示例如下所示：

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:light="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light">
    <Application.Resources>
        <ResourceDictionary MergedWith="light:LightThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3.加载主题类

按照这[故障排除步骤](~/xamarin-forms/user-interface/themes/index.md)，并在 iOS 和 Android 应用程序项目中添加所需的代码。

## <a name="4-use-styleclass"></a>4.使用 StyleClass

下面是按钮和标签浅色主题，以及生成它们的标记中的示例。

[![](light-images/light-theme-sml.png "按钮和标签浅色主题中")](light-images/light-theme.png#lightbox "按钮和浅色主题中的标签")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />
    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

[内置类的完整列表](~/xamarin-forms/user-interface/themes/index.md)显示哪些样式是可用于某些常见的控件。
