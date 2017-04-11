---
title: "C# 类型和变量 | C# 语言介绍"
description: "了解如何在 C 中定义类型和声明变量#"
keywords: ".NET, C#, 类型, 引用类型, 值类型"
author: BillWagner
ms.author: wiwagn
ms.date: 08/10/2016
ms.topic: article
ms.prod: .net
ms.technology: devlang-csharp
ms.devlang: csharp
ms.assetid: f8a8051e-0049-43f1-b594-9c84cc7b1224
translationtype: Human Translation
ms.sourcegitcommit: a06bd2a17f1d6c7308fa6337c866c1ca2e7281c0
ms.openlocfilehash: 93d09003ea4c54e6851ce00bbc4edecf039d0324
ms.lasthandoff: 03/13/2017

---

# <a name="types-and-variables"></a>类型和变量

C# 有两种类型：*值类型*和*引用类型*。 值类型的变量直接包含数据，而引用类型的变量则存储对数据（称为“对象”）的引用。 借助引用类型，两个变量可以引用同一对象；因此，对一个变量执行的运算可能会影响另一个变量引用的对象。 借助值类型，每个变量都有自己的数据副本；因此，对一个变量执行的运算不会影响另一个变量（`ref` 和 `out` 参数变量除外）。

C# 值类型又细分为*简单类型*、*枚举类型*、*结构类型*和*可以为 null 的值类型*。 C# 引用类型又细分为*类类型*、*接口类型*、*数组类型*和*委托类型*。

下面概述了 C# 的类型系统。

* 值类型
    - 简单类型
        * 有符号的整型：`sbyte`、`short`、`int`、`long`
        * 无符号的整型：`byte`、`ushort`、`uint`、`ulong`
        * Unicode 字符：`char`
        * IEEE 浮点：`float`、`double`
        * 高精度小数：`decimal`
        * 布尔：`bool`
    - 枚举类型
        * 格式为 `enum E {...}` 的用户定义类型
    - 结构类型
        * 格式为 `struct S {...}` 的用户定义类型
    - 可以为 null 的值类型
        * 值为 `null` 的其他所有值类型的扩展
* 引用类型
    - 类类型
        * 其他所有类型的最终基类：`object`
        * Unicode 字符串：`string`
        * 格式为 `class C {...}` 的用户定义类型
    - 接口类型
        * 格式为 `interface I {...}` 的用户定义类型
    - 数组类型
        * 一维和多维，例如 `int[]` 和 `int[,]`
    - 委托类型
        * 格式为 `delegate int D(...)` 的用户定义类型

八个整型类型支持带符号或不带符号格式的 8 位、16 位、32 位和 64 位值。

两个浮点类型（`float` 和 `double`）分别使用 32 位单精度和 64 位双精度 IEC-60559 格式表示。

`decimal` 类型是适用于财务和货币计算的 128 位数据类型。

C# 的 `bool` 类型用于表示布尔值（`true` 或 `false`）。

C# 使用 Unicode 编码处理字符和字符串。 `char` 类型表示 UTF-16 代码单元，`string` 类型表示一系列 UTF-16 代码单元。

下面总结了 C# 的数值类型。

* 有符号的整型
    - `sbyte`：8 位，介于 -128 到 127 之间
    - `short`：16 位，介于 -32,768 到 32,767 之间
    - `int`：32 位，介于 -2,147,483,648 到 2,147,483,647 之间
    - `long`：64 位，介于 -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 之间
* 无符号的整型
    - `byte`：8 位，介于 0 到 255 之间
    - `ushort`：16 位，介于 0 到 65,535 之间
    - `uint`：32 位，介于 0 到 4,294,967,295 之间
    - `ulong`：64 位，介于 0 到 18,446,744,073,709,551,615 之间
* 浮点
    - `float`：32 位，介于 1.5 × 10<sup>-45</sup> 到 3.4 × 10<sup>38</sup> 之间，7 位精度
    - `double`：64 位，介于 5.0 × 10<sup>-324</sup> 到 1.7 × 10<sup>308</sup> 之间，15 位精度
* Decimal
    - `decimal`：128 位，至少介于 -7.9 × 10<sup>-28</sup> 到 7.9 × 10<sup>28</sup> 之间，至少为 28 位精度
    
