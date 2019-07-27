---
title: 调试集成
description: 本文档介绍如何在 Windows 和 Mac 上调试代理端和客户端 Xamarin Workbooks 集成。
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 6b89c0855b35a10a2afcbb69c4a011079c2aaf1d
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511865"
---
# <a name="debugging-integrations"></a>调试集成

## <a name="debugging-agent-side-integrations"></a>调试代理端集成

调试代理端集成的最佳方式是使用中`Log` `Xamarin.Interactive.Logging`类提供的日志记录方法。

在 macOS 上, 日志消息显示在日志查看器菜单 (**窗口 > 日志查看器**) 和客户端日志中。 在 Windows 上, 消息只显示在客户端日志中, 因为没有日志查看器。

客户端日志位于 macOS 和 Windows 上的以下位置:

- Mac`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

需要注意的一点是, 在开发过程中通过常用`#r`机制加载集成时, 集成程序集将被选取为工作簿的_依赖项_, 并在不使用绝对路径的情况下将其打包。 这可能会导致更改显示为不会传播, 就好像重新生成集成无关。

## <a name="debugging-client-side-integrations"></a>调试客户端集成

当客户端集成用 JavaScript 编写并加载到我们的 web 浏览器图面 (请参阅[体系结构](~/tools/workbooks/sdk/architecture.md)文档) 时, 调试它们的最佳方式是使用 Mac 上的 WebKit 开发人员工具, 或在 Windows 上使用 F12 选择器。

这两组工具都允许您查看 JavaScript/TypeScript 源、设置断点、查看控制台输出, 以及检查和修改 DOM。

### <a name="mac"></a>Mac

若要为 Mac 上的 Xamarin Workbooks 启用开发人员工具, 请在终端中运行以下命令:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

然后重新启动 Xamarin Workbooks。 完成此操作后, 你应看到 "**检查元素**" 显示在右键单击上下文菜单中, 新的 "**开发人员**" 窗格将在工作簿首选项中可用。 此选项允许你选择是否要在启动时打开开发人员工具:

[![开发人员窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

此首选项也只是重新启动–您需要重新启动工作簿客户端, 才能使其在新工作簿中生效。 通过上下文菜单或首选项激活开发人员工具将显示熟悉的 Safari UI:

[![Safari 开发工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

有关使用 Safari 开发人员工具的信息, 请参阅[WebKit 检查器文档][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows 上, IE 团队提供了称为 "F12 选择器" 的工具, 它是嵌入式 Internet Explorer 实例的远程调试器。 可以在以下位置找到该工具:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

运行 F12 选择器, 应会看到在列表中显示工作簿客户端图面的嵌入实例。 选择它, 并且 Internet Explorer 中的熟悉的 F12 调试工具将显示, 并附加到客户端:

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
