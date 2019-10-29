---
title: Xamarin iOS 分析规则
description: 本文档介绍一组分析规则，这些规则检查 Xamarin 项目设置，以帮助确定是否有更多/更好的优化设置。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: c0f40ea6fc7d429867f90d3d3c1b49dacb63acb5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030868"
---
# <a name="xamarinios-analysis-rules"></a>Xamarin iOS 分析规则

Xamarin iOS 分析是一组规则，用于检查你的项目设置，以帮助你确定是否有更好的优化设置。

尽可能频繁地运行分析规则以查找可能的改进，并节省开发时间。

若要运行规则，请在 Visual Studio for Mac 的菜单中，选择 "**项目 >" 运行代码分析**"。

> [!NOTE]
> Xamarin iOS 分析仅对当前所选的配置运行。 强烈建议运行用于调试**和**发布配置的工具。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **问题：** 在调试模式下，链接器在设备上处于禁用状态。
- **修复：** 应尝试通过链接器运行代码，以避免出现任何意外情况。
若要进行设置，请参阅 Project > iOS Build > 链接器行为。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **问题：** 用于初始化 Test Cloud 代理的应用程序生成将在提交时被 Apple 拒绝，因为它们使用的是私有 API。
- **修复：** 添加或修复必需的 #if，并在代码中定义。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **问题：** 使用开发人员签名密钥的调试配置不应生成 IPA，因为只需要分发，而现在使用发布向导。
- **修复：** 对于调试配置，禁用项目选项中的 IPA 生成。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **问题：** "Release | 支持的体系结构"设备 "不兼容64位，缺少 ARM64。 这是一个问题，因为 Apple 在 AppStore 中不接受仅32位的 iOS 应用。
- **修复：** 双击 iOS 项目，转到 "生成" "生成 >" iOS 生成 "，并更改受支持的体系结构，使其 ARM64。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **问题：** 不使用 float32 选项（--aot-options =-O = float32）会导致性能巨大，尤其是在移动设备上，双精度数学计算显著。 请注意，即使对于 float，.NET 也会在内部使用双精度，因此启用此选项会影响精度和兼容性。
- **修复：** 双击 iOS 项目，中转到 "生成 >" iOS 生成 "，并取消选中" 将所有32位 float 操作作为64位浮点数执行 "。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **问题：** 建议使用本机 HttpClient 处理程序而不是托管的处理程序，以获得更好的性能、较小的可执行文件大小以及轻松支持较新的标准。
- **修复：** 双击 iOS 项目，转到 "生成" "生成 >" iOS 生成 "，并将 HttpClient 实现更改为 NSUrlSession （iOS 7 +）或 CFNetwork，以支持 iOS 7 之前的版本。

<a name="XIA0007" />

## <a name="xia0007-usellvmrule"></a>XIA0007: UseLLVMRule

- **问题：** 对于 "发布 | iPhone" 配置，建议启用 LLVM 编译器，该编译器将生成更快的代码，以生成时的成本执行。
- **修复：** 双击 iOS 项目，前往 "生成 >" iOS 生成 "，并选择" 发布 | iPhone "，然后选中" LLVM 优化编译器 "选项。
