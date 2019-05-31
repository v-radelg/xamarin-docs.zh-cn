---
title: 带片段的 ViewPager
description: ViewPager 是使您可以实现动作导航的布局管理器。 动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。 本指南介绍如何实现使用 ViewPager，使用片段作为数据页的可轻扫 UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 748443352c106fbad88f8eda895cde097ce14a45
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394709"
---
# <a name="viewpager-with-fragments"></a>带片段的 ViewPager

_ViewPager 是使您可以实现动作导航的布局管理器。动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。本指南介绍如何实现使用 ViewPager，使用片段作为数据页的可轻扫 UI。_

 
## <a name="overview"></a>概述

`ViewPager` 通常使用与片段结合使用，因此它更轻松地管理中每个页面的生命周期`ViewPager`。 在此演练中，`ViewPager`用于创建名为应用程序**FlashCardPager**的闪存卡上显示一系列数学问题。 每个闪存卡实现为一个片段。 用户通过闪存卡左右轻扫并点击以显示其答案对数学问题。 此应用程序创建`Fragment`实例的每个适配器派生自的闪存卡并实现`FragmentPagerAdapter`。 在中[Viewpager 和视图](~/android/user-interface/controls/view-pager/viewpager-and-views.md)，执行大部分工作的`MainActivity`生命周期方法。 在中**FlashCardPager**，将通过完成大部分工作`Fragment`在其生命周期方法之一。 

本指南不涉及片段的基础知识&ndash;如果您尚不熟悉在 Xamarin.Android 中的片段，请参阅[片段](~/android/platform/fragments/index.md)来帮助你开始使用片段。 



## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**FlashCardPager**。 接下来，启动 NuGet 包管理器 (有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 查找和安装**Xamarin.Android.Support.v4**包中所述[Viewpager 和视图](~/android/user-interface/controls/view-pager/viewpager-and-views.md)。 



## <a name="add-an-example-data-source"></a>添加示例数据源

在**FlashCardPager**，数据源是由表示的 flash 一副纸牌`FlashCardDeck`类; 此数据源提供，数量`ViewPager`项内容。 `FlashCardDeck` 包含一系列现成数学问题和答案。 `FlashCardDeck`构造函数不需要任何参数： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

集合中的闪存卡`FlashCardDeck`组织，因此可以通过索引器访问每个闪存卡。 例如，以下代码行检索一副纸牌中的第四个闪存卡问题： 

```csharp
string problem = flashCardDeck[3].Problem;
```

这行代码检索上一个问题的相应答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

因为的实现细节`FlashCardDeck`了解与不相关`ViewPager`，则`FlashCardDeck`此处未列出的代码。
向源代码`FlashCardDeck`网址[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)。
下载此源文件 (或复制并粘贴到一个新的代码**FlashCardDeck.cs**文件) 并将其添加到你的项目。



## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开**Resources/layout/Main.axml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

此 XML 定义`ViewPager`占据整个屏幕。 请注意，必须使用完全限定名称**android.support.v4.view.ViewPager**因为`ViewPager`打包在支持库中。 `ViewPager` 只有通过[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); 它不是在 Android SDK 中提供。


## <a name="set-up-viewpager"></a>设置 ViewPager

编辑**MainActivity.cs**并添加以下`using`语句：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

