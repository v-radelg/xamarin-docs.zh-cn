---
title: 为什么应用程序提交失败，出现以下错误：不允许的路径（Itunesmetadata.plist. info.plist）。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0255c918f7d6e984c9af2b396d9a2a00286286e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030993"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>为什么应用程序提交失败，出现以下错误：不允许的路径（Itunesmetadata.plist. info.plist）。

> 错误：错误 ITMS-90047： "在以下位置找到了禁止的路径（" Itunesmetadata.plist. info.plist "）：负载/iPhoneApp1

此错误是由于 Apple 的 App Store 验证过程发生了更改，以防止用户遇到[Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180)之类的问题。 此特定错误与您已安装的 Xamarin 的特定_版本无关，_ 因此降级_不_会有帮助。

有关此问题的解决方法信息和最新更新，请参阅[此处](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)的论坛讨论。
