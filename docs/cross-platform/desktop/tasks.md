---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常见任务比较
description: 本文档比较了如何在 WPF 和 Xamarin 上执行各种常见任务。 它查看按钮、计时器、字体大小、打开 URI 以及显示操作表。
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290332"
---
# <a name="common-tasks-comparison"></a>常见任务比较

| 任务 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按钮在屏幕上显示一条消息|`MessageBox`|`Page.DisplayAlert`|
|创建计时器|`DispatcherTimer` 类|`Device.StartTimer`静态方法|
|获取默认字体大小|`SystemFonts`静态类|`Device.GetNamedSize`静态方法|
|打开 URI/URL|`Process.Start`|`Device.OpenUri`|
|显示操作表（按钮列表）|n/a|`Page.DisplayActionSheet`|
