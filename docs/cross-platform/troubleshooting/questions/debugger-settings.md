---
title: 调试器需要哪些项目设置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3A024E4E-ACA3-4C7A-ADEF-541665D15779
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: e7a383f899fab0400104493fa89b125788d610aa
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831333"
---
# <a name="what-project-settings-are-required-for-the-debugger"></a>调试器需要哪些项目设置？

为了使调试器能够正常运行 （命中的断点、 显示调试日志等），开发人员检测和调试信息显示必须同时启用。

请按照以下步骤来检查您的环境设置：

## <a name="visual-studio"></a>Visual Studio
1. 打开项目选项
2. 转到**生成 > 高级...** 将调试信息设置为**完整**
3. 每个平台的的设置：
   - 转到**Android 选项 > 调试选项**。 刻度线**启用开发者检测**框。
   - 转到**iOS 调试 > 调试和检测**。 刻度线**启用调试**框。

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac
1. 打开项目选项
2. 转到**生成 > 编译器 > 常规选项**。 将调试信息设置为**完整**
3. 每个平台的的设置：
    - 转到**生成 > Android 生成 > 调试选项**。 刻度线**启用开发者检测**框。
    - 转到**生成 > iOS 调试**。 刻度线**启用调试**框。

