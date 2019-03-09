---
title: 可以将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: e439d39dd8591cad14485e64aabab2d6016a8e27
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668219"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>可以将 Xamarin.Forms 默认模板更新到较新的 NuGet 包？

本指南使用 Xamarin.Forms.NET 标准库模板作为示例，但相同的常规方法也适用于 Xamarin.Forms 共享项目模板。 使用 Xamarin.Forms 1.5.1.6471 到 2.1.0.6529 从更新的示例编写本指南，但相同的步骤可以将其他版本设置为默认值。

1.  复制原始模板`.zip`从：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  解压缩`.zip`到临时位置。

3.  将所有出现的窗体包的旧版本更改为你想要使用的新版本。
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    示例： `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  将"name"元素的主要更改[多项目模板文件](https://msdn.microsoft.com/library/ms185308.aspx)(`Xamarin.Forms.PCL.vstemplate`) 使其成为唯一。 例如：
    > <Name>空白应用 （Xamarin.Forms 可移植）-2.1.0.6529</Name>

5.  重新压缩整个模板文件夹。 请确保要匹配的原始文件结构`.zip`文件。 `Xamarin.Forms.PCL.vstemplate`文件应位于顶部`.zip`文件不在任何文件夹。

6.  在您的每个用户 Visual Studio 模板文件夹中创建"移动应用"子目录：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  将新的压缩模板文件夹复制到新的"移动应用"目录。

8.  下载与步骤 3 中的版本匹配的 NuGet 包。 例如， [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (另请参阅[ https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file))，并将其复制到相应的子文件夹中的 Xamarin Visual Studio 扩展文件夹：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
