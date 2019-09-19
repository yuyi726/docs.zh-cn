---
title: 如何：创建未签名的友元程序集
ms.date: 08/19/2019
ms.assetid: 78cbc4f0-b021-4141-a4ff-eb4edbd814ca
dev_langs:
- csharp
- vb
ms.openlocfilehash: 9d5699f772dba994b10408d15422faa3c5931f45
ms.sourcegitcommit: 005980b14629dfc193ff6cdc040800bc75e0a5a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70991681"
---
# <a name="how-to-create-unsigned-friend-assemblies"></a><span data-ttu-id="c3af5-102">如何：创建未签名的友元程序集</span><span class="sxs-lookup"><span data-stu-id="c3af5-102">How to: Create unsigned friend assemblies</span></span>

<span data-ttu-id="c3af5-103">本示例演示如何将友元程序集和未签名的程序集一起使用。</span><span class="sxs-lookup"><span data-stu-id="c3af5-103">This example shows how to use friend assemblies with assemblies that are unsigned.</span></span>

## <a name="create-an-assembly-and-a-friend-assembly"></a><span data-ttu-id="c3af5-104">创建程序集和友元程序集</span><span class="sxs-lookup"><span data-stu-id="c3af5-104">Create an assembly and a friend assembly</span></span>

1. <span data-ttu-id="c3af5-105">打开命令提示。</span><span class="sxs-lookup"><span data-stu-id="c3af5-105">Open a command prompt.</span></span>

2. <span data-ttu-id="c3af5-106">创建名为 friend_unsigned_A 的 C# 或 Visual Basic 文件，其中包含以下代码  。</span><span class="sxs-lookup"><span data-stu-id="c3af5-106">Create a C# or Visual Basic file named *friend_unsigned_A* that contains the following code.</span></span> <span data-ttu-id="c3af5-107">该代码使用 <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> 属性将 friend_unsigned_B 声明为友元程序集  。</span><span class="sxs-lookup"><span data-stu-id="c3af5-107">The code uses the <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> attribute to declare *friend_unsigned_B* as a friend assembly.</span></span>

   ```csharp
   // friend_unsigned_A.cs
   // Compile with:
   // csc /target:library friend_unsigned_A.cs
   using System.Runtime.CompilerServices;
   using System;

   [assembly: InternalsVisibleTo("friend_unsigned_B")]

   // Type is internal by default.
   class Class1
   {
       public void Test()
       {
           Console.WriteLine("Class1.Test");
       }
   }

   // Public type with internal member.
   public class Class2
   {
       internal void Test()
       {
           Console.WriteLine("Class2.Test");
       }
   }
   ```

   ```vb
   ' friend_unsigned_A.vb
   ' Compile with:
   ' vbc -target:library friend_unsigned_A.vb
   Imports System.Runtime.CompilerServices
   Imports System

   <Assembly: InternalsVisibleTo("friend_unsigned_B")>

   ' Friend type.
   Friend Class Class1
       Public Sub Test()
           Console.WriteLine("Class1.Test")
       End Sub
   End Class

   ' Public type with Friend member.
   Public Class Class2
       Friend Sub Test()
           Console.WriteLine("Class2.Test")
       End Sub
   End Class
   ```

3. <span data-ttu-id="c3af5-108">使用以下命令编译 friend_unsigned_A 并为其签名  ：</span><span class="sxs-lookup"><span data-stu-id="c3af5-108">Compile and sign *friend_unsigned_A* by using the following command:</span></span>

   ```csharp
   csc /target:library friend_unsigned_A.cs
   ```

   ```vb
   vbc -target:library friend_unsigned_A.vb
   ```

4. <span data-ttu-id="c3af5-109">创建名为 friend_unsigned_B 的 C# 或 Visual Basic 文件，其中包含以下代码  。</span><span class="sxs-lookup"><span data-stu-id="c3af5-109">Create a C# or Visual Basic file named *friend_unsigned_B* that contains the following code.</span></span> <span data-ttu-id="c3af5-110">由于 friend_unsigned_A 将 friend_unsigned_B 指定为友元程序集，因此 friend_unsigned_B 中的代码可以访问 `internal` (C#) 或 friend_unsigned_A 中的 `Friend` (Visual Basic) 类型和成员     。</span><span class="sxs-lookup"><span data-stu-id="c3af5-110">Because *friend_unsigned_A* specifies *friend_unsigned_B* as a friend assembly, the code in *friend_unsigned_B* can access `internal` (C#) or `Friend` (Visual Basic) types and members from *friend_unsigned_A*.</span></span>

   ```csharp
   // friend_unsigned_B.cs
   // Compile with:
   // csc /r:friend_unsigned_A.dll /out:friend_unsigned_B.exe friend_unsigned_B.cs
   public class Program
   {
       static void Main()
       {
           // Access an internal type.
           Class1 inst1 = new Class1();
           inst1.Test();

           Class2 inst2 = new Class2();
           // Access an internal member of a public type.
           inst2.Test();

           System.Console.ReadLine();
       }
   }
   ```

   ```vb
   ' friend_unsigned_B.vb
   ' Compile with:
   ' vbc -r:friend_unsigned_A.dll friend_unsigned_B.vb
   Module Module1
       Sub Main()
           ' Access a Friend type.
           Dim inst1 As New Class1()
           inst1.Test()

           Dim inst2 As New Class2()
           ' Access a Friend member of a public type.
           inst2.Test()

           System.Console.ReadLine()
       End Sub
   End Module
   ```

