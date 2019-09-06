---
title: 为何应用提交失败，且随附：在 ...？上找到了不允许的路径（Itunesmetadata.plist. info.plist）
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291036"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>为何应用提交失败，且随附：在 ...？上找到了不允许的路径（Itunesmetadata.plist. info.plist）

> 条错误 ITMS-90047：在以下位置找到 "不允许的路径（" Itunesmetadata.plist. info.plist "）：负载/iPhoneApp1 "

此错误是由于 Apple 的 App Store 验证过程发生了更改，以防止用户遇到[Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180)之类的问题。 此特定错误与您已安装的 Xamarin 的特定_版本无关，_ 因此降级_不_会有帮助。

有关此问题的解决方法信息和最新更新，请参阅[此处](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)的论坛讨论。
