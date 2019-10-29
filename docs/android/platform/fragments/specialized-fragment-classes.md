---
title: 专用片段类
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027290"
---
# <a name="specialized-fragment-classes"></a>专用片段类

片段 API 提供其他子类，用于封装在应用程序中发现的一些更常用的功能。 这些子类为：

- **ListFragment** &ndash; 此片段用于显示绑定到数据源（例如数组或游标）的项的列表。

- **DialogFragment** &ndash; 此片段用作对话框周围的包装器。 片段会在其活动之上显示该对话框。

- **PreferenceFragment** &ndash; 此片段用于将首选项对象显示为列表。

## <a name="the-listfragment"></a>ListFragment

`ListFragment` 在概念和功能上非常类似于 `ListActivity`;它是在片段中承载 `ListView` 的包装器。 下图显示了在平板电脑和手机上运行的 `ListFragment`：

[在平板电脑和手机上使用 ListFragment 的![屏幕快照](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>用 ListAdapter 绑定数据

`ListFragment` 类已经提供默认布局，因此不需要重写 `OnCreateView` 以显示 `ListFragment`的内容。 使用 `ListAdapter` 实现将 `ListView` 绑定到数据。 下面的示例演示如何使用简单的字符串数组完成此操作：

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

设置 `ListAdapter`时，必须使用 `ListFragment.ListAdapter` 属性，而不是 `ListView.ListAdapter` 属性。 使用 `ListView.ListAdapter` 会导致跳过重要的初始化代码。

### <a name="responding-to-user-selection"></a>响应用户选择

若要响应用户选择，应用程序必须重写 `OnListItemClick` 方法。 下面的示例展示了这种可能性：

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

在上面的代码中，当用户在 `ListFragment`中选择某一项时，将在宿主活动中显示一个新片段，其中显示了有关所选项的更多详细信息。

## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是一个片段，用于显示在活动窗口顶部浮动的片段内的对话框对象。 它旨在替换托管对话框 Api （从 Android 3.0 开始）。 以下屏幕截图显示了一个 `DialogFragment`的示例：

[显示 "添加新车辆" 的 DialogFragment 屏幕截图 "编辑框![](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

`DialogFragment` 确保片段和对话框之间的状态保持一致。 对话框对象的所有交互和控制都应通过 `DialogFragment` API 发生，而不会对对话框对象进行直接调用。 `DialogFragment` API 为每个实例提供了一个用于显示片段的 `Show()` 方法。 可以通过两种方法来删除片段：

- 对 `DialogFragment` 实例调用 `DialogFragment.Dismiss()`。 

- 显示另一个 `DialogFragment`。

若要创建 `DialogFragment`，类继承自 `Android.App.DialogFragment,`，然后重写以下两种方法之一：

- **OnCreateView** &ndash; 此创建并返回视图。

- **OnCreateDialog** &ndash; 这将创建一个自定义对话框。 它通常用于显示*AlertDialog*。 重写此方法时，不需要重写 `OnCreateView`。

### <a name="a-simple-dialogfragment"></a>简单的 DialogFragment

以下屏幕截图显示了一个具有 `TextView` 和两 `Button`的简单 `DialogFragment`：

[使用 TextView 和两个按钮![示例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView` 将显示用户在 `DialogFragment`中单击一个按钮的次数，而单击其他按钮将关闭该片段。 `DialogFragment` 的代码是：

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

与所有片段一样，`DialogFragment` 在 `FragmentTransaction`的上下文中显示。

`DialogFragment` 上的 `Show()` 方法采用 `FragmentTransaction` 和 `string` 作为输入。 此对话框将添加到活动中，并提交 `FragmentTransaction`。

下面的代码演示活动可以使用 `Show()` 方法来显示 `DialogFragment`的一种可能的方法：

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>关闭片段

对 `DialogFragment` 的实例调用 `Dismiss()` 会导致从活动中删除片段并提交该事务。
将调用与某个片段的销毁相关的标准片段生命周期方法。

### <a name="alert-dialog"></a>警报对话框

`DialogFragment` 可以改为重写 `OnCreateDialog`，而不是重写 `OnCreateView`。 这允许应用程序创建由片段管理的[AlertDialog](xref:Android.App.AlertDialog) 。 下面的代码示例使用 `AlertDialog.Builder` 创建 `Dialog`：

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

为了帮助管理首选项，片段 API 提供了 `PreferenceFragment` 子类。 `PreferenceFragment` 类似于[PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; 它将向用户显示一个片段的首选项层次结构。 当用户与首选项交互时，它们将自动保存到[SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高版本的应用程序中，可以使用 `PreferenceFragment` 来处理应用程序中的首选项。 下图显示了 `PreferenceFragment`的示例：

[![示例 PreferencesFragment 与 inline、dialog 和启动首选项](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>从资源创建首选项片段

可以使用[PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*)方法从 XML 资源文件中放大首选项片段。 在片段生命周期中调用此方法的逻辑位置将是 `OnCreate` 方法。

上面所示的 `PreferenceFragment` 通过从 XML 加载资源来创建。 资源文件为：

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

首选项片段的代码如下所示：

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

### <a name="querying-activities-to-create-a-preference-fragment"></a>查询活动以创建首选项片段

创建 `PreferenceFragment` 的另一种方法涉及查询活动。 每个活动都可以使用[元数据\_键\_首选项](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences)属性，该属性将指向 XML 资源文件。 在 Xamarin 中，这是通过将活动与 `MetaDataAttribute`装饰，然后指定要使用的资源文件来完成的。 `PreferenceFragment` 类提供[AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*)方法，该方法可用于查询活动以查找此 XML 资源并为其提供一个首选项层次结构。

下面的代码段中提供了此过程的示例，该代码片段使用 `AddPreferencesFromIntent` 来创建 `PreferenceFragment`：

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

Android 将查看类 `MyActivityWithPreference`。 必须用 `MetaDataAttribute,` 来装饰类，如下面的代码段所示：

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

`MetaDataAttribute` 声明 `PreferenceFragment` 将用于使首选项层次结构膨胀的 XML 资源文件。 如果未提供 `MetatDataAttribute`，则在运行时将引发异常。 此代码运行时，`PreferenceFragment` 如以下屏幕截图所示：

[显示 PreferenceFragment 的示例应用的屏幕截图![](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
