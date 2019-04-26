---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420532"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe 未找到 rver.BaseCommand.OnRequest

> [!IMPORTANT]
> 最新版本的 Xamarin 中已解决此问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。


## <a name="error-message"></a>错误消息

此错误可能会出现在*Mac 服务器日志*Visual Studio 中：

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

此消息中有 2 个独立的问题：

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    此错误是无害的但它也具有误导性。 它[将删除](https://bugzilla.xamarin.com/show_bug.cgi?id=21667)未来版本中。

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    此错误是真正的问题。 由于到遗憾的是，[限制](https://bugzilla.xamarin.com/show_bug.cgi?id=22080)此异常堆栈跟踪是*不完整*。 如果您注意到 Mac 服务器日志中此类的不完整的堆栈跟踪，可以检查`~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log`上 Mac 生成主机，若要查找完整堆栈跟踪文件。
