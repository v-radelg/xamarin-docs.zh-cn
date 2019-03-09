---
title: 使用 watchOS 在 Xamarin 中的本地化
description: 本文档介绍如何本地化使用 Xamarin 生成的 watchOS 应用。 它讨论了 watch 应用，监视扩展，在代码中，字符串情节提要文本、 测试和的详细信息。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 1362767bf9a80af1eac37d316bd99a6ab364063f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669604"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>使用 watchOS 在 Xamarin 中的本地化

_调整 watchOS 应用程序以通过多种语言_

![](localization-images/both-languages-sml.png "Apple Watch 显示本地化的内容")

watchOS 应用已本地化使用标准 iOS 方法：

- 使用**本地化 ID**情节提要元素
- **.strings**情节提要，与关联的文件和
- **Localizable.strings**文件使用在代码中的文本。

默认的情节提要和资源中的位置**Base**目录中，和特定于语言的翻译和其他资源存储在 **.lproj**目录。
iOS 和监视 OS 自动将使用用户的语言选择加载正确的字符串和资源。

因为 Apple Watch 应用有两个部分-监视应用和监视扩展的两个位置，具体取决于如何使用所需资源的已本地化字符串。

本地化的文本和资源将被*不同*中监视应用和监视扩展。

## <a name="watch-app"></a>Watch 应用

Watch 应用包含描述应用程序的用户界面的情节提要。 任何控件 (如`Label`并`Image`)，具有支持本地化**本地化 ID**。

每个特定于语言的 **.lproj**目录应包含 **.strings**提供翻译的每个元素的文件 (使用**本地化 ID**)，以及图像引用的情节提要。

## <a name="watch-extension"></a>监视扩展

监视扩展是运行应用程序代码。 从代码将向用户显示任何文本需要本地化的扩展中并不在 watch 应用。

扩展还应包含特定于语言的 **.lproj**目录，但 **.strings**文件只需要在代码中使用的文本翻译。

## <a name="globalizing-the-watch-solution"></a>全球化监视解决方案

全球化是对应用程序的可本地化的过程。
对于这意味着设计演示图板记住的不同文本长度与监视应用程序，确保每个屏幕布局调整相应地显示具体取决于什么文本。 此外需要确保可以使用转换监视扩展代码中引用的任何字符串`LocalizedString`方法。

### <a name="watch-app"></a>Watch 应用

默认情况下监视应用程序未配置用于本地化。 需要移动默认情节提要文件，并创建你的翻译一些其他目录：

1. 创建**Base.lproj**目录并将其转移**Interface.storyboard**到其中。

2. 创建 **<language>.lproj**为你想要支持每种语言的目录。

3. **.Lproj**目录应包含**Interface.strings**文本文件 （文件名应与 storboard 的名称）。 您可以根据需要将需要本地化这些目录中的任何映像。

（仅英语和西班牙语语言已添加文件） 进行这些更改后，监视应用程序项目如下所示：

  ![](localization-images/watchapp-solution.png "监视应用程序项目与英语和西班牙语语言文件")

#### <a name="storyboard-text"></a>情节提要文本

当您编辑情节提要时，选择每个元素，并请注意**本地化 ID**中显示**属性**板：

  [![](localization-images/storyboard-sml.png "在属性面板中显示的本地化 ID")](localization-images/storyboard.png#lightbox)

中**Base.lproj**文件夹中，创建键 / 值对，如下所示，该密钥通过形成**本地化 ID**并在控件中的属性名称的已加入由圆点 (`.`)。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

请注意，在此示例中，**本地化 ID**可以是简单的数字字符串 （例如。 "0"、"1"等) 或更复杂的字符串 （如"AgC-eL-Hgc")。 `Label` 控件具有`Text`属性和`Button`具有`Title`属性，它们已本地化的值设置的方式反映是确保使用小写字母的属性名称，如上面的示例中所示。

情节提要的手表上呈现时，正确的值将自动提取并显示根据用户选择的语言。

