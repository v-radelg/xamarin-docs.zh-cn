---
title: 适用于 Xamarin.iOS 应用启动屏幕
description: 本文介绍如何在任何分辨率和方向，使用单个统一情节提要时创建所有的 iOS 设备，应用程序启动屏幕。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2018
ms.openlocfilehash: 0ec1defa29a4fe85c4ae3e809d8733e68cc268ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087408"
---
# <a name="launch-screens-for-xamarinios-apps"></a>适用于 Xamarin.iOS 应用启动屏幕

_本文介绍如何在任何分辨率和方向，使用单个统一情节提要时创建所有的 iOS 设备，应用程序启动屏幕。_

在 iOS 8 之前创建的 iOS 应用程序启动屏幕需要开发人员为每个不同设备外观造型和解决方法在其中运行应用程序无法提供的图像资产。 自 iOS 8 的发布，但是，它有了可以使用单一的统一的情节提要来创建一个看起来在所有情况下正确的启动屏幕。

本简短演练介绍如何使用新项目中默认情况下提供的任一情节提要或使用演示图板添加到现有项目中手动创建启动屏幕。 然后演示如何使用 iOS 设计器添加到情节提要，这些视图上设置约束，并验证情节提要看起来正确为各种设备和方向的图像视图和一个标签。

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>管理使用演示图板启动屏幕

在 iOS 8 （和更高版本），开发人员可以创建特殊统一情节提要提供而不是使用一个或多个静态启动映像启动屏幕。 如果创建 iOS 设计器启动情节提要，请使用大小类和自动布局来定义不同的显示环境的不同布局。 通过使用大小类和自动布局，开发人员可以创建一个看起来没问题在所有设备上的单一启动屏幕和显示环境。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，通过选择创建新项目**文件 > 新的解决方案**，然后选择**单一视图应用**: 

    ![新建项目窗口中，使用所选的单一视图应用](launch-screens-images/launch01.png)

    - 默认情况下，新的项目包括**LaunchScreen.storyboard**定义的启动屏幕界面文件。 
    - 若要改为添加到现有项目的启动屏幕情节提要，请右键单击项目名称在**Solution Pad** ，然后选择**添加 > 新建文件...** ，然后选择**启动屏幕**:

    ![具有 iOS 启动屏幕选择的新文件窗口](launch-screens-images/launch01b.png)

    - 将文件命名**LaunchScreen**或所选的另一个名称。

2. 项目配置为使用相应的情节提要为其启动屏幕：

    - 双击**Info.plist**中的文件**Solution Pad**打开进行编辑。
    - 在中**启动图像**部分中，请确保**启动屏幕**设置为适当的情节提要的名称：

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch02.png)

    - 默认情况下，新的项目配置为使用**LaunchScreen.storyboard**作为其启动屏幕。

