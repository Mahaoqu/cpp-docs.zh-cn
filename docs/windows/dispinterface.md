---
title: "调度接口 |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-windows
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords: vc-attr.dispinterface
dev_langs: C++
helpviewer_keywords: dispinterface attribute
ms.assetid: 61c5a4a1-ae92-47e9-8ee4-f847be90172b
caps.latest.revision: "9"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.openlocfilehash: e52d050b9b05ccb72969c531297367e729c258b1
ms.sourcegitcommit: ebec1d449f2bd98aa851667c2bfeb7e27ce657b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="dispinterface"></a>dispinterface
将一个接口作为调度接口置于 .idl 文件中。  
  
## <a name="syntax"></a>语法  
  
```  
  
[dispinterface]  
  
```  
  
## <a name="remarks"></a>备注  
 当 **dispinterface** C++ 属性优先于接口时，会导致将接口置于生成的 .idl 文件中的 library 块中。  
  
 除非指定基类，否则调度接口派生自 `IDispatch`。 必须为调度接口的成员指定 [id](../windows/id.md) 。  
  
 MIDL 文档中的 [dispinterface](http://msdn.microsoft.com/library/windows/desktop/aa366802) 用法示例：  
  
```  
dispinterface helloPro   
   { interface hello; };   
```  
  
 对于 **dispinterface** 属性无效。  
  
## <a name="example"></a>示例  
 有关如何使用 [dispinterface](../windows/bindable.md) 的示例，请参阅 **bindable**示例。  
  
## <a name="requirements"></a>要求  
  
### <a name="attribute-context"></a>特性上下文  
  
|||  
|-|-|  
|**适用对象**|`interface`|  
|**可重复**|No|  
|**必需的特性**|无|  
|**无效的特性**|**dual**、 **object**、 **oleautomation**、 `local`、 **ms_union**|  
  
 有关详细信息，请参见 [特性上下文](../windows/attribute-contexts.md)。  
  
## <a name="see-also"></a>另请参阅  
 [IDL 特性](../windows/idl-attributes.md)   
 [按用法分的特性](../windows/attributes-by-usage.md)   
 [uuid](../windows/uuid-cpp-attributes.md)   
 [双](../windows/dual.md)   
 [自定义](../windows/custom-cpp.md)   
 [对象](../windows/object-cpp.md)   
 [__interface](../cpp/interface.md)   