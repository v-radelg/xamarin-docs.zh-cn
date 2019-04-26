---
title: 开始使用 macOS
description: 本文档介绍了如何开始使用和 macOS.NET 嵌入。 它讨论了要求，并且会显示示例应用程序来演示如何进行绑定的托管程序集，并在 Xcode 项目中使用生成的输出。
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61181504"
---
# <a name="getting-started-with-macos"></a>开始使用 macOS

## <a name="what-you-will-need"></a>你将需要什么

* 按照中的说明进行操作[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南。

## <a name="hello-world"></a>Hello world

首先，构建在 C# 中的简单你好 world 示例。

### <a name="create-c-sample"></a>创建 C# 示例

打开 Visual Studio for Mac 中，创建一个名为的新 Mac 类库项目**你好从 csharp**，并将其保存到 **~/Projects/hello-from-csharp**。

中的代码替换**MyClass.cs**文件使用以下代码片段：

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

生成项目。 生成的程序集另存为 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>绑定的托管程序集

托管程序集后，将其绑定通过调用.NET 嵌入。

如中所述[安装](~/tools/dotnet-embedding/get-started/install/install.md)指南中，这可以作为后期生成步骤，在项目中，使用自定义 MSBuild 目标，或手动：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

该框架将放入 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode 并创建新的 Cocoa 应用程序。 其命名为**你好从 csharp** ，然后选择**Objective C**语言。

打开 **~/Projects/hello-from-csharp/output**目录中查找工具，选择**你好从 csharp.framework**，将其拖到 Xcode 项目，并将其放正上方**你好从 csharp**项目文件夹中的。

![拖放到框架](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

请确保**需要时复制项**签入对话框弹出，并单击**完成**。

![需要时复制项](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

选择**你好从 csharp**项目，然后导航到**你好从 csharp**目标的**常规**选项卡。在中**嵌入二进制**部分中，添加**你好从 csharp.framework**。

![嵌入的二进制文件](macos-images/hello-from-csharp-mac-embedded-binaries.png)

打开**ViewController.m**，并将与内容为：

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

最后，运行 Xcode 项目中，并将显示如下所示：

![从 C# 示例在模拟器中运行的 hello](macos-images/hello-from-csharp-mac.png)

比较完整和更好看示例[提供了](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather)。
