---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 1e359d510b1ff92299dc4b9792a2911d5583e66a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769732"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe 未找到 rver.BaseCommand.OnRequest

> [!IMPORTANT]
> 此问题已在最新版本的 Xamarin 中解决。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

## <a name="error-message"></a>错误消息

此错误可能出现在 Visual Studio 中的*Mac 服务器日志*中：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此消息中有2个不同的问题：

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    此错误是无害的，但也是误导性的。 它[将](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)在未来版本中删除。

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    此错误是真正的问题。 不幸的是，由于[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)，此异常堆栈跟踪不*完整*。 如果在 mac 服务器日志中注意到类似于此的不完整堆栈跟踪，则可以`~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log`在 mac 生成主机上检查文件，以查找完整的堆栈跟踪。