#### <a name="storyboard-images"></a>情节提要图像

该示例解决方案还包括**gradient@2x.png**每个语言文件夹中的映像。 此映像可以是不同的每种语言 （例如。 它可能会有嵌入的文本需要转换，或使用本地化插图）。

只需设置的映像**图像**情节提要和正确的映像中的属性将根据用户选择的语言手表上呈现。

![](localization-images/storyboard-image.png "在情节提要中设置图像映像属性")

注意： 因为所有 Apple Watch 都具有 Retina 显示，只有 **@2x** 映像的版本是必需的。 不需要指定 **@2x** 情节提要中。

### <a name="watch-extension"></a>监视扩展

监视扩展需要类似的目录结构，以支持本地化，但是没有任何情节提要。 在扩展中的本地化的字符串只包括引用的C#代码。

![](localization-images/watchextension-solution.png "监视扩展目录结构，以支持本地化")

#### <a name="strings-in-code"></a>在代码中的字符串

**Localizable.strings**文件具有与是否使用演示图板关联略有不同的结构。 在这种情况下，我们可以选择"密钥"的任何字符串;Apple 的建议是使用一个键，它反映了将以默认语言显示的实际文本：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString`方法用于将字符串解析为其已翻译的对应项，如下面的代码中所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>在代码中的图像

可以通过两种方式设置用代码填充的映像。

1. 您可以更改`Image`控制通过其值设置为图像的字符串名称的已存在，在监视应用中，例如

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. 您可以将图像从扩展中移动到监视使用`FromBundle`，应用会自动选择适合用户的语言选择的图像。 示例解决方案中没有图像**language@2x.png**在每个语言文件夹，并显示在`DetailController`使用以下代码：

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  请注意，不需要指定 **@2x** 引用图像的文件名时。

第二个方法也是适用于从远程服务器上监视; 呈现下载映像但是在这种情况下您应该确保你下载的映像已正确本地化根据用户的首选项。

## <a name="localization"></a>本地化

一旦你已配置你的解决方案，翻译人员将需要处理你 **.strings**文件和你想要支持每种语言的映像。

您可以创建任意多个 **.lproj**作为你的目录需要 （一个用于每个受支持的语言）。 它们被命名为使用语言代码，如**en**， **es**， **de**，**日本**， **PT**，等等。 （对于英语西班牙语、 德语、 日语和葡萄牙语 （巴西） 分别)。

附加的示例使用 （计算机生成） 翻译来演示如何 watchOS 应用程序进行本地化。

### <a name="watch-app"></a>Watch 应用

使用这些值将监视应用的情节提要中定义的用户界面。 *键*值是每个情节提要控件的组合**本地化 ID**和要转换的属性。

建议添加注释，以便翻译人员知道转换应为包含的文件的原始文本。

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

下面显示了情节提要 （机器翻译） 西班牙语语言字符串。 最好将注释添加到每个行，因为很难知道**本地化 ID**否则指：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>监视扩展

在代码中使用这些值将转换之前向用户显示的信息。 *密钥*时，他们在编写的代码中，选择由开发人员，并且通常包含要转换的实际字符串。

#### <a name="eslprojlocalizablestrings-file"></a>es.lproj/Localizable.strings 文件

（机器翻译） Spansish 语言字符串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>正在测试

要更改语言首选项的方法而异的模拟器和物理设备。

### <a name="simulator"></a>模拟器

在模拟器中，选择要使用 iOS 进行测试的语言**设置**应用 （模拟器主屏幕中的灰色齿轮图标）。

  ![](localization-images/sim-settings-sml.png "IOS 设置应用程序本地化设置")

### <a name="watch-device"></a>监视设备

当测试监视时，更改手表的语言**Apple Watch**配对的 iPhone 上的应用。

  ![](localization-images/phone-settings-sml.png "更改配对的 iPhone 上的 Apple Watch 应用中监视的语言")



## <a name="related-links"></a>相关链接

- [WatchLocalization （示例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
