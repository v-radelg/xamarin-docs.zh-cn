---
title: 如何降级 NuGet 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0c70859845915a821bb83b0f9d29528634b1a5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013613"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何降级 NuGet 包？

& Visual Studio 的 Visual Studio for Mac 都具有用于选择旧版本包和自动安装包的功能;与更新包的工作方式类似。 下面介绍了这些步骤。

## <a name="visual-studio"></a>Visual Studio

1. **> 程序包管理器控制台中转到 "工具" > NuGet 包管理器**
2. 在**默认项目**下设置项目
3. 使用以下语法：

    > 安装包 [PackageName]-版本 [版本菜单选项卡]

你还可以从包的 NuGet 页面复制/粘贴准确的命令。 Xamarin 的示例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 在项目中，右键单击 "包" 文件夹 & 选择 "**添加包**"
2. 在 searchbar 中，可以使用以下语法搜索所需的包：

    `[PackageName] version:*`

### <a name="examples"></a>示例 

- 列出所有 Xamarin. Forms 包： 

    `Xamarin.Forms version:`

- 列出所有 Xamarin. Forms 1.4. x 包： 

    `Xamarin.Forms version:1.4`

> [!NOTE]
> 如果在版本号 & `version:` 之间添加一个空格，则搜索的行为就好像未指定任何版本一样。
