---
title: 备选布局视图
description: 本主题说明如何布局可进行版本控制使用的资源限定符。 例如，可以有在设备处于横向模式时，才使用的布局的版本和仅适用于纵向模式下的布局版本。
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 03b80d3fb1ed7c8db108f86b3b3923c20e1d908f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61089690"
---
# <a name="alternative-layout-views"></a>备选布局视图

_本主题说明如何通过使用的资源限定符可以版本布局。例如，创建在设备处于横向模式时，才使用的布局的版本和仅适用于纵向模式下的布局版本。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>创建备用布局

当您单击**备选布局视图**图标 (左侧**设备**)，预览窗格将打开，并列出项目中可用的备用布局。 如果不有任何其他布局**默认**显示视图： 

[![备用布局视图窗格](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "备用布局视图窗格")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

当单击绿色加号旁边**新版**，则**创建布局变体**对话框随即打开，以便你可以选择用于此布局变体的资源限定符： 

[![创建布局变体](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "创建布局变体")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

在下面的示例的资源限定符**屏幕方向**设置为**横向**，并**屏幕大小**更改为**大**. 这将创建名为的新布局版本**大型 land**:

[![大型 land 变体](alternative-layout-views-images/vs/03-large-land-sml.png "大 land 变体")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

请注意，在左侧预览窗格中显示的资源限定符选项的效果。 单击**添加**创建备选布局，并切换到该布局的设计器。 **替代项布局视图**预览窗格指示哪种布局已加载到小型右指针通过设计器，如以下屏幕截图中所示： 

[![加载的布局指示器](alternative-layout-views-images/vs/04-new-layout-sml.png "加载的布局指示符")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)


## <a name="editing-alternative-layouts"></a>编辑其他布局

通常，在创建其他布局时，很才能使适用于布局的所有分支版本的单个更改。 例如，你可能想要将按钮文本更改为在所有布局中的黄色。 如果有大量的布局，并且您需要将传播到所有版本的单个更改，维护可快速变得繁琐且容易出错。

若要简化维护的多个布局版本，在设计器提供了**多编辑**跨一个或多个布局传播所做的更改的模式。 如果存在，多个布局**多编辑**显示图标： 

[![多编辑图标](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多编辑图标")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

当您单击**多编辑**图标，显示线指示 （如下所示） 链接布局; 即，当更改一个布局，则所做的更改传播到任何链接的布局。 您可以通过单击下面的屏幕截图所示的带圆圈的图标中取消链接所有布局： 

[![取消链接所有布局](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消都链接所有布局")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

如果您有两个以上的布局，可以有选择地切换到左侧的每个布局预览版来确定哪些布局链接在一起的编辑按钮。 例如，如果你想要进行传播的单个更改为第一个和最后的三种布局，您将首先取消链接的中间布局如下所示： 

[![取消链接中间布局](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消链接中间布局")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

在此示例中，更改为**默认**或**长**布局将传播到其他布局而不适用于**大型 land**布局。

### <a name="multi-edit-example"></a>多项编辑示例 

一般情况下，当更改一个布局，该相同的更改传播到所有其他链接的布局。 例如，添加一个新`TextView`小组件设**默认**布局，并更改它的文本字符串到`Portrait`将导致相同的更改不会对所有链接的布局。 下面是其外观**默认**布局： 

[![添加 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "添加 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
`TextView`还将添加到**大型 land**布局查看，因为它链接到**默认**布局： 

[![横向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "横向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
但如果你想要进行更改的本地只有一种布局 （也就是说，您不希望更改传播到任何其他布局）？ 若要执行此操作，必须取消链接你想要更改在修改之前，如下所述的布局。 

### <a name="making-local-changes"></a>进行本地更改 

假设我们希望能够在添加这两种布局`TextView`，但我们还想要更改中的文本字符串**大型 land**到布局`Landscape`而非`Portrait`。 如果我们进行到此更改**大型 land**虽然链接这两种布局，更改将传播回**默认**布局。 因此，我们必须首先取消链接两个布局之前做出更改。 当我们修改中的文本**大型 land**到`Landscape`，在设计器将标记的红框指示的更改是本地的与此更改**大型 land**布局是*不*传播回**默认**布局： 

[![本地更改](alternative-layout-views-images/vs/10-local-change-sml.png "本地更改")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
当您单击**默认**布局进行查看时，`TextView`文本字符串仍设置为`Portrait`。 

## <a name="handling-conflicts"></a>处理冲突 

如果您决定更改中的文本的颜色**默认**为绿色的布局，您将看到链接布局上出现一个警告图标。 单击该布局将打开以显示冲突的布局。 红框突出显示导致冲突的小组件，并显示以下消息：*最近的更改冲突导致此备选布局中*。 

[![冲突更改](alternative-layout-views-images/vs/11-conflicting-change-sml.png "冲突更改")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
一个*冲突框*显示在小组件的右侧，以解释该冲突： 

[![冲突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

冲突框中显示的已更改的属性列表并列出它们的值。 单击**忽略冲突**属性更改仅适用于此小组件。 单击**Apply**属性更改应用于此小组件以及关于中链接的与小组件**默认**布局。 如果应用了所有属性更改，将自动丢弃该冲突。 

### <a name="view-group-conflicts"></a>查看组冲突 

属性更改不会冲突的唯一来源。 插入或删除小组件时，可以检测到冲突。 例如，当**大型 land**布局从已取消链接**默认**布局，并`TextView`中**大型 land**布局拖放上面`Button`，设计器将标记移动小组件中，以指示冲突：

[![查看组冲突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "查看组冲突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
但是，没有任何标记上`Button`。 尽管的位置`Button`已更改`Button`显示了特定于任何应用的更改**大型 land**配置。 

如果`CheckBox`添加到**默认**布局，将生成另一个冲突，并且通过显示一个警告图标**大型 land**布局： 

[![复选框冲突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "复选框冲突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
单击**大型 land**布局会显示冲突。 显示以下消息：*最近的更改冲突导致此备选布局中*: 

[![Alt 布局冲突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Alt 布局冲突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

此外，冲突框中显示以下消息：

[![冲突消息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

添加`CheckBox`导致冲突，因为**大型 land**布局中发生更改`LinearLayout`包含它。 但是，在这种情况下冲突框中显示刚刚插入的小组件**默认**布局 ( `CheckBox`)。

如果单击**忽略冲突**，在设计器将解决冲突，允许的框中显示冲突拖放到布局小组件缺少小组件 (在这种情况下，**大型 land**布局): 

[![解决组冲突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "解析组冲突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

上一示例中所示`Button`，则`CheckBox`不具有红色更改标记，因为只有`LinearLayout`已应用中的更改**大型 land**布局。



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>创建备用布局

当您单击**备选布局视图**图标 (左侧**设备**)，预览窗格将打开，并列出项目中可用的备用布局。 如果不有任何其他布局**默认**显示视图： 

[![备用布局视图窗格](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

当单击绿色加号旁边**新版**，则**创建布局变体**对话框随即打开，以便你可以选择用于此布局变体的资源限定符： 

[![创建布局变体](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

在下面的示例的资源限定符**屏幕方向**设置为**横向**，并**屏幕大小**更改为**大**. 这将创建名为的新布局版本**大型 land**:

[![大型 land 变体](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

请注意，在左侧预览窗格中显示的资源限定符选项的效果。 单击**添加**创建备选布局，并切换到该布局的设计器。 **替代项布局视图**预览窗格指示哪种布局已加载到小型右指针通过设计器，如以下屏幕截图中所示： 

[![加载的布局指示符](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>编辑其他布局

通常，在创建其他布局时，很才能使适用于布局的所有分支版本的单个更改。 例如，你可能想要将按钮文本更改为在所有布局中的黄色。 如果有大量的布局，并且您需要将传播到所有版本的单个更改，维护可快速变得繁琐且容易出错。

若要简化维护的多个布局版本，在设计器提供了**多编辑**跨一个或多个布局传播所做的更改的模式。 如果存在，多个布局**多编辑**显示图标： 

[![多编辑图标](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

当您单击**多编辑**图标，显示线指示 （如下所示） 链接布局; 即，当更改一个布局，则所做的更改传播到任何链接的布局。 您可以通过单击下面的屏幕截图所示的带圆圈的图标中取消链接所有布局： 

[![取消链接所有布局](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

如果您有两个以上的布局，可以有选择地切换到左侧的每个布局预览版来确定哪些布局链接在一起的编辑按钮。 例如，如果你想要进行传播的单个更改为第一个和最后的三种布局，您将首先取消链接的中间布局如下所示： 

[![取消链接中间布局](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

在此示例中，更改为**默认**或**长**布局将传播到其他布局而不适用于**大型 land**布局。 

### <a name="multi-edit-example"></a>多项编辑示例 

一般情况下，当更改一个布局，该相同的更改传播到所有其他链接的布局。 例如，添加一个新`TextView`小组件设**默认**布局，并更改它的文本字符串到`Portrait`将导致相同的更改不会对所有链接的布局。 下面是其外观**默认**布局： 

[![添加 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

`TextView`还将添加到**大型 land**布局查看，因为它链接到**默认**布局： 

[![横向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
但如果你想要进行更改的本地只有一种布局 （也就是说，您不希望更改传播到任何其他布局）？ 若要执行此操作，必须取消链接你想要更改在修改之前，如下所述的布局。 

### <a name="making-local-changes"></a>进行本地更改 

假设我们希望能够在添加这两种布局`TextView`，但我们还想要更改中的文本字符串**大型 land**到布局`Landscape`而非`Portrait`。 如果我们进行到此更改**大型 land**虽然链接这两种布局，更改将传播回**默认**布局。 因此，我们必须首先取消链接两个布局之前做出更改。 当我们修改中的文本**大型 land**到`Landscape`，在设计器将标记的红框指示的更改是本地的与此更改**大型 land**布局是*不*传播回**默认**布局： 

[![本地更改](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

当您单击**默认**布局进行查看时，`TextView`文本字符串仍设置为`Portrait`。 

## <a name="handling-conflicts"></a>处理冲突 

如果您决定更改中的文本的颜色**默认**为绿色的布局，您将看到链接布局上出现一个警告图标。 单击该布局将打开以显示冲突的布局。 红框突出显示导致冲突的小组件，并显示以下消息：*最近的更改冲突导致此备选布局中*。 

[![冲突的更改](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

一个*冲突框*显示在小组件的右侧，以解释该冲突： 

[![冲突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

冲突框中显示的已更改的属性列表并列出它们的值。 单击**忽略冲突**属性更改仅适用于此小组件。 单击**Apply**属性更改应用于此小组件以及关于中链接的与小组件**默认**布局。 如果应用了所有属性更改，将自动丢弃该冲突。 

### <a name="view-group-conflicts"></a>查看组冲突 

属性更改不会冲突的唯一来源。 插入或删除小组件时，可以检测到冲突。 例如，当**大型 land**布局从已取消链接**默认**布局，并`TextView`中**大型 land**布局拖放上面`Button`，设计器将标记移动小组件中，以指示冲突：

[![视图组冲突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
但是，没有任何标记上`Button`。 尽管的位置`Button`已更改`Button`显示了特定于任何应用的更改**大型 land**配置。 

如果`CheckBox`添加到**默认**布局，将生成另一个冲突，并且通过显示一个警告图标**大型 land**布局： 

[![复选框冲突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
单击**大型 land**布局会显示冲突。 显示以下消息：*最近的更改冲突导致此备选布局中*。 

[![Alt 布局冲突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
此外，冲突框中显示以下消息：

[![冲突消息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

添加`CheckBox`导致冲突，因为**大型 land**布局中发生更改`LinearLayout`包含它。 但是，在这种情况下冲突框中显示刚刚插入的小组件**默认**布局 ( `CheckBox`)。

如果单击**忽略冲突**，在设计器将解决冲突，允许的框中显示冲突拖放到布局小组件缺少小组件 (在这种情况下，**大型 land**布局): 

[![解决组冲突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
上一示例中所示`Button`，则`CheckBox`不具有红色更改标记，因为只有`LinearLayout`已应用中的更改**大型 land**布局。


-----


### <a name="conflict-persistence"></a>冲突暂留

冲突将保留在布局文件中为 XML 注释，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，当关闭并重新打开项目，所有冲突仍在那里&ndash;甚至是那些已忽略。



