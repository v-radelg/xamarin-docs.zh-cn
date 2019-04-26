---
title: IOS 入门
description: 本文档介绍如何开始使用.NET 嵌入 iOS。 它讨论了要求，并且会显示一个示例应用来演示如何将托管程序集绑定和 Xcode 项目中使用的输出。
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 009772ac88ad57bab53fb71c9705b71f0f8acc8b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318677"
---
# <a name="getting-started-with-ios"></a>IOS 入门

## <a name="requirements"></a>要求

除了从要求我们[入门 Objective C](~/tools/dotnet-embedding/get-started/objective-c/index.md)指南中，您还需要：

* [Xamarin.iOS 10.11](https://visualstudio.microsoft.com/xamarin/)或更高版本

## <a name="hello-world"></a>Hello world

首先，构建在 C# 中的简单你好 world 示例。

### <a name="create-c-sample"></a>创建 C# 示例

打开 Visual Studio for Mac 中，创建新的 iOS 类库项目，将其命名**你好从 csharp**，并将其保存到 **~/Projects/hello-from-csharp**。

中的代码替换**MyClass.cs**文件使用以下代码片段：

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

生成项目，并生成的程序集另存为 **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**。

### <a name="bind-the-managed-assembly"></a>绑定的托管程序集

托管程序集后，将其绑定通过调用.NET 嵌入。

如中所述[安装](~/tools/dotnet-embedding/get-started/install/install.md)指南中，这可以作为后期生成步骤，在项目中，使用自定义 MSBuild 目标，或手动：

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

该框架将放入 **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**。

### <a name="use-the-generated-output-in-an-xcode-project"></a>在 Xcode 项目中使用生成的输出

打开 Xcode 中，创建新的 iOS 单视图应用程序，将其命名**你好从 csharp**，然后选择**Objective C**语言。

打开 **~/Projects/hello-from-csharp/output**目录中查找工具，选择**你好从 csharp.framework**，将其拖到 Xcode 项目，并将其放正上方**你好从 csharp**项目文件夹中的。

![拖放到框架](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

请确保**需要时复制项**签入对话框弹出，并单击**完成**。

![需要时复制项](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

选择**你好从 csharp**项目，然后导航到**你好从 csharp**目标的**常规选项卡**。在中**嵌入二进制**部分中，添加**你好从 csharp.framework**。

![嵌入的二进制文件](ios-images/hello-from-csharp-ios-embedded-binaries.png)

打开**ViewController.m**，并将与内容为：

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

.NET 嵌入不当前支持 iOS，对于某些 Xcode 项目模板启用 bitcode。 

在项目设置中禁用它：

![Bitcode 选项](../../images/ios-bitcode-option.png)

最后，运行 Xcode 项目中，并将显示如下所示：

![从 C# 示例在模拟器中运行的 hello](ios-images/hello-from-csharp-ios.png)
