---
title: "如何： 为 ADO.NET 封送 ANSI 字符串 (C + + /cli CLI) |Microsoft 文档"
ms.custom: 
ms.date: 11/04/2016
ms.reviewer: 
ms.suite: 
ms.technology: cpp-windows
ms.tgt_pltfrm: 
ms.topic: get-started-article
dev_langs: C++
helpviewer_keywords:
- native strings [C++]
- ADO.NET [C++], marshaling ANSI strings
- strings [C++], ADO.NET
ms.assetid: 6759d5a2-515f-4079-856b-73b1c1e68f2d
caps.latest.revision: "11"
author: mikeblome
ms.author: mblome
manager: ghogen
ms.workload:
- cplusplus
- dotnet
ms.openlocfilehash: 91d97658436e2d5563c70765da5c3c98e1cbeed5
ms.sourcegitcommit: 8fa8fdf0fbb4f57950f1e8f4f9b81b4d39ec7d7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-marshal-ansi-strings-for-adonet-ccli"></a>如何：为 ADO.NET 封送 ANSI 字符串 (C++/CLI)
演示如何添加本机字符串 (`char *`) 到数据库，以及如何封送<xref:System.String?displayProperty=fullName>从数据库到本机的字符串。  
  
## <a name="example"></a>示例  
 在此示例中，类 DatabaseClass 创建与 ADO.NET 交互<xref:System.Data.DataTable>对象。 请注意，此类是本机 c + + `class` (相比`ref class`或`value class`)。 这是必需的因为我们想要使用此类从本机代码，并且不能在本机代码中使用托管的类型。 此类将编译为面向 CLR，是由`#pragma managed`类声明前面的指令。 此指令的详细信息，请参阅[managed、 unmanaged](../preprocessor/managed-unmanaged.md)。  
  
 请注意 DatabaseClass 类的私有成员： `gcroot<DataTable ^> table`。 本机类型不能包含托管的类型，因为`gcroot`关键字是必需的。 有关详细信息`gcroot`，请参阅[如何： 在本机类型中声明处理](../dotnet/how-to-declare-handles-in-native-types.md)。  
  
 此示例中的代码的其余部分是本机 c + + 代码中，是由`#pragma unmanaged`指令前面`main`。 在此示例中，我们要创建 DatabaseClass 的新实例和调用其方法来创建一个表并填充表中的一些行。 请注意，本机 c + + 字符串正在传送作为 StringCol 的数据库列的值。 在 DatabaseClass，这些字符串会封送到使用在中找到的封送处理功能的托管字符串<xref:System.Runtime.InteropServices?displayProperty=fullName>命名空间。 具体而言，该方法<xref:System.Runtime.InteropServices.Marshal.PtrToStringAnsi%2A>用于封送`char *`到<xref:System.String>，和方法<xref:System.Runtime.InteropServices.Marshal.StringToHGlobalAnsi%2A>用于封送<xref:System.String>到`char *`。  
  
> [!NOTE]
>  分配的内存<xref:System.Runtime.InteropServices.Marshal.StringToHGlobalAnsi%2A>必须通过调用释放<xref:System.Runtime.InteropServices.Marshal.FreeHGlobal%2A>或`GlobalFree`。  
  
```  
// adonet_marshal_string_native.cpp  
// compile with: /clr /FU System.dll /FU System.Data.dll /FU System.Xml.dll  
#include <comdef.h>  
#include <gcroot.h>  
#include <iostream>  
using namespace std;  
  
#using <System.Data.dll>  
using namespace System;  
using namespace System::Data;  
using namespace System::Runtime::InteropServices;  
  
#define MAXCOLS 100  
  
#pragma managed  
class DatabaseClass  
{  
public:  
    DatabaseClass() : table(nullptr) { }  
  
    void AddRow(char *stringColValue)  
    {  
        // Add a row to the table.  
        DataRow ^row = table->NewRow();  
        row["StringCol"] = Marshal::PtrToStringAnsi(  
            (IntPtr)stringColValue);  
        table->Rows->Add(row);  
    }  
  
    void CreateAndPopulateTable()  
    {  
        // Create a simple DataTable.  
        table = gcnew DataTable("SampleTable");  
  
        // Add a column of type String to the table.  
        DataColumn ^column1 = gcnew DataColumn("StringCol",  
            Type::GetType("System.String"));  
        table->Columns->Add(column1);  
    }  
  
    int GetValuesForColumn(char *dataColumn, char **values,  
        int valuesLength)  
    {  
        // Marshal the name of the column to a managed  
        // String.  
        String ^columnStr = Marshal::PtrToStringAnsi(  
                (IntPtr)dataColumn);  
  
        // Get all rows in the table.  
        array<DataRow ^> ^rows = table->Select();  
        int len = rows->Length;  
        len = (len > valuesLength) ? valuesLength : len;  
        for (int i = 0; i < len; i++)  
        {  
            // Marshal each column value from a managed string  
            // to a char *.  
            values[i] = (char *)Marshal::StringToHGlobalAnsi(  
                (String ^)rows[i][columnStr]).ToPointer();  
        }  
  
        return len;  
    }  
  
private:  
    // Using gcroot, you can use a managed type in  
    // a native class.  
    gcroot<DataTable ^> table;  
};  
  
#pragma unmanaged  
int main()  
{  
    // Create a table and add a few rows to it.  
    DatabaseClass *db = new DatabaseClass();  
    db->CreateAndPopulateTable();  
    db->AddRow("This is string 1.");  
    db->AddRow("This is string 2.");  
  
    // Now retrieve the rows and display their contents.  
    char *values[MAXCOLS];  
    int len = db->GetValuesForColumn(  
        "StringCol", values, MAXCOLS);  
    for (int i = 0; i < len; i++)  
    {  
        cout << "StringCol: " << values[i] << endl;  
  
        // Deallocate the memory allocated using  
        // Marshal::StringToHGlobalAnsi.  
        GlobalFree(values[i]);  
    }  
  
    delete db;  
  
    return 0;  
}  
```  
  
```Output  
StringCol: This is string 1.  
StringCol: This is string 2.  
```  
  
## <a name="compiling-the-code"></a>编译代码  
  
-   若要从命令行中编译代码，将代码示例保存在名为 adonet_marshal_string_native.cpp 的文件，然后输入以下语句：  
  
    ```  
    cl /clr /FU System.dll /FU System.Data.dll /FU System.Xml.dll adonet_marshal_string_native.cpp  
    ```  
  
## <a name="net-framework-security"></a>.NET Framework 安全性  
 有关涉及 ADO.NET 的安全问题的信息，请参阅[保护 ADO.NET 应用程序](/dotnet/framework/data/adonet/securing-ado-net-applications)。  
  
## <a name="see-also"></a>请参阅  
 <xref:System.Runtime.InteropServices>   
 [使用 ADO.NET 访问数据 (C + + /cli CLI)](../dotnet/data-access-using-adonet-cpp-cli.md)   
 [ADO.NET](/dotnet/framework/data/adonet/index)   
 [互操作性](http://msdn.microsoft.com/en-us/afcc2e7d-3f32-48d2-8141-1c42acf29084)   
 [本机和 .NET 的互操作性](../dotnet/native-and-dotnet-interoperability.md)