---
title: 在 Xamarin 中使用 watchOS 本地化
description: 本文档介绍如何本地化用 Xamarin 生成的 watchOS 应用。 本文介绍了如何监视应用、监视扩展、代码中的字符串、情节提要文本、测试等内容。
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 82b739697705ac4c90390a36405d755a5f523159
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028403"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>在 Xamarin 中使用 watchOS 本地化

_针对多种语言调整 watchOS 应用_

![](localization-images/both-languages-sml.png "Apple Watch displaying localized content")

watchOS 应用使用标准 iOS 方法进行本地化：

- 使用情节提要元素上的**本地化 ID** ，
- 与情节提要关联的**字符串**文件和
- 代码中使用的文本的可**本地化的字符串**文件。

默认情节提要和资源位于**基**目录中，特定于语言的翻译和其他资源存储在**lproj**目录中。
iOS 和监视 OS 会自动使用用户的语言选择来加载正确的字符串和资源。

由于 Apple Watch 应用有两部分-"监视" 应用和 "监视" 扩展-在两个位置需要本地化的字符串资源，具体取决于它们的使用方式。

已本地化的文本和资源将在 "监视" 应用和 "监视" 扩展中*有所不同*。

## <a name="watch-app"></a>观看应用

Watch 应用包含描述应用用户界面的情节提要。 支持本地化的任何控件（如 `Label` 和 `Image`）都具有**本地化 ID**。

每个语言特定的**lproj**目录都应包含**字符串**文件，每个元素都具有翻译（使用**本地化 ID**）以及情节提要引用的图像。

## <a name="watch-extension"></a>监视扩展

"监视扩展" 是应用代码的运行位置。 从代码向用户显示的任何文本都需要在扩展中进行本地化，而不是在 watch 应用中进行本地化。

扩展还应包含特定于语言的**lproj**目录，但**字符串**文件只要求翻译代码中使用的文本。

## <a name="globalizing-the-watch-solution"></a>全球化监视解决方案

全球化是使应用程序可本地化的过程。
对于 "监视应用"，这意味着在设计情节提要时要记住不同的文本长度，确保每个屏幕布局根据显示的文本进行适当的调整。 还需要确保可以使用 `LocalizedString` 方法转换监视扩展代码中引用的任何字符串。

### <a name="watch-app"></a>观看应用

默认情况下，不会为监视应用配置本地化。 需要移动默认情节提要文件，并为翻译创建其他目录：

1. 创建**lproj**目录，并将其移动**到其中。**

2. 为要支持的每种语言创建 **\<> 语言的 lproj**目录。

3. **Lproj**目录应包含**Interface**文本文件（filename 应与 storboard 的名称匹配）。 您可以选择将需要本地化的任何图像放入这些目录。

进行这些更改后，"监视" 应用项目将如下所示（仅添加英语和西班牙语语言文件）：

  ![](localization-images/watchapp-solution.png "The watch app project with English and Spanish language files")

#### <a name="storyboard-text"></a>情节提要文本

