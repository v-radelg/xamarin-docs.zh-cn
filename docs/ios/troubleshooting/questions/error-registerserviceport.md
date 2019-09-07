---
title: 含有 RegisterServicePort 的 iOS 设计器错误
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 815eb638b3e394dcfe4b60d716820c610b01f8a1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769327"
---
# <a name="ios-designer-error-with-registerserviceport"></a>含有 RegisterServicePort 的 iOS 设计器错误

## <a name="sample-error"></a>示例错误
> System.AggregateException:发生了一个或多个错误---> SystemException：RegisterServicePort （MTHosting. 2a0b1，，PowerManagement）：返回的内核：-308 （-308）：（ipc/mig-15）服务器终止

## <a name="explanation"></a>说明
错误消息`RegisterServicePort`和类似错误消息通常是计算机上的间谍软件/恶意软件的问题。 有关详细信息，请参阅有关[此错误报告的注释](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)，以及指向有关如何删除可能感染的[Apple 论坛讨论](https://discussions.apple.com/thread/5596008)的链接。 

若要帮助诊断此问题，请打开 macOS 应用程序**控制台**，并删除**用户诊断报告**部分[http://screencast.com/t/y9i3NKcuMy](http://screencast.com/t/y9i3NKcuMy)中的每个文件。 然后启动 Visual Studio for Mac 并尝试使用设计器。 如果设计器未能初始化后，此节中显示了任何新的日志文件，请将其保存供我们分析。  

请注意，要检查的最重要的事项是此文件： 
> /usr/lib/libimckit.dylib

无论以上结果如何，如果该文件存在，则计算机上会出现上述间谍软件/恶意软件问题。  

以下链接包含删除此间谍软件/恶意软件的步骤：[http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  
