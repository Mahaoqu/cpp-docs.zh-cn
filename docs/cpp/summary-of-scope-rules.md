---
title: "范围规则摘要 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-language
ms.tgt_pltfrm: 
ms.topic: language-reference
dev_langs: C++
helpviewer_keywords:
- class scope [C++], rules
- classes [C++], scope
- class names [C++], scope rules
- names [C++], class
- scope [C++], class names
ms.assetid: 47e26482-0111-466f-b857-598c15d05105
caps.latest.revision: "8"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: c530a586ca2b8b70cfdc967c354738e93435f20c
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="summary-of-scope-rules"></a>范围规则摘要
名称的使用在其范围内必须是明确的（直至确定重载的点）。 如果名称表示一个函数，则该函数的参数的数目和类型必须明确。 如果名称保持明确，[成员访问](../cpp/member-access-control-cpp.md)应用规则。  
  
## <a name="constructor-initializers"></a>构造函数初始值设定项  
 构造函数初始值设定项 (中所述[初始化基类和成员](http://msdn.microsoft.com/en-us/2f71377e-2b6b-49da-9a26-18e9b40226a1)) 的构造函数指定它们的最外层块范围中计算。 因此，它们可使用构造函数的参数名。  
  
## <a name="global-names"></a>全局名称  
 在以下情况下，对象、函数或枚举器的名称为全局名称：在任何函数或类的外部引用该名称或将全局一元范围运算符 (`::`) 作为其前缀，以及未将该名称与上述任何二元运算符结合使用：  
  
-   范围解析 (`::`)  
  
-   对象和引用的成员选择 (**。**)  
  
-   指针的成员选择 (**->**)  
  
## <a name="qualified-names"></a>限定名称  
 用于二进制范围解析运算符 (`::`) 的名称称为“限定名”。 在二进制范围解析运算符后指定的名称必须是在该运算符左侧指定的类的成员或其基类的成员。  
  
 成员选择运算符后指定的名称 (**。** 或 **->** ) 必须是该运算符左侧或其基类的成员上指定的对象的类类型的成员。 成员选择运算符右侧指定的名称 (**->**) 也可以是对象的另一个类类型，提供程序的左侧 **->** 是一个类对象和该类定义的重载的成员选择运算符 (**->**) 计算结果为指向某个其他类类型的指针。 (在将更详细地讨论了此配置[类成员访问](../cpp/member-access.md)。)  
  
 编译器将按以下顺序搜索名称，并在找到名称时停止搜索：  
  
1.  如果名称在函数内部使用，则搜索当前块范围；否则搜索全局范围。  
  
2.  由里向外的每个封闭块范围，包括最外面的函数范围（包括函数参数）。  
  
3.  如果名称在成员函数内使用，则在类的范围中搜索名称。  
  
4.  在类的基类中搜索名称。  
  
5.  搜索封闭的嵌套类范围（如果有）及其基项。 搜索继续，直到搜索最外面的封闭类范围。  
  
6.  在全局范围中搜索。  
  
 但是，您可对此搜索顺序做如下修改：  
  
1.  在名称前面放置 `::` 可强制从全局范围处开始搜索。  
  
2.  名称前面放置**类**， `struct`，和**联合**关键字强制编译器仅搜索**类**， `struct`，或**联合**名称。  
  
3.  范围解析运算符左侧的名称 (`::`) 只能是**类**， `struct`，**命名空间**，或**联合**名称。  
  
 如果名称引用非静态成员，但用于静态成员函数，则将生成错误消息。 同样，如果名称引用封闭类中任何非静态成员，将生成错误消息因为封闭的类不具备封闭类**这**指针。  
  
## <a name="function-parameter-names"></a>函数参数名  
 函数定义中的函数参数名被视为位于函数的最外层块的范围内。 因此，它们是本地名称并且将在函数退出时超出范围。  
  
 函数声明（原型）中的函数参数名位于声明的局部范围内，并且将在声明结尾处超出范围。  
  
 默认参数位于它们作为默认值的参数的范围内，如前面两段中所述。 但是，它们无法访问局部变量或非静态类成员。 默认参数的计算时间是函数调用时，但计算位置是在函数声明的原始范围内。 因此，成员函数的默认参数始终在类范围中计算。  
  
## <a name="see-also"></a>请参阅  
 [继承](../cpp/inheritance-cpp.md)