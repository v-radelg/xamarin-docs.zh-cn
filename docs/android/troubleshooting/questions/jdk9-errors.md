---
title: Xamarin.Android 和 Java 开发工具包 9
description: 本文介绍如何解决 Java 开发工具包 (JDK) 9 或更高版本在 Xamarin.Android 中的错误。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153311"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android 和 Java 开发工具包 9 或更高版本

_本文介绍如何解决 Java 开发工具包 (JDK) 9 或更高版本在 Xamarin.Android 中的错误。_


## <a name="overview"></a>概述

Xamarin.Android 使用 Java 开发工具包 (JDK) 将与用于构建 Android 应用程序和运行 Android 设计器的 Android SDK 进行集成。 Android SDK (API 24 及更高版本) 的最新版本需要 JDK 8 (1.8) 或 Microsoft 移动 OpenJDK 预览版。 **可从 Google 的 Android SDK 工具尚不与 JDK 9 兼容，因为 Xamarin.Android 不适 JDK 9 或更高版本。**

## <a name="jdk-errors"></a>JDK 错误

如果你尝试生成 Xamarin.Android 项目使用的 JDK 8 比更高版本的 JDK 版本，则会出现显式错误，指示不支持此版本的 JDK。 例如：

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

若要解决这些错误，您必须安装 JDK 8 (1.8) 中所述[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。
或者，也可以安装[Microsoft Mobile OpenJDK 预览版](~/android/get-started/installation/openjdk.md)Microsoft Mobile OpenJDK Xamarin.Android 开发最终将取代 JDK 8。


## <a name="checking-the-jdk-version"></a>正在检查 JDK 版本

您可以检查已通过输入以下命令安装的 Java 版本 (JDK`bin`目录必须包含在你`PATH`):

```shell
java -version
```

如果安装了 JDK 9，您将看到一条消息，如下所示：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果安装 JDK 9 或更高版本，则必须安装 Java JDK 8 (1.8) 或 Microsoft 移动 OpenJDK 预览版。 有关如何安装 JDK 8 的信息，请参阅[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。 有关如何安装 Microsoft Mobile OpenJDK 的信息，请参阅[Microsoft Mobile OpenJDK 预览版](~/android/get-started/installation/openjdk.md)。

请注意，不需要卸载更高版本的 jdk;但是，必须确保 JDK 8 而不是更高版本的 JDK 版本，使用 Xamarin。 在 Visual Studio 中，单击**工具 > 选项 > Xamarin > Android 设置**。 如果**Java Development Kit 位置**未设置为 JDK 8 位置 (如**c:\\Program Files\\Java\\jdk1.8.0_111**)，单击**更改**并将其设置为安装了 JDK 8 的位置。 在 Visual Studio for Mac 中，导航到**首选项 > 项目 > SDK 位置 > Android > Java SDK (JDK)** 然后单击**浏览**来更新此路径。

## <a name="known-issues-with-jdk-9"></a>使用 JDK 9 的已知的问题

### <a name="apksigner"></a>apksigner

没有使用 apksigner 和 JDK 9 中的一个已知的问题`apksigner.bat`文件调用`apksigner.jar`与`-Djava.ext.dirs`而不是`-classpath`需要 JDK 9。 建议使用 JDK 8 (1.8)。 有关如何安装 JDK 8 的信息，请参阅[如何更新 Java 开发工具包 (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

如果已安装 JDK 9，请确保以下路径未设置上你`PATH`环境变量，因为它将仍指向 JDK 9: `C:\ProgramData\Oracle\Java\javapath`。 删除了它，`java-version`的命令行上应显示 JDK 8。
