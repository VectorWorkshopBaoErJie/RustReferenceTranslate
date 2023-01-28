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

{==+==}


{==+==}
A _type alias_ defines a new name for an existing [type]. Type aliases are
declared with the keyword `type`. Every value has a single, specific type, but
may implement several different traits, or be compatible with several different
type constraints.
{==+==}
_类型别名_ 为一个现有的 [type] 定义了一个新的名字。
类型别名是用关键字 `type` 来声明的。
每个值拥有单一的、特定的类型，但是可以实现几个不同的trait，或者与几个不同的类型约束共存。
{==+==}


{==+==}
For example, the following defines the type `Point` as a synonym for the type
`(u8, u8)`, the type of pairs of unsigned 8 bit integers:
{==+==}
例如，下面定义了类型 `Point` 作为类型 `(u8, u8)` 的同义词，即无符号8位整数的类型。
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
对元组结构或单元结构的类型别名不能用来限定该类型的构造函数。
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
当类型别名不作为关联类型使用时，必须包含一个 [_Type_] ，并且不能包含 [_TypeParamBounds_] 。
{==+==}


{==+==}
A type alias, when used as an [associated type] in a [trait], must not include a
[_Type_] specification but may include [_TypeParamBounds_].
{==+==}
当类型别名作为[trait]中的[associated type]使用时，必须不包含 [_Type_] 说明，但可以包含 [_TypeParamBounds_] 。
{==+==}


{==+==}
A type alias, when used as an [associated type] in a [trait impl], must include
a [_Type_] specification and may not include [_TypeParamBounds_].
{==+==}
当类型别名作为 [trait impl] 中的 [associated type] 使用时，必须包含 [_Type_] 说明，并且不能包含 [_TypeParamBounds_] 。
{==+==}


{==+==}
Where clauses before the equals sign on a type alias in a [trait impl] (like
`type TypeAlias<T> where T: Foo = Bar<T>`) are deprecated. Where clauses after
the equals sign (like `type TypeAlias<T> = Bar<T> where T: Foo`) are preferred.
{==+==}
在 [trait impl] 中类型别名的等号之前的 where 子句被弃用 (如`type TypeAlias<T> where T: Foo = Bar<T>`) 。
在等号之后的 where 子句是首选 (如 `type TypeAlias<T> = Bar<T> where T: Foo`) 。
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