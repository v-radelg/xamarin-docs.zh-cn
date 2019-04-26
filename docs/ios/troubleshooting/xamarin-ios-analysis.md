---
title: Xamarin.iOS 分析规则
description: 本文档介绍一系列检查 Xamarin.iOS 项目设置，以帮助确定更/better optimized 设置是否可用的分析规则。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 8a4990ce7b2bcacbd4b97b214458531b3d94122e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416378"
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析规则

Xamarin.iOS 分析是一组检查你的项目设置以帮助你确定如果提供了更好/更优化的设置的规则。

分析规则的运行频率可能在早期查找可能的改善措施并节省开发时间。

若要运行规则，在 Visual Studio for Mac 的菜单中，选择**项目 > 运行代码分析**。

> [!NOTE]
> Xamarin.iOS 分析仅在你当前所选的配置上运行。 我们强烈建议运行的调试工具**和**发布配置。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001:DisabledLinkerRule

- **问题：** 链接器已禁用调试模式下的设备上。
- **解决方法：** 应尝试使用链接器以避免任何惊喜运行你的代码。
若要将它设置，请转到项目 > iOS 生成 > 链接器行为。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002:TestCloudAgentReleaseRule

- **问题：** 应用初始化测试云代理的生成将 Apple 提交时，被拒绝，因为它们使用私有 API。
- **解决方法：** 添加或修复需要 #if 并在代码中定义。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003:IPADebugBuildsRule

- **问题：** 使用签名密钥的开发人员的调试配置不应生成 IPA 如只有所需的分发，现在将使用发布向导。
- **解决方法：** 禁用调试配置项目选项中的生成 IPA。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004:Missing64BitSupportRule

- **问题：** 支持的体系结构的"发布 |设备"不是 64 位兼容，缺少 ARM64。 这是一个问题，如 Apple 不接受 32 位只有 iOS 应用商店中的应用。
- **解决方法：** 双击你的 iOS 项目，请转到生成 > iOS 生成并更改支持的体系结构，因此它具有 ARM64。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005:Float32Rule

- **问题：** 不使用 float32 选项 (-aot 选项 =-O = float32) 会带来巨大的额外的性能成本，尤其是在移动设备、 双精度算术是显著降低速度较慢。 请注意，.NET 使用双精度在内部，甚至对于 float，因此启用此选项会影响精度以及可能的兼容性。
- **解决方法：** 双击你的 iOS 项目，请转到生成 > iOS 生成，并取消选中"按 64 位浮点执行所有 32-bit 浮动操作"。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006:HttpClientAvoidManaged

- **问题：** 我们建议使用本机 HttpClient 处理程序而不托管一个更好的性能，较小的可执行文件大小，并轻松地支持较新的标准。
- **解决方法：** 双击你的 iOS 项目，请转到生成 > iOS 生成和 HttpClient 实现更改为 NSUrlSession (iOS 7 +) 或 CFNetwork 以支持 iOS 7 之前的版本。
