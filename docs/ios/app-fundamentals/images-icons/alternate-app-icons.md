---
title: 在 Xamarin.iOS 中的备用的应用图标
description: 本文档介绍如何在 Xamarin.iOS 中使用备用的应用图标。 它讨论了如何将这些图标添加到 Xamarin.iOS 项目、 如何修改 Info.plist 文件中，以及如何以编程方式管理应用的图标。
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: cc5052c8988a27605cf7680a3853f80e7afd38b7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170873"
---
# <a name="alternate-app-icons-in-xamarinios"></a>在 Xamarin.iOS 中的备用的应用图标

_本文介绍如何在 Xamarin.iOS 中使用备用的应用图标。_

Apple 向 iOS 10.3 的允许的应用程序来管理其图标添加了多项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用图标徽章中 Springboard。
 - `SupportsAlternateIcons` -如果`true`应用具有一组替代的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定替换图标的应用程序的图标。

![](alternate-app-icons-images/icons04.png "示例警报，如果应用程序改变它的图标")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>将备用图标添加到 Xamarin.iOS 项目

若要允许的应用程序切换到备用的图标，将需要的图标图像集合要包含在 Xamarin.iOS 应用程序项目中。 这些映像不能添加到项目使用的典型`Assets.xcassets`方法，必须将它们添加到**资源**直接文件夹。

请执行以下操作：

1. 选择所需的图标图像的文件夹中，选择所有并将其拖至**资源**中的文件夹**解决方案资源管理器**:

    ![](alternate-app-icons-images/icons00.png "选择文件夹中的图标图像")

2. 出现提示时，选择**副本**，**为所有选定文件使用相同的操作**然后单击**确定**按钮：

    ![](alternate-app-icons-images/icons02.png "将文件添加到文件夹对话框")

3. **资源**文件夹应如下所示完成时：

    ![](alternate-app-icons-images/icons01.png "资源文件夹应如下所示")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>修改 Info.plist 文件

使用添加到所需图像**资源**文件夹中， [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13)密钥将需要添加到项目的**Info.plist**文件。 此密钥将定义新建图标和构成它的映像的名称。

请执行以下操作：

1. 在“解决方案资源管理器”中，双击“Info.plist”文件，将其打开进行编辑。
2. 切换到**源**视图。
3. 添加**捆绑包图标**键，将保留**类型**设置为**字典**。
4. 添加`CFBundleAlternateIcons`项并将设置**类型**到**字典**。
5. 添加`AppIcon2`项并将设置**类型**到**字典**。 这将是新的备用的应用图标集的名称。
6. 添加`CFBundleIconFiles`项并将设置**类型**到**数组**
7. 添加到新的字符串`CFBundleIconFiles`省去扩展每个图标文件的数组和`@2x`， `@3x`，等后缀 (示例`100_icon`)。 对于每个文件构成备用图标集重复此步骤。
8. 添加`UIPrerenderedIcon`关键`AppIcon2`字典中，设置**类型**到**布尔**到**否**。
9. 保存对文件所做的更改。

得到**Info.plist**文件应如下所示完成时：

![](alternate-app-icons-images/icons03.png "已完成的 Info.plist 文件")

或在文本编辑器中打开像这样：

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>管理应用程序的图标 

在使用包含在 Xamarin.iOS 项目中的图标图像并**Info.plist**正确配置的文件，开发人员可以使用添加到 iOS 10.3 的许多新功能之一来控制应用的图标。

`SupportsAlternateIcons`属性的`UIApplication`类允许开发人员，确定应用是否支持备用的图标。 例如：

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber`属性的`UIApplication`类允许开发人员在获取或设置当前的应用图标的徽章数 Springboard。 默认值为零 (0)。 例如：

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName`的属性`UIApplication`类，开发人员可获取当前所选的备用应用图标的名称或它将返回`null`如果应用正在使用主图标。 例如：

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName`属性的`UIApplication`类允许开发人员更改应用程序图标。 传递图标以选择的名称或`null`以返回到主图标。 例如：

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

如果运行应用时，用户选择一个备用的图标将显示如下所示的警报：

![](alternate-app-icons-images/icons04.png "示例警报，如果应用程序改变它的图标")

如果用户切换回主图标时，将显示如下所示的警报：

![](alternate-app-icons-images/icons05.png "示例警报，如果应用程序更改为主图标")

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍将备用的应用图标添加到 Xamarin.iOS 项目并在应用内使用它们。



## <a name="related-links"></a>相关链接

- [iOSTenThree 示例](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
