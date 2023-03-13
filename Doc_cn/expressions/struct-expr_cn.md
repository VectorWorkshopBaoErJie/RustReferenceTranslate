{==+==}
# Struct expressions
{==+==}
# 结构体表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _StructExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _StructExprStruct_\
> &nbsp;&nbsp; | _StructExprTuple_\
> &nbsp;&nbsp; | _StructExprUnit_
>
> _StructExprStruct_ :\
> &nbsp;&nbsp; [_PathInExpression_] `{` (_StructExprFields_ | _StructBase_)<sup>?</sup> `}`
>
> _StructExprFields_ :\
> &nbsp;&nbsp; _StructExprField_ (`,` _StructExprField_)<sup>\*</sup> (`,` _StructBase_ | `,`<sup>?</sup>)
>
> _StructExprField_ :\
> &nbsp;&nbsp; [_OuterAttribute_] <sup>\*</sup>\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [IDENTIFIER]\
> &nbsp;&nbsp; &nbsp;&nbsp; | ([IDENTIFIER] | [TUPLE_INDEX]) `:` [_Expression_]\
> &nbsp;&nbsp; )
>
> _StructBase_ :\
> &nbsp;&nbsp; `..` [_Expression_]
>
> _StructExprTuple_ :\
> &nbsp;&nbsp; [_PathInExpression_] `(`\
> &nbsp;&nbsp; &nbsp;&nbsp; ( [_Expression_] (`,` [_Expression_])<sup>\*</sup> `,`<sup>?</sup> )<sup>?</sup>\
> &nbsp;&nbsp; `)`
>
> _StructExprUnit_ : [_PathInExpression_]
{==+==}

{==+==}


{==+==}
A *struct expression* creates a struct, enum, or union value.
It consists of a path to a [struct], [enum variant], or [union] item followed by the values for the fields of the item.
There are three forms of struct expressions: struct, tuple, and unit.

The following are examples of struct expressions:
{==+==}
*结构体表达式* 用于创建结构体、枚举或联合体值。
它由指向 [结构体][struct] 、 [枚举变体][enum variant] 或 [联合体][union] 条目的路径，以及该条目字段的值组成。结构体表达式有三种形式：结构体、元组和单元结构体。

以下是结构体表达式的示例：
{==+==}


{==+==}
```rust
# struct Point { x: f64, y: f64 }
# struct NothingInMe { }
# struct TuplePoint(f64, f64);
# mod game { pub struct User<'a> { pub name: &'a str, pub age: u32, pub score: usize } }
# struct Cookie; fn some_fn<T>(t: T) {}
Point {x: 10.0, y: 20.0};
NothingInMe {};
TuplePoint(10.0, 20.0);
TuplePoint { 0: 10.0, 1: 20.0 }; // Results in the same value as the above line
let u = game::User {name: "Joe", age: 35, score: 100_000};
some_fn::<Cookie>(Cookie);
```
{==+==}
```rust
# struct Point { x: f64, y: f64 }
# struct NothingInMe { }
# struct TuplePoint(f64, f64);
# mod game { pub struct User<'a> { pub name: &'a str, pub age: u32, pub score: usize } }
# struct Cookie; fn some_fn<T>(t: T) {}
Point {x: 10.0, y: 20.0};
NothingInMe {};
TuplePoint(10.0, 20.0);
TuplePoint { 0: 10.0, 1: 20.0 }; // 得到的结果与上一行相同
let u = game::User {name: "Joe", age: 35, score: 100_000};
some_fn::<Cookie>(Cookie);
```
{==+==}


{==+==}
## Field struct expression

A struct expression with fields enclosed in curly braces allows you to specify the value for each individual field in any order.
The field name is separated from its value with a colon.

A value of a [union] type can only be created using this syntax, and it must specify exactly one field.
{==+==}
## 带有字段的结构体表达式

带有花括号括起来的字段结构体表达式允许您以任何顺序指定每个单独字段的值。字段名称用冒号与其值分开。

[联合体][union] 类型的值只能使用此语法创建，并且必须指定一个字段。
{==+==}


{==+==}
## Functional update syntax

A struct expression that constructs a value of a struct type can terminate with the syntax `..` followed by an expression to denote a functional update.
The expression following `..` (the base) must have the same struct type as the new struct type being formed.

The entire expression uses the given values for the fields that were specified and moves or copies the remaining fields from the base expression.
As with all struct expressions, all of the fields of the struct must be [visible], even those not explicitly named.
{==+==}
## 函数式更新语法

用于构造结构体类型值的结构体表达式可以以 `..` 语法结尾，后跟表达式，表示函数式更新。
`..` 后面的表达式（基表达式）必须与新结构体类型相同。

整个表达式使用给定值填充指定的字段，并将其余字段从基表达式中移动或复制。
与所有结构体表达式一样，结构体的所有字段都必须是 [可见的][visible] ，即使未显式命名。
{==+==}


{==+==}
```rust
# struct Point3d { x: i32, y: i32, z: i32 }
let mut base = Point3d {x: 1, y: 2, z: 3};
let y_ref = &mut base.y;
Point3d {y: 0, z: 10, .. base}; // OK, only base.x is accessed
drop(y_ref);
```
{==+==}
```rust
# struct Point3d { x: i32, y: i32, z: i32 }
let mut base = Point3d {x: 1, y: 2, z: 3};
let y_ref = &mut base.y;
Point3d {y: 0, z: 10, .. base}; // 成功, 仅 base.x 是被访问
drop(y_ref);
```
{==+==}


