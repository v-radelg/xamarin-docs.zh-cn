---
title: 为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: ce318faab1af07b95a7769d81a506979b37a34e4
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73843004"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？

Xamarin 目前不支持 XAML 文件的可视化设计器。 因此，当尝试使用编码在 Visual Studio 的*XAML Ui 设计器*或*Xaml ui 设计器*中打开窗体 XAML 文件时，将引发以下错误消息：

> "无法用所选编辑器打开此文件。 请选择另一个编辑器。 "

[Xamarin](~/xamarin-forms/xaml/xaml-basics/index.md)中介绍了这种限制：

> "尚未提供用于在 Xamarin. Forms 应用程序中生成 XAML 的可视化设计器，因此所有 XAML 都必须手动编写。"

但是，可以通过选择 **> 其他 Windows > Xamarin 预览**器菜单选项的视图来显示 Xamarin。窗体 XAML 预览器。
