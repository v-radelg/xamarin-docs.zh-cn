---
title: 可以使用的较旧版本的 Xcode 或 Xamarin.iOS
description: 本指南概述了与使用旧版本的 Xamarin.iOS 或 Xcode （比当前稳定版本） 的问题。
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 2a208d39454a33adc849bcccc66802361693e82e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419310"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>可以使用的较旧版本的 Xcode 或 Xamarin.iOS？

Xamarin 文档假设使用最新的 Xamarin.iOS 和 Xcode 中，我们建议这样做。 但是，某些客户想要使用较旧 Xamarin.iOS 和/或 Xcode，并想要的结果的详细信息。

发行说明包含以下警告：

> [!WARNING]
> **使用较旧的 Xcode 版本**
>
> 使用较旧的 Xcode 版本 (比在上面所述[要求](https://docs.microsoft.com/xamarin/ios/release-notes/12/12.8#requirements)) 通常是可行的但某些功能可能不可用。 此外一些限制需要解决方法，例如：
>
> - 静态注册机构需要 Xcode 标头文件生成应用程序，从而导致[ `MT0091` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT0091)或[ `MT4109` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT4109)如果缺少 Api 的错误。 在大多数情况下启用托管链接器将帮助 （通过删除 API）。
> - Bitcode 生成服务 （适用于 tvOS 和 watchOS） 可能会提交到 App Store 失败，除非使用 Xcode 9.0 + 工具链。

## <a name="further-information"></a>详细信息

Microsoft 强烈建议使用最新的 Xcode 和最新的 Xamarin.iOS 版本开发和提交应用程序时。 Apple 要求提交应用程序时使用最新的 Xcode。

请注意，使用最新的 Xcode 不会阻止从面向较旧 iOS 版本应用程序。 支持的 iOS 版本基于你**Info.plist**条目和应用程序使用的 Api。

可以安装多个版本的 Xcode 并排地使用不同的名称，如**Xcode101.app**并**Xcode102.app**。 如果使用多个版本，请确保设置 active Xcode [Visual Studio for Mac 设置](~/ios/troubleshooting/questions/ios-sdk.md)和与[ `xcode-select` ](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [命令行工具](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_)。

但是，极少数情况下可能需要使用较旧的组件。 本文档将介绍您可能会遇到时的一般经验使用早于最新版本。

从 Apple 每个版本，是唯一的可能会遇到本文未提供其他缺陷。

这些挑战，有时会非常用来解决，这样每当尽可能坚持使用的最新的 Xcode 和最新的 Xamarin.iOS 支持的配置。

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>与旧 Xcode 旧 Xamarin.iOS 的使用

不更新 Xamarin.iOS 和 Xcode 是有可能，至少为一段时间。 限制是，在某些时候，Apple 将需要的最低版本为 Xcode 提交您的应用程序。 此时应更新到最新版本 （或新、 最小版本的 Xcode Apple 和匹配的 Xamarin.iOS 版本所需的） 所有组件 (macOS、 Xcode 和 Xamarin.iOS)。

它是通常可以容易地逐步更新并及时了解较小的更改。 对于大型项目中的更新很难跟上，留在已知的工作集可以是很好地折衷。

## <a name="use-of-new-xamarinios-with-older-xcode"></a>与较旧的 Xcode 新 Xamarin.iOS 的使用

一般情况下，Xamarin.iOS 支持较旧的 Xcode 版本，只要合理可行的。 一些可能遇到的难题包括：

- 较新的 Xamarin.iOS 可能会支持某些功能，在所选 Xcode 中不存在的 Api。 
- **静态注册机构**需要 Xcode 标头文件生成应用程序，从而导致[ `MT0091` ](~/ios/troubleshooting/mtouch-errors.md#MT0091)或[ `MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109)如果缺少 Api 的错误。
  - 在大多数情况下启用托管链接器将它帮助 （通过删除新的 API 的托管的绑定） 如果未使用。
- Bitcode 生成服务 （适用于 tvOS 和 watchOS） 可能会提交到 App Store 失败，除非使用 Xcode 9.0 + 工具链。

## <a name="use-of-new-xcode-with-older-xamarinios"></a>使用较旧的 Xamarin.iOS 的新 Xcode 的使用

此用例是大大增强其难度，因为 Xamarin.iOS 不可以预测的新 Xcode 不断变化的要求。 MacOS 的更新可能还会产生问题，并不兼容性修补程序的情况下的 Xamarin.iOS 的许多部分可能会受到影响。 

有许多潜在方面这时事情可能会错误包括：

- 与不兼容`mlaunch`:
  - 模拟器支持可能无法正确 （或所有）
  - 设备支持可能无法正确 （或所有）
- 未知的支持 `mtouch` 
  - 新框架不支持
  - 不支持新的权利
  - 不支持新的或更新工具
    - 这可能会影响代码以及签名

### <a name="new-appstore-submission-rules"></a>新的应用商店提交规则

Apple 保留随时更新部署到应用商店提交规则的权利。 这些规则的更改只在某些时候预先宣布推出。 这些更改的某些需要工具更改为支持，这要求更新的 Xamarin.iOS 组件。

除了规则更改，Apple 通常将其他验证添加到已提交的应用，或提高了现有的。 其中一些需要我们的工具 （例如新已列入阻止名单符号） 中的更改。 其中许多首次遇到由客户提交，因为没有规则没有公告 （或列表）。

## <a name="summary"></a>总结

只要有可能，为保持安全以下 Apple 的指南和上开发并提交与最新的 Xcode App Store 上发布。

反过来，使用最新的 Xamarin.iOS 发布。 这将跟踪可能会影响已提交的应用程序并符合的最新的规则更改的最新修补程序。

如果这不可行，应考虑使用匹配的较旧 Xcode 和 Xamarin.iOS 版本。 这可正常时间内，但在某些时候 Apple 会要求在较新工具时相应地规划。
