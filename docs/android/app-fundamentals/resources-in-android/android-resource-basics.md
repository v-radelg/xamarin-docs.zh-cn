---
title: Android 资源基础知识
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: b0f747c37362997563a35d9b94f8e677d4104ee1
ms.sourcegitcommit: e7f27ba75cae5099ef053b819b84132a77d4f9e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59587759"
---
# <a name="android-resource-basics"></a>Android 资源基础知识

几乎所有的 Android 应用程序将在其中; 具有某种形式的资源在最低限度它们通常具有 XML 文件的窗体中的用户界面布局。 当首次创建 Xamarin.Android 应用程序时，默认资源是通过 Xamarin.Android 项目模板的安装程序：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![资源文件](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![资源文件](android-resource-basics-images/01-resource-files-xs.png)
 
-----

在资源文件夹中创建了五个文件构成默认资源：

-  **Icon.png** &ndash;应用程序的默认图标

-  **Main.axml** &ndash;应用程序的默认用户界面布局文件。 请注意，Android 时使用 **.xml**文件扩展名，Xamarin.Android 使用 **.axml**文件扩展名。

-  **Strings.xml** &ndash;字符串表，以帮助进行本地化的应用程序

-  **AboutResources.txt** &ndash;这不是必需的并安全地删除。 它只提供资源文件夹和文件的高级别概述。

-  **Resource.designer.cs** &ndash;此文件自动生成并维护的 Xamarin.Android 和保存的唯一 ID 分配给每个资源。 这是非常类似，与在 Java 中编写的 Android 应用程序必须 R.java 文件用途相同。 它由 Xamarin.Android 工具自动创建，并将不时重新生成。


## <a name="creating-and-accessing-resources"></a>创建和访问资源

创建资源只需将文件添加到所涉及的资源类型的目录。 下面的屏幕快照显示了德语区域设置的字符串资源已添加到项目。 当**Strings.xml**到文件中，添加**生成操作**自动设置为**AndroidResource**由 Xamarin.Android 工具：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![生成操作设置为 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![生成操作设置为 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

这样，若要正确编译和嵌入的 APK 文件中的资源的 Xamarin.Android 工具。 如果由于某种原因**生成操作**未设置为**Android 资源**、 然后将从 APK 中排除文件和任何尝试加载或访问的资源将导致运行时错误，应用程序将崩溃。

此外，务必注意，当时 Android 仅支持的资源项的小写形式的文件名，Xamarin.Android 有点内更能容忍;它将支持大写和小写的文件名。 映像名称的约定是使用小写带下划线作为分隔符 (例如，**我\_映像\_name.png**)。 请注意是否使用短划线或空格作为分隔符，不能处理资源名称。

资源添加到项目后，有两种方法在应用程序中使用它们&ndash;（内代码） 以编程方式或从 XML 文件。


## <a name="referencing-resources-programmatically"></a>以编程方式引用的资源

若要以编程方式访问这些文件，它们将分配一个唯一的资源 id。 此资源 ID 是一个名为的特殊类中定义一个整数`Resource`，这在文件中找到**Resource.designer.cs**，如下所示：

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

每个资源 ID，包含在对应于资源类型的嵌套类。 例如，当文件**Icon.png**已添加到项目中，Xamarin.Android 更新`Resource`类，创建嵌套的类名为`Drawable`常量的内部使用名为`Icon`。
这样，文件**Icon.png**作为代码中引用`Resource.Drawable.Icon`。 `Resource`类不应进行手动编辑，因为 Xamarin.Android 将覆盖对其进行任何更改。

引用时以编程方式 （在代码中） 的资源，它们可以访问通过使用以下语法的资源类层次结构：

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash;了提供资源和仅的包时需要其他包中的资源的使用。

-  **ResourceType** &ndash;这是上面所述的资源类中的嵌套的资源类型。

-  **资源名称**&ndash;这是 （不带扩展名） 资源的文件名或 XML 元素中的资源使用 android: name 属性的值。


## <a name="referencing-resources-from-xml"></a>从 XML 引用的资源

XML 文件中的资源访问的以下特殊语法：

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

-  **PackageName** &ndash;了提供资源和仅的包时需要其他包中的资源的使用。

-  **ResourceType** &ndash;这是资源类中的嵌套的资源类型。

-  **资源名称**&ndash;这是该资源的文件名 (*而无需*文件类型扩展名) 的值或`android:name`的 XML 元素中的资源的属性。

例如布局文件的内容**Main.axml**，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

此示例中有[ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview)需要一个名为的可绘制资源**标志**。 `ImageView`具有其`src`属性设置为`@drawable/flag`。 Android 活动启动时，将在目录中查找**资源/Drawable**为名为的文件**flag.png** (文件扩展名可能是另一种图像格式，如**flag.jpg**)加载此文件，并将其显示在`ImageView`。
运行此应用程序时，它看起来与以下图像：

![本地化的 ImageView](android-resource-basics-images/03-localized-screenshot.png)
