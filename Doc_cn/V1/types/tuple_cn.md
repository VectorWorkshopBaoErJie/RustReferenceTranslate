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
> **<sup>语法</sup>**\
> _元组类型_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` `)`\
> &nbsp;&nbsp; | `(` ( [_类型_][_Type_] `,` )<sup>+</sup> [_类型_][_Type_]<sup>?</sup> `)`
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
*元组类型* 是一组由其他异构类型组成的结构体类型 [^1] 。

元组类型的语法是由逗号分隔的类型列表，括在圆括号之中。
一元元组需要在其元素类型后面加上逗号，以便与 [括号类型][parenthesized type] 区分。

元组类型的字段数量等于类型列表的长度。字段数量决定了元组的 *元数* 。拥有 `n` 个字段的元组被称为 *n 元元组*。

例如，具有 2 个字段的元组是一个 2 元元组。元组的字段使用递增的数字名称进行命名，匹配它们在类型列表中的位置。
第一个字段是 `0` ，第二个字段是 `1` ，以此类推。每个字段的类型是元组列表中相同位置的类型。

出于便捷和历史原因，没有字段的元组类型 `()` 通常被称为 *单元* 或 *单元类型* ，其值称为 *单元* 或 *单元值*。
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
元组类型的一些例子:

* `()` (单元类型)
* `(f64，f64)`
* `(String，i32)`
* `(i32，String)` (与前一个示例的类型不同)
* `(i32，f64，Vec<String>，Option<bool>)`

可以使用 [元组表达式][tuple expression] 构造该类型的值。
另外，则各类表达式在不能求解为有意义的值时，将产生单元值。
可以通过 [元组索引表达式][tuple index expression] 或 [模式匹配][pattern matching] 访问元组字段。

[^1]: 如果其内部类型等价，则构造类型始终是等价的。有关元组结构体的具名版本，请参见 [元组结构体][tuple structs] 。
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