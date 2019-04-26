---
title: 片段演练 - 第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 7ec8ad6ce428107d2255dd07c7e69c9e77780c09
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026234"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段演练&ndash;横向

[片段演练&ndash;第 1 部分](./walkthrough.md)演示了如何创建和使用面向较小屏幕手机上的 Android 应用中的片段。 本演练的下一步是修改应用程序以充分利用平板电脑上的额外水平间距&ndash;将有一个活动，它将始终为播放列表 ( `TitlesFragment`) 和`PlayQuoteFragment`将动态添加到用户所做的选择内容的响应中的活动：

[![平板电脑上运行的应用](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

在横向模式下运行的电话也将受益于此增强功能：

[![在横向模式下在 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新应用程序来处理横向方向

以下修改之处将生成执行的操作为基础[片段演练-电话](./walkthrough.md)

1. 创建备用布局，同时显示这两`TitlesFragment`和`PlayQuoteFragment`。
1. 更新`TitlesFragment`来检测设备如果同时显示这两个片段，并相应地更改行为。
1. 更新`PlayQuoteActivity`关闭时在设备处于横向模式。

## <a name="1-create-an-alternate-layout"></a>1.创建备用布局

Android 设备上创建主活动后，Android 将决定要加载的布局基于设备的方向。 默认情况下，Android 将提供**Resources/layout/activity_main.axml**布局文件。 对于在横向模式下加载的设备将提供 Android **Resources/layout-land/activity_main.axml**布局文件。 上的参考线[Android 资源](/xamarin/android/app-fundamentals/resources-in-android)包含 Android 如何决定要为应用程序加载哪些资源文件的详细信息。

创建面向的备用布局**横向**中所述的步骤的方向[备用布局](/xamarin/android/user-interface/android-designer/alternative-layout-views)指南。 这应将新的布局资源文件添加到项目中， **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在解决方案资源管理器中的备用布局](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Solution Pad 中的备用布局](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

在创建备用布局之后, 编辑该文件的源**Resources/layout-land/activity_main.axml** ，使其匹配此 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

活动的根视图提供的资源 ID`two_fragments_layout`并且具有两个子视图`fragment`和一个`FrameLayout`。 虽然`fragment`静态加载`FrameLayout`充当将被替换为在运行时通过的"占位符" `PlayQuoteFragment`。 每次在中选择新 play `TitlesFragment`，则`playquote_container`将使用的新实例更新`PlayQuoteFragment`。

子视图的每个将占用其父级的最大高度。 由控制每个子视图的宽度`android:layout_weight`和`android:layout_width`属性。 在此示例中，每个子视图将占用 50%的宽度由父提供。 请参阅[Google 的文档上 LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html)有关详细信息_布局权重_。

## <a name="2-changes-to-titlesfragment"></a>2.对 TitlesFragment 的更改

一旦创建备用布局后，有必要更新`TitlesFragment`。 当应用显示的两个片段上一个活动，然后`TitlesFragment`应加载`PlayQuoteFragment`父活动中。 否则为`TitlesFragment`应会启动`PlayQuoteActivity`哪台主机`PlayQuoteFragment`。 一个布尔型标志将帮助`TitlesFragment`确定应使用哪种行为。 此标志将在初始化`OnActivityCreated`方法。

首先，在顶部添加一个实例变量`TitlesFragment`类：

```csharp
bool showingTwoFragments;
```

然后，添加以下代码片段`OnActivityCreated`初始化该变量： 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

如果设备运行在横向模式下，则`FrameLayout`使用的资源 ID`playquote_container`将显示在屏幕上，因此`showingTwoFragments`将初始化为`true`。 如果设备运行在纵向模式下，然后`playquote_container`不会在屏幕上，因此`showingTwoFragments`将`false`。

`ShowPlayQuote`方法将需要更改它如何显示引号&ndash;片段或启动新的活动。  更新`ShowPlayQuote`方法以加载片段时显示两个片段，否则应启动活动：

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

如果用户已经选择了不同于当前显示在播放`PlayQuoteFragment`，然后一个新`PlayQuoteFragment`创建并将内容替换为`playquote_container`的上下文中`FragmentTransaction`。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整代码

在完成到以前的所有更改后`TitlesFragment`，完整的类应与此代码相匹配：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }


        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3.对 PlayQuoteActivity 的更改

没有一个最终的详细信息，需要注意：`PlayQuoteActivity`时在设备处于横向模式下不需要。 如果设备在横向模式下`PlayQuoteActivity`应该是不可见。 更新`OnCreate`方法的`PlayQuoteActivity`，以便它将关闭本身。 此代码是最终版本`PlayQuoteActivity.OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

此修改将添加对设备方向的检查。 如果是，在横向模式下则`PlayQuoteActivity`关闭本身。

## <a name="4-run-the-application"></a>4.运行此应用程序

完成这些更改后，运行应用时，旋转为横向模式下 （如有必要），该设备，然后选择播放。 应为的播放列表在同一屏幕上显示引号：

[![在横向模式下在 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板电脑上运行应用](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
