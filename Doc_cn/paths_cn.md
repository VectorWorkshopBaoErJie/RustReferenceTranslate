{==+==}
# Paths

A *path* is a sequence of one or more path segments _logically_ separated by
a namespace <span class="parenthetical">qualifier (`::`)</span>. If a path
consists of only one segment, it refers to either an [item] or a [variable] in
a local control scope. If a path has multiple segments, it always refers to an
item.

Two examples of simple paths consisting of only identifier segments:
{==+==}
# 路径

*路径* 是一个由一个或多个路径段 _逻辑上_ 由命名空间 <span class="parenthetical">限定符(`::`)</span> 分隔的序列。
如果路径只包含一个段，则它引用一个局部控制作用域中的 [条目][item] 或 [变量][variable] 。如果路径有多个段，它总是引用一个条目。

以下是由标识符段组成的简单路径的两个示例：
{==+==}


{==+==}
<!-- ignore: syntax fragment -->
```rust,ignore
x;
x::y::z;
```
{==+==}

{==+==}


{==+==}
## Types of paths

### Simple Paths

> **<sup>Syntax</sup>**\
> _SimplePath_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _SimplePathSegment_ (`::` _SimplePathSegment_)<sup>\*</sup>
>
> _SimplePathSegment_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `super` | `self` | `crate` | `$crate`

Simple paths are used in [visibility] markers, [attributes], [macros], and [`use`] items.
Examples:

```rust
use std::io::{self, Write};
mod m {
    #[clippy::cyclomatic_complexity = "0"]
    pub (in super) fn f1() {}
}
```
{==+==}
## 路径的类型

### 简单路径

> **<sup>Syntax</sup>**\
> _SimplePath_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _SimplePathSegment_ (`::` _SimplePathSegment_)<sup>\*</sup>
>
> _SimplePathSegment_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `super` | `self` | `crate` | `$crate`

简单路径在 [可见性][visibility] 标记， [属性][attributes] ， [宏][macros] 和 [`use`] 条目中使用。
例如：

```rust
use std::io::{self, Write};
mod m {
    #[clippy::cyclomatic_complexity = "0"]
    pub (in super) fn f1() {}
}
```
{==+==}


{==+==}
### Paths in expressions
{==+==}
### 表达式中的路径
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _PathInExpression_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _PathExprSegment_ (`::` _PathExprSegment_)<sup>\*</sup>
>
> _PathExprSegment_ :\
> &nbsp;&nbsp; _PathIdentSegment_ (`::` _GenericArgs_)<sup>?</sup>
>
> _PathIdentSegment_ :\
> &nbsp;&nbsp; [IDENTIFIER] | `super` | `self` | `Self` | `crate` | `$crate`
>
> _GenericArgs_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `<` `>`\
> &nbsp;&nbsp; | `<` ( _GenericArg_ `,` )<sup>\*</sup> _GenericArg_ `,`<sup>?</sup> `>`
>
> _GenericArg_ :\
> &nbsp;&nbsp; [_Lifetime_] | [_Type_] | _GenericArgsConst_ | _GenericArgsBinding_
>
> _GenericArgsConst_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_BlockExpression_]\
> &nbsp;&nbsp; | [_LiteralExpression_]\
> &nbsp;&nbsp; | `-` [_LiteralExpression_]\
> &nbsp;&nbsp; | [_SimplePathSegment_]
>
> _GenericArgsBinding_ :\
> &nbsp;&nbsp; [IDENTIFIER] `=` [_Type_]
{==+==}

{==+==}


{==+==}
Paths in expressions allow for paths with generic arguments to be specified. They are
used in various places in [expressions] and [patterns].

The `::` token is required before the opening `<` for generic arguments to avoid
ambiguity with the less-than operator. This is colloquially known as "turbofish" syntax.
{==+==}
表达式中的路径允许指定带有泛型参数的路径。它们在表达式和模式的各种位置使用。

