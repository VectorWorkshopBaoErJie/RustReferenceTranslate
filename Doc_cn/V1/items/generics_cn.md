{==+==}
# Generic parameters
{==+==}
# 泛型参数
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _GenericParams_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `<` `>`\
> &nbsp;&nbsp;  | `<` (_GenericParam_ `,`)<sup>\*</sup> _GenericParam_ `,`<sup>?</sup> `>`
>
> _GenericParam_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> ( _LifetimeParam_ | _TypeParam_ | _ConstParam_ )
>
> _LifetimeParam_ :\
> &nbsp;&nbsp; [LIFETIME_OR_LABEL]&nbsp;( `:` [_LifetimeBounds_] )<sup>?</sup>
>
> _TypeParam_ :\
> &nbsp;&nbsp; [IDENTIFIER]( `:` [_TypeParamBounds_]<sup>?</sup> )<sup>?</sup> ( `=` [_Type_] )<sup>?</sup>
>
> _ConstParam_:\
> &nbsp;&nbsp; `const` [IDENTIFIER] `:` [_Type_] ( `=` _[Block][block]_ | [IDENTIFIER] | -<sup>?</sup>[LITERAL] )<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _泛型参数组_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `<` `>`\
> &nbsp;&nbsp;  | `<` (_泛型参数_ `,`)<sup>\*</sup> _泛型参数_ `,`<sup>?</sup> `>`
>
> _泛型参数_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> ( _生命周期参数_ | _类型参数_ | _常量参数_ )
>
> _生命周期参数_ :\
> &nbsp;&nbsp; [生命周期或标签][LIFETIME_OR_LABEL]&nbsp;( `:` [_生命周期约束组_][_LifetimeBounds_] )<sup>?</sup>
>
> _类型参数_ :\
> &nbsp;&nbsp; [标识符][IDENTIFIER]( `:` [_类型参数约束组_][_TypeParamBounds_]<sup>?</sup> )<sup>?</sup> ( `=` [_类型_][_Type_] )<sup>?</sup>
>
> _常量参数_:\
> &nbsp;&nbsp; `const` [标识符][IDENTIFIER] `:` [_类型_][_Type_] ( `=` _[块][block]_ | [标识符][IDENTIFIER] | -<sup>?</sup>[字面值][LITERAL] )<sup>?</sup>
{==+==}


{==+==}
[Functions], [type aliases], [structs], [enumerations], [unions], [traits], and
[implementations] may be *parameterized* by types, constants, and lifetimes. These
parameters are listed in angle <span class="parenthetical">brackets (`<...>`)</span>,
usually immediately after the name of the item and before its definition. For
implementations, which don't have a name, they come directly after `impl`.
The order of generic parameters is restricted to lifetime parameters and then type and const parameters intermixed.
{==+==}
[函数][Functions]、 [类型别名][type aliases] 、 [结构体][structs] 、 [枚举][enumerations] 、 [联合体][unions] 、 [traits]和 [实现][implementations] 可以通过类型、常量和生命周期参数 *泛型化* 。这些参数在尖括号 <span class="parenthetical"> (`<...>`) </span> 中列出，通常紧跟在条目名称之后，在其定义之前。对于没有名称的实现，它们直接在 `impl` 之后。泛型参数的顺序仅限于先生命周期参数，然后交替出现类型和常量参数。
{==+==}


{==+==}
Some examples of items with type, const, and lifetime parameters:
{==+==}
一些带有类型、常量和生命周期参数的条目的例子:
{==+==}


{==+==}
```rust
fn foo<'a, T>() {}
trait A<U> {}
struct Ref<'a, T> where T: 'a { r: &'a T }
struct InnerArray<T, const N: usize>([T; N]);
struct EitherOrderWorks<const N: bool, U>(U);
```
{==+==}

{==+==}


{==+==}
Generic parameters are in scope within the item definition where they are
declared. They are not in scope for items declared within the body of a
function as described in [item declarations].
{==+==}
泛型参数在它们声明的条目定义中是有效的。根据 [条目声明][item declarations] 所述，它们不在函数体中声明的条目作用域内。
{==+==}


