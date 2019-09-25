---
title: 使用 Xamarin. Android Designer
description: 本文是 Android Designer 的演练。 它演示了如何为小颜色浏览器应用程序创建用户界面;此用户界面是完全在设计器中创建的。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: f696e8159d929bf41596c4dc9498e682e361f412
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249991"
---
# <a name="using-the-xamarinandroid-designer"></a>使用 Xamarin. Android Designer

_本文是 Android Designer 的演练。它演示了如何为小颜色浏览器应用程序创建用户界面;此用户界面是完全在设计器中创建的。_

## <a name="overview"></a>概述

可以通过使用 XML 文件以声明方式创建 Android 用户界面，也可以通过编写代码以编程方式创建。 Android Designer 允许开发人员直观地创建和修改声明性布局，而无需手动编辑 XML 文件。 设计器还提供实时反馈，使开发人员无需重新部署到设备或仿真程序即可评估 UI 更改。 这些设计器功能可以极大地加快 Android UI 开发。
本文演示如何使用 Xamarin Android Designer 直观地创建用户界面。

> [!TIP]
> Visual Studio 的较新版本支持在 Android Designer 中打开 .xml 文件。
>
> .axml 和 .xml 文件均受 Android Designer 支持。

## <a name="walkthrough"></a>演练

本演练的目的是使用 Android Designer 为示例 color browser 应用创建用户界面。 Color browser 应用显示颜色列表、颜色名称及其 RGB 值。 你将了解如何将小组件添加到**Design Surface** ，以及如何以可视化方式对这些小组件进行布局。 之后，你将了解如何在**Design Surface**上以交互方式修改小组件，或使用设计器的 "**属性**" 窗格。 最后，你将了解到在设备或模拟器上运行应用时，设计的外观。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>创建新的项目

第一步是创建新的 Xamarin Android 项目。 启动 visual Studio，单击 "**新建项目 ...** "，然后选择**Visual\# C > android > android 应用（Xamarin）** "模板。
将新应用命名为**DesignerWalkthrough** ，然后单击 **"确定"** 。

