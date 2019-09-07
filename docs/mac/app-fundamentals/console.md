---
title: 使用控制台应用的 Xamarin 绑定
description: 使用 Xamarin 创建无外设控制台应用，以访问本机 macOS Api。
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769894"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>控制台应用中的 Xamarin 绑定

在某些情况下，你想C#要在中使用一些 Apple 本机 api 来生成无人参与&ndash;应用程序，该应用程序没有使用&ndash; C#的用户界面。

Mac 应用程序的项目模板包括对的调用`NSApplication.Init()` `NSApplication.Main(args)`，然后调用，它通常如下所示：

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

调用可`Init`准备 Xamarin 运行时，调用将`Main(args)`启动 Cocoa 应用程序主循环，从而使应用程序能够接收键盘和鼠标事件，并显示应用程序的主窗口。   对的调用`Main`还将尝试查找 Cocoa 资源，准备 toplevel 窗口，并期望程序成为应用程序捆绑包的一部分（在`.app`包含扩展和非常具体布局的目录中分发的程序）。

无外设应用程序不需要用户界面，无需作为应用程序捆绑包的一部分运行。

## <a name="creating-the-console-app"></a>创建控制台应用

因此最好从常规 .NET 控制台项目类型着手。

你需要执行以下操作：

- 创建一个空项目。
- 引用 Xamarin .dll 库。
- 将非托管依赖项引入你的项目。

下面更详细地说明了这些步骤：

### <a name="create-an-empty-console-project"></a>创建空控制台项目

创建新的 .NET 控制台项目，确保它是 .NET，而不是 .NET Core，因为在 .NET Core 运行时中不运行 Xamarin。

### <a name="reference-the-xamarinmac-library"></a>引用 Xamarin 库

若要编译代码，需要从该目录引用`Xamarin.Mac.dll`程序集：`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

为此，请转到项目引用，选择 " **.Net 程序集**" 选项卡，然后单击 "**浏览**" 按钮以在文件系统上找到该文件。  导航到上述路径，然后从该目录中选择 " **Xamarin** "。

这会使你能够在编译时访问 Cocoa Api。   此时，你可以将添加`using AppKit`到文件的顶部，并`NSApplication.Init()`调用方法。   在运行应用程序之前，只需要执行一个步骤。

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>将非托管支持库引入你的项目

在运行应用程序之前，你需要将支持库`Xamarin.Mac`引入你的项目。   为此，请将新文件添加到项目（在 "项目选项" 中，选择 "**添加**"，然后选择 "**添加现有文件**"）并导航到此目录：

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

在此处，选择文件**libxammac. .dylib**。   系统会提供复制、链接或移动选项。   我个人喜欢链接，但也是复制工作。    然后，你需要选择文件，并在属性板中（选择 "**查看 >" > 属性**"。如果属性板不可见，请参阅"**生成**"部分，并将"**复制到输出目录**"设置为" 复制到输出目录 "设置为"**如果较新则复制**"。

你现在可以运行 Xamarin Mac 应用程序。

你的 bin 目录中的结果将如下所示：

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

若要运行此应用程序，你将需要同一目录中的所有文件。

## <a name="building-a-standalone-application-for-distribution"></a>构建独立的应用程序以进行分发

你可能想要向用户分配一个可执行文件。  为此，可以使用`mkbundle`工具将各种文件转换为独立的可执行文件。

首先，请确保您的应用程序进行编译和运行。   对结果感到满意后，可以从命令行运行以下命令：

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

在上面的命令行调用中，选项`-o`用于指定生成的输出，在此示例中，我们传递`/tmp/consoleapp`了。   现在这是一个独立的应用程序，你可以分发此应用程序，并且不会对 Mono 或 Xamarin 使用外部依赖关系，它是完全独立的可执行文件。

命令行手动指定了要使用的**machine.config**文件以及系统范围的库映射配置文件。   它们不是所有应用程序所必需的，但将其捆绑起来非常方便，因为当你使用 .NET 的更多功能时使用它们

## <a name="project-less-builds"></a>不限项目的生成

若要创建独立的 Xamarin Mac 应用程序，你还可以使用简单的 Unix 生成程序来完成作业。   下面的示例演示如何为简单的命令行应用程序设置生成文件：

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

上述`Makefile`内容提供三个目标：

- `make`将生成程序
- `make run`将在当前目录中生成并运行程序
- `make bundle`将创建一个独立的可执行文件
