---
title: 为什么我的 iOS 9 应用程序失败，出现以下错误： system.exception：无法封送目标 C 对象？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 1bb67eaa884e523e96ef1015daaa6b959ea1512d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031097"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>为什么我的 iOS 9 应用程序失败，出现以下错误： system.exception：无法封送目标 C 对象？

你可能会看到以下格式的错误：

> System.exception：无法封送目标 C 对象 。找不到此对象的现有托管实例 。

IOS 9 中的 API 更改要求在调用非托管代码时使用回调构造函数，因为基础 API 现在需要用到它。 使用以下行将回调构造函数添加到类： 

`public foo (IntPtr handle) : base (handle)` 

### <a name="next-steps"></a>后续步骤

若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug. 
