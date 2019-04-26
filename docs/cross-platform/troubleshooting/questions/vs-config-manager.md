---
title: 为什么 Visual Studio 未将引用的库项目包含在生成中？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: d7aeac2f433e8fdf231f5887f1537f15e2bd1976
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341303"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>为什么 Visual Studio 未将引用的库项目包含在生成中？

Visual Studio 使用 **Configuration Manager** 确定解决方案中自动包含在给定版本或部署配置中的项目。

某些使用引用的库项目生成的模板可能已将引用的库包含在配置中，否则需要手动进行设置。

## <a name="how-to-use-the-configuration-manager"></a>如何使用 Configuration Manager

1. 打开“生成”>“Configuration Manager”
2. 选择要自定义，例如配置**调试 | iPhone**
3. 选择要包括的项目的复选框。

> [!NOTE]
> 灰色框自动处理和无需任何更改。

这些步骤的截屏视频： [http://screencast.com/t/zLoQOpEn](http://screencast.com/t/zLoQOpEn)
