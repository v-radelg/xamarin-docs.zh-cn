---
title: 使用 Xamarin.Mac 绑定适用于控制台应用
description: 创建使用 Xamarin.Mac 访问本机 macOS Api 的无外设的控制台应用程序。
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378271"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>在控制台应用程序中的 Xamarin.Mac 绑定

某些情况下，你想要使用 C# 中某些 Apple 本机 Api 构建的无外设的应用程序&ndash;一个不具有用户界面&ndash;使用 C#。

Mac 应用程序的项目模板包括对`NSApplication.Init()`调用后跟`NSApplication.Main(args)`，它通常如下所示：

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

在调用`Init`准备 Xamarin.Mac 运行时，调用`Main(args)`启动 Cocoa 应用程序主循环中，该准备应用程序接收键盘和鼠标事件并显示你的应用程序的主窗口。   在调用`Main`也会尝试查找 Cocoa 的资源，请准备顶级窗口和预期要的应用程序捆绑包一部分的程序 (在目录中分发的程序`.app`扩展和特定布局)。

无外设的应用程序不需要用户接口，并不需要运行应用程序捆绑包的一部分。

## <a name="creating-the-console-app"></a>创建控制台应用程序

因此最好使用常规的.NET 控制台项目类型启动。

您需要做一些事情：

- 创建一个空项目。
- Xamarin.Mac.dll 库的引用。
- 引入你的项目的非托管依赖项。

下面更详细地说明了这些步骤：

### <a name="create-an-empty-console-project"></a>创建一个空的控制台项目

创建新的.NET 控制台项目，请确保它是.NET 和不是.NET Core，作为 Xamarin.Mac.dll 之下的.NET Core 运行时不运行，则只有与 Mono 运行时运行。

### <a name="reference-the-xamarinmac-library"></a>引用 Xamarin.Mac 库

若要编译代码，您将想要引用`Xamarin.Mac.dll`从该目录的程序集： `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

若要执行此操作，请转到选择的项目引用 **.NET 程序集**选项卡，然后单击**浏览**按钮以查找文件系统上的文件。  导航到更高版本，路径，然后选择**Xamarin.Mac.dll**该目录中。

这将授予你访问 Cocoa Api 在编译时。   此时，您可以添加`using AppKit`的文件，并调用页首`NSApplication.Init()`方法。   还有一个步骤之前可以运行应用程序。

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>非托管的支持库放入项目

将运行你的应用程序之前，需要将`Xamarin.Mac`支持库到你的项目。   若要执行此操作，请将新文件添加到你的项目 (项目选项中选择**添加**，然后**添加现有文件**) 并导航到此目录：

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

在这里，选择的文件**libxammac.dylib**。   将提供一个选择的复制、 链接或移动。   我个人喜欢的链接，但复制也适用。    则需要选择该文件，并在属性面板 (选择**视图 > 面板 > 属性**如果属性面板将不可见)，请转到**构建**部分，然后设置**将复制到输出目录**设置为**如果较新则复制**。

你现在可以运行在 Xamarin.Mac 应用程序。

您的 bin 目录中的结果将如下所示：

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

若要运行此应用，将需要相同的目录中的所有这些文件。

## <a name="building-a-standalone-application-for-distribution"></a>构建用于分发的独立应用程序

你可能想要分发给用户的单个可执行文件。  若要执行此操作，可以使用`mkbundle`工具以将各种文件转换为独立的可执行文件。

首先，请确保你的应用程序编译和运行。   结果感到满意后，你可以从命令行运行以下命令：

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

在上面的命令行调用选项`-o`是用来指定生成的输出，在这种情况下，我们传递`/tmp/consoleapp`。   这现在是独立的应用程序，可以将分发和 Mono 或 Xamarin.Mac 上没有外部依赖关系，它是一个完全自包含可执行文件。

手动指定的命令行**machine.config**要使用，文件和系统级库映射配置文件。   它们不是必需的所有应用程序，但因为使用.NET 的更多的功能时需要用很方便地将它们，绑定

## <a name="project-less-builds"></a>无项目生成

不需要完整的项目创建一个自包含的 Xamarin.Mac 应用程序，还可以使用简单的 Unix 生成文件来完成工作。   下面的示例演示如何安装程序的一个简单的命令行应用程序生成文件：

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)


bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

上述`Makefile`提供了三个目标：

- `make` 将生成程序
- `make run` 将生成并运行当前目录中的程序
- `make bundle` 将创建一个自包含的可执行文件