{==+==}
[References], [raw pointers], [arrays], [slices], [tuples], and
[function pointers] have lifetime or type parameters as well, but are not
referred to with path syntax.
{==+==}
[引用][References] 、 [原始指针][raw pointers] 、 [数组][arrays] 、 [切片][slices] 、 [元组][tuples] ， 和 [函数指针][function pointers] 也有生命周期或类型参数，但不是用路径语法来引用。
{==+==}


{==+==}
### Const generics
{==+==}
### 常量泛型
{==+==}


{==+==}
*Const generic parameters* allow items to be generic over constant values. The
const identifier introduces a name for the constant parameter, and all
instances of the item must be instantiated with a value of the given type.
{==+==}
*常量泛型参数* 允许条目对常量值进行泛型。
const 标识符引入了常量参数的名称，并且必须使用给定类型的值来实例化所有条目实例。
{==+==}


{==+==}
<!-- TODO: update above to say "introduces a name in the [value namespace]"
    once namespaces are added. -->
{==+==}

{==+==}


{==+==}
The only allowed types of const parameters are `u8`, `u16`, `u32`, `u64`, `u128`, `usize`,
`i8`, `i16`, `i32`, `i64`, `i128`, `isize`, `char` and `bool`.
{==+==}
仅允许的常量参数类型是 `u8` 、 `u16` 、 `u32` 、 `u64` 、 `u128` 、 `usize` 、`i8` 、 `i16` 、 `i32` 、 `i64` 、 `i128` 、 `isize` 、 `char` 、 `bool`.
{==+==}


{==+==}
Const parameters can be used anywhere a [const item] can be used, with the
exception that when used in a [type] or [array repeat expression], it must be
standalone (as described below). That is, they are allowed in the following
places:
{==+==}
常量参数可以在任何 [常量条目][const item] 可用的地方使用，但是当它们用于 [类型][type] 或 [数组重复表达式][array repeat expression] 时，必须是独立的 (如下所述) 。也就是说，它们允许出现在以下位置:
{==+==}


{==+==}
1. As an applied const to any type which forms a part of the signature of the
   item in question.
2. As part of a const expression used to define an [associated const], or as a
   parameter to an [associated type].
3. As a value in any runtime expression in the body of any functions in the
   item.
4. As a parameter to any type used in the body of any functions in the item.
5. As a part of the type of any fields in the item.
{==+==}
1. 作为有关条目签名的一部分的任何类型应用 const 。
2. 作为定义 [关联常量][associated const] 或作为 [关联类型][associated type] 参数的常量表达式的一部分。
3. 作为条目中任何函数体中运行时表达式中的值。
4. 作为条目中任何函数体中使用的任何类型的参数。
5. 作为条目字段类型的一部分。
{==+==}


{==+==}
```rust
// Examples where const generic parameters can be used.

// Used in the signature of the item itself.
fn foo<const N: usize>(arr: [i32; N]) {
    // Used as a type within a function body.
    let x: [i32; N];
    // Used as an expression.
    println!("{}", N * 2);
}

// Used as a field of a struct.
struct Foo<const N: usize>([i32; N]);

impl<const N: usize> Foo<N> {
    // Used as an associated constant.
    const CONST: usize = N * 4;
}

trait Trait {
    type Output;
}

impl<const N: usize> Trait for Foo<N> {
    // Used as an associated type.
    type Output = [i32; N];
}
```
{==+==}
```rust
// 可以使用常量泛型参数的例子。

// 用于条目本身签名。
fn foo<const N: usize>(arr: [i32; N]) {
    // 用作函数体中的类型。
    let x: [i32; N];
    // 用作表达式。
    println!("{}", N * 2);
}

// 用作结构的字段。
struct Foo<const N: usize>([i32; N]);

impl<const N: usize> Foo<N> {
    // 用作关联常量。
    const CONST: usize = N * 4;
}

trait Trait {
    type Output;
}

impl<const N: usize> Trait for Foo<N> {
    // 用作关联类型。
    type Output = [i32; N];
}
```
{==+==}


{==+==}
```rust,compile_fail
// Examples where const generic parameters cannot be used.
fn foo<const N: usize>() {
    // Cannot use in item definitions within a function body.
    const BAD_CONST: [usize; N] = [1; N];
    static BAD_STATIC: [usize; N] = [1; N];
    fn inner(bad_arg: [usize; N]) {
        let bad_value = N * 2;
    }
    type BadAlias = [usize; N];
    struct BadStruct([usize; N]);
}
```
{==+==}
```rust,compile_fail
// 不能使用常量泛型参数的例子。
fn foo<const N: usize>() {
    // 不能在函数体中的条目定义上使用。
    const BAD_CONST: [usize; N] = [1; N];
    static BAD_STATIC: [usize; N] = [1; N];
    fn inner(bad_arg: [usize; N]) {
        let bad_value = N * 2;
    }
    type BadAlias = [usize; N];
    struct BadStruct([usize; N]);
}
```
{==+==}


