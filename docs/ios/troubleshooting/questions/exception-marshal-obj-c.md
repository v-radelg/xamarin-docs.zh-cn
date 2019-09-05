---
title: 为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 62a63dc5156d1acf9ad6ca15029978131c151726
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290473"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？

你可能会看到以下格式的错误：

> “System.Exception:未能封送目标 C 对象 。找不到此对象的现有托管实例 。

IOS 9 中的 API 更改要求在调用非托管代码时使用回调构造函数，因为基础 API 现在需要用到它。 使用以下行将回调构造函数添加到类： 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>后续步骤

若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug. 