编辑情节提要时，请选择每个元素，并注意 "**属性**板" 中显示的**本地化 ID** ：

  [![](localization-images/storyboard-sml.png "The Localization ID that appears in the Properties pad")](localization-images/storyboard.png#lightbox)

在**lproj**文件夹中，创建如下所示的键值对，其中的键由**本地化 ID**和控件上的属性名称构成，由点（`.`）进行联接。

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

请注意，在此示例中，**本地化 ID**可以是简单数字字符串（例如 "0"、"1" 等）或更复杂的字符串（如 "AgC-Hgc"）。 `Label` 控件具有 `Text` 属性，`Button`s 具有 `Title` 属性，该属性将在其本地化值的设置方式中反映出来-请务必使用上面的示例中所示的小写属性名称。

当在手表上呈现情节提要时，将根据用户选择的语言自动提取并显示正确的值。

#### <a name="storyboard-images"></a>情节提要图像

示例解决方案还包括每个语言文件夹中的 **gradient@2x.png** 图像。 对于每种语言，此映像可能有所不同（例如 它可能包含需要翻译的嵌入文本，或使用本地化的插图）。

只需在情节提要中设置图像的**image**属性，正确的图像就会根据用户选择的语言呈现在手表上。

![](localization-images/storyboard-image.png "Set the images Image property in the storyboard")

注意：由于所有 Apple watch 都显示 Retina，因此只需要映像的 **@2x** 版本。 不需要在情节提要中指定 **@2x** 。

### <a name="watch-extension"></a>监视扩展

监视扩展需要类似的目录结构来支持本地化，但没有情节提要。 扩展中的本地化字符串只是C#代码引用的字符串。

![](localization-images/watchextension-solution.png "The watch extension directory structure to support localization")

#### <a name="strings-in-code"></a>代码中的字符串

可**本地化的字符串**文件具有略微不同于与情节提要关联的结构。 在这种情况下，我们可以选择任何 "密钥" 字符串;Apple 建议使用一个密钥来反映将以默认语言显示的实际文本：

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

`NSBundle.MainBundle.LocalizedString` 方法用于将字符串解析为其转换的对应项，如以下代码所示。

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>代码中的图像

可以通过两种方式设置通过代码填充的图像。

1. 可以通过将 `Image` 控件的值设置为已存在于 Watch 应用中的映像的字符串名称来更改该控件，例如

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. 你可以使用 `FromBundle` 将图像从扩展移动到手表，应用将自动为用户的语言选择选择正确的映像。 在示例解决方案中，每个语言文件夹中 **language@2x.png** 有一个映像，并且使用以下代码在 `DetailController` 上显示它：

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    请注意，在引用图像的文件名时，无需指定 **@2x** 。

第二种方法也适用于从远程服务器下载要在手表上呈现的映像的情况;但在这种情况下，应确保根据用户的偏好正确地本地化下载的映像。

## <a name="localization"></a>本地化

配置解决方案后，翻译人员需要为要支持的每种语言处理**字符串**文件和映像。

你可以根据需要创建任意数量的**lproj**目录（每种支持的语言都有一个）。 它们使用语言代码（如**en**、 **es**、 **de**、 **ja**、 **pt-BR**等）进行命名，分别用于英语、西班牙语、德语、日语和葡萄牙语（巴西）。

附加示例使用（计算机生成的）翻译演示如何本地化 watchOS 应用。

### <a name="watch-app"></a>观看应用

这些值用于转换在手表应用的情节提要中定义的用户界面。 *键值*是每个情节提要控件的**本地化 ID**和要翻译的属性的组合。

建议将包含原始文本的注释添加到文件中，以使翻译人员知道转换内容。

#### <a name="eslprojinterfacestrings"></a>lproj/Interface

下面显示了情节提要的（机器翻译的）西班牙语语言字符串。 向每一行添加注释会很有帮助，因为很难知道**本地化 ID**所引用的内容：

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>监视扩展

在代码中使用这些值来转换信息，然后将信息显示给用户。 当开发人员在编写代码时选择该*密钥*，通常包含要翻译的实际字符串。

#### <a name="eslprojlocalizablestrings-file"></a>lproj/可本地化的字符串文件

（机器翻译的） Spansish 语言字符串：

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>正在测试

更改语言首选项的方法在模拟器和物理设备之间有所不同。

### <a name="simulator"></a>模拟器

在模拟器上，选择要使用 iOS "**设置**" 应用测试的语言（模拟器主屏幕中的灰色齿轮图标）。

  ![](localization-images/sim-settings-sml.png "The iOS Settings app Localization settings")

### <a name="watch-device"></a>监视设备

使用 "监视" 进行测试时，请在配对的 iPhone 上更改**Apple Watch**应用中的手表语言。

  ![](localization-images/phone-settings-sml.png "Change the watch's language in the Apple Watch app on the paired iPhone")

## <a name="related-links"></a>相关链接

- [WatchLocalization （示例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
