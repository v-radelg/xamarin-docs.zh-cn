---
title: 如何降级 NuGet 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 206336cbcdc85e5e2f3f010e947981cb96e7cd1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344557"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>如何降级 NuGet 包？

Visual Studio for Mac 和 Visual Studio 这两个具有选择较旧版本的包和自动保存功能。 安装它们的功能类似于如何更新包的工作原理。 下面介绍这些步骤。

## <a name="visual-studio"></a>Visual Studio

1. 转到**工具 > NuGet 包管理器 > 程序包管理器控制台**
2. 设置下的项目**默认项目**
3. 使用以下语法：

    > Install-Package [PackageName] -Version [tab for version menu]

您还可以复制/粘贴包的 NuGet 页中的确切命令。 适用于 Xamarin.Forms 的示例： [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 在项目中，右键单击包文件夹和选择**添加包**
2. 在 searchbar，可以使用以下语法搜索所需的包：

    `[PackageName] version:*`

### <a name="examples"></a>示例 
- 列出所有 Xamarin.Forms 包： 

    `Xamarin.Forms version:`

- 列出所有 Xamarin.Forms 1.4.x 包： 

    `Xamarin.Forms version:1.4`

*注意：如果添加之间有空格`version:`和版本号，搜索的行为就好像未指定版本。*
