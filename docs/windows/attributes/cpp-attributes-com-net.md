---
title: 适用于 COM 和 .NET 的 C++ 属性
ms.custom: index-page
ms.date: 11/19/2018
ms.topic: conceptual
helpviewer_keywords:
- attributes [C++/CLI], reference topics
ms.assetid: 613a3611-b3eb-4347-aa38-99b654600e1c
ms.openlocfilehash: 734d82a30df3e143a6f47cb1b3eca2cd778830bd
ms.sourcegitcommit: 857fa6b530224fa6c18675138043aba9aa0619fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80214924"
---
# <a name="c-attributes-for-com-and-net"></a>适用于 COM 和 .NET 的 C++ 属性

Microsoft 定义了一组C++属性，这些属性可简化 COM 编程，并 .NET Framework 公共语言运行时开发。 当您在源文件中包含特性时，编译器将与提供程序 Dll 一起使用以插入代码或修改生成的对象文件中的代码。 这些特性有助于创建 .idl 文件、接口、类型库和其他 COM 元素。 在集成开发环境（IDE）中，向导和属性窗口支持特性。

尽管属性消除了编写 COM 对象所需的一些详细编码，但你需要使用[COM 的基础知识](/windows/win32/com/the-component-object-model)来最大程度地使用它们。

> [!NOTE]
> 如果正在查找C++标准属性，请参阅[属性](../../cpp/attributes.md)。

## <a name="purpose-of-attributes"></a>特性用途

特性在C++当前不可能的情况下扩展，无需打破语言的经典结构。 特性允许提供程序（单独的 Dll）以动态方式扩展语言功能。 特性的主要目标是简化 COM 组件的创作，同时提高组件开发人员的工作效率。 特性可应用到几乎任何C++构造，如类、数据成员或成员函数。 下面是此新技术提供的优点的突出显示：

- 公开熟悉且简单的调用约定。

- 使用与宏不同的插入代码，由调试器识别。

- 允许从基类中轻松派生，无需执行繁重的实现细节。

- 使用几个简明的属性替换 COM 组件所需的大量 IDL 代码。

例如，若要实现泛型 ATL 类的简单事件接收器，可以将[event_receiver](event-receiver.md)特性应用到特定类，如 `CMyReceiver`。 然后，Microsoft C++编译器将编译 `event_receiver` 属性，该编译器将适当的代码插入到对象文件中。

```cpp
[event_receiver(com)]
class CMyReceiver
{
   void handler1(int i) { ... }
   void handler2(int i, float j) { ... }
}
```

然后，你可以设置 `CMyReceiver` 方法，`handler1` 和 `handler2` 处理事件源（使用内部函数[__hook](../../cpp/hook.md)）中的事件（使用[event_source](event-source.md)创建的事件）。

## <a name="basic-mechanics-of-attributes"></a>特性的基本机制

有三种方法可将属性插入项目中。 首先，可以手动将其插入到源代码中。 其次，你可以使用项目中对象的属性网格插入它们。 最后，您可以使用各种向导插入它们。 有关使用 "**属性**" 窗口和各种向导的详细信息，请参阅[Visual Studio 项目C++- ](../../build/creating-and-managing-visual-cpp-projects.md)。

与之前一样，生成项目时，编译器会分析每个C++源文件，生成一个对象文件。 但是，当编译器遇到某个属性时，将对其进行分析和语法验证。 然后，编译器动态调用特性提供程序以在编译时插入代码或进行其他修改。 提供程序的实现因特性的类型而异。 例如，ATL 相关属性由 Atlprov 实现。

下图演示了编译器和属性提供程序之间的关系。

![组件属性通信](../media/vccompattrcomm.gif "组件特性通信")

> [!NOTE]
> 属性使用不会改变源文件的内容。 生成的属性代码只有在调试会话期间才可见。 此外，对于项目中的每个源文件，您可以生成一个文本文件，用于显示属性替换的结果。 有关此过程的详细信息，请参阅[/fx （合并插入的代码）](../../build/reference/fx-merge-injected-code.md)和[调试注入的代码](/visualstudio/debugger/how-to-debug-injected-code)。

与大多数C++构造一样，特性具有定义其正确用法的一组特性。 这称为属性的上下文，并在每个属性参考主题的特性上下文表中解决。 例如， [coclass](coclass.md)特性只能应用于现有的类或结构，而不能应用于[cpp_quote](cpp-quote.md)特性，该特性可插入到源文件中的C++任何位置。

## <a name="building-an-attributed-program"></a>生成特性化程序

将视觉C++属性放入源代码后，你可能希望 Microsoft C++编译器为你生成类型库和 .idl 文件。 以下链接器选项可帮助你生成 .tlb 和 .idl 文件：

- [/IDLOUT](../../build/reference/idlout-name-midl-output-files.md)

- [/IGNOREIDL](../../build/reference/ignoreidl-don-t-process-attributes-into-midl.md)

- [/MIDL](../../build/reference/midl-specify-midl-command-line-options.md)

- [/TLBOUT](../../build/reference/tlbout-name-dot-tlb-file.md)

某些项目包含多个独立的 .idl 文件。 它们用于产生两个或更多个 .tlb 文件，还可以将它们绑定到资源块。 视觉对象C++目前不支持此方案。

此外，可视C++链接器还会将所有与 IDL 相关的属性信息输出到单个 MIDL 文件中。 无法从单个项目生成两个类型库。

## <a name="attribute-contexts"></a><a name="contexts"></a>特性上下文

C++可以使用以下四个基本字段来描述属性：可以将其应用于（**适用**于），如果它们是可重复的或不是（可**重复**的），则需要存在其他属性（**必需的属性**），并与其他属性（**无效属性**）不兼容。 这些字段在每个属性的参考主题的随附表中列出。 下面介绍了其中的每个字段。

### <a name="applies-to"></a>应用于

此字段描述为指定C++属性的合法目标的不同语言元素。 例如，如果属性在 "**应用**于" 字段中指定 "类"，则表示该属性只能应用于合法C++类。 如果该特性应用于类的成员函数，则会导致语法错误。

有关详细信息，请参阅[按使用](attributes-by-usage.md)情况的属性。

### <a name="repeatable"></a>可重复

此字段指示该属性是否可重复应用于同一目标。 大多数属性是不可重复的。

### <a name="required-attributes"></a>必需的属性

此字段列出了需要存在（即应用于同一目标）以使指定属性正常工作的其他属性。 属性具有此字段的任何条目的情况并不常见。

### <a name="invalid-attributes"></a>属性无效

此字段列出与指定特性不兼容的其他特性。 属性具有此字段的任何条目的情况并不常见。

## <a name="in-this-section"></a>本节内容

[特性编程常见问题解答](attribute-programming-faq.md)<br/>
[按组分的特性](attributes-by-group.md)<br/>
[按用法分的特性](attributes-by-usage.md)<br/>
[按字母顺序的特性参考](attributes-alphabetical-reference.md)
