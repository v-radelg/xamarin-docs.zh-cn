---
title: 将 Xamarin Mac 统一应用程序更新到64位
description: 本指南介绍如何将 Xamarin 应用程序更新为面向64位。 它还提供了进行此更改时可能遇到的错误种类的示例。
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 5539bab417c5efc0064cd1753cb74c7524463ee5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70765912"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>将 Xamarin Mac 统一应用程序更新到64位

从2018年1月起，Apple 要求新的[Mac 应用商店提交目标为64位](https://developer.apple.com/news/?id=06282017a)。 Mac App Store 上已有的应用必须更新到 6 2018 月64位的目标。

默认情况下，"**新建**Xamarin" 项目模板  > **文件**会创建64位应用程序，因此，任何最近创建的应用都已与64兼容，不需要进行任何更改。

## <a name="targeting-64-bit"></a>面向64位

1. 打开 Xamarin 应用程序的 "**项目选项**" 窗口：

   ![项目的上下文菜单](mac-64-bit-images/1-contextual_menu-vsmac.png "项目的上下文菜单")

2. 选择 " **Mac 生成**" 并将**支持的体系结构**设置为**x86 \_64**：

   [![将支持的体系结构设置为 x86_64](mac-64-bit-images/2-project_options-vsmac.png "将支持的体系结构设置为 x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果你的应用程序具有任何外部依赖关系（如本机引用或绑定项目），请将其更新为面向64位。

### <a name="errors"></a>错误

首次生成或运行具有64位支持的应用程序时，可能会遇到来自 clang 或运行时问题的链接错误。 如果第三方依赖项（例如，Xamarin 或绑定项目中的本机引用或手动加载的系统范围框架）未更新为64位，则会出现这些错误。

> [!TIP]
> 将项目转换为64位是一项重大更改，可能会间接发现各种编程错误。 特别是，它可能会更改数据结构的大小和对齐方式，这将影响项目中链接的 p/invoke 签名和本机代码。 考虑查看给定的任何生成警告，并在以后完全测试应用程序以捕获潜在问题。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>由于动态链接的第三方依赖项（不以64位为目标）导致的示例错误：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

@No__t_0 返回 `IntPtr.Zero` 而不是预期的句柄，则在运行时可能会出现此错误。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>由于静态链接的第三方依赖项（不以64位为目标）导致的示例错误：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要成功生成并运行，请将这些依赖项更新为64位并重新编译应用程序。
