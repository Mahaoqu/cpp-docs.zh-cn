---
title: '&lt;exception&gt;'
ms.date: 11/04/2016
f1_keywords:
- <exception>
helpviewer_keywords:
- exception header
ms.assetid: 28900768-5dd7-4834-b907-5e37ab3407db
ms.openlocfilehash: 1627ba411fd201c84daee534ea134be2dba73376
ms.sourcegitcommit: 1f009ab0f2cc4a177f2d1353d5a38f164612bdb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2020
ms.locfileid: "87193648"
---
# <a name="ltexceptiongt"></a>&lt;exception&gt;

定义与异常处理相关的若干类型和函数。 异常处理用于系统可从错误中恢复的情形。 它提供了将控制权从函数返回给程序的一种方法。 合并异常处理的目标是提高程序的可靠性，同时提供一种有序地从错误中恢复的方法。

## <a name="requirements"></a>要求

**标头：**\<exception>

**命名空间:** std

## <a name="members"></a>成员

### <a name="typedefs"></a>Typedef

|||
|-|-|
|[exception_ptr](../standard-library/exception-typedefs.md#exception_ptr)|该类型描述指向异常的指针。|
|[terminate_handler](../standard-library/exception-typedefs.md#terminate_handler)|该类型描述指向适合用作 `terminate_handler` 的函数的指针。|
|[unexpected_handler](../standard-library/exception-typedefs.md#unexpected_handler)|该类型描述指向适合用作 `unexpected_handler` 的函数的指针。|

### <a name="functions"></a>函数

|||
|-|-|
|[current_exception](../standard-library/exception-functions.md#current_exception)|获取指向当前异常的指针。|
|[get_terminate](../standard-library/exception-functions.md#get_terminate)|获取当前的 `terminate_handler` 函数。|
|[get_unexpected](../standard-library/exception-functions.md#get_unexpected)|获取当前的 `unexpected_handler` 函数。|
|[make_exception_ptr](../standard-library/exception-functions.md#make_exception_ptr)|创建保留异常副本的 `exception_ptr` 对象。|
|[rethrow_exception](../standard-library/exception-functions.md#rethrow_exception)|引发作为参数传递的异常。|
|[rethrow_if_nested](../standard-library/exception-functions.md#rethrow_if_nested)|如果嵌套，则强制转换和引发异常。|
|[set_terminate](../standard-library/exception-functions.md#set_terminate)|建立程序终止时要调用的新 `terminate_handler`。|
|[set_unexpected](../standard-library/exception-functions.md#set_unexpected)|建立遇到意外异常时要调用的新 `unexpected_handler`。|
|[终止](../standard-library/exception-functions.md#terminate)|调用终止处理程序。|
|[throw_with_nested](../standard-library/exception-functions.md#throw_with_nested)|如果嵌套，则引发异常。|
|[uncaught_exception](../standard-library/exception-functions.md#uncaught_exception)|**`true`** 仅当当前正在处理引发的异常时返回。|
|[之外](../standard-library/exception-functions.md#unexpected)|调用意外处理程序。|

### <a name="classes"></a>类

|||
|-|-|
|[bad_exception 类](../standard-library/bad-exception-class.md)|该类描述可从 `unexpected_handler` 引发的异常。|
|[exception 类](../standard-library/exception-class.md)|该类用作某些表达式和 C++ 标准库所引发的所有异常的基类。|
|[nested_exception 类](../standard-library/nested-exception-class.md)|类描述了一个可以捕获和存储的异常，以便以后使用。|

## <a name="see-also"></a>另请参阅

[标头文件引用](../standard-library/cpp-standard-library-header-files.md)\
[C + + 标准库中的线程安全](../standard-library/thread-safety-in-the-cpp-standard-library.md)
