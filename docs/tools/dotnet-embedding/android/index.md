---
title: 在 Android 上嵌入 .NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: conceptdev
ms.author: crdun
ms.date: 06/15/2018
ms.openlocfilehash: 1369d5cd901207618128da8b0111e488eae7b83e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772223"
---
# <a name="net-embedding-on-android"></a>在 Android 上嵌入 .NET

在某些情况下，您可能希望将 Xamarin .NET 库添加到现有的本机 Android 项目中。 为此，您可以使用[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)工具将.NET 库转换为本机库，以便可以合并到基于 Java 的本机 Android 应用程序中。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Xamarin Android 要求

要使 Xamarin.Android 可以与.NET Embedding 一起使用，您需要以下内容：

- 必须安装 Xamarin [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本。 &ndash;

- **Android Studio**&ndash;必须安装 [Android Studio 1.x](https://developer.android.com/studio/) 或更高版本。

- **Java 开发人员工具包**&ndash;必须安装 [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

要在本机 Android 项目中使用.NET 库，请使用以下步骤：

1. 创建一个C＃Android库项目。

2. 安装[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找到**Embeddinator-4000**并将其添加到你的**路径**。 例如：

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. 对库程序集运行 Embeddinator 4000。 例如：

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 Java 项目中使用生成的 AAR 文件。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Xamarin Android 要求

要使 Xamarin.Android 可以与.NET Embedding 一起使用，您需要以下内容：

- 必须安装 Xamarin [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本。 &ndash;

- **Android Studio**&ndash;必须安装 [Android Studio 1.x](https://developer.android.com/studio/) 或更高版本。

- **Java 开发人员工具包**&ndash;必须安装 [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。

- **Mono**&ndash;必须安装 [mono 5.0](https://www.mono-project.com/download/) 或更高版本（mono 随 Visual Studio for Mac 一起安装）。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

要在本机 Android 项目中使用.NET 库，请使用以下步骤：

1. 创建一个C＃Android库项目。

2. 安装[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找到**Embeddinator-4000**并将**mono**添加到你的路径。 例如：

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. 对库程序集运行 Embeddinator 4000。 例如：

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 Java 项目中使用生成的 AAR 文件。

-----

[Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文档中描述了用法和命令行选项。

## <a name="callbacks"></a>回电

了解如何[在C＃和Java之间进行调用. ](callbacks.md)。

## <a name="samples"></a>示例

- [天气示例应用](https://github.com/jamesmontemagno/embeddinator-weather)