{==+==}
As a further restriction, const parameters may only appear as a standalone
argument inside of a [type] or [array repeat expression]. In those contexts,
they may only be used as a single segment [path expression], possibly inside a
[block] (such as `N` or `{N}`). That is, they cannot be combined with other
expressions.
{==+==}
作为进一步的限制，常量参数只能出现在 [类型][type] 或 [数组重复表达式][array repeat expression] 内部作为独立的参数。
在这些上下文中，它们只能用作单个段 [路径表达式][path expression] ，可能包含在 [块][block] 中 (如 `N` 或 `{N}` ) 。也就是说，它们不能与其他表达式结合使用。
{==+==}


{==+==}
```rust,compile_fail
// Examples where const parameters may not be used.

// Not allowed to combine in other expressions in types, such as the
// arithmetic expression in the return type here.
fn bad_function<const N: usize>() -> [u8; {N + 1}] {
    // Similarly not allowed for array repeat expressions.
    [1; {N + 1}]
}
```
{==+==}
```rust,compile_fail
// 不可以使用常量参数的例子。

// 不允许在其他类型的表达式中组合，比如这里的返回类型中的算术表达式。
fn bad_function<const N: usize>() -> [u8; {N + 1}] {
    // 同样不允许用于数组重复表达式。
    [1; {N + 1}]
}
```
{==+==}


{==+==}
A const argument in a [path] specifies the const value to use for that item.
The argument must be a [const expression] of the type ascribed to the const
parameter. The const expression must be a [block expression][block]
(surrounded with braces) unless it is a single path segment (an [IDENTIFIER])
or a [literal] (with a possibly leading `-` token).
{==+==}
[路径][path] 中的常量参数指定用于该条目的常量值。
参数必须是为常量参数指定的类型的 [常量表达式][const expression] 。
除非它是单个路径段 ([标识符][IDENTIFIER]) 或 [字面值][literal] (可能以 `-` 令牌开头) ，否则常量表达式必须是 [块表达式][block] (用大括号括起来) 。
{==+==}


{==+==}
> **Note**: This syntactic restriction is necessary to avoid requiring
> infinite lookahead when parsing an expression inside of a type.
{==+==}
> **注意**: 这种句法限制是必要的，以避免在解析类型内的表达式时需要无限前瞻。
{==+==}


{==+==}
```rust
fn double<const N: i32>() {
    println!("doubled: {}", N * 2);
}

const SOME_CONST: i32 = 12;

fn example() {
    // Example usage of a const argument.
    double::<9>();
    double::<-123>();
    double::<{7 + 8}>();
    double::<SOME_CONST>();
    double::<{ SOME_CONST + 5 }>();
}
```
{==+==}
```rust
fn double<const N: i32>() {
    println!("doubled: {}", N * 2);
}

const SOME_CONST: i32 = 12;

fn example() {
    // const参数的使用示例。
    double::<9>();
    double::<-123>();
    double::<{7 + 8}>();
    double::<SOME_CONST>();
    double::<{ SOME_CONST + 5 }>();
}
```
{==+==}


{==+==}
When there is ambiguity if a generic argument could be resolved as either a
type or const argument, it is always resolved as a type. Placing the argument
in a block expression can force it to be interpreted as a const argument.
{==+==}
当一个泛型参数可以同时被解析为类型或常量参数时，它将总是被解析为类型参数。
将该参数放在块表达式中可以强制将其解释为常量参数。
{==+==}


{==+==}
<!-- TODO: Rewrite the paragraph above to be in terms of namespaces, once
    namespaces are introduced, and it is clear which namespace each parameter
    lives in. -->
{==+==}

{==+==}


