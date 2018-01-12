---
title: "编译器错误 C3697 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-tools
ms.tgt_pltfrm: 
ms.topic: error-reference
f1_keywords: C3697
dev_langs: C++
helpviewer_keywords: C3697
ms.assetid: 2d3f63c4-b7f8-421d-a7a5-2bf17fd054f9
caps.latest.revision: "8"
author: corob-msft
ms.author: corob
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: 153d488d1d1f332c8317a5c1dc7fdd565fa47eb7
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="compiler-error-c3697"></a>编译器错误 C3697
限定符： 不能使用此限定符 ^  
  
 跟踪句柄 (^) 已应用于它未设计为其的限定符。  
  
 下面的示例生成 C3697:  
  
```  
// C3697.cpp  
// compile with: /clr  
using namespace System;  
int main() {  
   String ^__restrict s;   // C3697  
   String ^ s2;   // OK  
}  
```