C# 程序使用*类型声明*创建新类型。 类型声明指定新类型的名称和成员。 用户可定义以下五种 C# 类型：类类型、结构类型、接口类型、枚举类型和委托类型。

`class` 类型定义包含数据成员（字段）和函数成员（方法、属性及其他）的数据结构。 类类型支持单一继承和多形性，即派生类可以扩展和专门针对基类的机制。

`struct` 类型定义包含数据成员和函数成员的结构，这一点与类类型相似。 不过，与类不同的是，结构是值类型，通常不需要进行堆分配。 结构类型不支持用户指定的继承，并且所有结构类型均隐式继承自类型 `object`。

`interface` 类型将协定定义为一组已命名的公共函数成员。 实现 `interface` 的 `class` 或 `struct` 必须提供接口函数成员的实现代码。 `interface` 可以继承自多个基接口，`class` 和 `struct` 可以实现多个接口。

`delegate` 类型表示引用包含特定参数列表和返回类型的方法。 通过委托，可以将方法视为可分配给变量并可作为参数传递的实体。 委托类同于函数式语言提供的函数类型。 委托也类似于其他一些语言中的函数指针概念，但与函数指针不同的是，委托不仅面向对象，还类型安全。

class、`struct`、`interface` 和 `delegate` 类型全部都支持泛型，因此可以使用其他类型对它们进行参数化。

`enum` 类型是一种包含已命名常量的独特类型。 每个 `enum` 类型都有一个基础类型（必须是八种整型类型之一）。 `enum` 类型的值集与基础类型的值集相同。

C# 支持任意类型的一维和多维数组。 与上述类型不同，数组类型无需先声明即可使用。 相反，数组类型是通过在类型名称后面添加方括号构造而成。 例如，`int[]` 是 `int` 类型的一维数组，`int[,]` 是 `int` 类型的二维数组，`int[][]` 是由 `int` 类型的一维数组构成的一维数组。

可以为 null 的值类型也无需先声明即可使用。 对于所有不可以为 null 的值类型 `T`，都有对应的可以为 null 的值类型 `T?`，后者可以包含附加值 `null`。 例如，`int?` 是可以包含任何 32 位整数或值 `null` 的类型。

C# 采用统一的类型系统，因此任意类型的值都可视为 `object`。 每种 C# 类型都直接或间接地派生自 `object` 类类型，而 `object` 是所有类型的最终基类。 只需将值视为类型 `object`，即可将引用类型的值视为对象。 通过执行*装箱*和*取消装箱操作*，可以将值类型的值视为对象。 在以下示例中，`int` 值被转换成 `object`，然后又恢复成 `int`。

[!code-csharp[装箱](../../../samples/snippets/csharp/tour/types-and-variables/Program.cs#L1-L10)]

当值类型的值被转换成类型 `object` 时，将分配 `object` 实例（亦称为“箱”）来包含值，然后将值复制到相应的箱中。 相反，当 `object` 引用被显式转换成值类型时，将检查引用的 `object` 是否是具有正确值类型的箱；如果检查成功，则会将箱中的值复制出来。

C# 统一的类型系统实际上意味着可以“按需”将值类型转换成对象。 鉴于这种统一性，使用类型 `object` 的常规用途库可以与引用类型和值类型结合使用。

C# 有多种*变量*，其中包括字段、数组元素、局部变量和参数。 变量表示存储位置，每个变量都具有一种类型，用于确定可以在变量中存储哪些值，如下文所述。

* 不可以为 null 的值类型
    - 具有精确类型的值
* 可以为 null 的值类型
    - `null` 值或具有精确类型的值
* object
    - `null` 引用、对任意引用类型的对象的引用，或对任意值类型的装箱值的引用
* 类类型
    - `null` 引用、对类类型实例的引用，或对派生自类类型的类实例的引用
* 接口类型
    - `null` 引用、对实现接口类型的类类型实例的引用，或对实现接口类型的值类型的装箱值的引用
* 数组类型
    - `null` 引用、对数组类型实例的引用，或对兼容的数组类型实例的引用
* 委托类型
    - `null` 引用或对兼容的委托类型实例的引用

>[!div class="step-by-step"]
[上一页](program-structure.md)
[下一页](expressions.md)
