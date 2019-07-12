---
title: 为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 3dbb4d9132d5d94e4533704730e95002b5aec0be
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832273"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？

可能会看到此窗体的错误：

> “System.Exception:无法封送 OBJECTIVE-C 对象...找不到此对象的现有托管的实例...

IOS 9 中的 API 更改需要作为基础 API 调用非托管的代码，需要它时，使用回调构造函数。 使用以下行添加到类回调构造函数： 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>后续步骤

获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。 
