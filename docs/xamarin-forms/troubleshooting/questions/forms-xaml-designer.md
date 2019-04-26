---
title: 为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247790"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>为何 Visual Studio XAML 设计器对 Xamarin.Forms XAML 文件不起作用？

Xamarin.Forms XAML 文件当前不支持的可视化设计器。 因此，尝试在任一 Visual Studio 中打开窗体 XAML 文件时*XAML UI 设计器*或*带编码功能的 XAML UI 设计器*，引发以下错误消息：

> "使用在所选编辑器无法打开该文件。 请选择另一个编辑器。"

此限制中所述[概述](~/xamarin-forms/xaml/xaml-basics/index.md#Overview)一部分[Xamarin.Forms XAML 基础知识](~/xamarin-forms/xaml/xaml-basics/index.md)指南：

> "不存在，但可视化设计器生成 XAML 在 Xamarin.Forms 应用程序，因此所有 XAML 都必须手动编写"。

但是，通过选择显示 Xamarin.Forms XAML 预览程序**视图 > 其他 Windows > Xamarin.Forms 预览程序**菜单选项。
