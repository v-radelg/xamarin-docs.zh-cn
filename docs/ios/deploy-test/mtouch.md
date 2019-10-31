---
title: 使用 mtouch 捆绑 Xamarin.iOS 应用
description: 本文档介绍 mtouch，该工具可完成将 Xamarin.iOS 应用程序转换为捆绑包、在模拟器中启动并将其部署到物理设备所需的多个步骤。
ms.prod: xamarin
ms.assetid: BCA491DA-E4C1-8689-3EC9-E4C72495A798
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 2a0f9d063b319c0f412f6e8f47a59f0f994678ae
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026286"
---
# <a name="using-mtouch-to-bundle-xamarinios-apps"></a>使用 mtouch 捆绑 Xamarin.iOS 应用

iPhone 应用程序以应用程序捆绑包的形式发货。 这些是扩展名为 `.app` 的目录，其中包含 iPhone 用于了解应用程序的代码、数据、配置文件和清单。

将 .NET 可执行文件转变为应用程序的过程主要由 `mtouch` 命令驱动，它是一种集成将应用程序转变为捆绑包所需的多个步骤的工具。 此工具用于在模拟器中启动应用程序并将软件部署到实际的 iPhone 或 iPod Touch设备。

## <a name="detailed-instructions"></a>详细说明

检查 [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) 手册页和 mtouch 工具的所有可能用法。

## <a name="installation"></a>安装

在 Mac 上，`mtouch` 与 Xamarin.iOS 捆绑在一起。 可在以下目录中找到它：

/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin 

若要使 `mtouch` 便于使用，请将它的父目录添加到系统的 `PATH` 环境变量。  

例如，若要在 Bash 中执行此操作，可将以下行添加到 ~/.bash_profile  文件末尾：

```bash
export PATH=$PATH:/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin
```

> [!WARNING]
> 若要使用 `mtouch`，请不要依赖于是否存在 /Developer/MonoTouch/usr/bin  ，一个指向 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin  的符号链接。 该符号链接的存在只是为了与未在 /Library/Frameworks/...  中安装的旧 MonoTouch 版本保持兼容性，它可能会在未来版本中消失。

## <a name="building"></a>生成

`mtouch` 命令可以用三种不同的方式编译代码：

- 针对模拟器测试进行编译。
- 针对设备部署进行编译。
- 将可执行文件部署到设备。

### <a name="building-for-the-simulator"></a>针对模拟器进行构建

开始时，最常用的方案是在模拟器中试用应用程序，因此，请使用 `mtouch -sim` 将代码编译为模拟器程序包。 具体方式如下：

```bash
$ mtouch -sim Hello.app hello.exe
```

### <a name="building-for-the-device"></a>针对设备进行构建

若要针对设备构建软件，请使用 `mtouch -dev` 选项构建应用程序，此外，你需要提供用于对应用程序进行签名的证书的名称。 下面演示了如何针对设备构建应用程序：

```bash
$ mtouch -dev -c "iPhone Developer: Miguel de Icaza" foo.exe
```

在此特定情况下，我们将使用“iPhone Developer:Miguel de Icaza”证书来对此应用程序签名。 此步骤非常重要，否则，物理设备将拒绝加载应用程序。

 <a name="Running_your_Application" />

## <a name="running-your-application"></a>运行应用程序

### <a name="launching-on-the-simulator"></a>在模拟器上启动

拥有应用程序捆绑包后，在模拟器上启动会非常简单：

```bash
$ mtouch --sdkroot /Applications/Xcode.app -launchsim Hello.app 
```

如果未设置 `--sdkroot` 标志，它会默认为 xcode-select 路径，并导致以下警告：

> 例如 warning MT0061:No Xcode.app specified (using --sdkroot), using the system Xcode as reported by 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

上面的命令行将生成如下输出：

```bash
Launching application
Application launched
PID: 98460
Press enter to terminate the application
```

强烈建议你同时保留标准输出和标准错误文件，以帮助进行调试。 `Console.WriteLine` 的输出会转到 `stdout`，且 `Console.Error.WriteLine` 中的输出和任意其他运行时错误消息会转到 `stderr`。

为此，可以使用 `--stdout` 和 `--stderr` 标志：

```bash
../../tools/mtouch/mtouch --launchsim=Hello.app --stdout=output --stderr=error
```

如果应用程序失败，你可以查看输出和错误来诊断问题。

### <a name="deploying-to-a-device"></a>部署到设备

若要部署设备，你需要如 Apple 的[管理设备](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/00-About_the_iOS_Application_Development_Workflow/introduction.html)文档中所述预配设备。 一旦正确预配设备后，你可以使用 mtouch 命令将已编译的“.app”部署到设备中。 可使用此命令执行此操作：

```bash
$ mtouch —sdkroot /Applications/Xcode.app -installdev=MyApp.app
```

如果未设置 `--sdkroot` 标志，它会默认为 xcode-select 路径，并导致以下警告：

> 例如 warning MT0061:No Xcode.app specified (using --sdkroot), using the system Xcode as reported by 'xcode-select --print-path': /Applications/Xcode.app/Contents/Developer 

通常由 Visual Studio for Mac 执行这些步骤。

## <a name="reference"></a>参考

请参阅 [mtouch(1)](http://docs.go-mono.com/?link=man%3amtouch(1)) 手册页获取有关其他命令行选项的详细信息。

