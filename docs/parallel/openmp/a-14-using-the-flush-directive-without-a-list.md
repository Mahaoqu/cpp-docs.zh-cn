---
title: "使用不带列表刷新指令 A.14 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-windows
ms.tgt_pltfrm: 
ms.topic: article
dev_langs: C++
ms.assetid: 9e63141a-d0c6-43a5-ac16-b0bd7c89b871
caps.latest.revision: "7"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload: cplusplus
ms.openlocfilehash: b2a88558de3c04f1ae05922b1ad5bc7f63aed996
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="a14---using-the-flush-directive-without-a-list"></a>A.14   使用不带列表的 flush 指令
下面的示例 (对于[部分 2.6.5](../../parallel/openmp/2-6-5-flush-directive.md)第 20 页上) 区分受共享的对象`flush`指令与任何从共享不受影响的对象的列表：  
  
## <a name="example"></a>示例  
  
### <a name="code"></a>代码  
  
```  
// omp_flush_without_list.c  
#include <omp.h>  
  
int x, *p = &x;  
  
void f1(int *q)  
{  
    *q = 1;  
    #pragma omp flush  
    // x, p, and *q are flushed  
    //   because they are shared and accessible  
    // q is not flushed because it is not shared.  
}  
  
void f2(int *q)  
{  
    #pragma omp barrier  
    *q = 2;  
  
    #pragma omp barrier  
    // a barrier implies a flush  
    // x, p, and *q are flushed  
    //   because they are shared and accessible  
    // q is not flushed because it is not shared.  
}  
  
int g(int n)  
{  
    int i = 1, j, sum = 0;  
    *p = 1;  
  
    #pragma omp parallel reduction(+: sum) num_threads(10)  
    {  
        f1(&j);  
        // i, n and sum were not flushed  
        //   because they were not accessible in f1  
        // j was flushed because it was accessible  
        sum += j;  
        f2(&j);  
        // i, n, and sum were not flushed  
        //   because they were not accessible in f2  
        // j was flushed because it was accessible  
        sum += i + j + *p + n;  
    }  
    return sum;  
}  
  
int main()  
{  
}  
```