在泛型参数的左括号 `<` 前面需要添加 `::` 标记以避免与小于号操作符产生歧义。这通常被称为 "turbofish" 语法。
{==+==}


{==+==}
```rust
(0..10).collect::<Vec<_>>();
Vec::<u8>::with_capacity(1024);
```
{==+==}

{==+==}


{==+==}
The order of generic arguments is restricted to lifetime arguments, then type
arguments, then const arguments, then equality constraints.

Const arguments must be surrounded by braces unless they are a
[literal] or a single segment path.

The synthetic type parameters corresponding to `impl Trait` types are implicit,
and these cannot be explicitly specified.
{==+==}
泛型参数的顺序是受限的，首先是生命周期参数，然后是类型参数，然后是常量参数，最后是相等性约束。

常量参数必须用花括号括起来，除非它们是字面量或单个路径段。

对于 `impl Trait` 类型对应的合成类型参数是隐式的，不能显式地指定。
{==+==}


{==+==}
## Qualified paths
{==+==}
## 限定路径
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _QualifiedPathInExpression_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _PathExprSegment_)<sup>+</sup>
>
> _QualifiedPathType_ :\
> &nbsp;&nbsp; `<` [_Type_] (`as` _TypePath_)<sup>?</sup> `>`
>
> _QualifiedPathInType_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _TypePathSegment_)<sup>+</sup>

Fully qualified paths allow for disambiguating the path for [trait implementations] and
for specifying [canonical paths](#canonical-paths). When used in a type specification, it
supports using the type syntax specified below.
{==+==}
> **<sup>Syntax</sup>**\
> _QualifiedPathInExpression_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _PathExprSegment_)<sup>+</sup>
>
> _QualifiedPathType_ :\
> &nbsp;&nbsp; `<` [_Type_] (`as` _TypePath_)<sup>?</sup> `>`
>
> _QualifiedPathInType_ :\
> &nbsp;&nbsp; _QualifiedPathType_ (`::` _TypePathSegment_)<sup>+</sup>

