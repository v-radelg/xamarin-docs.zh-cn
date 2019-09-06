---
title: IOS 入门
description: 本文档介绍如何开始使用 iOS 的 .NET 嵌入。 它讨论了要求，并提供了一个示例应用程序，用于演示如何绑定托管程序集并使用 Xcode 项目中的输出。
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: d5bde89ed90e55724fbc25fc473e265affa9ce2f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292947"
---
# <a name="getting-started-with-ios"></a>IOS 入门

## <a name="requirements"></a>要求

除了从我们的入门[-C 指南开始](~/tools/dotnet-embedding/get-started/objective-c/index.md)，还需要：

* [Xamarin 10.11](https://visualstudio.microsoft.com/xamarin/)或更高版本

## <a name="hello-world"></a>Hello world

首先，在中C#生成一个简单的 hello world 示例。

### <a name="create-c-sample"></a>创建C#示例

打开 Visual Studio for Mac，创建新的 iOS 类库项目，将其命名为 " **hello-从-csharp**"，并将其保存到 **~/Projects/hello-from-csharp**。

将**MyClass.cs**文件中的代码替换为以下代码片段：

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

生成项目，并将生成的程序集保存为 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>绑定托管程序集

有了托管程序集后，可通过调用 .NET 嵌入进行绑定。

如[安装](~/tools/dotnet-embedding/get-started/install/install.md)指南中所述，可以在项目中使用自定义 MSBuild 目标来完成生成后步骤，也可以手动执行此操作：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

将在 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**中放置该框架。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode，创建新的 iOS 单一视图应用程序，将其命名**为 "hello**-o"，并选择 "**目标 C** " 语言。

在查找器中打开 **~/Projects/hello-from-csharp/output**目录，选择 " **hello**-"，将其拖到 "Xcode" 项目，并将其放置在项目中的 " **hello-csharp** " 文件夹的紧上方。

![拖放框架](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

请确保在弹出的对话框中选中 "**需要时复制项**"，然后单击 "**完成**"。

![根据需要复制项](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

选择 " **hello-csharp** " 项目，并导航到 " **hello-csharp** " 目标的 "**常规" 选项卡**。在 "**嵌入的二进制文件**" 部分中，添加 " **hello-csharp**"。

![嵌入的二进制文件](ios-images/hello-from-csharp-ios-embedded-binaries.png)

打开**ViewController**，并将内容替换为：

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

.NET 嵌入目前不支持 iOS 上的 bitcode，对某些 Xcode 项目模板启用了此功能。 

在项目设置中禁用它：

![Bitcode 选项](../../images/ios-bitcode-option.png)

最后，运行 Xcode 项目，如下所示：

![在模拟器C#中运行的示例 Hello](ios-images/hello-from-csharp-ios.png)
