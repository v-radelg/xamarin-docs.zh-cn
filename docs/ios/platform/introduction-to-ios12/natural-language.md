---
title: 使用自然语言框架和 Xamarin.iOS
description: 本文档介绍自然语言框架。 在 iOS 12 中引入，自然语言框架是要使用的语言识别、 词类标识和命名的实体识别的首选的 iOS API。
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 41f629739b06431a9b20548f61111bc31e911abb
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870035"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>使用自然语言框架和 Xamarin.iOS

在 iOS 12 中引入，自然语言框架使设备上的自然语言处理。 它支持语言识别、 词汇切分，和标记。 词汇切分将文本拆分为其组件字、 句子或段落;标记标识词类、 人员、 地点和组织。

自然语言框架还可以使用自定义 Core ML 模型进行分类和标记文本在专用的上下文中。

[NSLinguisticTagger](xref:Foundation.NSLinguisticTagger)类是仍然可用。 但是，自然语言框架是使用自然语言处理的首选的机制。

## <a name="sample-app-xamarinnl"></a>应用程序示例：XamarinNL

若要了解如何通过 Xamarin.iOS 使用自然语言框架，看一看[XamarinNL 示例应用](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)。
此示例应用演示了如何使用自然语言 framework:

- [识别语言](#recognizing-languages)。
- [对文本进行词汇切分划分为单词和句子](#tokenizing-text-into-words-sentences-and-paragraphs)。
- [标记名为实体和词类](#tagging-named-entities-and-parts-of-speech)。

## <a name="recognizing-languages"></a>识别语言

**识别器**选项卡的示例应用演示了如何使用 [`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
若要确定文本块的语言。

> [!NOTE]
> 语言识别是特定类型的文本分类。 自然语言框架还支持通过开发人员提供核心机器学习模型的自定义文本分类。 有关详细信息，看一看[自然语言框架简介](https://developer.apple.com/videos/play/wwdc2018/713/)WWDC 2018 的会话。

### <a name="dominant-language"></a>主要语言

点击**语言**按钮来标识用户输入中的主要语言。

`HandleDetermineLanguageButtonTap`方法的`LanguageRecognizerViewController`使用 [`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
方法的`NLLanguageRecognizer`提取到 [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
主要语言的文本中找到：

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>语言概率

点击**语言概率**按钮以获取语言的假定供用户输入的列表。

`HandleLanguageProbabilitiesButtonTap`方法`LanguageRecognizerViewController`类实例化`NLLanguageRecognizer`并要求它为 [`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
用户的文本。 然后，它调用语言识别器的 [`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
方法会提取语言和关联的概率的字典。 `LanguageRecognizerTableViewController`类然后呈现这些语言和概率。

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

潜在`NLLanguage`值包括：

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

为提供了支持的语言的完整列表的一部分 [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
枚举 API 文档。

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>到字、 句子和段落标记化文本

**Tokenizer**选项卡的示例应用演示了如何隔离成其组件的单词的文本块或与句子[ `NLTokenizer` ](xref:NaturalLanguage.NLTokenizer)。

点击**单词**或**句子**按钮以获取令牌的列表。 每个标记都与 word 或句子中的原始文本相关联。

`ShowTokens` 通过调用用户输入到令牌的拆分 [`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
方法的`NLTokenizer`。 此方法返回的数组 [`NSValue`](xref:Foundation.NSValue)
对象，每个包装`NSRange`与中的原始文本标记相对应的值。

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController` 将呈现在每个表单元格中的单个令牌。 它提取`NSRange`从标记`NSValue`，查找相应的字符串中的原始文本，并在表视图单元格上设置标签：

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>标记命名的实体和词类

**标记器**XamarinNL 示例应用程序的选项卡将演示如何使用 [`NLTagger`](xref:NaturalLanguage.NLTagger)
若要将类别与输入字符串的令牌相关联的类。
自然语言框架包括对识别人物、 地点、 组织和词类的内置支持。

> [!NOTE]
> 自然语言框架还支持通过开发人员提供核心机器学习模型的自定义标记方案。 有关详细信息，看一看[自然语言框架简介](https://developer.apple.com/videos/play/wwdc2018/713/)WWDC 2018 的会话。

点击**名为实体**或**词类**按钮以提取：

- 一个数组`NSValue`对象，每个包装`NSRange`原始文本中的令牌。
- 一个数组[ `NLTag` ](xref:NaturalLanguage.NLTag)值 – 类别`NSValue`相同的数组索引处的令牌。

中`LanguageTaggerViewController`，`HandlePartsOfSpeechButtonTap`并`HandleNamedEntitiesButtonTap`每次调用`ShowTags`，并传递沿[ `NLTagScheme` ](xref:NaturalLanguage.NLTagScheme) – 任一`NLTagScheme.LexicalClass`（对于词类） 或`NLTagScheme.NameType`（对于名为实体）。

`ShowTags` 创建`NLTagger`，其实例化的数组`NLTagScheme`其中它将查询类型 (在此情况下，仅传入的`NLTagScheme`值)。 然后，它使用 [`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
方法`NLTagger`来确定与用户输入中的文本相关的标记。

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

然后，这些标记显示通过表中`LanguageTaggerTableViewController`。

潜在`NLTag`值包括：

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

为提供了支持的标记的完整列表的一部分 [`NLTag`](xref:NaturalLanguage.NLTag)
枚举 API 文档。

## <a name="related-links"></a>相关链接

- [示例应用程序 – XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [自然语言框架简介](https://developer.apple.com/videos/play/wwdc2018/713/)
- [自然语言 (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
