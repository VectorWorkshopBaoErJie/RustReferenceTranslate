{==+==}
# Function pointer types
{==+==}
# 函数指针类型
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _BareFunctionType_ :\
> &nbsp;&nbsp; [_ForLifetimes_]<sup>?</sup> _FunctionTypeQualifiers_ `fn`\
> &nbsp;&nbsp; &nbsp;&nbsp;  `(` _FunctionParametersMaybeNamedVariadic_<sup>?</sup> `)` _BareFunctionReturnType_<sup>?</sup>
>
> _FunctionTypeQualifiers_:\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> (`extern` [_Abi_]<sup>?</sup>)<sup>?</sup>
>
> _BareFunctionReturnType_:\
> &nbsp;&nbsp; `->` [_TypeNoBounds_]
>
> _FunctionParametersMaybeNamedVariadic_ :\
> &nbsp;&nbsp; _MaybeNamedFunctionParameters_ | _MaybeNamedFunctionParametersVariadic_
>
> _MaybeNamedFunctionParameters_ :\
> &nbsp;&nbsp; _MaybeNamedParam_ ( `,` _MaybeNamedParam_ )<sup>\*</sup> `,`<sup>?</sup>
>
> _MaybeNamedParam_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> ( ( [IDENTIFIER] | `_` ) `:` )<sup>?</sup> [_Type_]
>
> _MaybeNamedFunctionParametersVariadic_ :\
> &nbsp;&nbsp; ( _MaybeNamedParam_ `,` )<sup>\*</sup> _MaybeNamedParam_ `,` [_OuterAttribute_]<sup>\*</sup> `...`
{==+==}
> **<sup>语法</sup>**\
> _裸函数类型_ :\
> &nbsp;&nbsp; [_对于生命周期组_][_ForLifetimes_]<sup>?</sup> _函数类型限定符组_ `fn`\
> &nbsp;&nbsp; &nbsp;&nbsp;  `(` _函数参数可能命名为可变参数_<sup>?</sup> `)` _裸函数返回类型_<sup>?</sup>
>
> _函数类型限定符组_:\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> (`extern` [_Abi_]<sup>?</sup>)<sup>?</sup>
>
> _裸函数返回类型_:\
> &nbsp;&nbsp; `->` [_无约束类型组_][_TypeNoBounds_]
>
> _函数参数可能命名为可变参数_ :\
> &nbsp;&nbsp; _可能命名函数参数组_ | _可能命名函数参数可变_
>
> _可能命名函数参数组_ :\
> &nbsp;&nbsp; _可能命名函数参数_ ( `,` _可能命名函数参数_ )<sup>\*</sup> `,`<sup>?</sup>
>
> _可能命名函数参数_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> ( ( [标识符][IDENTIFIER] | `_` ) `:` )<sup>?</sup> [_类型_][_Type_]
>
> _可能命名函数参数可变_ :\
> &nbsp;&nbsp; ( _可能命名函数参数_ `,` )<sup>\*</sup> _可能命名函数参数_ `,` [_外围属性_][_OuterAttribute_]<sup>\*</sup> `...`
{==+==}


{==+==}
Function pointer types, written using the `fn` keyword, refer to a function
whose identity is not necessarily known at compile-time. They can be created
via a coercion from both [function items] and non-capturing [closures].

The `unsafe` qualifier indicates that the type's value is an [unsafe
function], and the `extern` qualifier indicates it is an [extern function].

Variadic parameters can only be specified with [`extern`] function types with
the `"C"` or `"cdecl"` calling convention.

An example where `Binop` is defined as a function pointer type:
{==+==}
函数指针类型，用 `fn` 关键字表示，用来引用一个函数，该函数的身份不一定在编译时已知。
它们可以通过从 [函数条目][function items] 和无捕获的 [闭包][closures] 进行强制转换来创建。

`unsafe` 修饰符表示该类型的值是一个 [非安全的函数][unsafe function] ，而 `extern` 修饰符表示它是一个 [外部函数][extern function] 。

可变参数只能在使用了 `"C"` 或 `"cdecl"` 调用约定的 [`extern`] 函数类型中指定。

下面是一个例子，其中 `Binop` 被定义为函数指针类型:
{==+==}


{==+==}
```rust
fn add(x: i32, y: i32) -> i32 {
    x + y
}

let mut x = add(5,7);

type Binop = fn(i32, i32) -> i32;
let bo: Binop = add;
x = bo(5,7);
```
{==+==}

{==+==}


{==+==}
## Attributes on function pointer parameters

Attributes on function pointer parameters follow the same rules and
restrictions as [regular function parameters].
{==+==}
## 函数指针参数上的属性

函数指针参数上的属性遵循与 [常规函数参数][regular function parameters] 相同的规则和限制。
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[_Abi_]: ../items/functions.md
[_ForLifetimes_]: ../trait-bounds.md#higher-ranked-trait-bounds
[_TypeNoBounds_]: ../types.md#type-expressions
[_Type_]: ../types.md#type-expressions
[_OuterAttribute_]: ../attributes.md
[`extern`]: ../items/external-blocks.md
[closures]: closure.md
[extern function]: ../items/functions.md#extern-function-qualifier
[function items]: function-item.md
[unsafe function]: ../unsafe-keyword.md
[regular function parameters]: ../items/functions.md#attributes-on-function-parameters
{==+==}

{==+==}