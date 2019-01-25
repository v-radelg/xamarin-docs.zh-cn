---
title: 新引用计数在 Xamarin.iOS 中的系统
description: 本文档介绍了 Xamarin 的增强型的引用计数系统中，默认情况下启用所有 Xamarin.iOS 应用程序中。
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 8c7b1a88284156cb5d4261f18d5659ed66dfaf64
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879324"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>新引用计数在 Xamarin.iOS 中的系统

Xamarin.iOS 9.2.1 引入了增强的引用计数的所有应用程序的系统默认情况下。 它可用于消除也很难跟踪和解决在早期版本的 Xamarin.iOS 的很多内存问题。

## <a name="enabling-the-new-reference-counting-system"></a>启用新引用计数系统

从 Xamarin 9.2.1 开始新引用计数系统已经启用了**所有**默认情况下的应用程序。

如果你正在开发现有应用程序，可以检查.csproj 文件，确保出现的所有`MtouchUseRefCounting`设置为`true`，如下面：

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

如果设置为`false`你的应用程序将不使用新的工具。

### <a name="using-older-versions-of-xamarin"></a>使用较旧版本的 Xamarin

Xamarin.iOS 7.2.1 和上述功能增强我们新引用计数系统的预览。

**经典 API:**

若要启用此新引用计数系统，请检查**使用引用计数扩展**中找到的复选框**高级**项目的选项卡**iOS 生成选项**如下所示： 

[![](newrefcount-images/image1.png "启用新引用计数系统")](newrefcount-images/image1.png#lightbox)

请注意，这些选项中，已删除较新版本的 Visual Studio for mac。

 **[统一的 API:](~/cross-platform/macios/unified/index.md)**

 新引用计数扩展是所必需的统一 API，因此应该默认情况下启用。 较旧版本的 IDE 可能没有自动检查此值，可能需要添加一个选中它自己。

    
> [!IMPORTANT]
> 由于 MonoTouch 5.2，但却仅适用于早期版本的此功能已被围绕**sgen**作为一个实验性预览版。 此新增强版本现也已可供**Boehm**垃圾回收器。


从历史上看发生了两种类型的对象由 Xamarin.iOS： 那些通过保持额外的内存中状态通常是只是一个本机对象 （对等对象） 和那些扩展或合并新功能 （派生对象） 的周围的包装。 以前，可能我们无法提供了具有状态的对等对象 (例如通过添加C#事件处理程序)，但我们让转未引用，然后收集的对象。 这可能导致崩溃更高版本上 (例如如果 Objective C 运行时回调到托管对象)。

新系统会自动升级到它们存储任何附加信息时由运行时管理的对象的对等对象。

这解决了这样一个情况中发生的各种故障：

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

而无需引用计数扩展此代码将会崩溃，因为`cell`变得可回收，，因此其`TouchDown`委托，这将转换为无关联的指针。

引用计数扩展可确保托管的对象保持处于活动状态并阻止其集合，提供的本机对象保留的本机代码。

新系统还无需*大多数*私有支持字段绑定-这是默认方法，以使实例保持活动状态中使用。 托管的链接器非常智能，若要删除所有这些*不需要*字段从应用程序使用的新引用计数扩展。

这意味着每个托管的对象实例使用比之前更少的内存。 它还解决了一些支持字段存放已不再需要通过 Objective C 运行时，使其难以回收某些内存的引用的相关的问题。
