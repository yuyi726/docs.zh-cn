---
title: 表达式是一个值，因此不能作为赋值目标
ms.date: 07/20/2015
f1_keywords:
- bc30068
- vbc30068
helpviewer_keywords:
- BC30068
ms.assetid: d65141e1-f31e-4ac5-a3b8-0b2e02a71ebf
ms.openlocfilehash: d5aae4d30abbf9ed2af260412352a5e0452e0dcc
ms.sourcegitcommit: 463f3f050cecc0b6403e67f19a61f870fb8e7b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68513038"
---
# <a name="expression-is-a-value-and-therefore-cannot-be-the-target-of-an-assignment"></a>表达式是一个值，因此不能作为赋值目标

语句尝试向表达式赋值。 可以在运行时将值仅分配给可写的变量、属性或数组元素。 下面的示例说明了如何发生此错误。

```vb
Dim yesterday As Integer
ReadOnly maximum As Integer = 45
yesterday + 1 = DatePart(DateInterval.Day, Now)
' The preceding line is an ERROR because of an expression on the left.
maximum = 50
' The preceding line is an ERROR because maximum is declared ReadOnly.
```

类似的示例可能适用于属性和数组元素。

**间接访问。** 通过值类型间接访问还会生成此错误。 请看下面的代码示例, 它尝试<xref:System.Drawing.Point>通过将值通过<xref:System.Windows.Forms.Control.Location%2A>间接访问来设置的值。

```vb
' Assume this code runs inside Form1.
Dim exitButton As New System.Windows.Forms.Button()
exitButton.Text = "Exit this form"
exitButton.Location.X = 140
' The preceding line is an ERROR because of no storage for Location.
```

前面的示例的最后一条语句失败, 因为它只为<xref:System.Drawing.Point> <xref:System.Windows.Forms.Control.Location%2A>属性返回的结构创建临时分配。 结构是一种值类型, 并且在运行语句后不会保留临时结构。 此问题的解决方法是: 声明和使用的<xref:System.Windows.Forms.Control.Location%2A>变量, 这会为<xref:System.Drawing.Point>结构创建更永久的分配。 下面的示例演示可以替换前面示例的最后一条语句的代码。

```vb
Dim exitLocation as New System.Drawing.Point(140, exitButton.Location.Y)
exitButton.Location = exitLocation
```

**错误 ID:** BC30068

## <a name="to-correct-this-error"></a>更正此错误

- 如果语句将值分配给表达式, 请将表达式替换为单个可写的变量、属性或数组元素。

- 如果语句通过值类型 (通常是结构) 进行间接访问, 则创建一个变量来保存值类型。

- 将适当的结构 (或其他值类型) 分配给该变量。

- 使用变量访问属性, 为其赋值。

## <a name="see-also"></a>请参阅

- [运算符和表达式](../../../visual-basic/programming-guide/language-features/operators-and-expressions/index.md)
- [语句](../../../visual-basic/programming-guide/language-features/statements.md)
- [过程疑难解答](../../../visual-basic/programming-guide/language-features/procedures/troubleshooting-procedures.md)
