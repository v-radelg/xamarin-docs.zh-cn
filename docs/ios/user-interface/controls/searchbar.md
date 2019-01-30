---
title: 在 Xamarin.iOS 中的搜索栏
description: 本文档介绍如何在 Xamarin.iOS 中使用搜索栏。 它讨论了如何以编程方式和情节提要中创建了搜索栏。
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 3c46aa1244699671d46560b0029197981a86d005
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233323"
---
# <a name="search-bars-in-xamarinios"></a>在 Xamarin.iOS 中的搜索栏

UISearchBar 用于搜索的值的列表。 

它包含三个主要组件： 

- 使用输入文本的字段。 用户可以使用此功能来输入其搜索词。
- 清除按钮，从搜索字段中删除的任何文本。
- 取消按钮中，若要退出搜索功能。

![搜索栏](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>实现搜索栏

若要实现通过实例化一个新的搜索栏开始：

```csharp
searchBar = new UISearchBar();
```

然后将它放。 下面的示例演示如何将其放置在导航栏中或在表的 HeaderView 中：

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

在搜索栏上的设置属性：

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![搜索栏属性](searchbar-images/image6.png)

引发`SearchButtonClicked`搜索按钮被按下时的事件。 这将调用你的搜索逻辑：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

有关管理表示法的搜索栏和搜索结果的信息，请参阅[搜索控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)方案。

## <a name="using-the-search-bar-in-the-designer"></a>在设计器中使用的搜索栏

在设计器提供两个选项用于在设计器中实现搜索栏

- 搜索栏
- 搜索栏搜索显示控制器 （不推荐使用）

![在设计器中的搜索栏控件](searchbar-images/image2.png)

使用属性面板在搜索栏上设置属性

![搜索栏属性设计器](searchbar-images/image3.png)

下面介绍了这些属性：

- **文本、 占位符，提示符**– 这些属性用于提供建议，并指示用户如何使用搜索栏。 例如，如果您的应用程序显示的存储列表您可以使用提示属性来建议，用户可以"输入城市、 文章名称或邮政编码"
- **搜索样式**– 你可以设置为在搜索栏**Prominent**或**最小**。 使用重要染色屏幕上，除了搜索栏，导致焦点要绘制到搜索栏中的其他所有内容。 最小样式搜索栏将混合使用其周围的环境。
- **功能**– 仅启用这些属性显示的 UI 元素。 对于这些通过引发正确的事件中所述，必须实现的功能[搜索栏 API 文档](xref:UIKit.UISearchBar)
    - 显示搜索结果 / 书签按钮 – 显示在搜索栏搜索结果或书签图标
    - 显示取消按钮-允许用户从搜索函数退出。 建议选择此。
    - 显示作用域栏 – 这样，用户可以将其搜索范围限制。 例如，音乐应用程序中搜索时，用户可以选择是他们想要搜索 Apple 音乐或其库的特定歌曲或艺术家。 若要显示的各种选项，添加一个数组的书名引入**ScopeBarTitles**属性。
    ![搜索作用域标题栏](searchbar-images/image4.png)

- **文本行为**– 这些选项用于解决中键入时用户输入的格式设置方式。 大小写将设置的每个单词或句子，启动或为大写的每个字符。 更正和拼写检查功能会提示使用的单词的拼写建议用户在键入时。
- **键盘**– 控件的键盘样式显示用于输入，并因此何种密钥都是键盘上可用。 这包括数字板、 Phone 板、 电子邮件、 URL 以及其他选项。
- **外观**– 控制键盘的外观样式并将任一深色或浅色主题化。
- **返回键**– 更改要更好地反映不会执行哪些操作的返回键上的标签。 支持的值包括 Go、 联接、 下一步、 路由，完成后，和搜索。
- **安全**– 标识是否屏蔽输入 （例如密码输入）。

## <a name="related-links"></a>相关链接

- [搜索控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
