---
title: 绑定 Eclipse 库项目
description: 此演练说明了如何使用 Xamarin.Android 项目模板绑定一个 Eclipse Android 库项目。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957613"
---
# <a name="binding-an-eclipse-library-project"></a>绑定 Eclipse 库项目

_此演练说明了如何使用 Xamarin.Android 项目模板绑定一个 Eclipse Android 库项目。_


## <a name="overview"></a>概述

尽管。AAR 文件正在逐渐成为 Android 库分发的标准，在某些情况下它为创建的绑定所需*Android 库项目*。 Android 库项目都包含可共享代码的特殊 Android 项目和 Android 应用程序项目，可以引用的资源。 通常情况下，您将绑定到一个 Android 库项目时在 Eclipse IDE 中创建库。
本演练介绍如何创建一个 Android 库项目的示例。从 Eclipse 项目的目录结构压缩。

Android 库项目的不同于常规的 Android 项目在于这些不编译到一个 APK，不，代表自身，可部署到设备。 而是意味着一个 Android 库项目引用的 Android 应用程序项目。 生成 Android 应用程序项目时，会首先编译 Android 库项目。 Android 应用程序项目将会吸收到已编译的 Android 库项目，然后将代码和资源包含到分发的 APK。 由于这种差异，创建一个 Android 库项目的绑定是创建 Java 绑定稍有不同。JAR 或。AAR 文件。



## <a name="walkthrough"></a>演练

在 Xamarin.Android Java 绑定项目中使用的 Android 库项目它时，第一个构建在 Eclipse 中的 Android 库项目。 编译完成后，下面的屏幕截图显示一个 Android 库项目的示例： 

[![在 Eclipse 中的示例库项目](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

请注意，已从 Android 库项目的源代码编译到一个临时。名为 JAR 文件**android mapviewballoons.jar**，和资源复制到**bin/res/危机**文件夹。 

在 Eclipse 中编译之后的 Android 库项目，它然后可将绑定使用 Xamarin.Android Java 绑定项目。 第一个。必须创建 ZIP 文件，其中包含**bin**并**res** Android 库项目的文件夹。 务必删除中间**危机**子目录，以便资源位于**bin/res**。下面的屏幕截图显示了内容的其中一个此类。ZIP 文件： 

[![Android 库项目.zip 的内容](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

这。ZIP 文件然后添加到 Xamarin.Android Java 绑定项目中，如以下屏幕截图中所示：

[![Zip 添加到 Java 绑定项目](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

请注意，生成操作的。ZIP 文件已自动设置为**LibraryProjectZip**。

如果有的话。所需的 Android 库项目的 JAR 文件，应将它们添加到**Jar** Java 绑定库项目的文件夹和**生成操作**设置为**ReferenceJar**. 此示例所示的屏幕截图： 

[![生成操作设置为 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成这些步骤后，如前面在本文档中所述，可以使用 Xamarin.Android Java 绑定项目。

> [!NOTE]
> 目前不支持在其他 Ide 中编译的 Android 库项目。 相同的目录结构或中的文件不能创建其他 Ide **bin** Eclipse 的文件夹。 


## <a name="summary"></a>总结

在本文中，我们介绍了通过绑定一个 Android 库项目的过程。 我们构建 Android 库项目在 Eclipse 中，然后我们创建了从一个 zip 文件**bin**并**res** Android 库项目的文件夹。 接下来，我们使用此 zip 创建 Xamarin.Android Java 绑定项目。 

