---
title: 提交到 App Store 时出错： "无效的捆绑包-不允许嵌入到 bitcode 中的选项在提交中检测"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: c6a03fa3327e81f577f85b05a033d9c97495eb2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031076"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交到 App Store 时出错： "无效的捆绑包-不允许嵌入到 bitcode 中的选项在提交中检测"

watchOS apps 和 tvOS apps 在提交到 App Store 时_需要_bitcode。 使用 Xcode 8.3 或更早版本生成和提交 watchOS 和 tvOS 应用时，尝试上传到应用商店时可能会发生以下错误（通过电子邮件通知）：

>无效的捆绑包-无法处理应用，因为在提交中检测到不允许嵌入到 bitcode 中的选项。 您可能不会在 Xcode 中提供的工具链生成应用程序。

此问题的解决方法是通过 Xcode 9 和最新版本的 Xamarin 生成应用程序。
