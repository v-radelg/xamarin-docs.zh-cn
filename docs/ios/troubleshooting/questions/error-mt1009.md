---
title: 错误 MT1009：无法复制程序集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 1ccf85c03ab1f2cff6428a0120a8ceb4de250761
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031147"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>错误 MT1009：无法复制程序集

> [!IMPORTANT]
> 此问题已在最新版本的 Xamarin 中解决。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

如我们的[发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md)中所述，这是 Xamarin 7.2.6 中的已知问题。 此问题的原因是，如果使用不同的用户帐户安装了 Xamarin iOS，则需要更高权限的文件权限，即开发人员的主帐户。

若要解决此问题，请在 Mac 工作站上打开 Terminal，并运行以下命令：

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
