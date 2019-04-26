---
title: 使用 Xamarin.Android 设计器
description: 本文是 Xamarin.Android 设计器的演练。 它演示了如何创建小型彩色的浏览器应用中，一个用户界面完全在设计器中创建此用户界面。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61207599"
---
# <a name="using-the-xamarinandroid-designer"></a>使用 Xamarin.Android 设计器

_本文是 Xamarin.Android 设计器的演练。它演示了如何创建小型彩色的浏览器应用中，一个用户界面完全在设计器中创建此用户界面。_


## <a name="overview"></a>概述

使用 XML 文件或以编程方式编写代码，可以以声明方式创建 android 用户界面。 Xamarin.Android 设计器允许开发人员能够创建和修改声明性布局以可视方式，而无需手动编辑的 XML 文件。 在设计器还提供允许开发人员评估 UI 更改而无需重新部署到设备或仿真程序的应用程序的实时反馈。 这些设计器功能可以极大地加快 Android UI 开发。
本文演示如何使用 Xamarin.Android 设计器直观地创建用户界面。

## <a name="walkthrough"></a>演练

本演练的目的是使用 Android 设计器创建一个颜色浏览器应用示例的用户界面。 颜色浏览器应用会提供颜色、 它们的名称和其 RGB 值的列表。 您将学习如何将添加到小组件**设计图面**以及如何以可视方式布置这些小组件。 之后，您将学习如何修改小组件上以交互方式**设计图面**或使用设计器的**属性**窗格。 最后，您将了解设计时在设备或仿真器上运行应用的外观。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>创建新的项目

第一步是创建新的 Xamarin.Android 项目。 启动 Visual Studio 中，单击**新建项目...**，然后选择**Visual C\# > Android > Android 应用 (Xamarin)** 模板。
新应用命名**DesignerWalkthrough**然后单击**确定**。