5. <span data-ttu-id="c3af5-111">使用以下命令编译 friend_unsigned_B  。</span><span class="sxs-lookup"><span data-stu-id="c3af5-111">Compile *friend_unsigned_B* by using the following command.</span></span>

   ```csharp
   csc /r:friend_unsigned_A.dll /out:friend_unsigned_B.exe friend_unsigned_B.cs
   ```

   ```vb
   vbc -r:friend_unsigned_A.dll friend_unsigned_B.vb
   ```

   <span data-ttu-id="c3af5-112">编译器生成的程序集的名称必须与传递给 <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> 属性的友元程序集的名称匹配。</span><span class="sxs-lookup"><span data-stu-id="c3af5-112">The name of the assembly that is generated by the compiler must match the friend assembly name that is passed to the <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> attribute.</span></span> <span data-ttu-id="c3af5-113">必须使用 `/out` 编译器选项显式指定输出程序集（.exe 或 .dll）的名称   。</span><span class="sxs-lookup"><span data-stu-id="c3af5-113">You must explicitly specify the name of the output assembly (*.exe* or *.dll*) by using the `/out` compiler option.</span></span> <span data-ttu-id="c3af5-114">有关详细信息，请参阅 [/out（C# 编译器选项）](../../csharp/language-reference/compiler-options/out-compiler-option.md)或 [out (Visual Basic)](../../visual-basic/reference/command-line-compiler/out.md)。</span><span class="sxs-lookup"><span data-stu-id="c3af5-114">For more information, see [/out (C# compiler options)](../../csharp/language-reference/compiler-options/out-compiler-option.md) or [-out (Visual Basic)](../../visual-basic/reference/command-line-compiler/out.md)..</span></span>

6. <span data-ttu-id="c3af5-115">运行 friend_unsigned_B.exe 文件  。</span><span class="sxs-lookup"><span data-stu-id="c3af5-115">Run the *friend_unsigned_B.exe* file.</span></span>

   <span data-ttu-id="c3af5-116">该程序输出两个字符串：“Class1.Test”和“Class2.Test”   。</span><span class="sxs-lookup"><span data-stu-id="c3af5-116">The program outputs two strings: **Class1.Test** and **Class2.Test**.</span></span>

## <a name="net-security"></a><span data-ttu-id="c3af5-117">.NET 安全性</span><span class="sxs-lookup"><span data-stu-id="c3af5-117">.NET security</span></span>

<span data-ttu-id="c3af5-118"><xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> 属性和 <xref:System.Security.Permissions.StrongNameIdentityPermission> 类之间具有相似之处。</span><span class="sxs-lookup"><span data-stu-id="c3af5-118">There are similarities between the <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> attribute and the <xref:System.Security.Permissions.StrongNameIdentityPermission> class.</span></span> <span data-ttu-id="c3af5-119">主要区别是，<xref:System.Security.Permissions.StrongNameIdentityPermission> 可以要求安全权限来运行一段特定代码，而 <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> 属性控制 `internal` 和 `Friend` (Visual Basic) 类型和成员的可见性。</span><span class="sxs-lookup"><span data-stu-id="c3af5-119">The main difference is that <xref:System.Security.Permissions.StrongNameIdentityPermission> can demand security permissions to run a particular section of code, whereas the <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute> attribute controls the visibility of `internal`  or `Friend` (Visual Basic) types and members.</span></span>

## <a name="see-also"></a><span data-ttu-id="c3af5-120">请参阅</span><span class="sxs-lookup"><span data-stu-id="c3af5-120">See also</span></span>

- <xref:System.Runtime.CompilerServices.InternalsVisibleToAttribute>
- [<span data-ttu-id="c3af5-121">.NET 中的程序集</span><span class="sxs-lookup"><span data-stu-id="c3af5-121">Assemblies in .NET</span></span>](index.md)
- [<span data-ttu-id="c3af5-122">友元程序集</span><span class="sxs-lookup"><span data-stu-id="c3af5-122">Friend assemblies</span></span>](friend.md)
- [<span data-ttu-id="c3af5-123">如何：创建已签名的友元程序集</span><span class="sxs-lookup"><span data-stu-id="c3af5-123">How to: Create signed friend assemblies</span></span>](create-signed-friend.md)
- [<span data-ttu-id="c3af5-124">C# 编程指南</span><span class="sxs-lookup"><span data-stu-id="c3af5-124">C# programming guide</span></span>](../../csharp/programming-guide/index.md)
- [<span data-ttu-id="c3af5-125">编程概念 (Visual Basic)</span><span class="sxs-lookup"><span data-stu-id="c3af5-125">Programming concepts (Visual Basic)</span></span>](../../visual-basic/programming-guide/concepts/index.md)