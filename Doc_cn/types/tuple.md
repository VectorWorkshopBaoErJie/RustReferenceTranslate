{==+==}
# Tuple types
{==+==}
# 元组类型
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TupleType_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` `)`\
> &nbsp;&nbsp; | `(` ( [_Type_] `,` )<sup>+</sup> [_Type_]<sup>?</sup> `)`
{==+==}

{==+==}


{==+==}
*Tuple types* are a family of structural types[^1] for heterogeneous lists of other types.

The syntax for a tuple type is a parenthesized, comma-separated list of types.
1-ary tuples require a comma after their element type to be disambiguated with a [parenthesized type].

A tuple type has a number of fields equal to the length of the list of types.
This number of fields determines the *arity* of the tuple.
A tuple with `n` fields is called an *n-ary tuple*.
For example, a tuple with 2 fields is a 2-ary tuple.

Fields of tuples are named using increasing numeric names matching their position in the list of types.
The first field is `0`.
The second field is `1`.
And so on.
The type of each field is the type of the same position in the tuple's list of types.

For convenience and historical reasons, the tuple type with no fields (`()`) is often called *unit* or *the unit type*.
Its one value is also called *unit* or *the unit value*.
{==+==}
*元组类型* 是一组由其他类型组成的异构列表的结构体类型 [^1] 。

元组类型的语法是由逗号分隔的类型列表，括在圆括号中。一元元组需要在其元素类型后面加上逗号，以与 [括号类型][parenthesized type] 加以区分。

元组类型的字段数量等于类型列表的长度。这个字段数量决定了元组的 *元数* 。拥有 `n` 个字段的元组被称为 *n 元元组*。

例如，具有 2 个字段的元组是一个 2 元元组。元组的字段使用递增的数字名称进行命名，匹配它们在类型列表中的位置。
第一个字段是 `0` ，第二个字段是 `1` ，以此类推。每个字段的类型是元组列表中相同位置的类型。

出于方便和历史原因，没有字段的元组类型 `()` 通常被称为 *单元* 或 *单元类型* 。它的一个值也称为 *单元* 或 *单元值*。
{==+==}


{==+==}
Some examples of tuple types:

* `()` (unit)
* `(f64, f64)`
* `(String, i32)`
* `(i32, String)` (different type from the previous example)
* `(i32, f64, Vec<String>, Option<bool>)`

Values of this type are constructed using a [tuple expression].
Furthermore, various expressions will produce the unit value if there is no other meaningful value for it to evaluate to.
Tuple fields can be accessed by either a [tuple index expression] or [pattern matching].

[^1]: Structural types are always equivalent if their internal types are equivalent.
      For a nominal version of tuples, see [tuple structs].
{==+==}
一些元组类型的示例:

* `()` (单位类型)
* `(f64，f64)`
* `(String，i32)`
* `(i32，String)` (与前一个示例的类型不同)
* `(i32，f64，Vec<String>，Option<bool>)`

可以使用 [元组表达式][tuple expression] 构造此类型的值。此外，如果没有其他有意义的值可以评估，则各种表达式将产生单位值。元组字段可以通过 [tuple index expression] 或 [pattern matching] 来访问。

[^1]: 如果它们的内部类型等效，则结构类型始终是等效的。有关元组结构体的名义版本，请参见 [tuple structs] 。
{==+==}


{==+==}
[_Type_]: ../types.md#type-expressions
[parenthesized type]: ../types.md#parenthesized-types
[pattern matching]: ../patterns.md#tuple-patterns
[tuple expression]: ../expressions/tuple-expr.md#tuple-expressions
[tuple index expression]: ../expressions/tuple-expr.md#tuple-indexing-expressions
[tuple structs]: ./struct.md
{==+==}

{==+==}