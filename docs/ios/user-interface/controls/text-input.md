---
title: 在 Xamarin.iOS 中的文本输入
description: 本文档介绍了 Xamarin.iOS 应用程序中的文本输入。 它讨论如何以编程方式和 iOS 设计器使用 UITextField 和 UITextVIew。
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4d23984d4fcfd0776fd6b3537d5a257f70e7837
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827122"
---
# <a name="text-input-in-xamarinios"></a>在 Xamarin.iOS 中的文本输入

接受用户的文本输入可以使用来完成`UITextField`单行输入和多行可编辑文本的 UITextView 的。 可以将任一这些控件拖动到屏幕上，然后双击以设置初始文本。

下面的屏幕截图显示了这些控件位于工具箱面板在 Visual Studio for Mac 的图标：

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

一旦您具有名为电源插座并保存情节提要文件，将更新 Visual Studio for Mac`.designer.cs`分部类和您可以添加C#引用的控件类的文件的代码。 每个控件都有自己唯一的属性和可访问中的事件在C#代码。

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

`UITextField`控件通常用来接受单个行的文本输入，如用户名或密码。 一些可用于自定义控件的选项如下所示：

 [![](text-input-images/image15a.png "UITextField 属性")](text-input-images/image15a.png#lightbox)

下面介绍了这些控件：

-  **占位符**– 这是可选的。 如果设置，则它显示在文本字段为空，通常以向用户说明预期输入内容时。
-  **清除按钮**– 此参数控制当标准清除按钮 （带有 (X) 的灰色圆圈） 出现在文本字段中，用户可以快速清除文本的方式。 它可以是永久隐藏、 永久可见，或所示，具体取决于正在编辑字段。
-  **最小字号**并**调整为适合页面**– 允许字体大小自动调整，以便容纳较长的文本并防止发生截断，但限制为不小于指定大小。
-  **大小写**– 是否自动将大写单词、 句子或所有输入。
-  **更正**– 是否启用了拼写检查功能和建议。
-  **键盘**– 控件的键盘样式显示用于输入，并因此何种密钥都是键盘上可用。 这包括数字板、 Phone 板、 电子邮件、 URL 以及其他选项。
-  **外观**– 控制键盘的外观样式并将任一深色或浅色主题化。
-  **返回键**– 更改要更好地反映不会执行哪些操作的返回键上的标签。 支持的值包括 Go、 联接、 下一步、 路由，完成后，和搜索。
-  **安全**– 标识是否屏蔽输入 （例如密码输入）。


如果调用 UITextField`textfield1`已添加到屏幕与设计器中，您可以设置或更改其属性在C#，如下所示：

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

在适当的轻松地选择所需的设置，例如，Xamarin.iOS 提供枚举`UIKeyboardType`和`UIReturnKeyType`在上面的代码段中。

### <a name="display-text-programmatically"></a>以编程方式显示文本

如果不想使用设计器设计屏幕，或如果你想要在运行时动态添加一些文本，您可以创建和显示 UITextField 中以编程方式`ViewDidLoad`如下的视图控制器方法：

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

`UITextView`控件可用于显示只读的文本或接受多行文本输入。 它具有许多相同的选项`UITextField`（如大小写、 更正，等）。

 [![](text-input-images/image16a.png "UITextView 属性")](text-input-images/image16a.png#lightbox)

特定的属性包括：

-  **行为**– 的文本是否为可编辑或只读的。
-  **检测**– 检测并将转换为可单击的元素所的数据如变得可以触发调用的电话号码地址链接到映射，在 Safari 中打开的 Url 或日期和时间的日历中成为事件。


如果 UITextView 已添加到一个包含设计器的屏幕，可以设置或更改其属性如下：

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/monotouch/Controls/)