更改`MainActivity`，以便从派生类声明`FragmentActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 派生自`FragmentActivity`(而非`Activity`) 因为`FragmentActivity`知道如何管理的片段的支持。 将 `OnCreate` 方法替换为以下代码： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此代码将执行以下操作：

1.  设置从视图**Main.axml**布局资源。

2.  检索到的引用`ViewPager`从布局。

3.  实例化新`FlashCardDeck`作为数据源。

当生成并运行此代码时，您应看到类似于以下屏幕截图显示： 

[![使用空的 ViewPager FlashCardPager 屏幕快照应用程序](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

在此情况下，`ViewPager`填充因为它缺少片段使用的是空`ViewPager`，并且它用于从数据中创建这些片段缺乏适配器**FlashCardDeck**。 

在以下部分中，`FlashCardFragment`是创建实现的功能的每个 flash 卡和一个`FragmentPagerAdapter`创建连接`ViewPager`到片段中创建`FlashCardDeck`。 



## <a name="create-the-fragment"></a>创建片段

每个闪存卡将由名为的 UI 片段`FlashCardFragment`。 `FlashCardFragment`视图将显示与单个的闪存卡所包含的信息。 每个实例`FlashCardFragment`将由承载`ViewPager`。 
`FlashCardFragment`视图将包含的`TextView`显示闪存卡问题文本。 此视图将实现的事件处理程序使用`Toast`来在用户点击闪存卡问题时显示答案。 



### <a name="create-the-flashcardfragment-layout"></a>创建 FlashCardFragment 布局

之前`FlashCardFragment`可以是实现，就必须定义其布局。 此布局是单个片段的片段容器布局。 添加到新的 Android 布局**资源/布局**称为**flashcard_layout.axml**。 打开**Resources/layout/flashcard_layout.axml**和其内容替换为以下代码： 

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

此布局定义的单个闪存卡片段;每个片段组成`TextView`显示数学问题使用较大 (100sp) 的字体。 此文本垂直和水平居中，闪存卡上。 



### <a name="create-the-initial-flashcardfragment-class"></a>创建初始 FlashCardFragment 类

添加名为的新文件**FlashCardFragment.cs**和其内容替换为以下代码：

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

此代码存根不可或缺`Fragment`定义将用于显示闪存卡。 请注意，`FlashCardFragment`派生自的支持库版本`Fragment`中定义`Android.Support.V4.App.Fragment`。 构造函数为空，以便`newInstance`工厂方法用于创建一个新`FlashCardFragment`而不是一个构造函数。 

`OnCreateView`生命周期方法创建并配置`TextView`。 增大的片段的布局`TextView`，并返回夸大`TextView`给调用方。 `LayoutInflater` 并`ViewGroup`传递给`OnCreateView`，以便它可以放大量布局。 `savedInstanceState`捆绑包包含数据的`OnCreateView`用于重新创建`TextView`从已保存状态。 

通过调用显式放大片段的视图`inflater.Inflate`。 `container`自变量是视图的父级，并`false`标志指示 inflater 不要夸大的视图添加到视图的父级 (将添加时`ViewPager`调用的适配器的`GetItem`更高版本在此方法演练）。 



### <a name="add-state-code-to-flashcardfragment"></a>将状态代码添加到 FlashCardFragment

类似于活动具有一个片段`Bundle`它用来保存和检索其状态。 在中**FlashCardPager**，则此`Bundle`用来保存问题和回答有关关联的闪存卡的文本。 在中**FlashCardFragment.cs**，添加以下`Bundle`顶部的关键`FlashCardFragment`类定义： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改`newInstance`工厂方法，使其创建`Bundle`对象并使用上述密钥存储传递的问题和回答片段中的文本，它实例化之后： 

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

修改片段生命周期方法`OnCreateView`来检索此信息从传入的捆绑和加载到的问题文本`TextBox`: 

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

`answer`不在这里，使用变量，但它将在事件处理程序代码添加到此文件时更高版本使用。 


## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用适配器控制器对象位于之间`ViewPager`和数据源 (请参阅图中 ViewPager[适配器](~/android/user-interface/controls/view-pager/index.md#adapter)文章)。 若要访问此数据，`ViewPager`要求你提供自定义适配器派生自`PagerAdapter`。 此示例使用片段，因为它使用`FragmentPagerAdapter` &ndash; `FragmentPagerAdapter`派生自`PagerAdapter`。 
`FragmentPagerAdapter` 表示为每个页`Fragment`的永久保留的片段管理器中，只要用户可以返回到页。 为浏览页用户扫`ViewPager`，则`FragmentPagerAdapter`从数据源中提取信息并使用它来创建`Fragment`s 表示`ViewPager`显示。 

当您实现`FragmentPagerAdapter`，必须重写以下：

-   **计数**&ndash;只读属性，该属性返回的视图 （页面） 可用。

-   **GetItem** &ndash;返回要为指定的页显示的片段。

添加名为的新文件**FlashCardDeckAdapter.cs**和其内容替换为以下代码：

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

此代码存根不可或缺`FragmentPagerAdapter`实现。 在以下部分中，每种方法将被替换的工作代码。 构造函数的目的是将传递到片段管理器`FlashCardDeckAdapter`的基类构造函数。 



### <a name="implement-the-adapter-constructor"></a>实现适配器构造函数

当应用程序实例化`FlashCardDeckAdapter`，它会提供对片段管理器和实例化的引用`FlashCardDeck`。 将以下成员变量添加到顶部`FlashCardDeckAdapter`类中**FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

添加以下代码行`FlashCardDeckAdapter`构造函数： 

```csharp
this.flashCardDeck = flashCards;
```

这行代码将存储`FlashCardDeck`实例的`FlashCardDeckAdapter`将使用。 



### <a name="implement-count"></a>实现计数

`Count`实现是相对简单： 它在闪存卡一副纸牌中返回的闪存卡数。 将 `Count` 替换为以下代码： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards`属性的`FlashCardDeck`数据集中返回的闪存卡 （的片段数） 数。 



### <a name="implement-getitem"></a>实现 GetItem