3. 添加到图像**Assets.xcassets**资产目录，这样就可在启动屏幕上使用。 有关详细信息，请参阅[将图像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一部分[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

4. 打开**LaunchScreen.storyboard**用于编辑中双击**Solution Pad**。

5. 选择设备和在其预览启动屏幕情节提要中 iOS 设计器上的方向。 打开底部工具栏上的设备选择面板，然后选择**iPhone 4 秒**并**纵向**。

    ![设备选择工具栏上](launch-screens-images/launch05.png)

    - 请注意，选择设备和方向仅更改 iOS 设计器预览设计的方式。 此处所做的选择，无论新添加的约束应用于所有设备和方向，除非**编辑特征**使用按钮来指定，否则。 

6. 设置**背景**视图控制器的主视图的颜色。 通过单击视图控制器的中间选择的视图和背景颜色使用调整**Properties Pad**:

    ![一个视图，其中紫色背景色](launch-screens-images/launch06.png)

7. 添加**图像视图**到启动屏幕，然后设置其源**映像**:

    - 拖动**图像视图**从**工具箱面板**到视图的中心。
    - 与**图像视图**后，在**小组件**一部分**Properties Pad**设置**映像**到映像已设置的属性添加到**Assets.xcassets**资产目录。 重新定位并调整其大小**图像视图**所需的方式：
    
    ![其图像属性设置与图像视图](launch-screens-images/launch07.png)

8. 添加**标签**如下**图像视图**，并使用**Properties Pad**以设置其属性： 

    ![具有其文本和颜色的一组的标签](launch-screens-images/launch08.png)

9. 通过使用中的右侧按钮切换到约束编辑模式**约束工具栏**:
    
    ![约束编辑模式按钮](launch-screens-images/launch09.png)

10. 将约束添加到**图像视图**、 设置其高度和宽度以及水平和垂直居中它：

    ![使用布局限制图像视图](launch-screens-images/launch10.png)

    - 有关如何添加约束的更多详细信息，请参阅[适用于 iOS 的 Xamarin 设计器的自动布局](~/ios/user-interface/designer/designer-auto-layout.md)。

11. 将约束添加到**标签**、 水平居中、 为其提供高度和宽度，和定位固定从垂直距离**图像视图**:

    ![具有布局限制的标签](launch-screens-images/launch11.png)

12. 测试其他设备和方向，以验证设计运行安装程序，在所有方案中所示。 在其中调整需要的特定设备或方向进行的情况下，使用**编辑特征**按钮添加的特定大小类的约束：

    ![呈现为 iPhone X 使用横向方向在启动屏幕](launch-screens-images/launch12.png)

13. 将所做的更改保存到情节提要。 在模拟器或设备上运行应用并启动屏幕会显示，如应用程序正在启动。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 创建新项目。 在 Visual Studio 中，选择**文件 > 新建 > 项目 > Visual C# > iPhone 和 iPad > iOS 应用 (Xamarin)**:

    ![新的项目，与 iOS 应用 (Xamarin) 所选的窗口](launch-screens-images/launch01.w157.png)

    选择**单一视图应用**模板，，然后单击**确定**:

    ![单一视图应用程序模板](launch-screens-images/launch01-2.w157.png)

2. 如果**资源 > LaunchScreen.xib**存在于**解决方案资源管理器**，右键单击该文件并选择删除**删除**。 此文件将替换为下一步中将情节提要。

3. 创建要用作启动屏幕情节提要。 在中**解决方案资源管理器**，右键单击该项目并选择**添加 > 新建项...** 跟**空情节提要**。 命名此情节提要**LaunchScreen.storyboard**然后单击**添加**:

    ![添加新项窗口中的与所选的空情节提要，](launch-screens-images/launch03.w157.png)

4. 将项目配置为使用**LaunchScreen.storyboard**作为其启动屏幕情节提要：

    - 在“解决方案资源管理器”中，双击 **Info.plist** 文件，打开它进行编辑。 
    - 上**视觉对象资产**选项卡上，设置**启动屏幕**到**LaunchScreen**。

    ![Info.plist 中的启动屏幕选择器](launch-screens-images/launch04-vs.png)

5. 将图像添加到项目中的资产目录，这样就可在启动屏幕上使用：

    - 在中**解决方案资源管理器**，右键单击**资产目录**，然后选择**添加资产目录**。 此新的资产目录命名**资产**:

    ![添加新项窗口中的使用选择的资产目录，](launch-screens-images/launch05.w157.png)

    - 添加新图像设置为**资产**资产目录，如中所述[将映像添加到资产目录映像集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一部分[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

6. 打开**LaunchScreen.storyboard**用于编辑中双击**解决方案资源管理器**。

    - 若要编辑的情节提要文件，Visual Studio 需要到 Mac 生成主机的活动连接。 请参阅[连接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南以详细信息。

7. 选择设备和在其预览启动屏幕情节提要中 iOS 设计器上的方向。 打开底部工具栏上的设备选择面板，然后选择**iPhone 4 秒**并**纵向**: 
 
    ![设备选择工具栏上](launch-screens-images/launch07-vs.png)

    - 请注意，选择设备和方向仅更改 iOS 设计器预览设计的方式。 此处所做的选择，无论新添加的约束应用于所有设备和方向，除非**编辑特征**使用按钮来指定，否则。 

8. 添加**视图控制器**到通过拖动其中一个从情节提要**工具箱**拖到设计图面： 

    ![一个空视图控制器添加到设计图面](launch-screens-images/launch08-vs.png)

9. 设置**背景**视图控制器的主视图的颜色。 通过单击视图控制器的中间选择的视图和背景颜色使用调整**属性窗口**:
    
    ![一个视图，其中紫色背景色](launch-screens-images/launch09-vs.png)

10. 添加**图像视图**到启动屏幕，然后设置其源**映像**:

    - 拖动**图像视图**从**工具箱**到视图的中心。
    - 与**图像视图**仍然选择，在**小组件**一部分**属性窗口**设置**映像**属性设置为映像设置已添加到**资产**资产目录。 重新定位并调整其大小**图像视图**所需的方式：
    
    ![其图像属性设置与图像视图](launch-screens-images/launch10-vs.png)

11. 添加**标签**如下**图像视图**:

    - 拖动**标签**从**工具箱**拖到设计图面中，将其放置下面**图像视图**。
    - 为设置属性**标签**使用**属性窗口**:

    ![具有其文本和颜色的一组的标签](launch-screens-images/launch11-vs.png) 

12. 通过使用中的右侧按钮切换到约束编辑模式**约束工具栏**:
    
    ![约束编辑模式按钮](launch-screens-images/launch12-vs.png) 

13. 将约束添加到**图像视图**、 设置其高度和宽度以及水平和垂直居中它：

    ![使用布局限制图像视图](launch-screens-images/launch13-vs.png) 

    - 有关添加约束的信息，请参阅[适用于 iOS 的 Xamarin 设计器的自动布局](~/ios/user-interface/designer/designer-auto-layout.md)。

14. 将约束添加到**标签**、 水平居中、 为其提供高度和宽度，和定位固定从垂直距离**图像视图**:
    
    ![具有布局限制的标签](launch-screens-images/launch14-vs.png) 

15. 测试其他设备和方向，以验证设计运行安装程序，在所有方案中所示。 在其中调整需要的特定设备或方向进行的情况下，使用**编辑特征**按钮添加的特定大小类的约束：

    ![呈现为 iPhone X 使用横向方向在启动屏幕](launch-screens-images/launch15-vs.png) 

16. 将所做的更改保存到情节提要。 在模拟器或设备上运行应用并启动屏幕会显示，如应用程序正在启动。

-----

> [!NOTE]
> 用作启动屏幕情节提要_必须_包括仅简单、 内置 UI 元素并**不能**执行任何计算或派生自定义类。

有关使用统一的情节提要创建启动屏幕的详细信息，请参阅[动态启动屏幕](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)一部分[统一情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="migrating-to-launch-screen-storyboards"></a>迁移以启动屏幕情节提要

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

当更新现有应用程序要用于其启动屏幕情节提要，右键单击**项目名称**中**解决方案资源管理器**，然后选择**添加** > **新文件...**.选择**iOS** > **启动屏幕**然后单击**新建**按钮：

![](launch-screens-images/storyboard02.png "选择 iOS 启动屏幕")

接下来，双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。 下**启动屏幕**，选择上面创建的新情节提要文件。

![](launch-screens-images/storyboard09.png "选择上面创建的新情节提要文件")


要将新情节提要用作启动屏幕，请执行以下操作：

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 滚动到**通用启动图像**部分中的编辑器中，打开**启动屏幕**下拉列表中，选择上面创建的情节提要的名称： 

    ![](launch-screens-images/storyboard08.png "设置启动屏幕情节提要")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击项目名称在**解决方案资源管理器**，然后选择**添加** > **新文件...**: 

    ![](launch-screens-images/image012.png "添加新的文件")
2. 输入启动屏幕的名称，然后单击**添加**按钮： 

    ![](launch-screens-images/image013.png "输入启动屏幕的名称")
3. 在中**解决方案资源管理器**，双击要打开以进行编辑的新创建的情节提要文件。
4. 絋粄**大小类**设置为**任何： 任何**并**查看方式**是**泛型**: 

    ![](launch-screens-images/image016.png "请确保大小类设置为任何： 任何和查看方式为泛型")
5. 程序集的大小类，简单的 UI 元素的启动屏幕 (如`UIImageView`) 和应用程序的捆绑包中包含的图像： 

    ![](launch-screens-images/image017.png "程序集在 iOS 设计器中启动屏幕")
6. 将所做的更改保存到情节提要。

-----

## <a name="related-links"></a>相关链接

- [动态启动屏幕 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [统一的情节提要](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 设计器基本知识](~/ios/user-interface/designer/index.md)
- [添加图像到资产目录映像设置](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [适用于 iOS 的 Xamarin 设计器的自动布局](~/ios/user-interface/designer/designer-auto-layout.md)
- [人机接口准则：启动屏幕](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
