{==+==}
# Union types

A *union type* is a nominal, heterogeneous C-like union, denoted by the name of
a [`union` item][item].

Unions have no notion of an "active field". Instead, every union access
transmutes parts of the content of the union to the type of the accessed field.
Since transmutes can cause unexpected or undefined behaviour, `unsafe` is
required to read from a union field. Union field types are also restricted to a
subset of types which ensures that they never need dropping. See the [item]
documentation for further details.

The memory layout of a `union` is undefined by default (in particular, fields do
*not* have to be at offset 0), but the `#[repr(...)]` attribute can be used to
fix a layout.
{==+==}
# 联合类型

*联合类型* 是具名的、异构的类似 C 的联合体，由 [`union` 条目][item] 的名称来表示。

联合体没有 "活动字段" 的概念。每次访问联合都会将联合内容的一部分转换为访问字段的类型。
由于转换可能导致意外错误或未定义的行为，因此读取联合字段时，需要使用 `unsafe` 关键字。
联合体字段类型子集受到限制，以确保永远不需要丢弃。有关详细信息，请参见 [条目][item] 文档。

默认情况下， `union` 的内存布局未明确的 (特别是，字段不需要位于偏移量为 0 的地址) ，可以使用 `#[repr(...)]` 属性来固定布局。
{==+==}


{==+==}
[`Copy`]: ../special-types-and-traits.md#copy
[`ManuallyDrop`]: ../../std/mem/struct.ManuallyDrop.html
[item]: ../items/unions.md
{==+==}

{==+==}