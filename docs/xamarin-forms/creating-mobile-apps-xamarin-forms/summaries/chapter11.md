---
title: 第 11 章的摘要。 可绑定的基础结构
description: 使用 Xamarin.Forms 创建移动应用：第 11 章的摘要。 可绑定的基础结构
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334290"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>第 11 章的摘要。 可绑定的基础结构

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

每个 C# 程序员是熟悉 C#*属性*。 属性包含*设置*访问器和/或一个*获取*访问器。 人们常*CLR 属性*公共语言运行时。

Xamarin.Forms 定义了名为增强型的属性定义*可绑定属性*封装[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)类，并支持[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类。 这些类是相关，但两种截然不同：`BindableProperty`用于定义的属性，然后重试。`BindableObject`类似于`object`，因为它是定义可绑定属性的类的基类。

## <a name="the-xamarinforms-class-hierarchy"></a>Xamarin.Forms 类层次结构

[ **ClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy)示例使用反射来显示类层次结构的 Xamarin.Forms 和演示所扮演的重要角色`BindableObject`此层次结构中。 `BindableObject` 派生自`Object`和是父类[ `Element` ](xref:Xamarin.Forms.Element)从中[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)派生。 这是到父类[ `Page` ](xref:Xamarin.Forms.Page)并[ `View` ](xref:Xamarin.Forms.View)，这是到父类[ `Layout` ](xref:Xamarin.Forms.Layout):

[![共享的类层次结构的三个屏幕截图](images/ch11fg01-small.png "类层次结构共享")](images/ch11fg01-large.png#lightbox "类层次结构共享")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>浏览到 BindableObject 和 BindableProperty

在派生类中`BindableObject`许多 CLR 属性被称为"支持"的可绑定属性。 例如， [ `Text` ](xref:Xamarin.Forms.Label.Text)的属性`Label`类是 CLR 属性，但`Label`类还定义了名为的公共静态只读字段[ `TextProperty` ](xref:Xamarin.Forms.Label.TextProperty)的类型`BindableProperty`。

应用程序可以设置或获取`Text`属性`Label`通常情况下，也可以设置应用程序`Text`通过调用[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法由定义`BindableObject`与`Label.TextProperty`自变量。 同样，应用程序可以获得的值`Text`属性通过调用[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法，再次使用`Label.TextProperty`参数。 这可通过演示[ **PropertySettings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)示例。

实际上，`Text`使用完全实现的 CLR 属性`SetValue`并`GetValue`定义的方法`BindableObject`结合`Label.TextProperty`静态属性。

`BindableObject` 和`BindableProperty`提供支持：

- 让属性默认值
- 将其当前值存储
- 提供用于验证属性值的机制
- 维护单个类中的相关属性之间的一致性
- 响应属性更改
- 属性将要更改或已更改时触发通知
- 支持数据绑定
- 支持样式
- 支持动态资源

属性支持的可绑定属性发生变化，每当`BindableObject`激发[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件标识已更改的属性。 属性设置为相同的值时，不触发此事件。

某些属性不受可绑定属性和一些 Xamarin.Forms 类&mdash;如`Span`&mdash;并非派生`BindableObject`。 只能从派生的类`BindableObject`可以支持的可绑定属性，因为`BindableObject`定义`SetValue`和`GetValue`方法。

因为`Span`不是派生自`BindableObject`，其属性&mdash;如`Text`&mdash;受可绑定的属性。 这就是为什么`DynamicResource`上设置`Text`的属性`Span`引发中出现的异常[ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)示例在上一章。 [ **DynamicVsStaticCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode)示例演示如何在代码中使用设置动态资源[ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String))方法由定义`Element`。 第一个参数是类型的对象`BindableProperty`。

同样， [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法由定义`BindableObject`具有类型的第一个参数`BindableProperty`。

## <a name="defining-bindable-properties"></a>定义可绑定属性

可以定义可绑定属性使用静态[ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法来创建类型的静态只读字段`BindableProperty`。

了这一点[ `AltLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 类派生自`Label`并让你以磅为单位指定字体大小。 中提供其演示[ **PointSizedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)示例。

四个参数的`BindableProperty.Create`是必需的方法：

- `propertyName`: （作为 CLR 属性名称相同） 的属性的文本名称
- `returnType`: CLR 属性的类型
- `declaringType`： 声明属性的类的类型
- `defaultValue`： 该属性的默认值

因为`defaultValue`属于类型`object`，编译器必须能够确定默认值的类型。 例如，如果`returnType`是`double`，则`defaultValue`应设置为类似于 0.0 而不是 0，或类型不匹配将触发在运行时异常。

它也是很常见的可绑定的属性包括：

- `propertyChanged`: 属性值更改时调用的静态方法。 第一个参数是类的更改其属性的实例。

其他参数`BindableProperty.Create`不常见：

- `defaultBindingMode`： 用于数据绑定 (如中所述[**第 16 章。数据绑定**](chapter16.md))
- `validateValue`： 一个回调，以检查有效的值
- `propertyChanging`： 一个回调，以指示即将更改属性时
- `coerceValue`： 一个回调，以强制设定的值与另一个值
- `defaultValueCreate`： 一个回调，以创建不能在类 （例如，一个集合） 的实例之间共享的默认值

### <a name="the-read-only-bindable-property"></a>只读的可绑定属性

可绑定的属性可以是只读的。 创建只读的可绑定属性需要调用静态方法[ `BindableProperty.CreateReadOnly` ](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))可以定义类型的私有静态只读字段[ `BindablePropertyKey` ](xref:Xamarin.Forms.BindablePropertyKey)。

然后，定义的 CLR 属性`set`访问器中作为`private`调用[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object))重载`BindablePropertyKey`对象。 这可以防止属性被设置该类的外部。

了这一点[ `CountedLabel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs)中使用类[ **BaskervillesCount** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)示例。

## <a name="related-links"></a>相关链接

- [第 11 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [第 11 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)
