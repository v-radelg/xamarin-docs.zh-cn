---
title: 在 Android 上嵌入 .NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007329"
---
# <a name="net-embedding-on-android"></a>在 Android 上嵌入 .NET

在某些情况下，可能需要将 Xamarin .NET 库添加到现有的本机 Android 项目。 若要执行此操作，可以使用[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)工具将 .net 库转换为本机库，该库可并入基于 Java 的本机 Android 应用程序。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Xamarin Android 要求

要使 Xamarin 适用于 .NET 嵌入，需要以下各项：

- 必须安装**xamarin** &ndash;[xamarin 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本。

- 必须安装**Android Studio** &ndash;[Android Studio](https://developer.android.com/studio/) 3.x 或更高版本。

- 必须安装 java**开发人员工具包**&ndash;[java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

若要在本机 Android 项目中使用 .NET 库，请执行以下步骤：

1. 创建一个C# Android 库项目。

2. 安装[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找到**Embeddinator-4000**并将其添加到你的**路径**。 例如:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. 对库程序集运行 Embeddinator-4000。 例如:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 Java 项目中使用生成的 AAR 文件。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Xamarin Android 要求

要使 Xamarin 适用于 .NET 嵌入，需要以下各项：

- 必须安装**xamarin** &ndash;[xamarin 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本。

- 必须安装**Android Studio** &ndash;[Android Studio](https://developer.android.com/studio/) 3.x 或更高版本。

- 必须安装 java**开发人员工具包**&ndash;[java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本。

- 必须安装**mono** &ndash;[mono 5.0](https://www.mono-project.com/download/)或更高版本（随 Visual Studio for Mac 安装 mono）。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

若要在本机 Android 项目中使用 .NET 库，请执行以下步骤：

1. 创建一个C# Android 库项目。

2. 安装[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找到**Embeddinator-4000**并将**mono**添加到你的路径。 例如:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. 对库程序集运行 Embeddinator-4000。 例如:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 Java 项目中使用生成的 AAR 文件。

-----

[Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文档中介绍了用法和命令行选项。

## <a name="callbacks"></a>回电

了解如何[在和 Java C#之间进行调用](callbacks.md)。

## <a name="samples"></a>示例

- [天气示例应用](https://github.com/jamesmontemagno/embeddinator-weather)
