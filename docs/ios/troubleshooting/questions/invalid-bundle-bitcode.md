---
title: 提交到 App Store 时出错："无效的捆绑包-在提交中检测不到 bitcode 中的选项"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 84244e0c4c24a8ca6ac71a79de963bedf5c1ee68
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292538"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交到 App Store 时出错："无效的捆绑包-在提交中检测不到 bitcode 中的选项"

watchOS apps 和 tvOS apps 在提交到 App Store 时_需要_bitcode。 使用 Xcode 8.3 或更早版本生成和提交 watchOS 和 tvOS 应用时，尝试上传到应用商店时可能会发生以下错误（通过电子邮件通知）：

>无效的捆绑包-无法处理应用，因为在提交中检测到不允许嵌入到 bitcode 中的选项。 您可能不会在 Xcode 中提供的工具链生成应用程序。

此问题的解决方法是通过 Xcode 9 和最新版本的 Xamarin 生成应用程序。