{==+==}
```rust,compile_fail
type N = u32;
struct Foo<const N: usize>;
// The following is an error, because `N` is interpreted as the type alias `N`.
fn foo<const N: usize>() -> Foo<N> { todo!() } // ERROR
// Can be fixed by wrapping in braces to force it to be interpreted as the `N`
// const parameter:
fn bar<const N: usize>() -> Foo<{ N }> { todo!() } // ok
```
{==+==}
```rust,compile_fail
type N = u32;
struct Foo<const N: usize>;
// 以下是一个错误，因为 `N` 被解释为类型别名 `N` 。
fn foo<const N: usize>() -> Foo<N> { todo!() } // ERROR
// 可以用大括号包起来，强制解释为 `N` 常量参数。
fn bar<const N: usize>() -> Foo<{ N }> { todo!() } // ok
```
{==+==}


{==+==}
Unlike type and lifetime parameters, const parameters can be declared without
being used inside of a parameterized item, with the exception of
implementations as described in [generic implementations]:
{==+==}
与类型和生命周期参数不同，常量参数可以在不在参数化条目内部使用的情况下声明，但在实现中除外，如 [泛型实现][generic implementations] 所述：
{==+==}


{==+==}
```rust,compile_fail
// ok
struct Foo<const N: usize>;
enum Bar<const M: usize> { A, B }

// ERROR: unused parameter
struct Baz<T>;
struct Biz<'a>;
struct Unconstrained;
impl<const N: usize> Unconstrained {}
```
{==+==}

{==+==}


{==+==}
When resolving a trait bound obligation, the exhaustiveness of all
implementations of const parameters is not considered when determining if the
bound is satisfied. For example, in the following, even though all possible
const values for the `bool` type are implemented, it is still an error that
the trait bound is not satisfied:
{==+==}
在解析 trait 约束职责时，在确定约束是否满足时，不考虑所有常量参数的实现是否穷尽。
例如，即使为 `bool` 类型实现了所有可能的常量值，但仍然会出错，因为 trait 约束未满足:
{==+==}


{==+==}
```rust,compile_fail
struct Foo<const B: bool>;
trait Bar {}
impl Bar for Foo<true> {}
impl Bar for Foo<false> {}

fn needs_bar(_: impl Bar) {}
fn generic<const B: bool>() {
    let v = Foo::<B>;
    needs_bar(v); // ERROR: trait bound `Foo<B>: Bar` is not satisfied
}
```
{==+==}
```rust,compile_fail
struct Foo<const B: bool>;
trait Bar {}
impl Bar for Foo<true> {}
impl Bar for Foo<false> {}

fn needs_bar(_: impl Bar) {}
fn generic<const B: bool>() {
    let v = Foo::<B>;
    needs_bar(v); // ERROR: trait 约束 `Foo<B>: Bar` 不满足
}
```
{==+==}


{==+==}
## Where clauses
{==+==}
## Where 子句
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _WhereClause_ :\
> &nbsp;&nbsp; `where` ( _WhereClauseItem_ `,` )<sup>\*</sup> _WhereClauseItem_ <sup>?</sup>
>
> _WhereClauseItem_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _LifetimeWhereClauseItem_\
> &nbsp;&nbsp; | _TypeBoundWhereClauseItem_
>
> _LifetimeWhereClauseItem_ :\
> &nbsp;&nbsp; [_Lifetime_] `:` [_LifetimeBounds_]
>
> _TypeBoundWhereClauseItem_ :\
> &nbsp;&nbsp; [_ForLifetimes_]<sup>?</sup> [_Type_] `:` [_TypeParamBounds_]<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _Where子句_ :\
> &nbsp;&nbsp; `where` ( _Where子句条目_ `,` )<sup>\*</sup> _Where子句条目_ <sup>?</sup>
>
> _Where子句条目_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _生命周期Where子句条目_\
> &nbsp;&nbsp; | _类型约束Where子句条目_
>
> _生命周期Where子句条目_ :\
> &nbsp;&nbsp; [_生命周期_][_Lifetime_] `:` [_生命周期约束组_][_LifetimeBounds_]
>
> _类型约束Where子句条目_ :\
> &nbsp;&nbsp; [_对于生命周期组_]<sup>?</sup> [_类型_][_Type_] `:` [_类型参数约束组_][_TypeParamBounds_]<sup>?</sup>
{==+==}


{==+==}
*Where clauses* provide another way to specify bounds on type and lifetime
parameters as well as a way to specify bounds on types that aren't type
parameters.
{==+==}
*where 子句* 提供了另一种方法来指定类型和生命周期参数的约束，以及指定非类型参数的类型约束的方法。
{==+==}


