---
title: 创建使用反射 API 的 Xamarin.iOS 应用程序
description: 本文档介绍 MonoTouch.Dialog 基于属性的反射 API 的说明进行操作，这将创建基于使用属性修饰的类的 UI。
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: fac19cd7273b5b396946fc1867049db19348c853
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039683"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>创建使用反射 API 的 Xamarin.iOS 应用程序

Mt。D 反射 API 允许类以使其使用属性修饰该 mt。D 使用自动创建的屏幕。 反射 API 提供了这些类，并在屏幕上显示的内容之间的绑定。 尽管此 API 不提供 API 的元素执行细粒度控制，它将复杂性降低自动构建基于类修饰的元素层次结构。

## <a name="setting-up-mtd"></a>设置 mt。D

MT。通过 Xamarin.iOS 分发 D。 若要使用它，右键单击**引用**节点的 Xamarin.iOS 项目在 Visual Studio 2017 或 Visual Studio for Mac，并添加对引用**MonoTouch.Dialog 1**程序集。 然后，添加`using MonoTouch.Dialog`根据代码源中的语句。

## <a name="getting-started-with-the-reflection-api"></a>反射 API 入门

使用反射 API 就像一样简单：

1.  创建一个类修饰的 mt。D 属性。
1.  创建`BindingContext`实例，并向其传递上述类的实例。 
1.  创建`DialogViewController`，并向其传递`BindingContext’s` `RootElement` 。 


让我们看一下示例演示了如何使用反射 API。 在此示例中，我们将构建简单的数据输入屏幕上，如下所示：

 [![](reflection-api-walkthrough-images/01-expense-entry.png "在此示例中，我们将构建简单的数据输入屏幕上，如下所示")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>使用 mt。 创建类D 属性

我们需要使用反射 API 的第一件事是使用属性修饰的类。 Mt。 将使用这些特性元素 API 中的 D 在内部，若要创建对象。 例如，考虑以下类定义：

```csharp
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
        
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
}
```

`SectionAttribute`将导致部分`UITableView`使用创建的用于填充该节的标头的字符串参数。 后声明一个部分，可以跟踪该域每个字段将包含在该部分中中,，直到声明另一个部分。
为字段创建的用户界面元素的类型将取决于字段的类型和 mt。D 修饰其属性。

例如，`Name`字段是`string`用修饰和`EntryAttribute`。 这会导致正在添加到具有文本输入字段和指定的标题的表中的行。 同样，`IsApproved`字段是`bool`与`CheckboxAttribute`，这会导致表行中的表格单元格右侧的复选框。 MT。D 将字段的名称，会自动添加一个空格，这种情况下，创建标题，因为它未指定的属性。

## <a name="adding-the-bindingcontext"></a>添加 BindingContext

若要使用`Expense`类，我们需要创建`BindingContext`。 一个`BindingContext`是会将数据绑定特性化类，以创建元素的层次结构从一个类。 若要创建一个，我们只是它进行实例化并将特性化类的实例中传递给构造函数。

例如，若要添加使用我们声明的 UI 中的属性`Expense`类中，包括中的以下代码`FinishedLaunching`方法的`AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

然后我们只需创建用户界面是添加`BindingContext`到`DialogViewController`并将其设置为`RootViewController`的窗口，如下所示：

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{   
    window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    var expense = new Expense ();
    var bctx = new BindingContext (null, expense, "Create a task");
    var dvc = new DialogViewController (bctx.Root);
            
    window.RootViewController = dvc;
    window.MakeKeyAndVisible ();
            
    return true;
}
```

运行应用程序现在会导致显示上面所示的屏幕。

### <a name="adding-a-uinavigationcontroller"></a>添加 UINavigationController

但是请注意，标题"创建任务"，我们传递给`BindingContext`不会显示。 这是因为`DialogViewController`不是属于`UINavigatonController`。 让我们更改代码以添加`UINavigationController`为窗口的`RootViewController,`并添加`DialogViewController`作为根的`UINavigationController`，如下所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

现在，我们运行该应用程序，标题会显示在`UINavigationController’s`如屏幕截图所示的导航栏：

 [![](reflection-api-walkthrough-images/02-create-task.png "现在当我们运行该应用程序，就会 UINavigationControllers 导航栏中显示标题")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

通过包括`UINavigationController`，我们现在可以充分利用其他功能的 mt。为其导航是必需的 D。 例如，我们可以添加到枚举`Expense`类定义的类别的费用，其中珠穆朗玛峰D 将自动创建选择屏幕。 为了演示，修改`Expense`类，以包含`ExpenseCategory`字段，如下所示：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    …

    [Caption("Category")]
    public Category ExpenseCategory;
}
```

运行应用程序现在会导致类别表中的新行所示：

 [![](reflection-api-walkthrough-images/03-set-details.png "运行应用程序现在会生成类别表中的新行中所示")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

选择的行生成应用程序导航到新的屏幕对应于枚举中的行，如下所示：

 [![](reflection-api-walkthrough-images/04-set-category.png "选择行会导致应用程序导航到新的屏幕与枚举相对应的行")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>总结

本文提供反射 API 的演练。 我们介绍了如何将属性添加到一个类以控制显示的内容。 我们还讨论了如何使用`BindingContext`将数据从一个类绑定到的元素层次结构创建，以及如何使用 mt。使用 D `UINavigationController`。


## <a name="related-links"></a>相关链接

- [MTDReflectionWalkthrough （示例）](https://developer.xamarin.com/samples/monotouch/MTDReflectionWalkthrough/)
- [简介 MonoTouch 对话框](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
