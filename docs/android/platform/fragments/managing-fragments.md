---
title: 管理片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 3e0430b8ed9c42030441021e71c3b08b1ddccc57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022189"
---
# <a name="managing-fragments"></a>管理片段

为了帮助管理片段，Android 提供`FragmentManager`类。 每个活动具有实例`Android.App.FragmentManager`，将查找或动态更改其片段。 这些更改的每个组称为*事务*，并使用该类中包含的 Api 之一执行`Android.App.FragmentTransation`，它由托管`FragmentManager`。 活动可能会启动此类事务：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

在执行这些更改到片段`FragmentTransaction`实例使用的方法类似于`Add()`，`Remove(),`并`Replace().`所做的更改使用，然后应用`Commit()`。 在事务中的更改不会立即执行。
相反，它们被计划尽可能快地运行活动的 UI 线程上。

下面的示例演示如何将片段添加到现有的容器：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果一个事务被提交之后`Activity.OnSaveInstanceState()`是调用，就会引发异常。 这是因为当该活动将保存其状态，Android 会保存任何托管片段的状态。 如果任何片段的事务提交此点后，这些事务的状态将丢失时还原该活动中。

可以将片段事务保存到活动的[back 堆栈](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)由调用`FragmentTransaction.AddToBackStack()`。 这允许用户通过向后导航片段更改何时**回**按下按钮。 而无需对此方法的调用，将删除的片段将被销毁和将用户导航返回到活动的情况下不可用。

下面的示例演示如何使用`AddToBackStack`方法的`FragmentTransaction`替换一个片段中，同时保留后退堆栈上的第一个片段的状态：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```


## <a name="communicating-with-fragments"></a>使用片段进行通信

*FragmentManager*知道有关所有附加到活动的片段，并提供两种方法来帮助查找这些片段：

-   **FindFragmentById** &ndash;此方法将查找一个片段作为事务的一部分添加的片段时使用的布局文件中指定的 ID 或容器 ID。

-   **FindFragmentByTag** &ndash;使用此方法来查找已的标记的布局文件中提供或在事务中已添加的片段。

片段和活动引用`FragmentManager`，因此相同的方法使用了它们之间的往返通信。 应用程序可能会使用这两种方法之一查找片段的引用、 强制转换为适当的类型，该引用，然后在片段上直接调用方法。 以下代码片段提供了一个示例：

还有可能要使用的活动的`FragmentManager`查找片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>与活动通信

很可能要使用的片段`Fragment.Activity`属性来引用其主机。 通过强制转换到更具体的类型的活动，它是调用方法和属性在其主机上，活动可能，如下面的示例中所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