完全限定路径用于在 [trait 实现][trait implementations] 中消除歧义，并用于指定 [规范路径](#canonical-paths) 。在类型规范中使用时，它支持使用下面指定的类型语法。
{==+==}


{==+==}
```rust
struct S;
impl S {
    fn f() { println!("S"); }
}
trait T1 {
    fn f() { println!("T1 f"); }
}
impl T1 for S {}
trait T2 {
    fn f() { println!("T2 f"); }
}
impl T2 for S {}
S::f();  // Calls the inherent impl.
<S as T1>::f();  // Calls the T1 trait function.
<S as T2>::f();  // Calls the T2 trait function.
```
{==+==}

{==+==}


{==+==}
### Paths in types
{==+==}
### 类型中路径
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TypePath_ :\
> &nbsp;&nbsp; `::`<sup>?</sup> _TypePathSegment_ (`::` _TypePathSegment_)<sup>\*</sup>
>
> _TypePathSegment_ :\
> &nbsp;&nbsp; _PathIdentSegment_ `::`<sup>?</sup> ([_GenericArgs_] | _TypePathFn_)<sup>?</sup>
>
> _TypePathFn_ :\
> `(` _TypePathFnInputs_<sup>?</sup> `)` (`->` [_Type_])<sup>?</sup>
>
> _TypePathFnInputs_ :\
> [_Type_] (`,` [_Type_])<sup>\*</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
Type paths are used within type definitions, trait bounds, type parameter bounds,
and qualified paths.

Although the `::` token is allowed before the generics arguments, it is not required
because there is no ambiguity like there is in _PathInExpression_.
{==+==}
类型路径 (Type paths) 在类型定义、trait 约束、类型参数约束和限定路径中使用。

虽然 `::` 令牌在泛型参数之前是允许的，但不是必须的，因为与在表达式中的情况不同，这里没有歧义。
{==+==}


{==+==}
```rust
# mod ops {
#     pub struct Range<T> {f1: T}
#     pub trait Index<T> {}
#     pub struct Example<'a> {f1: &'a i32}
# }
# struct S;
impl ops::Index<ops::Range<usize>> for S { /*...*/ }
fn i<'a>() -> impl Iterator<Item = ops::Example<'a>> {
    // ...
#    const EXAMPLE: Vec<ops::Example<'static>> = Vec::new();
#    EXAMPLE.into_iter()
}
type G = std::boxed::Box<dyn std::ops::FnOnce(isize) -> isize>;
```
{==+==}

{==+==}


{==+==}
## Path qualifiers

Paths can be denoted with various leading qualifiers to change the meaning of
how it is resolved.

### `::`

Paths starting with `::` are considered to be *global paths* where the segments of the path
start being resolved from a place which differs based on edition. Each identifier in
the path must resolve to an item.

> **Edition Differences**: In the 2015 Edition, identifiers resolve from the "crate root"
> (`crate::` in the 2018 edition), which contains a variety of different items, including
> external crates, default crates such as `std` or `core`, and items in the top level of
> the crate (including `use` imports).
>
> Beginning with the 2018 Edition, paths starting with `::` resolve from
> crates in the [extern prelude]. That is, they must be followed by the name of a crate.
{==+==}
## 路径限定符

路径可以用各种前导限定符来改变它解析的方式。

### `::`

以 `::` 开头的路径被认为是 *全局路径* ，路径中的段从不同的起点开始解析，这个起点是根据版本不同而不同。路径中的每个标识符都必须解析为一个条目。

> **版本差异**：在 2015 版本中，标识符从 “crate root” 开始解析 (在 2018 版本中是 `crate::`) ，其中包含各种不同的项，包括外部 crate、默认的 crate (如 `std` 或 `core`) 以及 crate 顶级的项 (包括 `use` 导入) 。
>
> 从 2018 版本开始，以 `::` 开头的路径从 [extern prelude] 中的 crate 解析。也就是说，它们必须跟随一个 crate 的名称。
{==+==}


{==+==}
```rust
pub fn foo() {
    // In the 2018 edition, this accesses `std` via the extern prelude.
    // In the 2015 edition, this accesses `std` via the crate root.
    let now = ::std::time::Instant::now();
    println!("{:?}", now);
}
```

```rust,edition2015
// 2015 Edition
mod a {
    pub fn foo() {}
}
mod b {
    pub fn foo() {
        ::a::foo(); // call `a`'s foo function
        // In Rust 2018, `::a` would be interpreted as the crate `a`.
    }
}
# fn main() {}
```
{==+==}
```rust
pub fn foo() {
    // 在 2018 版中，这通过 extern 预导模块访问 std。
    // 在 2015 版中，这通过 crate root 访问 std。
    let now = ::std::time::Instant::now();
    println!("{:?}", now);
}
```

```rust,edition2015
// 2015 版
mod a {
    pub fn foo() {}
}
mod b {
    pub fn foo() {
        ::a::foo(); // 调用 `a` 的 foo 函数
        // 在 Rust 2018 中， `::a` 将被解释为 crate `a` 。
    }
}
# fn main() {}
```
{==+==}


{==+==}
### `self`

`self` resolves the path relative to the current module. `self` can only be used as the
first segment, without a preceding `::`.
{==+==}
### `self`

`self` 用于相对于当前模块解析路径，只能作为路径的第一个段出现，不能在前面加上 `::`。
{==+==}


{==+==}
```rust
fn foo() {}
fn bar() {
    self::foo();
}
# fn main() {}
```
{==+==}

{==+==}


{==+==}
### `Self`

`Self`, with a capital "S", is used to refer to the implementing type within
[traits] and [implementations].

`Self` can only be used as the first segment, without a preceding `::`.
{==+==}
### `Self`

`Self`（注意大写）用于在 [traits] 和 [implementations] 中引用实现类型本身。

`Self` 只能作为第一个段使用，不能有前导 `::` 。
{==+==}


{==+==}
```rust
trait T {
    type Item;
    const C: i32;
    // `Self` will be whatever type that implements `T`.
    fn new() -> Self;
    // `Self::Item` will be the type alias in the implementation.
    fn f(&self) -> Self::Item;
}
struct S;
impl T for S {
    type Item = i32;
    const C: i32 = 9;
    fn new() -> Self {           // `Self` is the type `S`.
        S
    }
    fn f(&self) -> Self::Item {  // `Self::Item` is the type `i32`.
        Self::C                  // `Self::C` is the constant value `9`.
    }
}
```
{==+==}
```rust
trait T {
    type Item;
    const C: i32;
    // `Self` 将是任何实现 `T` 的类型。
    fn new() -> Self;
    // `Self::Item` 将是实现中的类型别名。
    fn f(&self) -> Self::Item;
}
struct S;
impl T for S {
    type Item = i32;
    const C: i32 = 9;
    fn new() -> Self {           // `Self` 是类型 `S`。
        S
    }
    fn f(&self) -> Self::Item {  // `Self::Item` 是类型 `i32`。
        Self::C                  // `Self::C` 是常量值 `9`。
    }
}
```
{==+==}


{==+==}
### `super`

`super` in a path resolves to the parent module. It may only be used in leading
segments of the path, possibly after an initial `self` segment.
{==+==}
### `super`

`super` 在路径中解析为父模块。它只能在路径的前导段中使用，可能在一个初始的 `self` 段之后。
{==+==}


{==+==}
```rust
mod a {
    pub fn foo() {}
}
mod b {
    pub fn foo() {
        super::a::foo(); // call a's foo function
    }
}
# fn main() {}
```
{==+==}

{==+==}


{==+==}
`super` may be repeated several times after the first `super` or `self` to refer to
ancestor modules.
{==+==}
`super` 可以在第一个 `super` 或 `self` 之后重复使用，以引用祖先模块。
{==+==}


{==+==}
```rust
mod a {
    fn foo() {}

    mod b {
        mod c {
            fn foo() {
                super::super::foo(); // call a's foo function
                self::super::super::foo(); // call a's foo function
            }
        }
    }
}
# fn main() {}
```
{==+==}

{==+==}


{==+==}
### `crate`

`crate` resolves the path relative to the current crate. `crate` can only be used as the
first segment, without a preceding `::`.
{==+==}
### `crate`

`crate` 用于解析与当前 crate 相关的路径。 `crate` 只能用作路径的第一个标识符，不能在其前面加上 `::` 。
{==+==}


{==+==}
```rust
fn foo() {}
mod a {
    fn bar() {
        crate::foo();
    }
}
# fn main() {}
```
{==+==}

{==+==}


{==+==}
### `$crate`

`$crate` is only used within [macro transcribers], and can only be used as the first
segment, without a preceding `::`. `$crate` will expand to a path to access items from the
top level of the crate where the macro is defined, regardless of which crate the macro is
invoked.
{==+==}
### `$crate`

`$crate` 仅在 [macro transcribers] 中使用，且只能用作第一个段，没有前置 `::` 。 `$crate` 将扩展为一个路径，以访问在定义宏的 crate 顶层的条目，而不管宏被调用的 crate 是哪个。
{==+==}


{==+==}
```rust
pub fn increment(x: u32) -> u32 {
    x + 1
}

#[macro_export]
macro_rules! inc {
    ($x:expr) => ( $crate::increment($x) )
}
# fn main() { }
```
{==+==}

{==+==}


{==+==}
## Canonical paths

Items defined in a module or implementation have a *canonical path* that
corresponds to where within its crate it is defined. All other paths to these
items are aliases. The canonical path is defined as a *path prefix* appended by
the path segment the item itself defines.

[Implementations] and [use declarations] do not have canonical paths, although
the items that implementations define do have them. Items defined in
block expressions do not have canonical paths. Items defined in a module that
does not have a canonical path do not have a canonical path. Associated items
defined in an implementation that refers to an item without a canonical path,
e.g. as the implementing type, the trait being implemented, a type parameter or
bound on a type parameter, do not have canonical paths.

The path prefix for modules is the canonical path to that module. For bare
implementations, it is the canonical path of the item being implemented
surrounded by <span class="parenthetical">angle (`<>`)</span> brackets. For
[trait implementations], it is the canonical path of the item being implemented
followed by `as` followed by the canonical path to the trait all surrounded in
<span class="parenthetical">angle (`<>`)</span> brackets.

The canonical path is only meaningful within a given crate. There is no global
namespace across crates; an item's canonical path merely identifies it within
the crate.
{==+==}
## 规范化路径

在模块或实现中定义的条目具有与其所在的 crate 中定义的位置对应的 *规范路径* 。对这些条目的所有其他路径都是别名。规范路径被定义为 *路径前缀* 加上条目本身定义的路径段。

[实现][Implementations] 和 [use 声明][use declarations] 没有规范路径，虽然实现定义的条目具有规范路径。在块表达式中定义的条目没有规范路径。
在没有规范路径的模块中定义的条目没有规范路径。在实现中定义的关联条目引用没有规范路径的条目，例如实现类型、被实现的 trait 、类型参数或类型参数的约束，没有规范路径。

对于模块，路径前缀是该模块的规范路径。对于裸实现，路径前缀是被实现条目的规范路径，用尖括号（`<>`）括起来。
对于 [trait 实现][trait implementations] ，路径前缀是被实现条目的规范路径，后跟`as`，后跟该 trait 的规范路径，全部用尖括号（`<>`）括起来。

规范路径仅在给定 crate 中具有意义。跨 crate 没有全局命名空间；条目的规范路径仅标识其在 crate 中的位置。
{==+==}


{==+==}
```rust
// Comments show the canonical path of the item.

mod a { // crate::a
    pub struct Struct; // crate::a::Struct

    pub trait Trait { // crate::a::Trait
        fn f(&self); // crate::a::Trait::f
    }

    impl Trait for Struct {
        fn f(&self) {} // <crate::a::Struct as crate::a::Trait>::f
    }

    impl Struct {
        fn g(&self) {} // <crate::a::Struct>::g
    }
}

mod without { // crate::without
    fn canonicals() { // crate::without::canonicals
        struct OtherStruct; // None

        trait OtherTrait { // None
            fn g(&self); // None
        }

        impl OtherTrait for OtherStruct {
            fn g(&self) {} // None
        }

        impl OtherTrait for crate::a::Struct {
            fn g(&self) {} // None
        }

        impl crate::a::Trait for OtherStruct {
            fn f(&self) {} // None
        }
    }
}

# fn main() {}
```
{==+==}

{==+==}


{==+==}
[_BlockExpression_]: expressions/block-expr.md
[_Expression_]: expressions.md
[_GenericArgs_]: #paths-in-expressions
[_Lifetime_]: trait-bounds.md
[_LiteralExpression_]: expressions/literal-expr.md
[_SimplePathSegment_]: #simple-paths
[_Type_]: types.md#type-expressions
[literal]: expressions/literal-expr.md
[item]: items.md
[variable]: variables.md
[implementations]: items/implementations.md
[use declarations]: items/use-declarations.md
[IDENTIFIER]: identifiers.md
[`use`]: items/use-declarations.md
[attributes]: attributes.md
[expressions]: expressions.md
[extern prelude]: names/preludes.md#extern-prelude
[macro transcribers]: macros-by-example.md
[macros]: macros-by-example.md
[patterns]: patterns.md
[trait implementations]: items/implementations.md#trait-implementations
[traits]: items/traits.md
[visibility]: visibility-and-privacy.md
{==+==}

{==+==}