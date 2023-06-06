{==+==}
# Struct types
{==+==}
# 结构体类型
{==+==}


{==+==}
A `struct` *type* is a heterogeneous product of other types, called the
*fields* of the type.[^structtype]

New instances of a `struct` can be constructed with a [struct expression].

The memory layout of a `struct` is undefined by default to allow for compiler
optimizations like field reordering, but it can be fixed with the
[`repr` attribute]. In either case, fields may be given in any order in a
corresponding struct *expression*; the resulting `struct` value will always
have the same memory layout.

The fields of a `struct` may be qualified by [visibility modifiers], to allow
access to data in a struct outside a module.

A _tuple struct_ type is just like a struct type, except that the fields are
anonymous.

A _unit-like struct_ type is like a struct type, except that it has no fields.
The one value constructed by the associated [struct expression] is the only
value that inhabits such a type.

[^structtype]: `struct` types are analogous to `struct` types in C, the
    *record* types of the ML family, or the *struct* types of the Lisp family.
{==+==}
`struct` *结构体类型* 是其他异构类型的集合，称为类型 *字段* 。[^structtype]

使用 [结构体表达式][struct expression] 可以构造 `struct` 的新实例。

默认情况下， `struct` 的内存布局是未确定的，以允许编译器进行优化，例如字段重新排序，或者通过 [`repr`属性][`repr` attribute] 确定。
结构体 *表达式* 中的字段可以按任意顺序给出，所生成的 `struct` 值始终具有相同的内存布局。

 `struct` 的字段可以由 [可见性修饰符][visibility modifiers] 修饰，以允许在模块之外访问结构体中的数据。

_元组结构体_ 类型与结构体类型相同，但字段是匿名的。

 _类单元结构体_ 类型类似于结构体类型，但是没有字段。
与之关联的 [结构体表达式][struct expression] 所构造的值是该类型的唯一值。

[^structtype]: `struct` 类型类似于 C 中的 `struct` 类型， ML 系列的 *record* 类型或 Lisp 系列的 *struct* 类型。
{==+==}


{==+==}
[`repr` attribute]: ../type-layout.md#representations
[struct expression]: ../expressions/struct-expr.md
[visibility modifiers]: ../visibility-and-privacy.md
{==+==}

{==+==}