`GetItem`方法返回与给定的位置相关联的片段。 当`GetItem`调用中 flash 纸牌的位置返回`FlashCardFragment`配置为在该位置显示闪存卡问题。 将 `GetItem` 方法替换为以下代码： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此代码将执行以下操作：

1.  查找数学问题字符串`FlashCardDeck`指定位置的一副纸牌。 

2.  查找答案字符串`FlashCardDeck`指定位置的一副纸牌。 

3.  调用`FlashCardFragment`工厂方法`newInstance`、 传入的闪存卡问题和答案字符串。 

4.  创建并返回新的闪存卡`Fragment`，包含该位置的问题和答案的文本。 

当`ViewPager`呈现`Fragment`在`position`，它将显示`TextBox`包含驻留在数学问题字符串`position`flash 卡片组中。 



## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

既然`FlashCardDeckAdapter`是实现，是时候将其添加到`ViewPager`。 在中**MainActivity.cs**，将以下代码行添加到末尾`OnCreate`方法：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此代码实例化`FlashCardDeckAdapter`，并传入`SupportFragmentManager`中的第一个参数。 ( `SupportFragmentManager` FragmentActivity 属性用于获取对引用`FragmentManager`&ndash;有关详细信息`FragmentManager`，请参阅[管理片段](~/android/platform/fragments/managing-fragments.md)。) 

现已完成的核心实现&ndash;生成并运行应用。
应看到显示在屏幕上，在左侧的下一步的屏幕截图中所示的第一个图像的闪存卡一副纸牌。 轻扫保留以查看更多的闪存卡，然后右轻扫可向后移动到闪存卡一副纸牌：

[![FlashCardPager 应用而无需寻呼指示器的示例屏幕快照](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>添加页导航指示器

此最小`ViewPager`实现显示每个闪存卡中的一副纸牌，但它可以提供没有指明用户所在的一副纸牌中。 下一步是添加`PagerTabStrip`。 `PagerTabStrip`通知的用户有关的问题数显示，并提供导航上下文通过显示上一个和下一步闪存卡的提示。 

打开**Resources/layout/Main.axml**并添加`PagerTabStrip`到布局：

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

当生成并运行应用时，应该会看到空`PagerTabStrip`显示在每个闪存卡的顶部： 

[![如果没有文本的 PagerTabStrip 特写](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>显示标题

若要添加到每个页选项卡标题，实现`GetPageTitleFormatted`适配器中的方法。 `ViewPager` 调用`GetPageTitleFormatted`（如果已实现） 获取描述指定位置处的页的标题字符串。 添加以下方法`FlashCardDeckAdapter`类中**FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

此代码将闪存卡一副纸牌中的位置转换为问题数。 生成的字符串转换为 Java`String`返回到`ViewPager`。 当使用此新方法运行该应用时，每个页面将显示问题数量中的`PagerTabStrip`: 

[![屏幕截图的 FlashCardPager 与每个页面上方显示的问题数](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以轻扫来回若要查看每个闪存卡顶部显示闪存卡一副纸牌中的问题编号。 



## <a name="handle-user-input"></a>处理用户输入

**FlashCardPager**提供了一系列中基于片段的闪存卡`ViewPager`，但它还没有一种方法，以显示每个问题的答案。 在本部分中，事件处理程序添加到`FlashCardFragment`来在用户点击闪存卡问题文本上时显示答案。 

打开**FlashCardFragment.cs**并将以下代码添加到末尾`OnCreateView`方法视图返回给调用方之前： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

这`Click`事件处理程序中将显示当用户点击 toast 通知显示答案`TextBox`。 `answer`变量已初始化之前从传递给绑定读取状态信息时`OnCreateView`。 生成和运行应用，然后点击上每个闪存卡以查看解答的问题文本： 

[![屏幕截图的 FlashCardPager 应用 Toast 点击数学问题时](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager**显示在本演练中使用`MainActivity`派生自`FragmentActivity`，但也可以派生`MainActivity`从`AppCompatActivity`（其中还提供对管理支持片段）。 若要查看`AppCompatActivity`示例中，请参阅[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/)示例库中。 



## <a name="summary"></a>总结

本演练提供如何生成基本的分步示例`ViewPager`-基于应用程序使用`Fragment`s。 显示包含闪存卡问题和答案，示例数据源`ViewPager`布局，以显示闪存卡中，和一个`FragmentPagerAdapter`连接的子类`ViewPager`到数据源。 为了帮助用户可以浏览闪存卡，说明已包含说明了如何添加`PagerTabStrip`若要在每个页面的顶部显示的问题数量。 最后，添加事件处理代码是为了在用户点击闪存卡问题时显示答案。 



## <a name="related-links"></a>相关链接

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
