---
title: Xamarin 中的搜索栏
description: 本文档介绍如何在 Xamarin 中使用搜索栏。 本文讨论如何在情节提要中以编程方式创建搜索栏。
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 8b129e0e70bf3ded787094d1b1f740e73a8cbca1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021982"
---
# <a name="search-bars-in-xamarinios"></a>Xamarin 中的搜索栏

UISearchBar 用于在值列表中进行搜索。

它包含三个主要组件：

- 用于输入文本的字段。 用户可以利用它来输入搜索词。
- 一个 "清除" 按钮，用于删除搜索字段中的任何文本。
- "取消" 按钮，用于退出搜索功能。

![搜索栏](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>实现搜索栏

若要通过实例化新的搜索栏开始，请执行以下操作：

```csharp
searchBar = new UISearchBar();
```

然后将其放置。 下面的示例演示如何将其放在导航栏或表的 HeaderView 中：

```csharp
NavigationItem.TitleView = searchBar;

// or

TableView.TableHeaderView = searchBar;
```

在搜索栏上设置属性：

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![搜索栏属性](searchbar-images/image6.png)

按下 "搜索" 按钮时，引发 `SearchButtonClicked` 事件。 这会调用搜索逻辑：

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

有关管理搜索栏和搜索结果的显示方式的信息，请参阅[搜索控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)食谱。

## <a name="using-the-search-bar-in-the-designer"></a>在设计器中使用搜索栏

设计器提供了两个用于在设计器中实现搜索栏的选项

- 搜索栏
- 搜索显示控制器的搜索栏（不推荐使用）

![设计器中的搜索栏控件](searchbar-images/image2.png)

使用 "属性" 面板在搜索栏上设置属性

![搜索栏属性设计器](searchbar-images/image3.png)

下面对这些属性进行了说明：

- **文本、占位符、提示**–这些属性用于建议和指示用户应如何使用搜索栏。 例如，如果应用显示商店列表，则可以使用 prompt 属性来告知用户可以 "输入城市、故事名称或邮政编码"
- **搜索样式**–可以将搜索栏设置为**突出**或**最小**。 使用突出显示的内容将以淡色显示屏幕上的所有其他内容，搜索栏除外，导致将焦点绘制到搜索栏。 最小样式搜索栏会与其周围的混合。
- **功能**–启用这些属性仅显示 UI 元素。 必须通过按[搜索栏 API 文档](xref:UIKit.UISearchBar)中的详细说明引发正确事件来实现这些功能。
  - 显示搜索结果/书签按钮–在搜索栏上显示搜索结果或书签图标
  - 显示 "取消" 按钮–允许用户退出搜索功能。 建议选择此选项。
  - 显示范围栏–这允许用户限制其搜索范围。 例如，当你在音乐应用程序中搜索时，用户可以选择是否要搜索特定歌曲或艺术家 Apple Music 或其库。 若要显示各种选项，请将标题数组添加到**ScopeBarTitles**属性。
  ![搜索栏范围标题](searchbar-images/image4.png)

- **文本行为**-这些选项用于处理键入时用户输入的格式。 大小写将设置每个单词或句子的开头，或将每个字符设置为大写。 更正和拼写检查提示用户键入时，会提示用户输入拼写建议。
- **键盘**–控制为输入显示的键盘样式，并因此可以在键盘上使用哪些键。 这包括数字板、手机 Pad、电子邮件和其他选项。
- **外观**–控制键盘的外观样式，并将为深色或浅色主题。
- **返回键**–更改返回键上的标签，以更好地反映要采取的操作。 受支持的值包括 "开始"、"联接"、"下一步"、"路由"
- **Secure** –标识是否屏蔽输入（例如密码输入）。

## <a name="related-links"></a>相关链接

- [搜索控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
