---
title: 含有 RegisterServicePort 的 iOS 设计器错误
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fc4c143d6b5f7c211d24e6e3ed2ed3bb8d264410
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421965"
---
# <a name="ios-designer-error-with-registerserviceport"></a>含有 RegisterServicePort 的 iOS 设计器错误

## <a name="sample-error"></a>示例错误
> System.AggregateException:---> System.SystemException 发生了一个或多个错误：RegisterServicePort （com.xamarin.MTHosting.2a0b1，com.apple.PowerManagement.control）：返回的内核:-308 (-308): (ipc/mig) 服务器已停机

## <a name="explanation"></a>说明
使用错误`RegisterServicePort`和类似如上面的错误消息通常是间谍软件/恶意软件的计算机上的问题。 请考虑[此 bug 报表上的注释](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4)有关详细信息，以及指向[Apple 论坛讨论](https://discussions.apple.com/thread/5596008)如何删除可能感染。 

若要帮助诊断此问题，请打开 macOS 应用程序**控制台**，并删除每个文件内的**用户诊断报告**部分[ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy)。 然后启动 Visual Studio for Mac，尝试使用设计器。 如果在设计器无法初始化后，任何新日志文件将显示在本部分中，请保存这些密钥以供分析。  

请注意要检查的最重要一点是此文件： 
> /usr/lib/libimckit.dylib

上面的结果，无论该文件存在，如果前面提到的间谍软件/恶意软件问题是存在于您的计算机上。  

下面的链接已删除此间谍软件/恶意软件的步骤： [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

