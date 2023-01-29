{==+==}
# Structs
{==+==}
# 结构
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Struct_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _StructStruct_\
> &nbsp;&nbsp; | _TupleStruct_
>
> _StructStruct_ :\
> &nbsp;&nbsp; `struct`
>   [IDENTIFIER]&nbsp;
>   [_GenericParams_]<sup>?</sup>
>   [_WhereClause_]<sup>?</sup>
>   ( `{` _StructFields_<sup>?</sup> `}` | `;` )
>
> _TupleStruct_ :\
> &nbsp;&nbsp; `struct`
>   [IDENTIFIER]&nbsp;
>   [_GenericParams_]<sup>?</sup>
>   `(` _TupleFields_<sup>?</sup> `)`
>   [_WhereClause_]<sup>?</sup>
>   `;`
>
> _StructFields_ :\
> &nbsp;&nbsp; _StructField_ (`,` _StructField_)<sup>\*</sup> `,`<sup>?</sup>
>
> _StructField_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [IDENTIFIER] `:` [_Type_]
>
> _TupleFields_ :\
> &nbsp;&nbsp; _TupleField_ (`,` _TupleField_)<sup>\*</sup> `,`<sup>?</sup>
>
> _TupleField_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [_Type_]
{==+==}

{==+==}


{==+==}
A _struct_ is a nominal [struct type] defined with the keyword `struct`.
{==+==}
_struct_ "结构" 是用关键字 `struct` 定义的名义上的  [struct type] "结构类型" 。
{==+==}


{==+==}
An example of a `struct` item and its use:
{==+==}
一个 `struct` 条目的示例和用途:
{==+==}


{==+==}
```rust
struct Point {x: i32, y: i32}
let p = Point {x: 10, y: 11};
let px: i32 = p.x;
```
{==+==}

{==+==}


{==+==}
A _tuple struct_ is a nominal [tuple type], also defined with the keyword
`struct`. For example:
{==+==}
 _tuple struct_ "元组结构" 是名义上的 [tuple type] "元组类型" ，也是用关键字 `struct` 定义的。比如:
{==+==}


{==+==}
[struct type]: ../types/struct.md
[tuple type]: ../types/tuple.md
{==+==}

{==+==}


{==+==}
```rust
struct Point(i32, i32);
let p = Point(10, 11);
let px: i32 = match p { Point(x, _) => x };
```
{==+==}

{==+==}


{==+==}
A _unit-like struct_ is a struct without any fields, defined by leaving off the
list of fields entirely. Such a struct implicitly defines a constant of its
type with the same name. For example:
{==+==}
 _unit-like struct_ "单元结构" 是一种没有任何字段的结构，通过完全省略字段列表来定义。
这样的结构隐式地定义了与其类型相同名称的常量。比如:
{==+==}


{==+==}
```rust
struct Cookie;
let c = [Cookie, Cookie {}, Cookie, Cookie {}];
```

is equivalent to

```rust
struct Cookie {}
const Cookie: Cookie = Cookie {};
let c = [Cookie, Cookie {}, Cookie, Cookie {}];
```
{==+==}
```rust
struct Cookie;
let c = [Cookie, Cookie {}, Cookie, Cookie {}];
```

相似于

```rust
struct Cookie {}
const Cookie: Cookie = Cookie {};
let c = [Cookie, Cookie {}, Cookie, Cookie {}];
```
{==+==}


{==+==}
The precise memory layout of a struct is not specified. One can specify a
particular layout using the [`repr` attribute].
{==+==}
没有精确指定结构的内存布局。可以使用 [`repr` attribute] 来指定特定的布局。
{==+==}


{==+==}
[`repr` attribute]: ../type-layout.md#representations

[_OuterAttribute_]: ../attributes.md
[IDENTIFIER]: ../identifiers.md
[_GenericParams_]: generics.md
[_WhereClause_]: generics.md#where-clauses
[_Visibility_]: ../visibility-and-privacy.md
[_Type_]: ../types.md#type-expressions
{==+==}

{==+==}