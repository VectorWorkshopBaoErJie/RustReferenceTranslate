{==+==}
# Tuple and tuple indexing expressions
{==+==}
# 元组和元组索引表达式
{==+==}


{==+==}
## Tuple expressions
{==+==}
## 元组达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TupleExpression_ :\
> &nbsp;&nbsp; `(` _TupleElements_<sup>?</sup> `)`
>
> _TupleElements_ :\
> &nbsp;&nbsp; ( [_Expression_] `,` )<sup>+</sup> [_Expression_]<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _元组表达式_ :\
> &nbsp;&nbsp; `(` _元组元素组_<sup>?</sup> `)`
>
> _元组元素组_ :\
> &nbsp;&nbsp; ( [_表达式_][_Expression_] `,` )<sup>+</sup> [_表达式_][_Expression_]<sup>?</sup>
{==+==}


{==+==}
A *tuple expression* constructs [tuple values][tuple type].

The syntax for tuple expressions is a parenthesized, comma separated list of expressions, called the *tuple initializer operands*.
1-ary tuple expressions require a comma after their tuple initializer operand to be disambiguated with a [parenthetical expression].

Tuple expressions are a [value expression] that evaluate into a newly constructed value of a tuple type.
The number of tuple initializer operands is the arity of the constructed tuple.
Tuple expressions without any tuple initializer operands produce the unit tuple.
For other tuple expressions, the first written tuple initializer operand initializes the field `0` and subsequent operands initializes the next highest field.
For example, in the tuple expression `('a', 'b', 'c')`, `'a'` initializes the value of the field `0`, `'b'` field `1`, and `'c'` field `2`.

Examples of tuple expressions and their types:
{==+==}
 *元组表达式* 构造了 [元组值][tuple type] 。

元组表达式的语法是括号包围的、用逗号分隔的表达式列表，称为 *元组初始化操作数* 。
一元组表达式需要在其元组初始化操作数后面加上逗号，以与 [圆括号表达式][parenthetical expression] 区分。

元组表达式是 [值表达式][value expression] ，它的结果是一个新构造的元组类型的值。
元组初始化操作数的数量是构造的元组的元的数量。
没有元组初始化操作数的元组表达式会产生一个单元元组。
对于其他元组表达式，第一个写入的元组初始化操作数初始化字段 `0` ，随后的操作数初始化下一个最高的字段。
例如，在元组表达式 `('a', 'b', 'c')` 中， `'a'` 初始化字段 `0` 的值， `'b'` 初始化字段 `1` 的值， `'c'` 初始化字段 `2` 的值。

以下是一些元组表达式及其类型的示例:
{==+==}


{==+==}
| Expression           | Type         |
| -------------------- | ------------ |
| `()`                 | `()` (unit)  |
| `(0.0, 4.5)`         | `(f64, f64)` |
| `("x".to_string(), )` | `(String, )`  |
| `("a", 4usize, true)`| `(&'static str, usize, bool)` |
{==+==}
| 表达式           | 类型         |
| -------------------- | ------------ |
| `()`                 | `()` (单元)  |
| `(0.0, 4.5)`         | `(f64, f64)` |
| `("x".to_string(), )` | `(String, )`  |
| `("a", 4usize, true)`| `(&'static str, usize, bool)` |
{==+==}


{==+==}
## Tuple indexing expressions
{==+==}
## 元组索引表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TupleIndexingExpression_ :\
> &nbsp;&nbsp; [_Expression_] `.` [TUPLE_INDEX]
{==+==}
> **<sup>语法</sup>**\
> _元组索引表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `.` [元组索引][TUPLE_INDEX]
{==+==}


{==+==}
A *tuple indexing expression* accesses fields of [tuples][tuple type] and [tuple structs][tuple struct].

The syntax for a tuple index expression is an expression, called the *tuple operand*, then a `.`, then finally a tuple index.
The syntax for the *tuple index* is a [decimal literal] with no leading zeros, underscores, or suffix.
For example `0` and `2` are valid tuple indices but not `01`, `0_`, nor `0i32`.

The type of the tuple operand must be a [tuple type] or a [tuple struct].
The tuple index must be a name of a field of the type of the tuple operand.

Evaluation of tuple index expressions has no side effects beyond evaluation of its tuple operand.
As a [place expression], it evaluates to the location of the field of the tuple operand with the same name as the tuple index.

Examples of tuple indexing expressions:
{==+==}
*元组索引表达式* 用于访问 [元组][tuple type] 和 [元组结构体][tuple struct] 中的字段。

元组索引表达式的语法是一个表达式，即 *元组操作数* ，后跟一个点号 ( `.` ) ，最后是一个元组索引。
元组索引的语法是一个 [十进制字面值][decimal literal] ，没有前导零、下划线或后缀。
例如，`0` 和 `2` 是有效的元组索引，但  `01` 、 `0_` 和 `0i32` 无效。

元组操作的类型必须是 [元组类型][tuple type] 或 [元组结构体][tuple struct] 的一种。
元组索引必须是元组操作类型中与之同名的字段。

求值元组索引表达式除了求值其元组操作之外没有任何副作用。
作为 [占位表达式][place expression] ，它将求值元组操作中与元组索引同名的字段的位置。

元组索引表达式的示例如下:
{==+==}


{==+==}
```rust
// Indexing a tuple
let pair = ("a string", 2);
assert_eq!(pair.1, 2);

// Indexing a tuple struct
# struct Point(f32, f32);
let point = Point(1.0, 0.0);
assert_eq!(point.0, 1.0);
assert_eq!(point.1, 0.0);
```
{==+==}
```rust
// 对元组进行检索
let pair = ("a string", 2);
assert_eq!(pair.1, 2);

// 对元组结构体进行检索
# struct Point(f32, f32);
let point = Point(1.0, 0.0);
assert_eq!(point.0, 1.0);
assert_eq!(point.1, 0.0);
```
{==+==}


{==+==}
> **Note**: Unlike field access expressions, tuple index expressions can be the function operand of a [call expression] as it cannot be confused with a method call since method names cannot be numbers.

> **Note**: Although arrays and slices also have elements, you must use an [array or slice indexing expression] or a [slice pattern] to access their elements.
{==+==}
> **注意**: 与字段访问表达式不同，元组索引表达式可以作为 [调用表达式][call expression] 的函数操作，因为它不能与方法调用混淆，因为方法名称不能是数字。

> **注意**: 尽管数组和切片也有元素，但必须使用 [数组或切片索引表达式][array or slice indexing expression] 或 [切片模式][slice pattern] 来访问它们的元素。
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[array or slice indexing expression]: array-expr.md#array-and-slice-indexing-expressions
[call expression]: ./call-expr.md
[decimal literal]: ../tokens.md#integer-literals
[field access expressions]: ./field-expr.html#field-access-expressions
[operands]: ../expressions.md
[parenthetical expression]: grouped-expr.md
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[slice pattern]: ../patterns.md#slice-patterns
[tuple type]: ../types/tuple.md
[tuple struct]: ../types/struct.md
[TUPLE_INDEX]: ../tokens.md#tuple-index
[value expression]: ../expressions.md#place-expressions-and-value-expressions
{==+==}

{==+==}