[![Android 空白应用](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

在 "**新建 Android 应用**" 对话框中，选择 "**空白应用**"，然后单击 **"确定"** ：

[![选择 Android 空白应用模板](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)

### <a name="adding-a-layout"></a>添加布局

下一步是创建一个**LinearLayout**来保存用户界面元素。 在**解决方案资源管理器**中右键单击 "**资源/布局**"，然后选择 "**添加 > 新项 ...** "。在 "**添加新项**" 对话框中，选择 " **Android 布局**"。 将该文件命名为**list_item** ，然后单击 "**添加**"：

[![新布局](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

新的**list_item**布局将显示在设计器中。 请注意，在左窗格&ndash;中*Design Surface*显示**list_item**的两个窗格，而其 XML 源显示在右窗格中。 可以通过单击以下两个窗格之间的 "**交换窗格**" 图标来交换 " **Design Surface** " 和 "**源**" 窗格的位置：

[![设计器视图](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

从 "**视图**" 菜单上，单击 "**其他窗口 > 文档大纲**" 以打开 "**文档大纲**"。 **文档大纲**显示布局当前包含单个**LinearLayout**小组件：

[![文档大纲](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

下一步是在此`LinearLayout`中创建颜色浏览器应用程序的用户界面。

### <a name="creating-the-list-item-user-interface"></a>创建列表项用户界面

如果未显示 "**工具箱**" 窗格，请单击左侧的 "**工具箱**" 选项卡。 在 "**工具箱**" 中，向下滚动到 "**图像 & Media** " 部分，并进一步向`ImageView`下滚动，直到找到：

[![找到 ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

或者，你可以在搜索栏中输入*ImageView*以查找`ImageView`：

[![ImageView 搜索](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

将此`ImageView`拖动到 Design Surface （这`ImageView`将用于在颜色浏览器应用中显示颜色样本）：

[![画布上的 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

接下来，将`LinearLayout (Vertical)`小组件从**工具箱**拖到设计器中。 请注意，蓝色轮廓指示添加`LinearLayout`的的边界。 **文档大纲**显示它是的子级`LinearLayout`，位于： `imageView1 (ImageView)`

[![蓝色轮廓](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

当你`ImageView`在设计器中选择时，蓝色轮廓会移动到`ImageView`外侧。 此外，所选内容将移动`imageView1 (ImageView)`到**文档大纲**中：

[![选择 ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

接下来，将`Text (Large)`小组件从**工具箱**拖到新添加`LinearLayout`的中。 请注意，设计器使用绿色突出显示来指示将插入新小组件的位置：

[![绿色突出显示](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

接下来，在`Text (Small)` `Text (Large)`小组件下面添加小组件：

[![添加小文本小组件](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

此时，设计器图面应类似于以下屏幕截图：

[![设计器布局](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

如果两个`textView`小组件不在`linearLayout1`内部，您可以将其`linearLayout1`拖放到**文档大纲**中，并将其放置在前面的屏幕截图中显示的`linearLayout1`位置（在下缩进）。

### <a name="arranging-the-user-interface"></a>排列用户界面

下一步是修改 UI，使其显示`ImageView`在左侧，将两个`TextView`小组件堆叠`ImageView`到右侧。

1. 选择 `ImageView`。

2. 在**属性窗口**的 "搜索" 框中输入*width* ，并找到 "**布局宽度**"。

3. 将**布局宽度**设置更改为`wrap_content`：

![设置环绕内容](designer-walkthrough-images/vs/15-wrap-content-w158.png)

更改此`Width`设置的另一种方法是单击小组件右侧的三角形，以将其宽度设置切换为`wrap_content`：

![拖动以设置宽度](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

再次单击该三角形会将`Width`设置返回`match_parent`到。 接下来，请在 "**文档大纲**" 窗格中选择`LinearLayout`根：

[![选择根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

选择`LinearLayout`根后，返回到 "**属性**" 窗格，在 "搜索" 框中输入 "*方向*"，然后找到 "**方向**" 设置。 更改**方向** `horizontal`：

![选择横向方向](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

此时，设计器图面应类似于以下屏幕截图。
请注意， `TextView`已将小组件移动到右侧： `ImageView`

[![设计器布局](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>修改间距

下一步是在 UI 中修改填充和边距设置，以便在小组件之间提供更多空间。 `ImageView`在设计图面上选择。 在 "**属性**" 窗格的`min` "搜索" 框中输入。 输入`70dp` **最小高度**和`50dp` **最小宽度**：

[![设置高度和宽度](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

在 "**属性**" 窗格中`padding` ，在搜索框中输入`10dp` ，然后输入进行**填充**。 这些`minHeight`设置和`padding`设置将在的`ImageView`所有边周围添加边距，并将其垂直 elongate。 `minWidth` 请注意，在输入这些值时，布局 XML 会发生更改：

[![设置填充](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

可以通过将值分别输入到**空白边距**、**左边距**、**右**填充和**填充顶部**字段，来独立设置下、左、右和上填充设置。
例如，将 "**左填充**" 字段设置`5dp`为，并将 "**下**边距"、"**右填充**" `10dp`和 "**填充顶部**" 字段设置为：

[![自定义填充设置](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

接下来，调整包含两个`LinearLayout` `TextView`小组件的小组件的位置。 在**文档大纲**中，选择`linearLayout1`。 在 "**属性**" 窗口中`margin` ，在 "搜索" 框中输入。 将**布局边距下**边距、**左边距边距**和**布局边距靠上**设置为`5dp`。 将**布局边距**设置为`0dp`从右到：

[![设置边距](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>删除默认图像

`ImageView`由于正在用于显示颜色（而不是图像），因此下一步是删除模板添加的默认图像源。

1. 在设计**器图面**上选择。 `ImageView`

2. 在 "**属性**" 中，在搜索框中输入*src* 。

3. 单击**Src**属性设置右侧的小正方形，然后选择 "**重置**"：

[![清除 ImageView src 设置](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

这会`android:src="@android:drawable/ic_menu_gallery"`从源 XML 中删除该的`ImageView`。

### <a name="adding-a-listview-container"></a>添加 ListView 容器

定义**list_item**布局后，下一步是将添加`ListView`到主布局。 这`ListView`将包含**list_item**的列表。 

在**解决方案资源管理器**中，打开**Resources/layout/activity_main. main.axml**。 在 "**工具箱**" 中， `ListView`找到小组件，并将其拖放到**Design Surface**上。 设计`ListView`器中的将为空，只不过选中它时轮廓边框的蓝色线除外。 您可以查看**文档大纲**来验证是否已正确添加**ListView** ：

[![新建 ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

默认情况下， `ListView`的`Id`值`@+id/listView1`为。
在`listView1` **文档大纲**中仍处于选中状态时，打开 "**属性**" 窗格，单击 "**排列**方式"，然后选择 "**类别**"。
打开**Main**，找到**Id**属性，将其值更改为`@+id/myListView`：

[![将 id 重命名为 myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

此时，用户界面已准备就绪，可供使用。

### <a name="running-the-application"></a>运行应用程序

打开**MainActivity.cs** ，并将其代码替换为以下代码：

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

此代码使用自定义`ListView`适配器加载颜色信息，并在刚创建的 UI 中显示此数据。 若要使此示例简短，请在列表中对颜色信息进行硬编码，但可以修改适配器以从数据源中提取颜色信息或动态计算颜色信息。 有关`ListView`适配器的详细信息，请参阅[ListView](~/android/user-interface/layouts/list-view/index.md)。

生成并运行应用程序。 以下屏幕截图举例说明了如何在设备上运行应用程序：

[![最终屏幕快照](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="creating-a-new-project"></a>创建新的项目

第一步是创建新的 Xamarin Android 项目。

启动 Visual Studio for Mac 并单击 "**新建项目 ...** "。选择 " **Android 应用**" 模板，然后单击 "**下一步**"：

[![Android 空白应用](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

将新应用命名为**DesignerWalkthrough**。 在 "**目标平台**" 下，选择 "**最新" 和 "最大** **"，** 然后单击

[![名称应用](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

在下一个对话框屏幕中，单击 "**创建**"。

### <a name="adding-a-layout"></a>添加布局

下一步是创建一个**LinearLayout**来保存用户界面元素。

在 Visual Studio for Mac 中，右键单击 "**解决方案**板" 中的 "**资源/布局**"，然后选择 "**添加 > 新文件 ...** "。在 "**新建文件**" 对话框中，选择 " **Android > 布局**"。 将该文件命名为**list_item**并单击 "**新建**"：

[![新布局](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

添加此文件后，新的**list_item**布局会显示在**Design Surface**上（如果看到消息，*此项目包含未成功编译的资源，呈现可能会受到影响*，请单击 "**生成" >生成全部**内容以生成项目）：

[![设计器视图](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

单击设计器底部的 "**源**" 选项卡，查看此布局的 XML 源。 单击右侧的 "**文档大纲**" 选项卡时，将显示布局当前包含单个**LinearLayout**小组件：

[![设计器 XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

下一步是创建颜色浏览器应用的用户界面。

### <a name="creating-the-list-item-user-interface"></a>创建列表项用户界面

单击屏幕底部的 "**设计器**" 选项卡以返回到**设计器图面**。 在右侧的 "**工具箱**" 窗格中，向下滚动到 "**图像 & Media** " `ImageView`部分，找到：

[![找到 ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

或者，你可以在搜索栏中输入*ImageView*以查找`ImageView`：

[![ImageView 搜索](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

将此`ImageView`拖动到**Design Surface** （这`ImageView`将用于在颜色浏览器应用中显示颜色样本）：

[![画布上的 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

接下来，将`LinearLayout (Vertical)`小组件从**工具箱**拖动到**Design Surface**中。 请注意，蓝色轮廓指示添加`LinearLayout`的的边界。 **文档大纲**显示它是的子级`LinearLayout`，如下`imageView1 (ImageView)`所示：

[![蓝色轮廓](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

当你`ImageView`在设计器中选择时，蓝色轮廓会移动到`ImageView`外侧。 此外，所选内容将移动`imageView1 (ImageView)`到**文档大纲**中：

[![选择 ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

接下来，将`Text (Large)`小组件从**工具箱**拖到新添加`LinearLayout`的中。 请注意，当您将鼠标拖动到**Design Surface**上时，它会突出显示新小组件的插入位置。
小组件应位于中`linearLayout1` ，如下所示： `Text (Large)`

[![添加大文本小组件](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

接下来，在`Text (Small)` `Text (Large)`小组件下面添加小组件。 此时， **Design Surface**应类似于以下屏幕截图：

[![添加小文本小组件](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

如果两个`textView`小组件不在`linearLayout1`内部，您可以将其`linearLayout1`拖放到**文档大纲**中，并将其放置在前面的屏幕截图中显示的位置`linearLayout1`（在下缩进）。

### <a name="arranging-the-user-interface"></a>排列用户界面

下一步是修改 UI，使其显示`ImageView`在左侧，将两个`TextView`小组件堆叠`ImageView`到右侧。

1. 选定后，请单击 "属性" 选项卡。 `ImageView`

2. 在 "**属性**" 选项卡下，单击 "**布局**"。

3. 向下滚动到**ViewGroup** ，并`Width`将设置`wrap_content`更改为：

[![设置环绕内容](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

更改此`Width`设置的另一种方法是单击小组件右侧的三角形，以将其宽度设置切换为`wrap_content`：

[![拖动以设置宽度](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

再次单击该三角形会将`Width`设置返回`match_parent`到。 接下来，请在 "**文档大纲**" 窗格中选择`LinearLayout`根：

[![选择根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

选择根`LinearLayout`后，返回到 "**属性**" 选项卡，然后单击 "**小组件**"。 将设置更改为`horizontal` ，如下所示。 `Orientation` 此时， **Design Surface**应类似于以下屏幕截图。 请注意， `TextView`已将小组件移动到右侧： `ImageView`

[![选择横向方向](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)

### <a name="modifying-the-spacing"></a>修改间距

下一步是修改 UI 中的 "填充" 和 "边距" 设置，以便在小组件之间提供更多空间。 选择，然后单击 "**属性**" 下的 "布局" 选项卡。 `ImageView` `50dp` `Padding`将更改为，`Min Height` 将更改`10dp`为，并将更改为。 `70dp` `Min Width`
这会在的`ImageView`所有边周围应用填充，并将其垂直 elongates：

[![设置填充](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

可以通过分别`Top`在、 `Right`、 `Bottom`和`Left`填充字段中输入值，来单独设置上、右、下和左填充设置。 `Left`例如，将填充值设置为`5dp` ，并`Top`将、 `Right`和`Bottom`填充值设置为`10dp`。 请注意， `Padding`设置将更改为这些值的逗号分隔列表：

[![自定义填充设置](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

接下来，调整包含两个`LinearLayout` `TextView`小组件的小组件的位置。 在**文档大纲**中，选择`linearLayout1`。 在 "**属性**" 窗格中，选择 "**布局**" 选项卡。向下滚动到 " **ViewGroup** " 部分， `Left`并分别`Right`将、 `Bottom` `Top`、和`5dp`边距设置`0dp`为、 `5dp` `5dp`、和：

[![设置边距](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>删除默认图像

`ImageView`由于正在用于显示颜色（而不是图像），因此下一步是删除模板添加的默认图像源。

1. 选择 `ImageView`。

2. 单击 "**属性**" 下的 "**小组件**" 选项卡。

3. 清除该`Src`设置，使其为空：

[![清除 ImageView src 设置](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

这会`android:src="@android:drawable/ic_menu_gallery"`从源 XML 中删除该的`ImageView`。

### <a name="adding-a-listview-container"></a>添加 ListView 容器

定义**list_item**布局后，下一步是将添加`ListView`到主布局。 这`ListView`将包含**list_item**的列表。 

在**解决方案资源管理器**中，打开**Resources/layout/main.axml**。
`Button`单击小组件（如果有）并将其删除。 在 "**工具箱**" 中， `ListView`找到小组件，并将其拖放到**Design Surface**上。
设计`ListView`器中的将为空，只不过选中它时轮廓边框的蓝色线除外。 您可以查看**文档大纲**来验证是否已正确添加**ListView** ：

[![新建 ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

默认情况下， `ListView`的`Id`值`@+id/listView1`为。
在`listView1` **文档大纲**中仍处于选中状态时，打开 "**属性**" 窗格，单击 "**排列**方式"，然后选择 "**类别**"。
打开**Main**，找到**Id**属性，将其值更改为`@+id/myListView`：

[![将 id 重命名为 myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

此时，用户界面已准备就绪，可供使用。

### <a name="running-the-application"></a>运行应用程序

打开**MainActivity.cs** ，并将其代码替换为以下代码：

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

此代码使用自定义`ListView`适配器加载颜色信息，并在刚创建的 UI 中显示此数据。 若要使此示例简短，请在列表中对颜色信息进行硬编码，但可以修改适配器以从数据源中提取颜色信息或动态计算颜色信息。 有关`ListView`适配器的详细信息，请参阅[ListView](~/android/user-interface/layouts/list-view/index.md)。

生成并运行应用程序。 以下屏幕截图举例说明了如何在设备上运行应用程序：

[![最终屏幕快照](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----

## <a name="summary"></a>总结

本文逐步介绍了如何在 Visual Studio 中使用 Xamarin Android Designer 来为基本应用程序创建用户界面。
本指南演示了如何为列表中的单个项创建接口，并演示了如何添加小组件并将它们直观地进行布局。
还介绍了如何分配资源，然后在这些小组件上设置不同的属性。
