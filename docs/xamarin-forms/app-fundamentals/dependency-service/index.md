---
title: Xamarin.Forms DependencyService
description: Xamarin.Forms DependencyService 类是允许 Xamarin.Forms 应用程序从共享代码调用本机平台功能的服务定位器。
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650445"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 类是允许 Xamarin.Forms 应用程序从共享代码调用本机平台功能的服务定位器。

## <a name="registration-and-resolutionregistration-and-resolutionmd"></a>[注册及解决方法](registration-and-resolution.md)

必须使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 注册平台实现，然后从共享代码进行解析，才能调用它们。

## <a name="picking-a-photo-from-the-libraryphoto-pickermd"></a>[从库中选取照片](photo-picker.md)

本文介绍如何使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 类从手机的图片库中选取照片。
