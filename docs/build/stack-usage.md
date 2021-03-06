---
title: x64 堆栈使用
ms.date: 12/17/2018
ms.assetid: 383f0072-0438-489f-8829-cca89582408c
ms.openlocfilehash: b598c33fbdd56630ca3e5ef0da551f38a73baa26
ms.sourcegitcommit: c123cc76bb2b6c5cde6f4c425ece420ac733bf70
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81335534"
---
# <a name="x64-stack-usage"></a>x64 堆栈使用

所有超出 RSP 当前地址的内存都被视为易失性内存：操作系统或调试器可能会在用户调试会话或中断处理程序期间覆盖此内存。 因此，在尝试对堆栈帧读取或写入值之前，必须始终设置 RSP。

本节讨论局部变量的堆栈空间分配和 alloca  内部函数。

## <a name="stack-allocation"></a>堆栈分配

函数的 prolog 负责为局部变量、保存的寄存器、堆栈参数和寄存器参数分配堆栈空间。

参数区域始终位于堆栈底部（即使使用 `alloca`），以便它在任何函数调用期间都始终与返回地址相邻。 它至少包含四个条目，但始终包含足够的空间来保存任何可能被调用的函数所需的所有参数。 请注意，系统始终为寄存器参数分配空间，即使这些参数本身从不驻留在堆栈中；会向被调用方保证为其所有参数分配了空间。 寄存器参数需要主地址，因此，如果所调用的函数需要获取参数列表 (va_list) 或单独参数的地址，则可使用连续区域。 此区域还提供了一个方便位置，用于在 thunk 执行期间保存寄存器参数，并作为调试选项（例如，如果参数存储在 prolog 代码中的主地址处，则可以在调试期间轻松查找参数）。 即使所调用的函数的参数少于 4 个，这 4 个堆栈位置实际上由所调用的函数所拥有，并且可能会由所调用的函数用于除保存参数寄存器值之外的其他用途。  因此，调用方可能不会在函数调用中将信息保存在此堆栈区域中。

如果在函数中动态分配空间 (`alloca`)，则必须使用非易失性寄存器作为帧指针来标记堆栈固定部分的基址，并且必须在 prolog 中保存和初始化该寄存器。 请注意，使用 `alloca` 时，从同一个调用方对同一个被调用方进行的调用对于其寄存器参数可能具有不同的主地址。

堆栈将始终保持为 16 字节对齐，除非是在 prolog 中（例如，压入返回地址之后），以及除非在某类帧函数的[函数类型](#function-types)中指明。

下面是堆栈布局的一个示例，其中函数 A 调用非叶函数 B。函数 A 的 prolog 已在堆栈底部为 B 所需的所有寄存器和堆栈参数分配了空间。 此调用会压入返回地址，而 B 的 prolog 会为其局部变量、非易失性寄存器以及它调用函数所需的空间分配空间。 如果 B 使用 `alloca`，则在局部变量/非易失性寄存器保存区域与参数堆栈区域之间分配空间。

![AMD 转换示例](../build/media/vcamd_conv_ex_5.png "AMD 转换示例")

当函数 B 调用另一个函数时，返回地址会压入到 RCX 的主地址正下方。

## <a name="dynamic-parameter-stack-area-construction"></a>动态参数堆栈区域构造

如果使用帧指针，则可选择动态创建参数堆栈区域。 当前在 x64 编译器中未实现此操作。

## <a name="function-types"></a>函数类型

主要有两种类型的函数。 需要堆栈帧的函数称为帧函数  。 不需要堆栈帧的函数称为叶函数  。

帧函数是分配堆栈空间、调用其他函数、保存非易失性寄存器或使用异常处理的函数。 它还需要函数表条目。 帧函数需要 prolog和 epilog。 帧函数可以动态分配堆栈空间，并可以使用帧指针。 帧函数可自行使用此调用标准的所有功能。

如果帧函数不调用另一个函数，则不需要使堆栈对齐（在[堆栈分配](#stack-allocation)一节中涉及）。

叶函数是不需要函数表条目的函数。 它无法更改任何非易失性寄存器（包括 RSP），这意味着它无法调用任何函数或分配堆栈空间。 它在执行时可以使堆栈保持未对齐状态。

## <a name="malloc-alignment"></a>malloc 对齐

[malloc](../c-runtime-library/reference/malloc.md) 保证返回适当对齐的内存，用于存储任何具有基本对齐以及可以适合所分配的内存量的对象。 基本对齐  是在没有对齐规范的情况下，小于或等于实现所支持的最大对齐的对齐。 （在 Visual C++ 中，这是 `double` 或 8 个字节所需的对齐。 在面向 64 位平台的代码中，是 16 个字节。）例如，四字节分配会在支持任何四字节或更小对象的边界上对齐。

Visual C++ 允许使用具有扩展对齐  的类型，这些类型也称为过度对齐  类型。 例如，SSE 类型 [__m128](../cpp/m128.md) 和 `__m256` 以及使用 `__declspec(align( n ))`（其中 `n` 大于 8）声明的类型具有扩展对齐。 `malloc` 不保证内存在适合于需要扩展对齐的对象的边界上对齐。 若要为过度对齐类型分配内存，请使用 [_aligned_malloc](../c-runtime-library/reference/aligned-malloc.md) 和相关函数。

## <a name="alloca"></a>alloca

[_alloca](../c-runtime-library/reference/alloca.md) 需要是 16 字节对齐，此外需要使用帧指针。

分配的堆栈需要在它后面包含用于后续调用函数的参数的空间，如[堆栈分配](#stack-allocation)中所述。

## <a name="see-also"></a>请参阅

[x64 软件约定](../build/x64-software-conventions.md)<br/>
[align](../cpp/align-cpp.md)<br/>
[__declspec](../cpp/declspec.md)
