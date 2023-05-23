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
> **<sup>语法</sup>**\
> _结构体_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _Struct结构体_\
> &nbsp;&nbsp; | _元组结构体_
>
> _Struct结构体_ :\
> &nbsp;&nbsp; `struct`
>   [标识符][IDENTIFIER]&nbsp;
>   [_泛型参数组_][_GenericParams_]<sup>?</sup>
>   [_Where子句_][_WhereClause_]<sup>?</sup>
>   ( `{` _结构体字段组_<sup>?</sup> `}` | `;` )
>
> _元组结构体_ :\
> &nbsp;&nbsp; `struct`
>   [标识符][IDENTIFIER]&nbsp;
>   [_泛型参数组_][_GenericParams_]<sup>?</sup>
>   `(` _元组字段组_<sup>?</sup> `)`
>   [_Where子句_][_WhereClause_]<sup>?</sup>
>   `;`
>
> _结构体字段组_ :\
> &nbsp;&nbsp; _结构体字段_ (`,` _结构体字段_)<sup>\*</sup> `,`<sup>?</sup>
>
> _结构体字段_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_可见性_][_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [标识符][IDENTIFIER] `:` [_类型_][_Type_]
>
> _元组字段组_ :\
> &nbsp;&nbsp; _元组字段_ (`,` _元组字段_)<sup>\*</sup> `,`<sup>?</sup>
>
> _元组字段_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; [_可见性_][_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [_类型_][_Type_]
{==+==}


{==+==}
A _struct_ is a nominal [struct type] defined with the keyword `struct`.
{==+==}
_结构体_ 是使用关键字 `struct` 定义具名的 [结构体类型][struct type] 。
{==+==}


{==+==}
An example of a `struct` item and its use:
{==+==}
以下是 `struct` 类型的一个示例及其使用:
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
_元组结构体_ 是一种具名的 [元组类型] ，同样使用关键字 `struct` 定义。例如:
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
 _单元结构体_ 是一种没有任何字段的结构体，通过完全省略字段列表定义。这样的结构体隐式地定义了一个与其类型相同的常量名称。例如：
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
结构体没有精确指定内存布局。可以使用 [`repr` 属性][`repr` attribute] 指定特定的布局。
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