[![空白的 android 应用程序](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

在中**新的 Android 应用**对话框中，选择**空白应用**然后单击**确定**:

[![选择 Android 空白应用模板](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>添加布局

下一步是创建**LinearLayout** ，将存放用户界面元素。 右键单击**资源/布局**中**解决方案资源管理器**，然后选择**添加 > 新建项...**.在中**添加新项**对话框中，选择**Android 布局**。 将文件命名**list_item**然后单击**添加**:

[![新的布局](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

新**list_item**布局显示在设计器中。 请注意两个窗格将显示&ndash;*设计图面*有关**list_item**是可见的左窗格中，而其 XML 源显示在右窗格。 你可以交换的位置**设计图面**和**源**通过单击窗格**交换窗格**图标位于之间的两个窗格：

[![设计器视图](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

从**视图**菜单上，单击**其他 Windows > 文档大纲**以打开**文档大纲**。 **文档大纲**显示布局当前包含单个**LinearLayout**小组件：

[![文档大纲](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

下一步是创建在此颜色浏览器应用的用户界面`LinearLayout`。

### <a name="creating-the-list-item-user-interface"></a>创建列表项用户界面

如果**工具箱**未显示窗格中，单击**工具箱**在左侧选项卡。 在中**工具箱**，向下滚动到**图像和媒体**部分，并继续向下滚动直到找到`ImageView`:

[![找到 ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

或者，可以输入*ImageView*在搜索栏查找`ImageView`:

[![ImageView 搜索](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

将该`ImageView`拖动到设计图面上 (这`ImageView`用于颜色浏览器应用中显示颜色样本):

[![在画布上 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

接下来，拖`LinearLayout (Vertical)`小组件从**工具箱**到设计器。 请注意，一个蓝色的轮廓指示添加的边界`LinearLayout`。 **文档大纲**显示它是的子级`LinearLayout`下的`imageView1 (ImageView)`:

[![蓝色边框](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

当选择`ImageView`设计器中，在蓝色边框移动来包围`ImageView`。 此外，将所选内容移到`imageView1 (ImageView)`中**文档大纲**:

[![选择 ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

接下来，拖`Text (Large)`小组件从**工具箱**到新添加`LinearLayout`。 请注意，在设计器使用绿色突出显示来指示将插入新的小组件的位置：

[![绿色突出显示](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

接下来，添加`Text (Small)`小组件下`Text (Large)`小组件：

[![添加小文本小组件](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

在此情况下，设计器图面应类似于以下屏幕截图：

[![设计器布局](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

如果两个`textView`小组件不是全面`linearLayout1`，你可以将其拖至`linearLayout1`中**文档大纲**并将它们置于，因此它们出现在上面的屏幕截图中所示 (下缩进`linearLayout1`)。


### <a name="arranging-the-user-interface"></a>排列的用户界面

下一步是修改 UI 以显示`ImageView`在左侧，两个`TextView`右侧的小组件堆积`ImageView`。

1.  选择 `ImageView`。

2.  在**属性窗口**，输入*宽度*在搜索框中，找到**布局宽度**。

3.  更改**布局宽度**将设置为`wrap_content`:

![设置包装内容](designer-walkthrough-images/vs/15-wrap-content-w158.png)

若要更改的另一种方法`Width`设置为单击小组件以其宽度设置切换到右侧的三角形`wrap_content`:

![拖动以调整宽度](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

再次单击三角形将返回`Width`设置为`match_parent`。 接下来，请转到**文档大纲**窗格，然后选择根`LinearLayout`:

[![选择根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

与根`LinearLayout`选择，返回到**属性**窗格中，输入*方向*到搜索框中，找到**方向**设置。 更改**方向**到`horizontal`:

![选择水平方向](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

在此情况下，设计器图面应类似于以下屏幕截图。
请注意，`TextView`小组件已移至右侧`ImageView`:

[![设计器布局](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>修改间距

下一步是修改 UI，以提供更多空间之间的小组件中的填充和边距设置。 选择`ImageView`设计图面上。 在中**属性**窗格中，输入`min`在搜索框中。 输入`70dp`有关**最小高度**并`50dp`有关**最小宽度**:

[![设置高度和宽度](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

在中**属性**窗格中，输入`padding`在搜索框，并输入`10dp`有关**填充**。 这些`minHeight`，`minWidth`并`padding`设置周围添加衬距的所有边`ImageView`和垂直拉长。 请注意，布局 XML 更改为输入以下值：

[![设置填充](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

下、 左、 右，和可以通过输入值到独立设置上边距设置**填充底部**，**填充左侧**，**填充右侧**，和**填充顶部**字段，分别。
例如，设置**填充左侧**字段`5dp`和**填充底部**，**填充右侧**，并**填充顶部**字段到`10dp`:

[![自定义的边距设置](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

接下来，调整的位置`LinearLayout`小工具，它包含两个`TextView`小组件。 在中**文档大纲**，选择`linearLayout1`。 在中**属性**窗口中，输入`margin`在搜索框中。 设置**布局边距底部**，**布局距左侧**，并**布局上边距**到`5dp`。 设置**布局边距右**到`0dp`:

[![设置边距](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>删除默认图像

因为`ImageView`用于显示的颜色 （而不是映像） 下, 一步是添加模板的默认图像源中删除。

1.  选择`ImageView`上**设计器图面**。

2.  在中**属性**，输入*src*在搜索框中。

3.  单击右侧的小正方形**Src**属性设置并选择**重置**:

[![清除 ImageView src 设置](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

此操作将删除`android:src="@android:drawable/ic_menu_gallery"`源 XML 中为此， `ImageView`。

### <a name="adding-a-listview-container"></a>将 ListView 容器添加

既然**list_item**定义布局下, 一步是添加`ListView`到主布局。 这`ListView`将包含一系列**list_item**。 

在中**解决方案资源管理器**，打开**Resources/layout/activity_main.axml**。 在中**工具箱**，找到`ListView`小组件并将其拖动到**设计图面**。 `ListView`在设计器中将为空白概述当选中时其边框的蓝线除外。 您可以查看**文档大纲**来确认**ListView**已正确添加：

[![新的 ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

默认情况下`ListView`给定`Id`的值`@+id/listView1`。
虽然`listView1`中仍处于选中状态**文档大纲**，打开**属性**窗格中，单击**排列方式**，然后选择**类别**.
打开**Main**，找到**Id**属性，其将值更改为`@+id/myListView`:

[![将 id 重命名为 myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

此时，已准备好使用用户界面。

### <a name="running-the-application"></a>运行应用程序

打开**MainActivity.cs**和其代码替换为以下代码：

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

此代码使用自定义`ListView`适配器加载颜色信息并在刚创建的 UI 中显示此数据。 若要使此简短的示例，该颜色信息是硬编码在列表中，但无法修改该适配器，以便从数据源中提取颜色信息或动态计算。 有关详细信息`ListView`适配器，请参阅[ListView](~/android/user-interface/layouts/list-view/index.md)。

生成并运行应用程序。 下面的屏幕截图是在设备上运行时应用的显示方式的示例：

[![最终屏幕快照](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="creating-a-new-project"></a>创建新的项目

第一步是创建新的 Xamarin.Android 项目。

启动 Visual Studio for Mac 和单击**新建项目...**.选择**Android 应用**模板，然后单击**下一步**:

[![空白的 android 应用程序](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

新应用命名**DesignerWalkthrough**。 下**目标平台**，选择**最新和最好**然后单击**下一步**:

[![应用程序名称](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

在下一步的对话框屏幕中，单击**创建**。

### <a name="adding-a-layout"></a>添加布局

下一步是创建**LinearLayout** ，将存放用户界面元素。

在 Visual Studio for Mac 中，右键单击**资源/布局**中**解决方案**填充并选择**添加 > 新建文件...**.在中**新的文件**对话框中，选择**Android > 布局**。 将文件命名**list_item**然后单击**新建**:

[![新的布局](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

添加此文件后，新**list_item**布局显示在**设计图面**(如果你看到消息，*此项目包含未成功，编译的资源可能影响渲染*，单击**生成 > 生成所有**以生成项目):

[![设计器视图](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

单击**源**要查看此布局的 XML 源的设计器底部的选项卡。 当您单击**文档大纲**选项卡在右侧，它显示布局当前包含单个**LinearLayout**小组件：

[![设计器 XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

下一步是创建颜色的浏览器应用的用户界面。

### <a name="creating-the-list-item-user-interface"></a>创建列表项用户界面

单击**设计器**返回到屏幕底部的选项卡**设计器图面**。 在中**工具箱**窗格在右侧的向下滚动到**图像和媒体**部分，并找到`ImageView`:

[![找到 ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

或者，可以输入*ImageView*在搜索栏查找`ImageView`:

[![ImageView 搜索](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

将该`ImageView`拖到**设计图面**(这`ImageView`用于颜色浏览器应用中显示颜色样本):

[![在画布上 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

接下来，拖`LinearLayout (Vertical)`小组件从**工具箱**到**设计图面**。 请注意，一个蓝色的轮廓指示添加的边界`LinearLayout`。 **文档大纲**显示它是的子级`LinearLayout`位于如下`imageView1 (ImageView)`:

[![蓝色边框](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

当选择`ImageView`设计器中，在蓝色边框移动来包围`ImageView`。 此外，将所选内容移到`imageView1 (ImageView)`中**文档大纲**:

[![选择 ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

接下来，拖`Text (Large)`小组件从**工具箱**到新添加`LinearLayout`。 请注意，当你拖动到鼠标**设计图面**，它会突出显示在此处插入新的小组件。
`Text (Large)`小组件应位于内`linearLayout1`如下所示：

[![添加大文本小组件](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

接下来，添加`Text (Small)`小组件下`Text (Large)`小组件。 在此情况下，**设计图面**应如以下屏幕截图所示：

[![添加小文本小组件](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

如果两个`textView`小组件不是全面`linearLayout1`，你可以将其拖至`linearLayout1`中**文档大纲**并将它们置于，以便它们显示如上面的屏幕截图中所示 (下缩进`linearLayout1`).


### <a name="arranging-the-user-interface"></a>排列的用户界面

下一步是修改 UI 以显示`ImageView`在左侧，两个`TextView`右侧的小组件堆积`ImageView`。

1.  与`ImageView`处于选中状态，单击**属性**选项卡。

2.  正下方**属性**选项卡上，单击**布局**。

3.  向下滚动到**ViewGroup**并将更改`Width`设置为`wrap_content`:

[![设置包装内容](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

若要更改的另一种方法`Width`设置为单击小组件以其宽度设置切换到右侧的三角形`wrap_content`:

[![拖动以调整宽度](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

再次单击三角形将返回`Width`设置为`match_parent`。 接下来，请转到**文档大纲**窗格，然后选择根`LinearLayout`:

[![选择根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

与根`LinearLayout`选择，返回到**属性**选项卡，单击**小组件**。 更改`Orientation`设置为`horizontal`，如下所示。 在此情况下，**设计图面**应类似于以下屏幕截图。 请注意，`TextView`小组件已移至右侧`ImageView`:

[![选择水平方向](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>修改间距

下一步是修改 UI，以提供更多空间之间的小组件中的填充和边距设置。 选择`ImageView`然后单击**布局**选项卡下**属性**。 更改`Min Width`到`50dp`，则`Min Height`到`70dp`，并`Padding`到`10dp`。
这适用于周围的所有边边距`ImageView`和垂直 elongates:

[![设置填充](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

上、 右、 下、 和左侧边距设置可以单独设置输入值插入`Top`， `Right`， `Bottom`，和`Left`分别填充字段。 例如，设置`Left`填充为值`5dp`和`Top`， `Right`，并`Bottom`中填充值到`10dp`。 请注意，`Padding`设置将更改为逗号分隔这些值的列表：

[![自定义的边距设置](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

接下来，调整的位置`LinearLayout`小工具，它包含两个`TextView`小组件。 在中**文档大纲**，选择`linearLayout1`。 在中**属性**窗格中，选择**布局**选项卡。向下滚动到**ViewGroup**部分，然后设置`Left`， `Top`， `Right`，并且`Bottom`到边距`5dp`， `5dp`， `0dp`，和`5dp`分别：

[![设置边距](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>删除默认图像

因为`ImageView`用于显示的颜色 （而不是映像） 下, 一步是添加模板的默认图像源中删除。

1.  选择 `ImageView`。

2.  单击**小组件**选项卡上的**属性**。

3.  清除`Src`设置，以便为空：

[![清除 ImageView src 设置](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

此操作将删除`android:src="@android:drawable/ic_menu_gallery"`源 XML 中为此， `ImageView`。

### <a name="adding-a-listview-container"></a>将 ListView 容器添加

既然**list_item**定义布局下, 一步是添加`ListView`到主布局。 这`ListView`将包含一系列**list_item**。 

在中**解决方案资源管理器**，打开**Resources/layout/Main.axml**。
单击`Button`小组件 （如果有） 并将其删除。 在中**工具箱**，找到`ListView`小组件并将其拖动到**设计图面**。
`ListView`在设计器中将为空白概述当选中时其边框的蓝线除外。 您可以查看**文档大纲**来确认**ListView**已正确添加：

[![新的 ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

默认情况下`ListView`给定`Id`的值`@+id/listView1`。
虽然`listView1`中仍处于选中状态**文档大纲**，打开**属性**窗格中，单击**排列方式**，然后选择**类别**.
打开**Main**，找到**Id**属性，其将值更改为`@+id/myListView`:

[![将 id 重命名为 myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

此时，已准备好使用用户界面。

### <a name="running-the-application"></a>运行应用程序

打开**MainActivity.cs**和其代码替换为以下代码：

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

此代码使用自定义`ListView`适配器加载颜色信息并在刚创建的 UI 中显示此数据。 若要使此简短的示例，该颜色信息是硬编码在列表中，但无法修改该适配器，以便从数据源中提取颜色信息或动态计算。 有关详细信息`ListView`适配器，请参阅[ListView](~/android/user-interface/layouts/list-view/index.md)。

生成并运行应用程序。 下面的屏幕截图是在设备上运行时应用的显示方式的示例：

[![最终屏幕快照](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>总结

本文介绍使用 Visual Studio 中 Xamarin.Android 设计器创建一个基本的应用的用户界面的过程。
它演示了如何在列表中，创建单个项的界面，它说明如何添加小组件和直观地对其进行安排。
它还介绍了如何将资源分配，然后对这些小组件中设置各种属性。
