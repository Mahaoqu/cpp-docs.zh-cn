---
title: fsetpos
ms.date: 4/2/2020
api_name:
- fsetpos
- _o_fsetpos
api_location:
- msvcrt.dll
- msvcr80.dll
- msvcr90.dll
- msvcr100.dll
- msvcr100_clr0400.dll
- msvcr110.dll
- msvcr110_clr0400.dll
- msvcr120.dll
- msvcr120_clr0400.dll
- ucrtbase.dll
- api-ms-win-crt-stdio-l1-1-0.dll
- api-ms-win-crt-private-l1-1-0.dll
api_type:
- DLLExport
topic_type:
- apiref
f1_keywords:
- fsetpos
helpviewer_keywords:
- streams, setting position indicators
- fsetpos function
ms.assetid: 6d19ff48-1a2b-47b3-9f23-ed0a47b5a46e
ms.openlocfilehash: 8fa6ec1f37703ce51e0c9c565d766c56cf164322
ms.sourcegitcommit: 5a069c7360f75b7c1cf9d4550446ec2fa2eb2293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82910180"
---
# <a name="fsetpos"></a>fsetpos

设置流位置指示器。

## <a name="syntax"></a>语法

```C
int fsetpos(
   FILE *stream,
   const fpos_t *pos
);
```

### <a name="parameters"></a>参数

*流*<br/>
指向**文件**结构的指针。

*位置*<br/>
位置指示器存储。

## <a name="return-value"></a>返回值

如果成功， **fsetpos**将返回0。 如果失败，函数将返回一个非零值，并将**errno**设置为以下清单常量之一（在 errno 中定义。H）： **ebadf (**，这意味着文件不可访问，或者*流*指向的对象不是有效的文件结构;或**EINVAL**，这意味着传递的*流*或*pos*的值无效。 如果传入了无效参数，这些函数则会调用无效参数处理程序，如[参数验证](../../c-runtime-library/parameter-validation.md)中所述。

有关这些和其他返回代码的详细信息，请参阅[_doserrno、errno、_sys_errlist 和 _sys_nerr](../../c-runtime-library/errno-doserrno-sys-errlist-and-sys-nerr.md) 。

## <a name="remarks"></a>备注

**Fsetpos**函数将*流*的文件位置指示器设置为*pos*的值，此值是在先前对**fgetpos**的调用中获取*的。* 函数会清除文件尾指示器，并撤消[ungetc](ungetc-ungetwc.md)对*流*的任何影响。 在调用**fsetpos**之后，*对流*的下一个操作可能是输入或输出。

默认情况下，此函数的全局状态的作用域限定为应用程序。 若要更改此项，请参阅[CRT 中的全局状态](../global-state.md)。

## <a name="requirements"></a>要求

|函数|必需的标头|
|--------------|---------------------|
|**fsetpos**|\<stdio.h>|

有关其他兼容性信息，请参阅[兼容性](../../c-runtime-library/compatibility.md)。

## <a name="example"></a>示例

请参阅 [fgetpos](fgetpos.md) 的示例。

## <a name="see-also"></a>另请参阅

[流 I/O](../../c-runtime-library/stream-i-o.md)<br/>
[fgetpos](fgetpos.md)<br/>
