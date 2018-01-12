---
title: "线程处理和封送处理 (C + + /cli CX) |Microsoft 文档"
ms.custom: 
ms.date: 12/30/2016
ms.prod: windows-client-threshold
ms.technology: cpp-windows
ms.reviewer: 
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: article
f1_keywords: C4451
helpviewer_keywords:
- threading issues, C++/CX
- agility, C++/CX
- C++/CX, threading issues
ms.assetid: 83e9ca1d-5107-4194-ae6f-e01bd928c614
caps.latest.revision: "17"
author: ghogen
ms.author: ghogen
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 74a00ece1e1853346b88c0340b32911618a9ff24
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="threading-and-marshaling-ccx"></a>线程处理和封送处理 (C++/CX)
在大多数情况下，可以从任意线程访问的 Windows 运行时类，如标准的 c + + 对象的实例。 此类称为“敏捷类”。 但是，一小部分 Windows 附带的 Windows 运行时类是非敏捷，并且必须更常用作 COM 对象而不是标准 c + + 对象。 您无需是 COM 专家即可使用非敏捷类，但您需要考虑类的线程模型及其封送处理行为。 本文提供了针对您需要使用非敏捷类的实例的极少情况的背景和指南。  
  
## <a name="threading-model-and-marshaling-behavior"></a>线程模型和封送处理行为  
 应用于它的两个特性指明的那样，Windows 运行时类可以以多种方式支持并发线程访问：  
  
-   `ThreadingModel` 特性可以具有下列值之一：STA、MTA 或 Both（由 `ThreadingModel` 枚举定义）。  
  
-   `MarshallingBehavior` 特性可以具有下列值之一：Agile、None 或 Standard（由 `MarshallingType` 枚举定义）。  
  
 `ThreadingModel` 特性指定激活时类的加载位置：仅在用户界面线程 (STA) 上下文中、仅在后台线程 (MTA) 上下文中或在创建对象 (Both) 的线程的上下文中。 `MarshallingBehavior` 特性值指该对象在各种线程上下文中的行为；大多数情况下，您无需详细了解这些值。  在 Windows API 提供的类中，大约 90 % 具有 `ThreadingModel`=Both 和 `MarshallingType`=Agile。 这意味着它们可以透明有效地处理低级别线程处理详细信息。   当您使用 `ref new` 创建“敏捷”类时，可以从您的主应用程序线程或从一个或多个辅助线程调用其上的方法。  换言之，您可以从您的代码的任何地方使用敏捷类，不管它是由 Windows 提供的还是第三方提供的。 您不必牵涉类的线程处理模型或封送处理行为。  
  
## <a name="consuming-windows-runtime-components"></a>使用 Windows 运行时组件  
 当创建通用 Windows 平台应用时，可以与敏捷和非敏捷组件交互。 与非敏捷组件交互时，可能会遇到以下警告。  
  
### <a name="compiler-warning-when-consuming-non-agile-classes-c4451"></a>使用非敏捷类时出现的编译器警告 (C4451)  
 由于各种原因，一些类不能是敏捷类。 如果从用户界面线程和后台线程访问非敏捷类的实例，则应格外小心以确保运行时正确的行为。 在全局范围内实例化您的应用程序中的非敏捷运行时类，或者在一个自身也标记为敏捷的 ref 类中将一个非敏捷类型声明为类成员时，Visual C++ 编译器发出警告。  
  
 在非敏捷类当中，最容易处理的是具有 `ThreadingModel`=Both 和 `MarshallingType`=Standard 的类。  只要使用 `Agile<T>` 帮助程序类就能使这些类成为敏捷类。   下面的示例演示类型 `Windows::Security::Credentials::UI::CredentialPickerOptions^`的非敏捷对象的声明，以及作为结果发出的编译器警告。  
  
```  
  
ref class MyOptions  
    {  
    public:  
        property Windows::Security::Credentials::UI::CredentialPickerOptions^ Options  
  
        {  
            Windows::Security::Credentials::UI::CredentialPickerOptions^ get()   
            {  
                return _myOptions;  
            }  
        }  
    private:  
        Windows::Security::Credentials::UI::CredentialPickerOptions^ _myOptions;  
    };  
```  
  
 下面是发出的警告：  
  
> `Warning 1 warning C4451: 'Platform::Agile<T>::_object' : Usage of ref class 'Windows::Security::Credentials::UI::CredentialPickerOptions' inside this context can lead to invalid marshaling of object across contexts. Consider using 'Platform::Agile<Windows::Security::Credentials::UI::CredentialPickerOptions>' instead`  
  
 在成员范围或全局范围添加对于具有“标准”封送处理行为的对象的引用时，编译器会发出警告来建议您将该类型包在 `Platform::Agile<T>`中： `Consider using 'Platform::Agile<Windows::Security::Credentials::UI::CredentialPickerOptions>' instead` 如果使用 `Agile<T>`，可以像使用任何其他敏捷类一样使用该类。 在这些情况下使用 `Platform::Agile<T>` ：  
  
