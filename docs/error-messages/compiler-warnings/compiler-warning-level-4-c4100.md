---
title: 编译器警告（等级 4）C4100
ms.date: 11/04/2016
f1_keywords:
- C4100
helpviewer_keywords:
- C4100
ms.assetid: 478ed97d-e502-49e4-9afb-ac2a6c61194b
ms.openlocfilehash: bcd51c66359d0553b7657d85f5b45ee22d4648ff
ms.sourcegitcommit: 573b36b52b0de7be5cae309d45b68ac7ecf9a6d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74991648"
---
# <a name="compiler-warning-level-4-c4100"></a>编译器警告（等级 4）C4100

“identifier”: 未引用的形参

未在函数体中引用此形参。 已忽略未引用的形参。

当代码在基元类型的未引用参数上调用析构函数时，也可能发出 C4100 错误。  这是 Microsoft C++编译器的一个限制。

下面的示例生成 C4100：

```cpp
// C4100.cpp
// compile with: /W4
void func(int i) {   // C4100, delete the unreferenced parameter to
                     //resolve the warning
   // i;   // or, add a reference like this
}

int main()
{
   func(1);
}
```
