---
title: Android 资源基础知识
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: 1df10c4b8eaa30ce417feb2abae7f52b2494edf6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526364"
---
# <a name="android-resource-basics"></a>Android 资源基础知识

几乎所有 Android 应用程序都将具有某种类型的资源;至少, 它们通常以 XML 文件的形式具有用户界面布局。 首次创建 Xamarin Android 应用程序时, 会通过 Xamarin Android 项目模板设置默认资源:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![资源文件](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![资源文件](android-resource-basics-images/01-resource-files-xs.png)
 
-----

在资源文件夹中创建组成默认资源的五个文件:

- **Icon:** &ndash;应用程序的默认图标

- Main.axml&ndash;应用程序的默认用户界面布局文件。 请注意, Android 使用 **.xml**文件扩展名时, Xamarin 使用**main.axml**文件扩展名。

- String **.xml** &ndash;一个字符串表, 可帮助本地化应用程序

- AboutResources 不是必需的, 可以安全地删除。 &ndash; 它只是提供资源文件夹以及其中的文件的高级概述。

- Resource.designer.cs&ndash;此文件通过 Xamarin 自动生成和维护, 并保存分配给每个资源的唯一 ID。 这与在 Java 中编写的 Android 应用程序所使用的 R .java 文件非常相似, 但用途相同。 它是由 Xamarin Android 工具自动创建的, 并将随时重新生成。


## <a name="creating-and-accessing-resources"></a>创建和访问资源

创建资源与将文件添加到相关资源类型的目录非常简单。 下面的屏幕截图显示了德语区域设置的字符串资源已添加到项目。 将**string .xml**添加到文件中时, "**生成" 操作**已自动设置为 Xamarin 的**AndroidResource** 。 Android 工具:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![将 String .xml 的生成操作设置为 AndroidResource](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![将 String .xml 的生成操作设置为 AndroidResource](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

这允许 Xamarin tools 将资源正确编译并嵌入到 APK 文件中。 如果由于某种原因而**生成操作**未设置为**Android 资源**, 则将从 APK 中排除这些文件, 尝试加载或访问资源的任何尝试都会导致运行时错误, 应用程序将崩溃。

另外, 请务必注意, 尽管 Android 仅支持资源项的小写文件名, 但包容性更多;它支持大写和小写文件名。 映像名称的约定是使用小写形式的下划线作为分隔符 (例如, **my\_image\_name .png**)。 请注意, 如果使用破折号或空格作为分隔符, 则无法处理资源名称。

将资源添加到项目后, 可通过两种方式在应用程序&ndash;中以编程方式 (在代码中) 或从 XML 文件中使用它们。


## <a name="referencing-resources-programmatically"></a>以编程方式引用资源

若要以编程方式访问这些文件, 将为它们分配唯一的资源 ID。 此资源 ID 是在名`Resource`为的特殊类中定义的一个整数, 在**Resource.designer.cs**文件中找到, 如下所示:

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

每个资源 ID 都包含在与资源类型相对应的嵌套类中。 例如, 将文件**图标 .png**添加到项目中时, Xamarin 更新了`Resource`类, `Drawable`并创建了一个名为的嵌套类, 并在其中指定`Icon`了常量。
这允许在代码中将文件**图标 .png**作为`Resource.Drawable.Icon`引用。 不应手动编辑类,因为对它所做的任何更改都将被Xamarin覆盖。`Resource`

以编程方式引用资源时 (在代码中), 可以通过资源类层次结构访问这些资源, 该层次结构使用以下语法:

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

- **PackageName**&ndash;提供资源的包, 仅当使用其他包中的资源时才需要此包。

- **ResourceType**&ndash;这是上面所述的资源类内的嵌套资源类型。

- **资源名称**&ndash;这是 XML 元素中的资源的文件名 (不带扩展名) 或 android: name 属性的值。


## <a name="referencing-resources-from-xml"></a>从 XML 引用资源

XML 文件中的资源可通过以下特殊语法来访问:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

- **PackageName**&ndash;提供资源的包, 仅当使用其他包中的资源时才需要此包。

- **ResourceType**&ndash;这是资源类中的嵌套资源类型。

- **资源名称**这是资源的文件名 (*不包括*文件类型扩展名) 或 XML 元素中的资源的`android:name`属性值。 &ndash;

例如, 布局文件的内容**main.axml**如下所示:

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

此示例具有一个[`ImageView`](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) , 它需要一个名为 "**标记**" 的可绘制资源。 将其`src`特性设置为`@drawable/flag`。 `ImageView` 当活动开始时, Android 将在目录**资源/可绘制**的文件中查找名为 "" 的文件, 该文件扩展名可以是另一个图像格式, 如 "**标志 .jpg**") 并加载该文件并`ImageView`将其显示在中。
运行此应用程序时, 它将如下图所示:

![已本地化 ImageView](android-resource-basics-images/03-localized-screenshot.png)