{==+==}
Struct expressions with curly braces can't be used directly in a [loop] or [if] expression's head, or in the [scrutinee] of an [if let] or [match] expression.
However, struct expressions can be in used in these situations if they are within another expression, for example inside [parentheses].

The field names can be decimal integer values to specify indices for constructing tuple structs.
This can be used with base structs to fill out the remaining indices not specified:
{==+==}
结构体表达式使用花括号括起来的字段，可以在 [loop] 或 [if] 表达式的头部， [if let] 或 [match] 表达式的 [受检项][scrutinee] 中不能直接使用。
但是，如果它们位于另一个表达式中，例如括号中，那么就可以在这些情况下使用结构体表达式。

字段名可以是十进制整数值，用于构造元组结构体的索引。这可以与基础结构体一起使用，以填充未指定的其余索引:
{==+==}


{==+==}
```rust
struct Color(u8, u8, u8);
let c1 = Color(0, 0, 0);  // Typical way of creating a tuple struct.
let c2 = Color{0: 255, 1: 127, 2: 0};  // Specifying fields by index.
let c3 = Color{1: 0, ..c2};  // Fill out all other fields using a base struct.
```
{==+==}
```rust
struct Color(u8, u8, u8); // 定义一个名为Color的元组结构体
let c1 = Color(0, 0, 0); // 创建元组结构体实例的一般方式
let c2 = Color{0: 255, 1: 127, 2: 0}; // 按索引指定结构体字段的值
let c3 = Color{1: 0, ..c2}; // 使用基础结构体填充所有其他字段
```
{==+==}


{==+==}
### Struct field init shorthand

When initializing a data structure (struct, enum, union) with named (but not numbered) fields, it is allowed to write `fieldname` as a shorthand for `fieldname: fieldname`.
This allows a compact syntax with less duplication.
For example:
{==+==}
### 结构体字段初始化的简写语法

在初始化具有命名 (但不是编号) 字段的数据结构 (struct、enum、union) 时，可以使用 `fieldname` 的写法代替 `fieldname: fieldname` 的完整写法。
这样可以使用更简洁的语法来避免重复。
例如:
{==+==}


{==+==}
```rust
# struct Point3d { x: i32, y: i32, z: i32 }
# let x = 0;
# let y_value = 0;
# let z = 0;
Point3d { x: x, y: y_value, z: z };
Point3d { x, y: y_value, z };
```
{==+==}

{==+==}


{==+==}
## Tuple struct expression

A struct expression with fields enclosed in parentheses constructs a tuple struct.
Though it is listed here as a specific expression for completeness, it is equivalent to a [call expression] to the tuple struct's constructor. For example:
{==+==}
## 元组结构体表达式

用括号括起来的字段的结构体表达式构造元组结构体。
虽然为了完整性而在此列出了它作为一个特定的表达式，但它等同于对元组结构体构造函数的 [调用表达式][call expression] 。
例如:
{==+==}


{==+==}
```rust
struct Position(i32, i32, i32);
Position(0, 0, 0);  // Typical way of creating a tuple struct.
let c = Position;  // `c` is a function that takes 3 arguments.
let pos = c(8, 6, 7);  // Creates a `Position` value.
```
{==+==}
```rust
struct Position(i32, i32, i32);
Position(0, 0, 0);  // 典型的创建元组结构体的方式
let c = Position;  // `c` 是一个接受 3 个参数的函数
let pos = c(8, 6, 7);  // 创建一个 `Position` 值
```
{==+==}


{==+==}
## Unit struct expression

A unit struct expression is just the path to a unit struct item.
This refers to the unit struct's implicit constant of its value.
The unit struct value can also be constructed with a fieldless struct expression. For example:
{==+==}
## 单元结构体表达式

单元结构体表达式只是指向一个空结构体条目的路径。
这个路径指向该结构体的值的隐式常量。
单元结构体的值也可以用一个没有字段的结构体表达式构造。例如:
{==+==}


{==+==}
```rust
struct Gamma;
let a = Gamma;  // Gamma unit value.
let b = Gamma{};  // Exact same value as `a`.
```
{==+==}
```rust
struct Gamma;
let a = Gamma;  // Gamma 单元值。
let b = Gamma{};  // 与 `a` 完全相同的值。
```
{==+==}


{==+==}
[_OuterAttribute_]: ../attributes.md
[IDENTIFIER]: ../identifiers.md
[TUPLE_INDEX]: ../tokens.md#tuple-index
[_Expression_]: ../expressions.md
[_PathInExpression_]: ../paths.md#paths-in-expressions
[call expression]: call-expr.md
[enum variant]: ../items/enumerations.md
[if let]: if-expr.md#if-let-expressions
[if]: if-expr.md#if-expressions
[loop]: loop-expr.md
[match]: match-expr.md
[parentheses]: grouped-expr.md
[struct]: ../items/structs.md
[union]: ../items/unions.md
[visible]: ../visibility-and-privacy.md
[scrutinee]: ../glossary.md#scrutinee
{==+==}

{==+==}