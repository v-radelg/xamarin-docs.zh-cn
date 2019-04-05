---
title: XAML 控件
description: 在 Xamarin.Forms 中定义的视图的所有可从 XAML 文件引用。
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/03/2019
ms.openlocfilehash: a8a61ac505eab8c458c49bde9184d6e96583d37f
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59020107"
---
# <a name="xaml-controls"></a>XAML 控件

[![Download 示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/FormsGallery/)

视图是用户界面对象，如标签、 按钮和滑块，通常称为分别*控件*或*小组件*其他图形的编程环境中。 支持所有派生自 Xamarin.Forms 的视图[ `View` ](xref:Xamarin.Forms.View)类。

在 Xamarin.Forms 中定义的视图的所有可从 XAML 文件引用。

## <a name="views-for-presentation"></a>演示文稿的视图

|     |     |
| --- | --- |
| <h3>BoxView</h3>显示特定颜色的矩形。<p align="center">![字数的屏幕截图](xaml-controls-images/BoxView.png "字数")</p>[API](xref:Xamarin.Forms.BoxView) / [指南](https://developer.xamarin.com/guides/xamarin-forms/user-interface/boxview/) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>图像</h3>显示位图。<p align="center">![图像的屏幕截图](xaml-controls-images/Image.png "图像")</p>[API](xref:Xamarin.Forms.Image) / [指南](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>Label</h3>显示文本的一个或多个的行。<p align="center">![标签的屏幕截图](xaml-controls-images/Label.png "标签")</p>[API](xref:Xamarin.Forms.Label) / [指南](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>映射</h3>显示了一个映射。<p align="center">![映射的屏幕截图](xaml-controls-images/Map.png "映射")</p>[API](xref:Xamarin.Forms.Maps.Map) / [指南](~/xamarin-forms/user-interface/map.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>WebView</h3>显示网页或 HTML 内容。<p align="center">![Web 视图的屏幕截图](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [指南](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>启动命令的视图

|     |     |
| --- | --- |
| <h3>Button</h3>将矩形对象中显示文本。<p align="center">![按钮的屏幕截图](xaml-controls-images/Button.png "按钮")</p>[API](xref:Xamarin.Forms.Button) / [指南](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>矩形对象中显示图像。<p align="center">![一个 ImageButton 的屏幕截图](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [指南](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>SearchBar</h3>显示搜索栏中的，为执行搜索。<p align="center">![SearchBar 的屏幕截图](xaml-controls-images/SearchBar.png "SearchBar")</p>[API](xref:Xamarin.Forms.SearchBar) | <p valign="center"><pre>&lt;SearchBar Placeholder="Xamarin.Forms Property"<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>对于设置值的视图

|     |     |
| --- | --- |
| <h3>Slider</h3>允许选择`double`从连续范围的值。<p align="center">![滑块的屏幕截图](xaml-controls-images/Slider.png "滑块")</p>[API](xref:Xamarin.Forms.Slider) / [指南](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>步进器</h3>允许选择`double`将增量在范围中的值。<p align="center">![分档器的屏幕截图](xaml-controls-images/Stepper.png "分档器")</p>[API](xref:Xamarin.Forms.Stepper) / [指南](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>开关</h3>允许选择`boolean`值。<p align="center">![交换机的屏幕截图](xaml-controls-images/Switch.png "开关")</p>[API](xref:Xamarin.Forms.Switch) | <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>允许选择一个日期。<p align="center">![DatePicker 的屏幕截图](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [指南](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>允许时间的选择。<p align="center">![TimePicker 的屏幕截图](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [指南](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>编辑文本的视图

|     |     |
| --- | --- |
| <h3>条目</h3>允许文本输入和编辑单个的行。<p align="center">![条目的屏幕截图](xaml-controls-images/Entry.png "条目")</p>[API](xref:Xamarin.Forms.Entry) / [指南](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>编辑器</h3>允许多行文本输入和编辑。<p align="center">![编辑器的屏幕截图](xaml-controls-images/Editor.png "标签")</p>[API](xref:Xamarin.Forms.Editor) / [指南](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>若要指示活动的视图

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>显示动画，以显示应用程序所从事的耗时较长的活动，而无需授予任何可能的进度。<p align="center">![ActivityIndicator 的屏幕截图](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>显示动画，以显示通过耗时较长的活动进行，该应用程序。<p align="center">![进度栏的屏幕截图](xaml-controls-images/ProgressBar.png "进度栏")</p>[API](xref:Xamarin.Forms.ProgressBar) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>显示集合的视图

|     |     |
| --- | --- |
| <h3>CollectionView</h3>显示可选择数据项，使用不同的布局规范的可滚动列表。<p align="center">![之间导航的屏幕截图](xaml-controls-images/CollectionView.png "之间导航")</p>[指南](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>ListView</h3>显示可选择数据项的可滚动列表。<p align="center">![ListView 的屏幕截图](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [指南](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>选取器</h3>显示从列表项的文本字符串。<p align="center">![选取器的屏幕截图](xaml-controls-images/Picker.png "选取器")</p>[API](xref:Xamarin.Forms.Picker) / [指南](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&lt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>显示的交互式行的列表。<p align="center">![屏幕截图 TableView](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [指南](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](https://developer.xamarin.com/samples/FormsGallery/)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Xamarin.Forms API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
