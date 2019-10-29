---
title: Xamarin 和 Java 开发工具包9
description: 本文介绍如何解决 Xamarin 中的 Java 开发工具包（JDK）9或更高版本的错误。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026835"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin 和 Java 开发工具包9或更高版本

_本文介绍如何解决 Xamarin 中的 Java 开发工具包（JDK）9或更高版本的错误。_

## <a name="overview"></a>概述

Xamarin 使用 Java 开发工具包（JDK）与用于构建 Android 应用和运行 Android 设计器的 Android SDK 集成。 最新版本的 Android SDK （API 24 和更高版本）需要 JDK 8 （1.8）或 Microsoft Mobile OpenJDK Preview。 **由于 Google 中提供的 Android SDK 工具尚未与 JDK 9 兼容，因此不能使用 JDK 9 或更高版本。**

## <a name="jdk-errors"></a>JDK 错误

如果尝试使用 jdk 8 以后版本的 JDK 生成 Xamarin Android 项目，则会出现一个显式错误，指示不支持此版本的 JDK。 例如:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

若要解决这些错误，必须按照[如何实现更新 Java 开发工具包（JDK）版本](~/android/troubleshooting/questions/update-jdk.md)中所述安装 JDK 8 （1.8）。
或者，你可以安装[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) ，Microsoft mobile OpenJDK 最终将为 Xamarin Android 开发替换 JDK 8。

## <a name="checking-the-jdk-version"></a>检查 JDK 版本

可以通过输入以下命令来查看已安装的 Java 版本（JDK `bin` 目录必须在 `PATH`中）：

```shell
java -version
```

如果安装了 JDK 9，你将看到如下所示的消息：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果安装了 JDK 9 或更高版本，则必须安装 Java JDK 8 （1.8）或 Microsoft Mobile OpenJDK Preview。 有关如何安装 JDK 8 的信息，请参阅[如何实现更新 Java 开发工具包（JDK）版本？](~/android/troubleshooting/questions/update-jdk.md)。 有关如何安装 Microsoft Mobile OpenJDK 的信息，请参阅[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。

请注意，您无需卸载 JDK 的更高版本;但是，必须确保 Xamarin 使用 JDK 8 而不是更高版本的 JDK 版本。 在 Visual Studio 中，单击 "**工具" > "Xamarin > Android 设置" > 选项**。 如果**Java 开发工具包位置**未设置为 jdk 8 位置（如**C：\\Program Files\\Java\\jdk 1.8.0 _111**），请单击 "**更改**"，并将其设置为安装 JDK 8 的位置。 在 Visual Studio for Mac 中，导航到 "**首选项" > 项目 > SDK 位置 > Android > JAVA SDK （JDK）** ，然后单击 "**浏览**" 更新此路径。

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知问题

### <a name="apksigner"></a>apksigner

Apksigner 和 JDK 9 存在一个已知问题，`apksigner.bat` 文件使用 `-Djava.ext.dirs` 而不是 `-classpath` JDK 9 所需的调用 `apksigner.jar`。 建议使用 JDK 8 （1.8）。 有关如何安装 JDK 8 的信息，请参阅[如何实现更新 Java 开发工具包（JDK）版本？](~/android/troubleshooting/questions/update-jdk.md)

如果已安装 JDK 9，请确保未在 `PATH` 环境变量上设置以下路径，因为它仍指向 JDK 9： `C:\ProgramData\Oracle\Java\javapath`。 删除后，命令行 `java-version` 应显示 JDK 8。