{==+==}
The `for` keyword can be used to introduce [higher-ranked lifetimes]. It only
allows [_LifetimeParam_] parameters.
{==+==}
 `for` 关键字可用于引入 [更高级的生命周期][higher-ranked lifetimes] 。它仅允许 [_生命周期参数_][_LifetimeParam_] 参数。
{==+==}


{==+==}
```rust
struct A<T>
where
    T: Iterator,            // Could use A<T: Iterator> instead
    T::Item: Copy,          // Bound on an associated type
    String: PartialEq<T>,   // Bound on `String`, using the type parameter
    i32: Default,           // Allowed, but not useful
{
    f: T,
}
```
{==+==}
```rust
struct A<T>
where
    T: Iterator,            // 可以用 A<T: Iterator> 代替
    T::Item: Copy,          // 绑定在一个关联类型上
    String: PartialEq<T>,   // 绑定在 `String` 上，使用类型参数
    i32: Default,           // 允许的，但没有用
{
    f: T,
}
```
{==+==}


{==+==}
## Attributes
{==+==}
## 属性
{==+==}


{==+==}
Generic lifetime and type parameters allow [attributes] on them. There are no
built-in attributes that do anything in this position, although custom derive
attributes may give meaning to it.
{==+==}
泛型生命周期和类型参数允许在它们上面使用 [属性][attributes] 。在这个位置上没有内置属性起作用，尽管自定义派生属性可能会赋予其意义。
{==+==}


{==+==}
This example shows using a custom derive attribute to modify the meaning of a
generic parameter.
{==+==}
此示例展示使用自定义衍生属性来修改泛型参数的含义。
{==+==}


{==+==}
<!-- ignore: requires proc macro derive -->
```rust,ignore
// Assume that the derive for MyFlexibleClone declared `my_flexible_clone` as
// an attribute it understands.
#[derive(MyFlexibleClone)]
struct Foo<#[my_flexible_clone(unbounded)] H> {
    a: *const H
}
```
{==+==}
<!-- ignore: requires proc macro derive -->
```rust,ignore
// 假设 MyFlexibleClone 衍生声明了 `my_flexible_clone` 作为它所理解的一个属性。
#[derive(MyFlexibleClone)]
struct Foo<#[my_flexible_clone(unbounded)] H> {
    a: *const H
}
```
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[LIFETIME_OR_LABEL]: ../tokens.md#lifetimes-and-loop-labels
{==+==}

{==+==}


{==+==}
[_ForLifetimes_]: ../trait-bounds.md#higher-ranked-trait-bounds
[_LifetimeParam_]: #generic-parameters
[_LifetimeBounds_]: ../trait-bounds.md
[_Lifetime_]: ../trait-bounds.md
[_OuterAttribute_]: ../attributes.md
[_Type_]: ../types.md#type-expressions
[_TypeParamBounds_]: ../trait-bounds.md
{==+==}

{==+==}


{==+==}
[array repeat expression]: ../expressions/array-expr.md
[arrays]: ../types/array.md
[slices]: ../types/slice.md
[associated const]: associated-items.md#associated-constants
[associated type]: associated-items.md#associated-types
[block]: ../expressions/block-expr.md
[const contexts]: ../const_eval.md#const-context
[const expression]: ../const_eval.md#constant-expressions
[const item]: constant-items.md
[enumerations]: enumerations.md
[functions]: functions.md
[function pointers]: ../types/function-pointer.md
[generic implementations]: implementations.md#generic-implementations
[higher-ranked lifetimes]: ../trait-bounds.md#higher-ranked-trait-bounds
[implementations]: implementations.md
[item declarations]: ../statements.md#item-declarations
[item]: ../items.md
[literal]: ../expressions/literal-expr.md
[path]: ../paths.md
[path expression]: ../expressions/path-expr.md
[raw pointers]: ../types/pointer.md#raw-pointers-const-and-mut
[references]: ../types/pointer.md#shared-references-
[structs]: structs.md
[tuples]: ../types/tuple.md
[trait object]: ../types/trait-object.md
[traits]: traits.md
[type aliases]: type-aliases.md
[type]: ../types.md
[unions]: unions.md
[attributes]: ../attributes.md
{==+==}

{==+==}