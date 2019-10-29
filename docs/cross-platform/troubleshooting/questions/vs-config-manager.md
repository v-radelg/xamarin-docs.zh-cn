---
title: 为什么 Visual Studio 未将引用的库项目包含在生成中？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: aba2e9dacd930c6302a96a8daf929eadab485922
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012757"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>为什么 Visual Studio 未将引用的库项目包含在生成中？

Visual Studio 使用 **Configuration Manager** 确定解决方案中自动包含在给定版本或部署配置中的项目。

某些使用引用的库项目生成的模板可能已将引用的库包含在配置中，否则需要手动进行设置。

## <a name="how-to-use-the-configuration-manager"></a>如何使用 Configuration Manager

1. 打开“生成”>“Configuration Manager”
2. 选择要自定义的配置，例如 "**调试" | "iPhone** "
3. 选择要包括的项目的复选框。

> [!NOTE]
> 灰显框会自动处理，不需要进行任何更改。

以下步骤的 Screencast： [https://screencast.com/t/zLoQOpEn](https://screencast.com/t/zLoQOpEn)
