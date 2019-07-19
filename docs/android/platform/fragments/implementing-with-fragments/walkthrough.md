---
title: Xamarin Android 片段演练-第1部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 4471f5ec199ef52a2dcb68ab85cc9a1209eb4802
ms.sourcegitcommit: 9a2a21974d35353c3765eb683ef2fd7161c1d94a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68329981"
---
# <a name="fragments-walkthrough-ndash-phone"></a>分段演练&ndash;电话

这是演练的第一部分, 它将创建面向 Android 设备的 Xamarin Android 应用。 本演练将讨论如何在 Xamarin 中创建片段, 以及如何将它们添加到示例。

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

将为此应用创建以下类:

1. `PlayQuoteFragment`&nbsp;此片段将显示由 William 莎士比亚播放的报价。 它将由`PlayQuoteActivity`托管。
1. `Shakespeare`&nbsp;此类将两个硬编码数组保存为属性。
1. `TitlesFragment`&nbsp;此片段将显示由 William 莎士比亚编写的播放标题列表。 它将由`MainActivity`托管。
1. `PlayQuoteActivity`将启动以响应用户选择 "播放"。 `TitlesFragment` &nbsp; `TitlesFragment` `PlayQuoteActivity`

## <a name="1-create-the-android-project"></a>1.创建 Android 项目

创建名为**FragmentSample**的新的 Xamarin Android 项目。
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![创建新的 Xamarin Android 项目](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![创建新的 Xamarin Android 项目](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建议为本演练选择**新式开发**。

创建项目后, 请将文件**布局 main.axml**重命名为**layout/activity_main main.axml**。

-----

## <a name="2-add-the-data"></a>2.添加数据

此应用程序的数据将存储在两个作为类名称`Shakespeare`属性的硬编码字符串数组中:

* `Shakespeare.Titles`&nbsp;此数组将保存 William 莎士比亚的播放列表。 这是的数据源`TitlesFragment`。
* `Shakespeare.Dialogue`此数组将保留中`Shakespeare.Titles`包含的其中一个播放的引号列表。 &nbsp; 这是的数据源`PlayQuoteFragment`。

向 FragmentSample 项目C#添加一个新  类并将其命名为**Shakespeare.cs**。 在此文件中, 创建一个C#具有以下`Shakespeare`内容的名为的新类

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3.创建 PlayQuoteFragment

`PlayQuoteFragment`是一个 Android 片段, 该片段将显示用户在应用程序中之前选择的莎士比亚 play 的报价, 此片段不会使用 Android 布局文件; 相反, 它会动态创建其用户界面。 将名`Fragment` `PlayQuoteFragment`为的新类添加到项目:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![添加新C#类](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![添加新C#类](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然后, 将片段的代码更改为类似于以下代码片段:

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

这是 Android 应用中的一种常见模式, 用于提供将实例化片段的工厂方法。 这可确保创建具有必要参数的片段, 以便正常工作。 在本演练中, 每次选择引号时, `PlayQuoteFragment.NewInstance`应用程序都应使用方法创建新的片段。 方法将使用一个参数&ndash;作为要显示的引号的索引。 `NewInstance`

当`OnCreateView`需要在屏幕上呈现片段时, Android 将调用方法。 它将返回作为片段`View`的 Android 对象。 此片段不使用布局文件来创建视图。 相反, 它将以编程方式创建视图, 方法是实例化**TextView**来保存引号, 并在**ScrollView**中显示该小组件。

> [!NOTE]
> 片段子类必须具有没有参数的公共默认构造函数。

## <a name="4-create-the-playquoteactivity"></a>4.创建 PlayQuoteActivity

片段必须承载于活动内, 因此, 此应用需要一个将承载的`PlayQuoteFragment`活动。 活动将在运行时将片段动态添加到其布局。 将新活动添加到应用程序并将其`PlayQuoteActivity`命名为:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![向项目添加 Android 活动](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![向项目添加 Android 活动](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

编辑`PlayQuoteActivity`以下代码:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

创建`PlayQuoteActivity`后, 它将在的上下文`FragmentTransaction`的`PlayQuoteFragment`根视图中实例化新的并加载该片段。 请注意, 此活动不会为其用户界面加载 Android 布局文件。 相反, 新`PlayQuoteFragment`的将添加到应用程序的根视图中。 资源标识符`Android.Resource.Id.Content`用于引用活动的根视图, 而无需知道其特定标识符。

## <a name="5-create-titlesfragment"></a>5.创建 TitlesFragment

将子类称为`ListFragment`的专用片段, 它封装了用于在片段中显示的`ListView`逻辑。 `TitlesFragment` `ListFragment` `OnListItemClick`公开一个属性(由用于显示其`ListView`内容) 和一个名为的事件处理程序, 该事件处理程序允许片段响应由显示的行的单击。 `ListView` `ListAdapter`

若要开始, 请向项目中添加一个新的片段, 并将其命名为**TitlesFragment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![将 Android 片段添加到项目](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![将 Android 片段添加到项目](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

编辑片段内的代码:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

创建活动时, Android 将调用`OnActivityCreated`此片段的方法; 这是创建的`ListView`列表适配器的位置。  方法将启动的`PlayQuoteActivity`实例, 以显示所选播放的报价单。 `ShowQuoteFromPlay`

## <a name="display-titlesfragment-in-mainactivity"></a>在 MainActivity 中显示 TitlesFragment

最后一步是在中`TitlesFragment` `MainActivity`显示。 活动不会动态加载片段。 相反, 将通过在活动的布局文件中使用`fragment`元素来声明片段来静态加载片段。 通过将`android:name`属性设置为片段类 (包括类型的命名空间) 来标识要加载的片段。 例如, 若要使用`TitlesFragment`, 则`android:name`将设置为`FragmentSample.TitlesFragment`。

编辑布局文件**activity_main**, 并将现有的 XML 替换为以下内容:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> 特性是的有效`android:name`替换。 `class` 对于首选窗体, 没有正式的指导, 这里有很多代码库示例, 它们将与`class`一起`android:name`使用。

MainActivity 无需更改代码。 该类中的代码应该非常类似于以下代码片段:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>运行应用

现在代码已完成, 请在设备上运行应用以查看其运行情况。

[![在电话上运行的应用程序的屏幕截图。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本演练的第2部分](./walkthrough-landscape.md)将为在横向模式下运行的设备 optimtize 此应用程序。
