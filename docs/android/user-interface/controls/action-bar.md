---
title: 适用于 Xamarin 的 ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 4a0d0e46147a37da4787224e797d403ab7b1097e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643040"
---
# <a name="actionbar-for-xamarinandroid"></a>适用于 Xamarin 的 ActionBar

使用`TabActivity`时, 用于创建选项卡图标的代码在针对 Android 4.0 framework 运行时不起作用。 尽管其工作方式与2.3 之前版本的 Android 中的`TabActivity`工作方式相同, 但在4.0 中已弃用类本身。 引入了一种创建选项卡式界面的新方法, 该方法使用操作栏, 接下来我们将讨论。


## <a name="action-bar-tabs"></a>操作栏选项卡

操作栏包括支持在 Android 4.0 中添加选项卡式界面。
以下屏幕截图显示了此类接口的示例。

[![模拟器中运行的应用程序的屏幕截图;显示了两个选项卡](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要在操作栏中创建选项卡, 首先需要将其`NavigationMode`属性设置为 "支持选项卡"。 在 Android 4 中, `ActionBar` "活动" 类上提供了一个属性, 可用于将设置为`NavigationMode`如下所示:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成此操作后, 可以通过对操作栏调用`NewTab`方法来创建一个选项卡。 对于此选项卡实例, 我们可以调用`SetText`和`SetIcon`方法来设置选项卡的标签文本和图标; 这些调用按下面所示的代码顺序进行:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

但在添加选项卡之前, 我们需要处理`TabSelected`事件。 在此处理程序中, 可以创建选项卡的内容。操作栏选项卡用于处理片段, 这些*片段*是表示活动中部分用户界面的类。 在此示例中, 片段的视图包含单个`TextView`, 这会将其放`Fragment`在子类中, 如下所示:

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

`TabSelected`事件中传递的事件参数的类型`TabEventArgs`为, 其中包含一个`FragmentTransaction`可用于添加片段的属性, 如下所示:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最后, 可以通过调用`AddTab`方法将选项卡添加到操作栏, 如以下代码所示:

```csharp
this.ActionBar.AddTab (tab);
```

有关完整的示例, 请参阅本文档的示例代码中的*HelloTabsICS*项目。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`类使共享操作可以从操作栏进行。 它负责创建一个操作视图, 其中包含可处理共享意向的应用列表, 并保留以前使用的应用程序的历史记录, 以便以后从操作栏轻松访问这些应用程序。 这允许应用程序通过在整个 Android 中一致的用户体验来共享数据。


### <a name="image-sharing-example"></a>图像共享示例

例如, 下面是一个操作栏屏幕截图, 其中包含一个用于共享图像的菜单项 (取自[ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)示例)。 当用户点击操作栏上的菜单项时, ShareActionProvider 会加载该应用程序以处理与`ShareActionProvider`相关联的意向。 在此示例中, 以前使用过消息应用程序, 因此它显示在操作栏。

[![操作栏中的消息应用程序图标的屏幕截图](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


当用户单击操作栏中的项时, 将启动包含共享映像的消息应用, 如下所示:

[![显示猴子映像的消息应用程序的屏幕截图](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>指定操作提供程序类

若要使用`ShareActionProvider`, 请对`android:actionProviderClass`操作栏菜单的 XML 中的菜单项设置属性, 如下所示:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>因为这样做菜单

为了放大菜单, 我们在活动`OnCreateOptionsMenu`子类中重写。 引用菜单后, 可以`ShareActionProvider` `ActionProvider`从菜单项的属性获取, 然后使用`ShareActionProvider`SetShareIntent 方法来设置的目的, 如下所示:

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

将使用传递给上述代码中的`SetShareIntent`方法的意向来启动相应的活动。 `ShareActionProvider` 在此示例中, 我们使用以下代码创建一个发送映像的意图:

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

上面的代码示例中的图像作为资产包含在应用程序中, 并在创建活动时已复制到可公开访问的位置, 因此其他应用程序 (如消息应用) 可以访问它。 本文附带的示例代码包含了此示例的完整源, 并说明了其用法。



## <a name="related-links"></a>相关链接

- [Hello 选项卡 ICS (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellotabsics)
- [ShareActionProvider 演示 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)
- [冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
