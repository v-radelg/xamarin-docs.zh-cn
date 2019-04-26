---
title: 更新到 64 位 Xamarin.Mac 统一应用程序
description: 本指南介绍如何更新到面向 64 位 Xamarin.Mac 应用程序。 它还提供时进行此更改可能会遇到的错误种类的示例。
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 9bd70fec5d6d3bbbc4855980e1542bd4e486acaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266705"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>更新到 64 位 Xamarin.Mac 统一应用程序

截至 2018 年 1 月，Apple 要求新[Mac App Store 提交针对 64 位](https://developer.apple.com/news/?id=06282017a)。 按 2018 年 6 月，已提供在 Mac App Store 上的应用必须将更新为面向 64 位。

**文件** > **新建**Xamarin.Mac 项目模板默认情况下，创建 64 位应用程序，因此所有最近创建的应用已兼容于 64 位，且不需要进行任何更改。

## <a name="targeting-64-bit"></a>面向 64 位

1. 打开**项目选项**的 Xamarin.Mac 应用的窗口：

   ![项目的上下文菜单](mac-64-bit-images/1-contextual_menu-vsmac.png "项目的上下文菜单")

2. 选择**Mac 生成**并设置**支持的体系结构**到**x86\_64**:

   [![将支持的体系结构设置为 x86_64](mac-64-bit-images/2-project_options-vsmac.png "到 x86_64 设置支持的体系结构")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. 如果您的应用程序具有任何外部依赖项如本机引用或绑定的项目，将它们更新为面向 64 位。

### <a name="errors"></a>错误

第一次生成或运行应用程序与 64 位支持，您可能会遇到来自 clang 或运行时问题的链接错误。 都会发生这些错误，如果第三方依赖项 — 例如，本机引用 Xamarin.Mac 或绑定项目或手动加载整个系统的框架中，尚未更新到 64 位。

> [!TIP]
> 将项目转换为 64 位进行了重大更改，可能会间接发现各种编程错误。 具体而言，它可能会更改的大小和数据结构，这会影响的 p/invoke 签名和链接在项目中的本机代码的对齐方式。 请考虑查看任何生成警告并全面测试您的应用程序之后，若要发现潜在问题。

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>示例中的动态链接第三方依赖项不面向 64 位导致错误：

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

此错误可能在运行时通过后接`dlopen`返回`IntPtr.Zero`而不是预期的句柄。

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>示例的静态链接第三方依赖项不面向 64 位导致错误：

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

若要生成并运行成功，这些依赖项更新到 64 位，并重新编译您的应用程序。

