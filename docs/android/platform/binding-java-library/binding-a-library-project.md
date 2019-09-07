---
title: 绑定 Eclipse 库项目
description: 本演练介绍如何使用 Xamarin android 项目模板来绑定 Eclipse Android 库项目。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 78b70ce70292e589aee4a1dbe56f3765552ece7a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757717"
---
# <a name="binding-an-eclipse-library-project"></a>绑定 Eclipse 库项目

_本演练介绍如何使用 Xamarin android 项目模板来绑定 Eclipse Android 库项目。_

## <a name="overview"></a>概述

即使.AAR 文件日益成为 Android 库分发的标准，在某些情况下，需要为*android 库项目*创建绑定。 Android 库项目是特殊的 Android 项目，其中包含可由 Android 应用程序项目引用的可共享代码和资源。 通常，当在 Eclipse IDE 中创建库时，将绑定到 Android 库项目。
本演练提供有关如何创建 Android 库项目的示例。从 Eclipse 项目的目录结构中压缩。

Android 库项目不同于常规 Android 项目，因为它们不会编译到 APK 中，也不会自行部署到设备。 但 Android 库项目应由 Android 应用程序项目引用。 生成 Android 应用程序项目时，将首先编译 Android 库项目。 然后，Android 应用程序项目将被导入到已编译的 Android 库项目中，并将代码和资源包含到 APK 中以便进行分发。 由于这种差异，为 Android 库项目创建绑定与为 Java 创建绑定略有不同。JAR 或。AAR 文件。

## <a name="walkthrough"></a>演练

若要在 Xamarin Android Java 绑定项目中使用 Android 库项目，首先需要在 Eclipse 中生成 Android 库项目。 下面的屏幕截图显示了编译后一个 Android 库项目的示例： 

[![Eclipse 中的示例库项目](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

请注意，已将 Android 库项目中的源代码编译为临时代码。名为**android-mapviewballoons**的 JAR 文件，并且资源已复制到**bin/res/捕获**文件夹。 

在 Eclipse 中编译 Android 库项目后，便可以使用 Xamarin Java 绑定项目对其进行绑定。 首先是。必须创建包含 Android 库项目的**bin**和**RES**文件夹的 ZIP 文件。 删除干预**捕获**子目录以使资源位于**bin/res**中是非常重要的。以下屏幕截图显示了这样一种情况的内容。ZIP 文件： 

[![Android 库项目的内容 .zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

此.然后，将 ZIP 文件添加到 Xamarin Java 绑定项目，如以下屏幕截图所示：

[![压缩添加到 Java 绑定项目](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

请注意，的生成操作。ZIP 文件已自动设置为**LibraryProjectZip**。

如果有，则为。Android 库项目所需的 JAR 文件，它们应添加到 Java 绑定库项目的 " **jar** " 文件夹，并将 "**生成" 操作**设置为**ReferenceJar**。 下面的屏幕截图中显示了这种情况的示例： 

[![生成操作设置为 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成这些步骤后，可以按本文档前面所述使用 Xamarin Java 绑定项目。

> [!NOTE]
> 此时不支持在其他 Ide 中编译 Android 库项目。 其他 Ide 可能不会将**bin**文件夹中的目录结构或文件创建为 Eclipse。 

## <a name="summary"></a>总结

在本文中，我们逐步完成了绑定 Android 库项目的过程。 我们在 Eclipse 中生成了 Android 库项目，然后从 Android 库项目的**bin**和**res**文件夹创建了一个 zip 文件。 接下来，我们使用此 zip 创建了一个 Xamarin Java 绑定项目。 
