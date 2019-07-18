---
title: Xamarin.Mac 链接器选项
description: 本文档介绍了 Xamarin.Mac 中的链接。 链接是一个功能强大的优化工具，可通过删除未使用的代码来缩减应用程序的大小。
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: dd42de4dda4b5d5b09156898fd7a8bab331adf88
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865910"
---
# <a name="xamarinmac-linker-options"></a>Xamarin.Mac 链接器选项

 链接是一个功能强大的优化工具，可通过删除未使用的代码来缩减应用程序的大小。

## <a name="overview"></a>概述

基于项目所用的[目标框架](~/mac/platform/target-framework.md)，可用的链接器选项可能会受到限制。 这是由于链接需要创建应用程序使用的每种类型的对象图，并且由于 System.Configuration，这不可能在完整版（或不受支持版）中实现。

有四个选项可用：

-  无 – 禁用所有链接。 现代版中的调试配置以及完整版中的全部配置中的默认值。
-  SDK – 链接所有 SDK 程序集，不包括用户程序集。 现代版中发布配置中的默认值。 在完整版中不可用。
-  完整 – 链接所有程序集。 这要求用户代码具有链接器安全性，请参阅[说明](~/ios/deploy-test/linker.md)了解详细信息。 在完整版中不可用。
-  平台 – 仅链接 Xamarin.Mac.dll。 有关详细信息，请参见以下内容。

## <a name="platform-linking"></a>平台链接

使用完整[目标框架](~/mac/platform/target-framework.md)链接应用程序通常是不安全的，但有很多情况需要非常受限的链接形式。

例如，提交到 macOS App Store 的应用程序不得引用大量弃用的 API（如 QTKit)，Xamarin.Mac 包含其中一些的绑定。 即使应用程序未调用这些绑定，调用也会存在于 SDK 中并被拒绝。

平台链接假定应用程序和 BCL 不具备链接器安全性，只需从 Xamarin.Mac.dll 中删除未使用的代码。 

不反映在 Xamarin.Mac.dll 类型上的任何应用程序均可从删除不必要的类型中看到微小的启动改进。

平台链接通常仅适用于使用完整目标框架的应用程序，因为新式应用程序可以使用功能更强大的 SDK 选项。

## <a name="setting-the-linker-configuration"></a>设置链接器配置

若要更改为 Xamarin.Mac 项目的链接器配置，请执行以下操作：

1. 在 Visual Studio for Mac 中打开 Xamarin.Mac 项目。
2. 在“解决方案资源管理器”   中，双击项目文件打开“项目选项”对话框。
3. 从“Mac 生成”  选项卡中，选择适合应用程序需求的“链接器行为”  ：

    ![选择要使用的链接器行为](linker-images/link-behavior.png "选择要使用的链接器行为")

4. 在未来更新之后，完整目标框架的平台链接才会出现在 IDE 中。 在此之前，请将 `--linkplatform` 添加到“其他 mmp 参数”  。
5. 单击“确定”  按钮保存更改。


## <a name="related-links"></a>相关链接

- [在 iOS 上链接](~/ios/deploy-test/linker.md)
