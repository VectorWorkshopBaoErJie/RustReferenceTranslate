{==+==}
# Enumerated types

An *enumerated type* is a nominal, heterogeneous disjoint union type, denoted
by the name of an [`enum` item]. [^enumtype]

An [`enum` item] declares both the type and a number of *variants*, each of
which is independently named and has the syntax of a struct, tuple struct or
unit-like struct.

New instances of an `enum` can be constructed with a [struct expression].

Any `enum` value consumes as much memory as the largest variant for its
corresponding `enum` type, as well as the size needed to store a discriminant.

Enum types cannot be denoted *structurally* as types, but must be denoted by
named reference to an [`enum` item].

[^enumtype]: The `enum` type is analogous to a `data` constructor declaration in
             ML, or a *pick ADT* in Limbo.
{==+==}
# 枚举类型

*枚举类型* 是一种命名的、异构的不相交联合类型，由一个 [`enum` 条目][`enum` item] 的名称表示。[^enumtype]

[`enum` 条目][`enum` item] 声明了类型以及若干个 *变体* ，每个变体都有独立的名称，语法类似于结构体、元组结构体或单元结构体。

可以使用 [结构体表达式][struct expression] 来构造枚举的新实例。对于枚举类型，任何一个 `enum` 值消耗的内存都至少等于对应枚举类型中最大的变体的内存大小加上需要存储一个标识符的大小。

枚举类型无法通过类型来进行结构上的表示，而必须通过指向 [`enum` 条目][`enum` item] 的命名引用来表示。

[^enumtype]: `enum` 类型类似于 ML 中的 `data` 构造声明，或者 Limbo 中的 *pick ADT* 。
{==+==}


{==+==}
[`enum` item]: ../items/enumerations.md
[struct expression]: ../expressions/struct-expr.md
{==+==}

{==+==}