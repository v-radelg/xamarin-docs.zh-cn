---
title: 备选布局视图
description: 本主题说明如何使用资源限定符对布局进行版本控制。 例如，在设备处于横向模式时，可以使用布局的一个版本，而布局版本仅用于纵向模式。
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 017d2d05c04dfaf2378ad1b0129eb4a75be2e777
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019492"
---
# <a name="alternative-layout-views"></a>替代布局视图

_本主题说明如何使用资源限定符来版本布局。例如，创建仅在设备处于横向模式时使用的布局版本和仅用于纵向模式的布局版本。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="creating-alternative-layouts"></a>创建替代布局

单击 "**设备**" 左侧的**替代布局视图**图标时，将打开一个预览窗格，其中列出了项目中可用的备用布局。 如果没有其他布局，将显示**默认**视图： 

[![替代布局视图窗格](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "替代布局视图窗格")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

单击 "**新版本**" 旁边的绿色加号时，将打开 "**创建布局变体**" 对话框，以便您可以为此布局变体选择资源限定符： 

[![创建布局变体](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "创建布局变体")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

在下面的示例中，**屏幕方向**的资源限定符设置为 "**横向**"，**屏幕大小**更改为 "**大**"。 这会创建一个名为 "**大型土地**" 的新布局版本：

[![大土地变化](alternative-layout-views-images/vs/03-large-land-sml.png "大土地变化")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

请注意，左侧的预览窗格将显示资源限定符选择的效果。 单击 "**添加" 将**创建替代布局，并将设计器切换到该布局。 **替代布局视图**预览窗格通过一个小的右指针指示将哪个布局加载到设计器中，如以下屏幕截图所示： 

[![已加载布局指示器](alternative-layout-views-images/vs/04-new-layout-sml.png "已加载布局指示器")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>编辑替代布局

创建备用布局时，通常需要进行一项更改，该更改适用于布局的所有分叉版本。 例如，您可能希望将所有布局中的按钮文本更改为黄色。 如果你有大量的布局，并且需要将单个更改传播到所有版本，则维护很快就会变得很繁琐，而且容易出错。

为了简化多个布局版本的维护，设计器提供了一种**多编辑**模式，可在一个或多个布局之间传播更改。 当存在多个布局时，将显示**多编辑**图标： 

[![多编辑图标](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "多编辑图标")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

单击 "**多编辑**" 图标时，将显示指示布局已链接的行（如下所示）;也就是说，当您对一个布局进行更改时，该更改将传播到任何链接的布局。 可以通过单击下列屏幕截图中所示的带圆圈图标取消所有布局的链接： 

[![取消链接所有布局](alternative-layout-views-images/vs/06-multi-linked-sml.png "取消链接所有布局")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

如果有两个以上的布局，则可以选择性地切换每个布局预览左侧的 "编辑" 按钮，以确定哪些布局链接在一起。 例如，如果你想要进行一个传播到三个布局的第一个和最后一个的更改，则首先取消链接中间布局，如下所示： 

[![取消链接中间布局](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "取消链接中间布局")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)

在此示例中，对**默认**布局或**长**布局所做的更改将传播到其他布局，但不会传播到**大土地**布局。

### <a name="multi-edit-example"></a>多编辑示例 

通常，当您更改一个布局时，相同的更改将传播到所有其他链接的布局。 例如，将新的 `TextView` 小组件添加到**默认**布局，并将其文本字符串更改为 `Portrait` 会导致对所有链接的布局进行相同的更改。 下面是它在**默认**布局中的外观： 

[![添加 TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "添加 TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)

还会将 `TextView` 添加到**大土地**布局视图中，因为它已链接到**默认**布局： 

[![横向 TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "横向 TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)

但是，如果您想要将本地更改为仅限一个布局（也就是说，您不希望将更改传播到任何其他布局），该怎么办？ 若要执行此操作，必须先取消链接要更改的布局，然后再对其进行修改，如下所述。 

### <a name="making-local-changes"></a>进行本地更改 

假设我们希望两种布局都具有添加的 `TextView`，但我们也希望将**大土地**布局中的文本字符串更改为 `Landscape` 而不是 `Portrait`。 如果在两个布局均已链接的情况下将此更改更改为**大型土地**，则更改将传播回**默认**布局。 因此，在进行更改之前，必须先取消两个布局的链接。 当我们修改**大**到 `Landscape` 中的文本时，设计器会将此更改标记为红色框架，以指示更改在**大型土地**布局的本地，*不*会传播回**默认**布局： 

[![本地更改](alternative-layout-views-images/vs/10-local-change-sml.png "本地更改")](alternative-layout-views-images/vs/10-local-change.png#lightbox)

单击**默认**布局以查看它时，`TextView` 文本字符串仍设置为 `Portrait`。 

## <a name="handling-conflicts"></a>处理冲突 

如果你决定将**默认**布局中的文本颜色更改为绿色，你将看到在链接的布局上显示警告图标。 单击该布局会打开布局以显示冲突。 导致冲突的小组件会以红色帧突出显示，并显示以下消息：*最近的更改导致此替代布局发生冲突*。 

[![冲突更改](alternative-layout-views-images/vs/11-conflicting-change-sml.png "冲突更改")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)

小组件的右侧会显示一个*冲突框*，用于说明冲突： 

[![冲突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

冲突框显示已更改的属性的列表，并列出其值。 单击 "**忽略冲突**" 将属性更改仅应用于此小组件。 单击 "**应用**" 可将属性更改应用于此小组件以及链接的**默认**布局中的对应小组件。 如果应用了所有属性更改，则会自动放弃冲突。 

### <a name="view-group-conflicts"></a>查看组冲突 

属性更改不是唯一的冲突源。 插入或删除小组件时，可以检测到冲突。 例如，当将**大型土地**布局从**默认**布局中取消链接，并将**大型土地**布局中的 `TextView` 拖放到 `Button` 上方时，设计器将标记移动小组件以指示冲突：

[![查看组冲突](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "查看组冲突")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)

但 `Button` 上没有标记。 尽管 `Button` 的位置发生了更改，但 `Button` 未显示特定于**大土地**配置的应用更改。 

如果向**默认**布局添加了 `CheckBox`，则会生成另一个冲突，并在**大土地**布局中显示警告图标： 

[![复选框冲突](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "复选框冲突")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)

单击**大土地**布局会显示冲突。 显示以下消息：*最近的更改导致此替代布局发生冲突*： 

[![Alt 布局冲突](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "Alt 布局冲突")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)

此外，冲突框还显示以下消息：

[![冲突消息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

添加 `CheckBox` 会导致冲突，因为**大型土地**布局在包含它的 `LinearLayout` 中发生了更改。 但是，在这种情况下，"冲突" 框将显示刚刚插入到**默认**布局（`CheckBox`）中的小组件。

如果单击 "**忽略冲突**"，设计器将解决冲突，并允许将冲突框中显示的小组件拖放到缺少小组件的布局中（在本例中为**大土地**布局）： 

[![解决的组冲突](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "解决的组冲突")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)

如上例所示，在 `Button` 中，`CheckBox` 没有红色更改标记，因为只有 `LinearLayout` 具有在**大土地**布局中应用的更改。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="creating-alternative-layouts"></a>创建替代布局

单击 "**设备**" 左侧的**替代布局视图**图标时，将打开一个预览窗格，其中列出了项目中可用的备用布局。 如果没有其他布局，将显示**默认**视图： 

[![替代布局视图窗格](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

单击 "**新版本**" 旁边的绿色加号时，将打开 "**创建布局变体**" 对话框，以便您可以为此布局变体选择资源限定符： 

[![创建布局变体](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

在下面的示例中，**屏幕方向**的资源限定符设置为 "**横向**"，**屏幕大小**更改为 "**大**"。 这会创建一个名为 "**大型土地**" 的新布局版本：

[![大土地变化](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

请注意，左侧的预览窗格将显示资源限定符选择的效果。 单击 "**添加" 将**创建替代布局，并将设计器切换到该布局。 **替代布局视图**预览窗格通过一个小的右指针指示将哪个布局加载到设计器中，如以下屏幕截图所示： 

[![加载的布局指示器](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

## <a name="editing-alternative-layouts"></a>编辑替代布局

创建备用布局时，通常需要进行一项更改，该更改适用于布局的所有分叉版本。 例如，您可能希望将所有布局中的按钮文本更改为黄色。 如果你有大量的布局，并且需要将单个更改传播到所有版本，则维护很快就会变得很繁琐，而且容易出错。

为了简化多个布局版本的维护，设计器提供了一种**多编辑**模式，可在一个或多个布局之间传播更改。 当存在多个布局时，将显示**多编辑**图标： 

[![多编辑图标](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

单击 "**多编辑**" 图标时，将显示指示布局已链接的行（如下所示）;也就是说，当您对一个布局进行更改时，该更改将传播到任何链接的布局。 可以通过单击下列屏幕截图中所示的带圆圈图标取消所有布局的链接： 

[![取消链接所有布局](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

如果有两个以上的布局，则可以选择性地切换每个布局预览左侧的 "编辑" 按钮，以确定哪些布局链接在一起。 例如，如果你想要进行一个传播到三个布局的第一个和最后一个的更改，则首先取消链接中间布局，如下所示： 

[![取消链接中间布局](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)

在此示例中，对**默认**布局或**长**布局所做的更改将传播到其他布局，但不会传播到**大土地**布局。 

### <a name="multi-edit-example"></a>多编辑示例 

通常，当您更改一个布局时，相同的更改将传播到所有其他链接的布局。 例如，将新的 `TextView` 小组件添加到**默认**布局，并将其文本字符串更改为 `Portrait` 会导致对所有链接的布局进行相同的更改。 下面是它在**默认**布局中的外观： 

[![添加 TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)

还会将 `TextView` 添加到**大土地**布局视图中，因为它已链接到**默认**布局： 

[![横向 TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)

但是，如果您想要将本地更改为仅限一个布局（也就是说，您不希望将更改传播到任何其他布局），该怎么办？ 若要执行此操作，必须先取消链接要更改的布局，然后再对其进行修改，如下所述。 

### <a name="making-local-changes"></a>进行本地更改 

假设我们希望两种布局都具有添加的 `TextView`，但我们也希望将**大土地**布局中的文本字符串更改为 `Landscape` 而不是 `Portrait`。 如果在两个布局均已链接的情况下将此更改更改为**大型土地**，则更改将传播回**默认**布局。 因此，在进行更改之前，必须先取消两个布局的链接。 当我们修改**大**到 `Landscape` 中的文本时，设计器会将此更改标记为红色框架，以指示更改在**大型土地**布局的本地，*不*会传播回**默认**布局： 

[![本地更改](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)

单击**默认**布局以查看它时，`TextView` 文本字符串仍设置为 `Portrait`。 

## <a name="handling-conflicts"></a>处理冲突 

如果你决定将**默认**布局中的文本颜色更改为绿色，你将看到在链接的布局上显示警告图标。 单击该布局会打开布局以显示冲突。 导致冲突的小组件会以红色帧突出显示，并显示以下消息：*最近的更改导致此替代布局发生冲突*。 

[![冲突更改](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)

小组件的右侧会显示一个*冲突框*，用于说明冲突： 

[![冲突警告](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

冲突框显示已更改的属性的列表，并列出其值。 单击 "**忽略冲突**" 将属性更改仅应用于此小组件。 单击 "**应用**" 可将属性更改应用于此小组件以及链接的**默认**布局中的对应小组件。 如果应用了所有属性更改，则会自动放弃冲突。 

### <a name="view-group-conflicts"></a>查看组冲突 

属性更改不是唯一的冲突源。 插入或删除小组件时，可以检测到冲突。 例如，当将**大型土地**布局从**默认**布局中取消链接，并将**大型土地**布局中的 `TextView` 拖放到 `Button` 上方时，设计器将标记移动小组件以指示冲突：

[![查看组冲突](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)

但 `Button` 上没有标记。 尽管 `Button` 的位置发生了更改，但 `Button` 未显示特定于**大土地**配置的应用更改。 

如果向**默认**布局添加了 `CheckBox`，则会生成另一个冲突，并在**大土地**布局中显示警告图标： 

[![Checkbox 冲突](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)

单击**大土地**布局会显示冲突。 显示以下消息：*最近的更改导致此替代布局发生冲突*。 

[![Alt 布局冲突](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)

此外，冲突框还显示以下消息：

[![冲突消息](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

添加 `CheckBox` 会导致冲突，因为**大型土地**布局在包含它的 `LinearLayout` 中发生了更改。 但是，在这种情况下，"冲突" 框将显示刚刚插入到**默认**布局（`CheckBox`）中的小组件。

如果单击 "**忽略冲突**"，设计器将解决冲突，并允许将冲突框中显示的小组件拖放到缺少小组件的布局中（在本例中为**大土地**布局）： 

[![解决的组冲突](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)

如上例所示，在 `Button` 中，`CheckBox` 没有红色更改标记，因为只有 `LinearLayout` 具有在**大土地**布局中应用的更改。

-----

### <a name="conflict-persistence"></a>冲突持久性

冲突作为 XML 注释保留在布局文件中，如下所示：

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

因此，当关闭并重新打开项目时，所有冲突仍会 &ndash; 即使已被忽略。