-   在全局范围内声明非敏捷变量。  
  
-   在类范围声明非敏捷变量，而使用代码有可能非法使用指针，即，在一个不同单元内未经正确封送处理就使用它。  
  
 如果这些条件都不适用，则您可以将包含类标记为非敏捷类。 换而言之，您应直接保留非敏捷对象仅在非敏捷类，并通过 platform:: agile 保留非敏捷对象\<T > 在敏捷类。  
  
 以下示例说明如何使用 `Agile<T>` 以便能安全忽略该警告。  
  
```  
  
#include <agile.h>  
ref class MyOptions  
    {  
    public:  
        property Windows::Security::Credentials::UI::CredentialPickerOptions^ Options  
  
        {  
            Windows::Security::Credentials::UI::CredentialPickerOptions^ get()   
            {  
                return m_myOptions.Get();  
            }  
        }  
    private:  
        Platform::Agile<Windows::Security::Credentials::UI::CredentialPickerOptions^> m_myOptions;  
  
    };  
  
```  
  
 请注意， `Agile` 不能在 ref 类中作为返回值或参数传递。 `Agile<T>::Get()` 方法返回句柄到对象 (^)，您可以在公共方法或属性中跨越应用程序二进制接口 (ABI) 传递它。  
  
 Visual c + + 中创建对具有"None"封送处理行为的进程外 Windows 运行时类的引用时编译器发出警告 C4451，但不建议你考虑使用`Platform::Agile<T>`。  编译器无法提供超过此警告的任何帮助，因此，您有责任正确使用该类，并确保您的代码只从用户界面线程调用 STA 组件，以及只从后台线程调用 MTA 组件。  
  
## <a name="authoring-agile-windows-runtime-components"></a>创作敏捷 Windows 运行时组件  
 在 ref 类定义在 C + + /CX 中，它是敏捷默认情况下 — 也就是说，它具有`ThreadingModel`= Both 和`MarshallingType`= Agile。  如果使用 [!INCLUDE[cppwrl](../cppcx/includes/cppwrl-md.md)]，则可以通过从 `FtmBase`（使用 `FreeThreadedMarshaller`）派生来使您的类为敏捷类。  如果创作具有 `ThreadingModel`=Both 或 `ThreadingModel`=MTA 的类，则请确保该类是线程安全的。 有关更多信息，请参见 [创建和使用对象 (WRL)](http://msdn.microsoft.com/en-us/d5e42216-e888-4f1f-865a-b5ccd0def73e)。  
  
 可以修改 ref 类的线程处理模型和封送处理行为。 但是，如果所做的更改会使类为非敏捷类，则必须了解与这些更改关联的影响。  
  
 下面的示例演示如何将应用`MarshalingBehavior`和`ThreadingModel`属性对 Windows 运行时类库中的运行时类。 当应用程序使用 DLL 并使用 `ref new` 关键字激活 `MySTAClass` 类对象时，该对象在单线程单元激活，并且不支持封送处理。  
  
```  
using namespace Windows::Foundation::Metadata;  
using namespace Platform;  
  
[Threading(ThreadingModel::STA)]  
[MarshalingBehavior(MarshalingType::None)]   
public ref class MySTAClass  
{  
};  
  
```  
 
 未密封的类必须具有封送处理和线程处理特性设置，以便编译器可以验证派生类对于这些特性是否具有同一值。 如果类没有显式设置这些设置，则编译器将生成错误且无法编译。 在下列情况中，从未密封类派生的任何类都会生成一个编译器错误：  
  
-   `ThreadingModel` 和 `MarshallingBehavior` 特性在派生类中未定义。  
  
-   派生类中 `ThreadingModel` 和 `MarshallingBehavior` 特性的值与基类中的值不匹配。  
  
 该组件的应用程序清单注册信息中指定的线程处理和封送处理所需的第三方 Windows 运行时组件的信息。 我们建议使您的所有 Windows 运行时组件敏捷。 这将确保客户端代码可以从应用程序中的任何线程调用您的组件，并能改善那些调用的性能，因为它们是没有封送处理的直接调用。 如果以这种方式创作类，则客户端代码无需使用 `Platform::Agile<T>` 来使用您的类。  
  
## <a name="see-also"></a>请参阅  
 [ThreadingModel](http://msdn.microsoft.com/library/windows/apps/xaml/windows.foundation.metadata.threadingmodel.aspx)   
 [MarshallingBehavior](http://msdn.microsoft.com/library/windows/apps/xaml/windows.foundation.metadata.marshalingbehaviorattribute.aspx)