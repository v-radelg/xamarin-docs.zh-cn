---
title: 带片段的 ViewPager
description: ViewPager 是一个布局管理器，可用于实现 gestural 导航。 Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。 本指南说明如何使用 ViewPager 实现 swipeable UI，并使用片段作为数据页。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d0fdfa44ce6caa0c5f0e0aa2eed6406e606eacc4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029033"
---
# <a name="viewpager-with-fragments"></a>带片段的 ViewPager

_ViewPager 是一个布局管理器，可用于实现 gestural 导航。Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。本指南说明如何使用 ViewPager 实现 swipeable UI，并使用片段作为数据页。_

## <a name="overview"></a>概述

`ViewPager` 通常与片段结合使用，以便更轻松地管理 `ViewPager`中每个页面的生命周期。 在本演练中，`ViewPager` 用于创建一个名为**FlashCardPager**的应用，用于在闪存卡上提供一系列数学问题。 每个闪存卡都作为一个片段实现。 用户通过闪存卡左右 swipes，点击数学问题以显示其答案。 此应用将为每个闪存卡创建一个 `Fragment` 实例，并实现从 `FragmentPagerAdapter`派生的适配器。 在[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中，大部分工作都是在 `MainActivity` 生命周期方法中完成的。 在**FlashCardPager**中，大部分工作都是通过其生命周期方法之一 `Fragment` 来完成的。 

本指南不涵盖片段的基础知识 &ndash; 如果你还不熟悉 Xamarin 中的片段，请参阅[片段](~/android/platform/fragments/index.md)，以帮助你开始处理片段。 

## <a name="start-an-app-project"></a>启动应用项目

创建名为**FlashCardPager**的新 Android 项目。 接下来，启动 NuGet 包管理器（有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)）。 查找并安装[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中所述的**Xamarin**包。 

## <a name="add-an-example-data-source"></a>添加示例数据源

在**FlashCardPager**中，数据源是由 `FlashCardDeck` 类表示的一组闪存卡;此数据源提供包含项内容的 `ViewPager`。 `FlashCardDeck` 包含数学问题和答案的现成集合。 `FlashCardDeck` 构造函数不需要任何参数： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

`FlashCardDeck` 中的闪存卡集合被组织，以便索引器可以访问每个闪存卡。 例如，下面的代码行检索第四个闪存卡问题： 

```csharp
string problem = flashCardDeck[3].Problem;
```

下面这行代码检索上一个问题的相应答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

由于 `FlashCardDeck` 的实现细节与理解 `ViewPager`无关，`FlashCardDeck` 代码未在此处列出。
[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)上提供了要 `FlashCardDeck` 的源代码。
下载此源文件（或将代码复制并粘贴到新的**FlashCardDeck.cs**文件中），并将其添加到项目。

## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开**Resources/layout/main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

此 XML 定义占用整个屏幕的 `ViewPager`。 请注意，必须使用完全限定的名称**ViewPager** ，因为 `ViewPager` 打包在支持库中。 `ViewPager` 仅适用于[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);它在 Android SDK 中不可用。

## <a name="set-up-viewpager"></a>设置 ViewPager

编辑**MainActivity.cs**并添加以下 `using` 语句：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

更改 `MainActivity` 类声明，使其派生自 `FragmentActivity`：

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 派生自`FragmentActivity` （而不是 `Activity`），因为 `FragmentActivity` 知道如何管理对片段的支持。 将 `OnCreate` 方法替换为以下代码： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此代码执行以下操作：

1. 设置**main.axml**布局资源中的视图。

2. 从布局中检索对 `ViewPager` 的引用。

3. 实例化一个新的 `FlashCardDeck` 作为数据源。

生成并运行此代码时，应会看到类似于以下屏幕截图的显示： 

[![带有空 ViewPager 的 FlashCardPager 应用程序的屏幕截图](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此时，`ViewPager` 为空，因为缺少用于填充 `ViewPager`的片段，并且缺少用于从**FlashCardDeck**中的数据创建这些片段的适配器。 

在以下部分中，将创建一个 `FlashCardFragment` 以实现每个闪存卡的功能，并创建一个 `FragmentPagerAdapter` 将 `ViewPager` 连接到从 `FlashCardDeck`中的数据创建的片段。 

## <a name="create-the-fragment"></a>创建片段

每个闪存卡将由名为 `FlashCardFragment`的 UI 片段进行管理。 `FlashCardFragment`的视图将显示单个闪存卡中包含的信息。 `FlashCardFragment` 的每个实例都将由 `ViewPager`承载。 
`FlashCardFragment`的视图将包含显示闪存卡问题文本的 `TextView`。 此视图将实现一个事件处理程序，该处理程序使用 `Toast` 来显示用户点击闪存卡问题时的答案。 

### <a name="create-the-flashcardfragment-layout"></a>创建 FlashCardFragment 布局

必须先定义其布局，才能实现 `FlashCardFragment`。 此布局是单个片段的片段容器布局。 向名为**flashcard_layout. main.axml**的**资源/布局**添加新的 Android 布局。 打开**Resources/layout/flashcard_layout** ，并将其内容替换为以下代码： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

此布局定义单个闪存卡片段;每个片段都包含使用大（100sp）字体显示数学问题的 `TextView`。 此文本在闪存卡上垂直和水平居中。 

### <a name="create-the-initial-flashcardfragment-class"></a>创建初始 FlashCardFragment 类

添加一个名为**FlashCardFragment.cs**的新文件，并将其内容替换为以下代码：

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

此代码会将用于显示闪存卡的基本 `Fragment` 定义存根。 请注意，`FlashCardFragment` 派生自 `Android.Support.V4.App.Fragment`中定义的 `Fragment` 的支持库版本。 构造函数为空，以便 `newInstance` 工厂方法用于创建新的 `FlashCardFragment` 而不是构造函数。 

`OnCreateView` 生命周期方法创建并配置 `TextView`。 它增加片段的 `TextView` 布局，并将膨胀的 `TextView` 返回给调用方。 `LayoutInflater` 和 `ViewGroup` 会传递到 `OnCreateView`，以便它能够使布局膨胀。 `savedInstanceState` 捆绑包含 `OnCreateView` 用于从保存状态重新创建 `TextView` 的数据。 

通过调用 `inflater.Inflate`来显式放大片段的视图。 `container` 参数是视图的父级，而 `false` 标志指示 inflater 转换过程避免将放大视图添加到视图的父级（在本演练后面 `ViewPager` 调用的适配器的 `GetItem` 方法时，将添加该视图）。 

### <a name="add-state-code-to-flashcardfragment"></a>向 FlashCardFragment 添加状态代码

与活动一样，片段有一个用于保存和检索其状态的 `Bundle`。 在**FlashCardPager**中，此 `Bundle` 用于保存相关闪存卡的问题和答案文本。 在**FlashCardFragment.cs**中，将以下 `Bundle` 项添加到 `FlashCardFragment` 类定义的顶部： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改 `newInstance` 工厂方法，使其创建 `Bundle` 对象，并使用上面的键将传递的问题和答案文本存储在片段中，并将其实例化： 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

修改片段生命周期方法 `OnCreateView` 从传入的捆绑包中检索此信息，并将问题文本加载到 `TextBox`中： 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

此处不使用 `answer` 变量，但稍后在将事件处理程序代码添加到此文件时将使用它。 

## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用位于 `ViewPager` 与数据源之间的适配器控制器对象（请参阅 ViewPager[适配器](~/android/user-interface/controls/view-pager/index.md#adapter)文章中的图）。 若要访问此数据，`ViewPager` 要求你提供从 `PagerAdapter`派生的自定义适配器。 由于本示例使用片段，因此它使用 `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` 派生自 `PagerAdapter`。 
`FragmentPagerAdapter` 表示每个页面作为 `Fragment`，只要用户可以返回到页面，就会永久保留在片段管理器中。 当用户 swipes `ViewPager`的页时，`FragmentPagerAdapter` 将从数据源中提取信息，并使用它来创建 `Fragment`以便显示 `ViewPager`。 

实现 `FragmentPagerAdapter`时，必须重写以下内容：

- **计数**&ndash; 只读属性，该属性返回可用的视图（页）数。

- **GetItem** &ndash; 返回要为指定页显示的片段。

添加一个名为**FlashCardDeckAdapter.cs**的新文件，并将其内容替换为以下代码：

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

此代码会将必要的基本 `FragmentPagerAdapter` 实现。 在以下部分中，这些方法中的每一个都被替换为工作代码。 构造函数的目的是将片段管理器传递到 `FlashCardDeckAdapter`的基类构造函数。 

### <a name="implement-the-adapter-constructor"></a>实现适配器构造函数

当应用程序实例化 `FlashCardDeckAdapter`时，它将提供一个对片段管理器和一个实例化 `FlashCardDeck`的引用。 在**FlashCardDeckAdapter.cs**中，将以下成员变量添加到 `FlashCardDeckAdapter` 类的顶部： 

```csharp
public FlashCardDeck flashCardDeck;
```

将以下代码行添加到 `FlashCardDeckAdapter` 构造函数： 

```csharp
this.flashCardDeck = flashCards;
```

下面这行代码存储 `FlashCardDeckAdapter` 将使用的 `FlashCardDeck` 实例。 

### <a name="implement-count"></a>实现计数

`Count` 实现相对简单：返回闪存卡中纸牌的卡数。 将 `Count` 替换为以下代码： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

`FlashCardDeck` 的 `NumCards` 属性返回数据集中的闪存卡数（碎片数）。 

### <a name="implement-getitem"></a>实现 GetItem

`GetItem` 方法返回与给定位置关联的片段。 为闪存卡中的某个位置调用 `GetItem` 时，它将返回一个 `FlashCardFragment` 配置为在该位置显示闪存卡问题。 将 `GetItem` 方法替换为以下代码： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此代码执行以下操作：

1. 在 `FlashCardDeck` 中查找指定位置的数学问题字符串。 

2. 在 `FlashCardDeck` 中查找指定位置的答案字符串。 

3. 调用 `FlashCardFragment` 工厂方法 `newInstance`，并传入闪存卡问题和答案字符串。 

4. 创建并返回一个新的闪存卡 `Fragment`，其中包含该位置的问题和答案文本。 

当 `ViewPager` 在 `position`呈现 `Fragment` 时，它将显示包含闪存卡中位于 `position` 的数学问题字符串的 `TextBox`。 

## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

现在，`FlashCardDeckAdapter` 已实现，接下来可以将其添加到 `ViewPager`中。 在**MainActivity.cs**中，将以下代码行添加到 `OnCreate` 方法的末尾：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此代码将实例化 `FlashCardDeckAdapter`，并传入第一个参数中的 `SupportFragmentManager`。 （FragmentActivity 的 `SupportFragmentManager` 属性用于获取对 `FragmentManager` 的引用 &ndash; 有关 `FragmentManager`的详细信息，请参阅[管理片段](~/android/platform/fragments/managing-fragments.md)。） 

核心实现现已完成 &ndash; 生成并运行应用。
屏幕上会显示 "闪存卡" 面板的第一个图像，如接下来的屏幕截图中所示。 向左轻扫可查看更多的闪存卡，并向右轻扫可通过闪光灯向后移动：

[![不带寻呼指标的 FlashCardPager 应用的示例屏幕截图](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>添加寻呼指示器

这一最小 `ViewPager` 实现将显示卡片组中的每个闪存卡，但不会指示用户在卡片组中的位置。 下一步是添加 `PagerTabStrip`。 `PagerTabStrip` 向用户通知显示了哪个问题号，并通过显示上一张和下一闪存卡的提示来提供导航上下文。 

打开**Resources/layout/main.axml** ，并将 `PagerTabStrip` 添加到布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

  <android.support.v4.view.PagerTabStrip
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:layout_gravity="top"
      android:paddingBottom="10dp"
      android:paddingTop="10dp"
      android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

当你生成并运行应用时，你应该会看到显示在每个闪存卡顶部的空 `PagerTabStrip`： 

[![不带文本的 PagerTabStrip 的特写](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>显示标题

若要将标题添加到每个页选项卡，请在适配器中实现 `GetPageTitleFormatted` 方法。 `ViewPager` 调用 `GetPageTitleFormatted` （如果已实现）来获取描述指定位置上页面的标题字符串。 将以下方法添加到**FlashCardDeckAdapter.cs**中的 `FlashCardDeckAdapter` 类： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

此代码将闪存卡中的位置转换为问题编号。 生成的字符串转换为返回到 `ViewPager`的 Java `String`。 当你通过此新方法运行应用时，每个页面都会在 `PagerTabStrip`中显示问题编号： 

[![屏幕截图，其中显示了每个页面上方显示的问题编号](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以来回切换，以查看每个闪存卡顶部显示的 "闪存卡" 中的问题号。 

## <a name="handle-user-input"></a>处理用户输入

**FlashCardPager**在 `ViewPager`中提供一系列基于片段的闪存卡，但它尚不能显示每个问题的答案。 在本部分中，将向 `FlashCardFragment` 添加一个事件处理程序，以便在用户点击闪存卡问题文本时显示答案。 

打开**FlashCardFragment.cs** ，并将以下代码添加到 `OnCreateView` 方法的末尾，然后将视图返回给调用方： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

此 `Click` 事件处理程序会在用户点击 `TextBox`时显示 Toast 中的答案。 在从传递给 `OnCreateView`的捆绑中读取状态信息时，先前初始化了 `answer` 变量。 构建并运行应用，然后点击每个闪存卡上的问题文本以查看答案： 

[点击数学问题时，![FlashCardPager 应用 Toast 的屏幕截图](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

本演练中介绍的**FlashCardPager**使用派生自 `FragmentActivity`的 `MainActivity`，但你也可以从 `AppCompatActivity` 派生 `MainActivity` （这也会为管理片段提供支持）。 若要查看 `AppCompatActivity` 示例，请参阅示例库中的[FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 。

## <a name="summary"></a>总结

本演练提供了有关如何使用 `Fragment`生成基于基本 `ViewPager`的应用的分步示例。 其中显示了一个包含闪存卡问题和答案的示例数据源、一个用于显示闪存卡的 `ViewPager` 布局以及一个将 `ViewPager` 连接到数据源的 `FragmentPagerAdapter` 子类。 为了帮助用户浏览闪存卡，包括说明如何添加 `PagerTabStrip` 以在每一页的顶部显示问题编号的说明。 最后，添加了事件处理代码以显示用户点击闪存卡问题时的答案。 

## <a name="related-links"></a>相关链接

- [FlashCardPager （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
