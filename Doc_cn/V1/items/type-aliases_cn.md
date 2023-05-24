{==+==}
# Type aliases
{==+==}
# 类型别名
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TypeAlias_ :\
> &nbsp;&nbsp; `type` [IDENTIFIER]&nbsp;[_GenericParams_]<sup>?</sup>
>              ( `:` [_TypeParamBounds_] )<sup>?</sup>
>              [_WhereClause_]<sup>?</sup> ( `=` [_Type_] [_WhereClause_]<sup>?</sup>)<sup>?</sup> `;`
{==+==}
> **<sup>语法</sup>**\
> _类型别名组_ :\
> &nbsp;&nbsp; `type` [标识符][IDENTIFIER]&nbsp;[_泛型参数组_][_GenericParams_]<sup>?</sup>
>              ( `:` [_类型参数约束组_][_TypeParamBounds_] )<sup>?</sup>
>              [_Where从句_][_WhereClause_]<sup>?</sup> ( `=` [_类型_][_Type_] [_Where从句_][_WhereClause_]<sup>?</sup>)<sup>?</sup> `;`
{==+==}


{==+==}
A _type alias_ defines a new name for an existing [type]. Type aliases are
declared with the keyword `type`. Every value has a single, specific type, but
may implement several different traits, or be compatible with several different
type constraints.
{==+==}
_类型别名_ 为现有 [类型][type] 定义了一个新的名称。类型别名使用关键字 `type` 进行声明。
每个值都有一个特定的类型，但可以实现多个不同的 trait，或兼容多个不同类型的约束。
{==+==}


{==+==}
For example, the following defines the type `Point` as a synonym for the type
`(u8, u8)`, the type of pairs of unsigned 8 bit integers:
{==+==}
例如，以下代码将类型 `Point` 定义为类型 `(u8, u8)` 的同义词，即由两个无符号 8 位整数组成的类型：
{==+==}


{==+==}
```rust
type Point = (u8, u8);
let p: Point = (41, 68);
```
{==+==}

{==+==}


{==+==}
A type alias to a tuple-struct or unit-struct cannot be used to qualify that type's constructor:
{==+==}
类型别名无法获得元组结构体或单元结构体的构造函数。
{==+==}


{==+==}
```rust,compile_fail
struct MyStruct(u32);

use MyStruct as UseAlias;
type TypeAlias = MyStruct;

let _ = UseAlias(5); // OK
let _ = TypeAlias(5); // Doesn't work
```
{==+==}

{==+==}


{==+==}
A type alias, when not used as an associated type, must include a [_Type_] and
may not include [_TypeParamBounds_].
{==+==}
当类型别名不作为关联类型使用时，必须包含一个 [_类型_][_Type_] ，不能包含 [_类型参数约束组_][_TypeParamBounds_] 。
{==+==}


{==+==}
A type alias, when used as an [associated type] in a [trait], must not include a
[_Type_] specification but may include [_TypeParamBounds_].
{==+==}
在 [trait] 中使用作为 [关联类型][associated type] 的类型别名，不应该包含类型本身的声明 [_类型_][_Type_] ，但是可以包含类型参数约束声明 [_类型参数约束组_][_TypeParamBounds_] 。
{==+==}


{==+==}
A type alias, when used as an [associated type] in a [trait impl], must include
a [_Type_] specification and may not include [_TypeParamBounds_].
{==+==}
类型别名在 [trait impl] 中作为 [关联类型][associated type] 使用时，必须包括 [_类型_][_Type_] ，但不能包括 [_类型参数约束组_][_TypeParamBounds_] 。
{==+==}


{==+==}
Where clauses before the equals sign on a type alias in a [trait impl] (like
`type TypeAlias<T> where T: Foo = Bar<T>`) are deprecated. Where clauses after
the equals sign (like `type TypeAlias<T> = Bar<T> where T: Foo`) are preferred.
{==+==}
在 [trait impl] 中，位于类型别名等号之前的 where 从句已被弃用 (例如 `type TypeAlias<T> where T: Foo = Bar<T>` ) 。
首选的是 where 从句位于等号之后 (例如 `type TypeAlias<T> = Bar<T> where T: Foo` ) 。
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[_GenericParams_]: generics.md
[_TypeParamBounds_]: ../trait-bounds.md
[_WhereClause_]: generics.md#where-clauses
[_Type_]: ../types.md#type-expressions
[associated type]: associated-items.md#associated-types
[trait]: traits.md
[type]: ../types.md
[trait impl]: implementations.md#trait-implementations
{==+==}

{==+==}