---
title: 提交到 App Store 时的错误："捆绑无效-不允许在 bitcode 中嵌入的选项中检测到的提交"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 867ad29abfa6a38971b60ac9ebf181905949dafd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421724"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>提交到 App Store 时的错误："捆绑无效-不允许在 bitcode 中嵌入的选项中检测到的提交"

watchOS 应用和 tvOS 应用_需要_bitcode 提交到 App Store 时。 以下的错误时生成并提交 watchOS 和 tvOS 应用使用 Xcode 8.3 或更早版本，可能会发生 （通过电子邮件通知） 时尝试将上传到应用商店：

>捆绑无效-不能处理应用程序，因为在提交中检测到不允许在 bitcode 中嵌入的选项。 很可能不构建应用程序与在 Xcode 中提供的工具链。

此问题的解决方案是生成使用 Xcode 9 和最新版本的 Xamarin.iOS 应用程序。
