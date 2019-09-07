---
title: Xamarin 中的嵌入式框架
description: 本文档介绍如何在 Xamarin iOS 应用程序中与嵌入框架共享代码。 可以通过 mtouch 工具或本机引用完成此操作。
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: ba3be4fea9999698c5a81faf5b07bec99fb1aa46
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753242"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Xamarin 中的嵌入式框架

_本文档介绍应用程序开发人员如何在其应用程序中嵌入用户框架。_

使用 iOS 8.0 Apple，可以创建嵌入框架，以便在应用扩展和 Xcode 中的主应用之间共享代码。

Xamarin 9.0 添加了对在 Xamarin iOS 应用中使用这些嵌入式框架（使用 Xcode 创建）的支持。 ***不**能从任何类型的 Xamarin iOS 项目创建嵌入的框架，只会使用现有的本机（目标-C）框架。*

可以通过两种方法在 Xamarin 中使用框架：

- 通过将以下内容添加到项目的**IOS 生成**选项中的其他 mtouch 参数，将框架传递给 mtouch 工具：

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  必须为每个项目配置设置此项。

- 从上下文菜单添加本机引用

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

右键单击 "项目" 并浏览以添加本机引用

![](embedded-frameworks-images/xam-native-refs.png "选择 \"添加本机引用 Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

右键单击 "项目" 并浏览以添加本机引用

![](embedded-frameworks-images/vs-native-refs.png "选择 \"在 Visual Studio 中添加本机引用\"")

-----

  这适用于所有配置。

在的未来 Visual Studio for Mac 版本中，可以在 IDE 中使用框架（不需要手动编辑项目文件），而使用 Visual Studio 的 Xamarin 工具。

可在[github](https://github.com/rolfbjarne/embedded-frameworks)上找到几个示例项目

## <a name="limitations"></a>限制

- 嵌入框架仅在[统一](~/cross-platform/macios/unified/index.md)项目中受支持。
- 仅在部署目标至少为 iOS 8.0 的项目中支持嵌入式框架。
- 如果扩展插件需要嵌入框架，则容器应用还必须引用框架，否则，该框架将不会包含在应用捆绑包中。

## <a name="the-mono-runtime"></a>Mono 运行时

在内部，Xamarin 利用此功能以框架的形式链接到 Mono 运行时，而不是静态地将 Mono 运行时链接到每个扩展和容器应用。

如果容器应用是统一应用，它包含扩展，并且目标部署为 iOS 8.0 或更高版本，则会自动执行此操作。

不带扩展的应用程序仍将以静态方式链接到 Mono 运行时，因为如果只有一个应用引用它，则使用框架的大小会有很大的影响。

应用开发人员可以重写此行为，方法是将以下内容作为附加的 mtouch 参数添加到项目的 iOS 生成选项中：

- `--mono:static`：以静态方式链接到 Mono 运行时。
- `--mono:framework`：将 Mono 运行时作为框架的链接。

将 Mono 运行时链接为框架的一种情况是，即使对于不带扩展的应用程序，也要减小可执行文件大小，以克服 Apple 强制执行的任何大小限制。 作为参考，Mono 运行时为每个体系结构添加大约 1.7 MB （与 Xamarin 8.12 相比，其版本不同，甚至在应用之间有所不同）。 Mono 框架每个体系结构大约增加了 2.3 MB，这意味着，对于没有任何扩展的单体系结构应用程序，使应用程序与 Mono 运行时作为框架进行链接会将可执行文件收缩 ~ 1.7 MB，但会添加 ~ 2.3 MB 框架，从而在大约 0.6 MB 的应用一起或者中。
