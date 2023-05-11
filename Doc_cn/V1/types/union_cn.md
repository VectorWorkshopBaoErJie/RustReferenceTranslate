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

*联合类型* 是一个名义上的、异构的类 C 联合，由 [`union` 条目][item] 的名称表示。


联合没有 "活动字段" 的概念。而且，每次访问联合都会将联合内容的一部分转换为访问字段的类型。
由于转换可能导致意外或未定义的行为，因此读取联合字段需要使用 `unsafe` 关键字。
联合字段类型也受到一组限制，以确保它们永远不需要丢弃。有关详细信息，请参见 [条目][item] 文档。

默认情况下， `union` 的内存布局未定义 (特别是，字段不需要位于偏移量 0 ) ，但是可以使用 `#[repr(...)]` 属性来修正布局。
{==+==}


{==+==}
[`Copy`]: ../special-types-and-traits.md#copy
[`ManuallyDrop`]: ../../std/mem/struct.ManuallyDrop.html
[item]: ../items/unions.md
{==+==}

{==+==}