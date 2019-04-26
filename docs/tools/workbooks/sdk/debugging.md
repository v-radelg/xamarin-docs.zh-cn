---
title: 调试集成
description: 本文档介绍如何调试 Xamarin 工作簿的集成，代理端和客户端上 Windows 和 mac
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293712"
---
# <a name="debugging-integrations"></a>调试集成

## <a name="debugging-agent-side-integrations"></a>调试代理端集成

调试代理端集成最好的做法是通过使用提供的日志记录方法`Log`类中`Xamarin.Interactive.Logging`。 请参阅[ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/)要调用的方法。

在 macOS 上，日志消息出现在日志查看器菜单 (**窗口 > 日志查看器**) 和客户端日志中。 在 Windows 中，消息仅显示在客户端日志中，因为没有任何日志查看器有。

客户端日志是在 macOS 和 Windows 上的以下位置：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

一件事需要注意的是，当加载通过常用的集成`#r`机制在开发过程集成程序集将提取为_依赖项_工作簿，并打包了它，如果绝对路径不使用。 这会导致更改才会出现不传播，如同重新生成集成未执行任何操作。

## <a name="debugging-client-side-integrations"></a>调试客户端的集成

客户端集成以 JavaScript 编写，并将其加载到我们的 web 浏览器图面 (请参阅[体系结构](~/tools/workbooks/sdk/architecture.md)文档)，对其进行调试的最佳方法是在 Mac 上，使用易于使用的功能开发人员工具或在 Windows 上使用 f12 键选择器.

这两个集的工具可查看 JavaScript/TypeScript 源、 设置断点、 查看控制台输出中，并检查和修改 dom。

### <a name="mac"></a>Mac

若要启用 Mac 上的 Xamarin 工作簿的开发人员工具，请在终端中运行以下命令：

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

然后重新启动 Xamarin 工作簿。 一旦这样做，您应看到**检查元素**出现在您右键单击上下文菜单中，和一个新**开发人员**窗格将在工作簿首选项中提供。 此选项可以选择是否要在启动时打开的开发人员工具：

[![开发人员窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

此首选项也是只重新启动，你需要重启以使其生效上新的工作簿的工作簿客户端。 激活通过上下文菜单或首选项的开发人员工具将显示熟悉的 Safari UI:

[![Safari 开发工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

有关使用 Safari 开发人员工具的信息，请参阅[易于使用的功能检查器文档][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows、 IE 团队提供了一个名为"f12 键选择器"工具，它是为嵌入的 Internet 资源管理器实例的远程调试器。 可以在以下位置找到该工具：

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

运行 f12 键选择器，并且应可以看到为列表中的工作簿客户端面提供支持的嵌入的实例。 选择它，并熟悉 F12 调试工具从 Internet 资源管理器不会显示，附加到客户端：

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
