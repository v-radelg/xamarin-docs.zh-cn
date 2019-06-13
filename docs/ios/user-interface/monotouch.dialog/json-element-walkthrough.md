---
title: 使用 JSON 在 Xamarin.iOS 中创建用户界面
description: MonoTouch.Dialog （mt。D） 包括对 JSON 数据通过动态 UI 生成的支持。 在本教程中，我们将演练如何使用 JSONElement 从 JSON 包含与应用程序，或从远程 Url 加载创建用户界面。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: e1eae07fab4a74a4f47f565d4c4ca0b7f6bc1aa9
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039673"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>使用 JSON 在 Xamarin.iOS 中创建用户界面

_MonoTouch.Dialog （mt。D） 包括对 JSON 数据通过动态 UI 生成的支持。在本教程中，我们将演练如何使用 JSONElement 从 JSON 包含与应用程序，或从远程 Url 加载创建用户界面。_

MT。D 支持在 JSON 中声明的创建用户界面。 当使用 JSON，mt。 声明元素D 将关联的元素为你自动创建。 JSON 可以加载从本地文件，已分析`JsonObject`实例或甚至远程 Url。

MT。D 支持使用 JSON 时元素 API 中提供的功能的完整范围。 例如，下面的屏幕截图中的应用程序是完全声明使用 JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "例如，此屏幕截图中的应用程序完全声明使用 JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png "例如，此屏幕截图中的应用程序完全使用声明JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

让我们重新访问中的示例[元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教程中，显示如何添加任务的详细信息屏幕，使用 JSON。

## <a name="setting-up-mtd"></a>设置 mt。D

MT。通过 Xamarin.iOS 分发 D。 若要使用它，右键单击**引用**节点的 Xamarin.iOS 项目在 Visual Studio 2017 或 Visual Studio for Mac，并添加对引用**MonoTouch.Dialog 1**程序集。 然后，添加`using MonoTouch.Dialog`根据代码源中的语句。

## <a name="json-walkthrough"></a>JSON 演练

本演练的示例允许创建任务。 第一个屏幕上选择一项任务后，详细信息屏幕显示所示：

 [![](json-element-walkthrough-images/03-task-list.png "所示的第一个屏幕上选择一项任务时，显示详细信息屏幕")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>创建 JSON

对于此示例中，我们将从名为的项目中的文件加载 JSON `task.json`。 MT。D 预期要符合反映元素 API 语法的 JSON。 就像使用元素 API 代码中，在使用 JSON 时，我们声明部分和内这些部分中，我们添加元素。 若要声明部分，并在 JSON 中的元素，我们使用字符串"部分"和"元素"分别作为键。 对于每个元素，使用设置关联的元素类型`type`密钥。 每个元素属性设置为键属性名称。

例如，以下 JSON 说明的各节和任务详细信息的元素：

```json
{
    "title": "Task",
    "sections": [
        {
            "elements" : [
                {
                    "id" : "task-description",
                    "type": "entry",
                    "placeholder": "Enter task description"
                },
                {
                    "id" : "task-duedate",
                    "type": "date",
                    "caption": "Due Date",
                    "value": "00:00"
                }
            ]
        }
    ]
}
```

请注意上面的 JSON 包含每个元素的 id。 任何元素可以包含一个 id，来在运行时对其进行引用。 我们将了解如何使用此方法稍后当我们显示如何加载代码中的 JSON。

## <a name="loading-the-json-in-code"></a>正在加载代码中的 JSON

一旦定义了 JSON，我们需要将其加载到 mt。D 使用`JsonElement`类。 假设使用我们在上面创建的 JSON 文件已添加到具有名称 sample.json 项目并提供生成操作的内容，加载`JsonElement`非常简单，调用以下代码行：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

由于我们要添加此按需每次创建一个任务，我们可以修改前一元素 API 示例，如下所示单击的按钮：

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>访问在运行时元素

还记得我们在我们声明它们中的 JSON 文件时，id 添加到这两个元素。 我们可以使用 id 属性访问在运行时修改其属性在代码中的每个元素。 例如，下面的代码引用的项和日期的元素从任务对象设置的值：

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    taskElement.Caption = task.Name;

    var description = taskElement ["task-description"] as EntryElement;

    if (description != null) {
        description.Caption = task.Name;
        description.Value = task.Description;       
    }

    var duedate = taskElement ["task-duedate"] as DateElement;

    if (duedate != null) {                
        duedate.DateValue = task.DueDate;
    }
    _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>从 url 加载 JSON

MT。D 还支持从外部 Url 动态加载 JSON，只需将 Url 传递给构造函数的`JsonElement`。 MT。D 将展开屏幕之间导航时声明 JSON 中按需分层。 有关示例，请如下面的 JSON 文件位于根目录下的本地 web 服务器：

```json
{
    "type": "root",
    "title": "home",
    "sections": [
        {
            "header": "Nested view!",
            "elements": [
                {
                    "type": "boolean",
                    "caption": "Just a boolean",
                    "id": "first-boolean",
                    "value": false
                },
                {
                    "type": "string",
                    "caption": "Welcome to the nested controller"
                }
            ]
        }
    ]
}
```

我们可以加载这使用`JsonElement`如以下代码所示：

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

在运行时，检索和分析 mt。 该文件当用户导航到第二个视图，如下面的屏幕截图中所示的 D:

 [![](json-element-walkthrough-images/04-json-web-example.png "该文件将检索和分析 mt。当用户导航到第二个视图的 D")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>总结

本文介绍了如何创建使用接口 mt。从 JSON D。 它介绍了如何加载的文件与应用程序以及从远程 Url 中包含的 JSON。 它还介绍了如何访问在运行时在 JSON 中所述的元素。

## <a name="related-links"></a>相关链接

- [MTDJsonDemo （示例）](https://developer.xamarin.com/samples/monotouch/MTDJsonDemo/)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
