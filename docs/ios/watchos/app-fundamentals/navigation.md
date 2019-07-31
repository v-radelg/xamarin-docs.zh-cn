---
title: 使用 watchOS 在 Xamarin 中导航
description: 本文档介绍如何使用在 watchOS 应用程序中进行导航。 它讨论模式接口、 分层导航，以及基于页面的接口。
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f49416eddb69e2900eb427f27057cd8f1cb9ceb5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654691"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>使用 watchOS 在 Xamarin 中导航

最简单导航上的选项可监视是一个简单[模式弹出框](#modal)，将出现当前场景的顶部。

多场景 watch 应用存在有两种导航模式可用：

- [分层导航](#Hierarchical_Navigation)
- [基于页面的接口](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>模式接口

使用`PresentController`方法以模式方式打开界面控制器。 界面控制器必须已在中定义**Interface.storyboard**。

```csharp
PresentController ("pageController","some context info");
```

有模式地显示控制器使用整个屏幕 （包括上一场景）。 默认情况下的标题设置为**取消**和点击它会消除控制器。

若要以编程方式关闭有模式地显示控制器，请调用`DismissController`。

```csharp
DismissController();
```

模式屏幕可以是一个场景或使用基于页面的布局。

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>分层导航

提供了可以通过反向导航，方式类似于一个堆栈等场景`UINavigationController`适用于 iOS。 可以推送到导航堆栈上和 （以编程方式或通过用户选择） 弹出的场景。

![](navigation-images/hierarchy-1.png "可以在导航堆栈上推送后台") ![](navigation-images/hierarchy-2.png "场景可以从导航堆栈中弹出")

与 iOS 一样左边缘扫导航回父控制器分层导航堆栈中。

这两个[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)并[WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)示例包括分层导航。

### <a name="pushing-and-popping-in-code"></a>推送和弹出代码中

观看工具包不需要过度存档的"导航控制器"若要创建类似 iOS 执行的操作-只需推送控制器使用`PushController`方法，并导航堆栈会自动创建。

```csharp
PushController("secondPageController","some context info");
```

监视的屏幕将包括**回**按钮在左上角，但您可以从导航堆栈中使用以编程方式删除场景`PopController`。

```csharp
PopController();
```

因为 ios，它也是无法返回到导航堆栈使用的根`PopToRootController`。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

Segue 可以在情节提要来定义分层导航中的场景之间创建。 若要获取上下文的目标场景，操作系统调用`GetContextForSegue`来初始化新的界面控制器。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>基于页面的接口

基于页面的接口往下轻扫从左到右，方式类似于`UIPageViewController`适用于 iOS。 指示器点沿屏幕以显示当前显示的页的底部显示。

![](navigation-images/paged-1.png "示例的第一页") ![](navigation-images/paged-2.png "示例第二页") ![](navigation-images/paged-5.png "示例第五个页")


若要使基于页面的界面监视应用程序的主 UI，请使用`ReloadRootControllers`界面控制器和上下文的数组：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

此外可以显示不是根的基于页面的控制器使用`PresentController`从一个应用程序中其他场景。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables （示例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
