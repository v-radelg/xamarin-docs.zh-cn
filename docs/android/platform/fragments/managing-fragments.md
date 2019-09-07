---
title: 管理片段
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: f5baf8b46571c9b528fcc666a3f1f4530f18ee07
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761496"
---
# <a name="managing-fragments"></a>管理片段

为了帮助管理片段，Android 提供`FragmentManager`了类。 每个活动都有一个`Android.App.FragmentManager`实例，该实例将查找或动态更改其片段。 这些更改的每一组称为*事务*，并使用类中包含的其中一个 api 来执行，此类`Android.App.FragmentTransation`由管理。 `FragmentManager` 活动可能会启动如下所示的事务：

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

这些对片段的更改是使用等方法`FragmentTransaction` `Add()`在实例中执行的， `Remove(),` `Replace().`然后使用`Commit()`来应用这些更改。 事务中的更改不会立即执行。
相反，它们计划为尽快在活动的 UI 线程上运行。

下面的示例演示如何向现有容器添加片段：

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

如果在调用后`Activity.OnSaveInstanceState()`提交事务，则会引发异常。 发生这种情况是因为当活动保存其状态时，Android 还会保存任何托管片段的状态。 如果在此点之后提交了任何片段事务，则在还原活动时，这些事务的状态将丢失。

可以通过调用来`FragmentTransaction.AddToBackStack()`将片段事务保存到活动的[back 堆栈](https://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html)中。 这样，当按下 "**后退**" 按钮时，用户可以通过片段更改向后导航。 如果不调用此方法，删除的片段将被销毁，如果用户向后导航到活动，则将无法使用。

下面的示例演示如何使用`AddToBackStack`的方法`FragmentTransaction`来替换一个片段，同时保留后堆栈上第一个片段的状态：

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

## <a name="communicating-with-fragments"></a>与片段通信

*FragmentManager*知道附加到活动的所有片段，并提供两种方法来帮助查找这些片段：

- **FindFragmentById**&ndash;此方法将通过使用在布局文件中指定的 ID 或在将片段作为事务的一部分添加时使用容器 id 查找片段。

- **FindFragmentByTag**&ndash;此方法用于查找具有在布局文件中提供或已在事务中添加的标记的片段。

片段和活动都引用`FragmentManager`，因此，使用相同的方法在它们之间来回通信。 应用程序可以通过使用这两种方法中的一种来查找引用片段，将该引用强制转换为相应的类型，然后直接对片段调用方法。 以下代码片段提供了一个示例：

活动还可以使用`FragmentManager`来查找片段：

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```

### <a name="communicating-with-the-activity"></a>与活动通信

片段可以使用`Fragment.Activity`属性来引用其主机。 通过将活动强制转换为更具体的类型，活动可以调用其主机上的方法和属性，如以下示例中所示：

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
