---
title: Xamarin 中的新引用计数系统
description: 本文档介绍 Xamarin 的增强引用计数系统，默认情况下在所有 Xamarin 应用程序中启用。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 8b1b82a1707a4aa58ef1e3dadbaeb79ada1ad6a1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291875"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Xamarin 中的新引用计数系统

默认情况下，Xamarin 9.2.1 将增强的引用计数系统引入到所有应用程序。 它可用于消除许多在早期版本的 Xamarin 中难以跟踪和修复的内存问题。

## <a name="enabling-the-new-reference-counting-system"></a>启用新的引用计数系统

从 Xamarin 9.2.1 开始，默认情况下，将为**所有**应用程序启用新的引用计数系统。

如果要开发现有应用程序，可以检查 .csproj 文件，以确保将的`MtouchUseRefCounting`所有匹配项都设置为`true`，如下所示：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果设置为， `false`则应用程序将不使用新工具。

### <a name="using-older-versions-of-xamarin"></a>使用早期版本的 Xamarin

Xamarin 7.2.1 及更高版本的功能是新的引用计数系统的增强预览版。

**Classic API：**

若要启用此新的引用计数系统，请选中在项目的**IOS 生成选项**的 "**高级**" 选项卡中找到的 "**使用引用计数扩展**" 复选框，如下所示： 

[![](newrefcount-images/image1.png "启用新的引用计数系统")](newrefcount-images/image1.png#lightbox)

请注意，在 Visual Studio for Mac 的较新版本中已删除了这些选项。

 **[Unified API：](~/cross-platform/macios/unified/index.md)**

 Unified API 需要新的引用计数扩展，并且应默认启用它。 较早版本的 IDE 可能没有自动检查此值，您可能必须自行进行检查。


> [!IMPORTANT]
> 此功能的早期版本已于 Monotouch.dialog 5.2，但仅适用于**sgen**作为实验性预览。 这一新的增强版本现在也可用于**Boehm**垃圾回收器。


过去，Xamarin 管理了两种对象：只是围绕本机对象（对等对象）的包装的对象，以及那些扩展或引入了新功能（派生对象）的对象-通常通过保留额外的内存中状态。 以前，我们可以使用状态（例如，通过添加C#事件处理程序）来扩充对等对象，但我们允许对象处于未引用状态，然后再收集。 这可能会导致以后发生故障（例如，如果目标 C 运行时回调到托管对象中）。

新系统在存储任何额外信息时，自动将对等对象升级到由运行时管理的对象。

这解决了这种情况下发生的各种崩溃：

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

如果没有引用计数扩展，此代码将崩溃`cell` ，因为成为可回收的， `TouchDown`因此它的委托将转换为无关联的指针。

引用计数扩展可确保托管对象保持活动状态并防止其收集，前提是本机对象由本机代码保留。

新系统还消除了对绑定中使用的*大多数*私有支持字段的需求-这是保持实例活动状态的默认方法。 托管链接器非常智能，可以使用新的引用计数扩展来从应用程序中删除所有不*需要*的字段。

这意味着每个托管对象实例使用的内存都比以前更少。 它还解决了相关的问题，其中某些支持字段将包含在目标 C 运行时不再需要的引用，因此很难回收某些内存。
