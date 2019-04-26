---
title: ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9d9bef6d1a0817abc12b5a9bd266b1e1e7d38348
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949603"
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>概述

当使用`TabActivity`，代码以创建选项卡图标具有针对 Android 4.0 框架运行时不起作用。 尽管它在功能上非常像以前那样在 2.3 开始之前, 的 Android 版本`TabActivity`类本身已在 4.0 中弃用。 创建选项卡式的界面的新方法引入了使用操作栏中，我们接下来要讨论的。


## <a name="action-bar-tabs"></a>操作栏选项卡

在操作栏包括支持 Android 4.0 中添加选项卡式的接口。
以下屏幕截图显示了此类接口的示例。

[![在仿真程序; 中运行的应用的屏幕截图显示两个选项卡](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要在操作栏中创建选项卡，我们首先需要设置其`NavigationMode`属性以支持的选项卡。 在 Android 4 中，`ActionBar`属性是可在活动类，我们可以用来设置上`NavigationMode`如下所示：

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成此操作后，我们可以通过调用创建一个选项卡`NewTab`操作栏上的方法。 使用此选项卡实例，我们可以调用`SetText`和`SetIcon`方法来设置选项卡的标签文本和图标，如下所示的代码中按顺序进行这些调用：

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

但是，我们可以添加选项卡之前，我们需要处理`TabSelected`事件。 在此处理程序，我们可以创建选项卡的内容。操作栏选项卡用于处理*片段*，表示在活动中的用户界面的一部分的类。 对于此示例中，片段的视图包含单个`TextView`，其中我们放在大量我们`Fragment`子类如下：

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);
       
        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

事件自变量传入`TabSelected`事件属于类型`TabEventArgs`，其中包括`FragmentTransaction`属性，我们可以使用添加该片段，如下所示：

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最后，我们可以将标签添加到操作栏通过调用`AddTab`方法在此代码中所示：

```csharp
this.ActionBar.AddTab (tab);
```

有关完整示例，请参阅*HelloTabsICS*本文档的示例代码中的项目。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`类，用于执行操作栏中的共享操作。 它负责使用的应用可以处理共享意向并从操作栏更高版本到这些保留的以前用过的应用程序，以方便访问的历史记录列表创建操作视图。 这允许应用程序通过在 Android 保持一致的用户体验来共享数据。


### <a name="image-sharing-example"></a>映像共享示例

例如，下面是操作栏与菜单项来共享映像的屏幕截图 (摘自[ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)示例)。 当用户点击操作栏上的菜单项时，ShareActionProvider 加载应用程序能够处理与关联的意向`ShareActionProvider`。 在此示例中，消息传送应用程序之前使用，以便在操作栏上显示。

[![消息传送操作栏中的应用程序图标的屏幕截图](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


当用户单击操作栏中的项时，会启动消息传递应用，其中包含共享的映像，如下所示：

[![消息传送应用显示 monkey 图像的屏幕截图](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>指定的操作提供程序类

若要使用`ShareActionProvider`，请将`android:actionProviderClass`属性在操作栏菜单的 XML 中的菜单项，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>以下菜单

若要放大量菜单上，我们重写`OnCreateOptionsMenu`活动子类中。 对菜单上的引用后，我们可以获取`ShareActionProvider`从`ActionProvider`属性的菜单项，然后使用 SetShareIntent 方法以设置`ShareActionProvider`的意图，如下所示：

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       
           
    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>创建意向

`ShareActionProvider`将使用意向，传递给`SetShareIntent`在上面的代码，以启动适当的活动的方法。 在这种情况下，我们创建使用以下代码将发送映像的意图：

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

包含为与应用程序的资产并复制到可公开访问的位置时创建活动，使其可供其他应用程序，如消息传递应用上面的代码示例中的图像。 本文附带的示例代码包含此示例中，说明其用途的完整源代码。



## <a name="related-links"></a>相关链接

- [Hello 选项卡 ICS （示例）](https://developer.xamarin.com/samples/HelloTabsICS/)
- [ShareActionProvider 演示 （示例）](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
