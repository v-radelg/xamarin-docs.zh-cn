---
title: 专用片段类
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 75d95d630415cdaa4c0c1ed3b8ddebb32b8e3c4d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670060"
---
# <a name="specialized-fragment-classes"></a>专用片段类

片段 API 提供了封装应用程序中的更常见功能的一些其他子类。 这些子类是：

-   **ListFragment** &ndash;此片段用于显示的项绑定到如数组或游标的数据源的列表。

-   **DialogFragment** &ndash;作为包装一个对话框，使用此片段。 该片段将显示在其活动之上对话框。

-   **PreferenceFragment** &ndash;此片段用于显示作为列表的首选项对象。



## <a name="the-listfragment"></a>ListFragment

`ListFragment`的概念和功能提供给非常类似`ListActivity`; 它是托管的包装器`ListView`片段中。 下的图显示`ListFragment`平板电脑和手机上运行：

[![屏幕截图的 ListFragment 平板电脑和手机上](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>绑定数据和 ListAdapter

`ListFragment`类已经提供了默认的布局，因此不需要重写`OnCreateView`若要显示的内容`ListFragment`。 `ListView`通过使用绑定到数据`ListAdapter`实现。 下面的示例演示如何这也可以由通过将简单的字符串数组：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

设置时`ListAdapter`，务必要使用`ListFragment.ListAdapter`属性，而不`ListView.ListAdapter`属性。 使用`ListView.ListAdapter`将导致重要的初始化代码要跳过。



### <a name="responding-to-user-selection"></a>响应用户选择

若要响应用户选择，应用程序必须重写`OnListItemClick`方法。 下面的示例显示了一个这种可能性：

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

在上面的代码，当用户选择中的项`ListFragment`，托管活动，其中显示有关所选的项的更多详细信息中显示新片段。



## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是用于显示一个对话框对象内的活动窗口的顶部将浮动的片段的片段。 它旨在替换为托管对话框中 （从开始在 Android 3.0 中） 的 Api。 以下屏幕截图显示的示例`DialogFragment`:

[![显示添加新车辆 EditBox DialogFragment 的屏幕截图](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

一个`DialogFragment`可确保该片段和对话框之间的状态保持一致。 所有的交互和对话框对象的控件应执行的操作通过`DialogFragment`API，并不会执行与直接调用对话框对象上。 `DialogFragment` API 提供了与每个实例`Show()`方法，用来显示一个片段。 有两种方法，若要消除的片段：

-  调用`DialogFragment.Dismiss()`上`DialogFragment`实例。 

-  显示另一个`DialogFragment`。

若要创建`DialogFragment`，一个类继承自`Android.App.DialogFragment,`和设置将覆盖以下两种方法之一：

- **OnCreateView** &ndash;这将创建并返回的视图。

- **OnCreateDialog** &ndash;这将创建一个自定义对话框。 它通常用于显示*AlertDialog*。 当重写此方法，不需要重写`OnCreateView`。



### <a name="a-simple-dialogfragment"></a>简单 DialogFragment

以下屏幕截图显示了一个简单`DialogFragment`，其`TextView`并将两个`Button`s:

[![使用一个 TextView 和两个按钮的示例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView`将显示的用户已单击了中的一个按钮的次数`DialogFragment`，而单击其他按钮将关闭该片段。 有关代码`DialogFragment`是：

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```


### <a name="displaying-a-fragment"></a>显示片段

像所有片段`DialogFragment`显示的上下文中`FragmentTransaction`。

`Show()`方法`DialogFragment`采用`FragmentTransaction`和一个`string`作为输入。 对话将添加到的活动和`FragmentTransaction`提交。

以下代码演示了一个活动可能会使用一种`Show()`方法来显示`DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>正在取消片段

调用`Dismiss()`的实例上`DialogFragment`导致要从活动中删除的片段并提交该事务。
将调用标准的片段生命周期方法涉及使用片段的析构。


### <a name="alert-dialog"></a>警报对话框

而不是替代`OnCreateView`、 一个`DialogFragment`可能会改为重写`OnCreateDialog`。 这允许应用程序来创建[AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/)由一个片段。 下面的代码是使用的示例，`AlertDialog.Builder`若要创建`Dialog`:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```



## <a name="preferencefragment"></a>PreferenceFragment

为了帮助管理首选项，该片段 API 提供了`PreferenceFragment`子类。 `PreferenceFragment`类似于[PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash;它会在片段中显示给用户的首选项的层次结构。 当用户首选项与交互时，它们将自动保存到[SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高版本的应用程序中，使用`PreferenceFragment`处理应用程序中的首选项。 下图显示的示例`PreferenceFragment`:

[![使用内联、 对话框和启动首选项的示例 PreferencesFragment](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>从资源中创建首选项片段

片段可能由使用放大从 XML 资源文件的首选项[PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/)方法。 逻辑起点片段的生命周期中调用此方法会采用`OnCreate`方法。

`PreferenceFragment`图所示更高版本已通过从 XML 加载资源。 资源文件是：

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

片段的首选项的代码如下所示：

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```



### <a name="querying-activities-to-create-a-preference-fragment"></a>查询活动创建的首选项片段

另一种方法创建`PreferenceFragment`涉及查询活动。 每个活动可以使用[元数据\_密钥\_首选项](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/)将指向的 XML 资源文件的属性。 在 Xamarin.Android 中，这是通过装饰的活动`MetaDataAttribute`，然后指定要使用的资源文件。 `PreferenceFragment`类提供了方法[AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) 可用于查询用于查找此 XML 资源和放大量有关它的首选项层次结构的活动。

在以下代码片段中，它使用提供了此过程的一个示例`AddPreferencesFromIntent`若要创建`PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android 将看看的类`MyActivityWithPreference`。 类必须标有`MetaDataAttribute,`如下面的代码段中所示：

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

`MetaDataAttribute`声明的 XML 资源文件`PreferenceFragment`将使用放大量首选项层次结构。 如果`MetatDataAttribute`未提供，则在运行时将引发异常。 此代码运行时，`PreferenceFragment`显示如以下屏幕截图中所示：

[![PreferenceFragment 显示示例应用程序的屏幕截图](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
