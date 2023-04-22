{==+==}
# Trait and lifetime bounds
{==+==}
# Trait 和 lifetime 约束
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TypeParamBounds_ :\
> &nbsp;&nbsp; _TypeParamBound_ ( `+` _TypeParamBound_ )<sup>\*</sup> `+`<sup>?</sup>
>
> _TypeParamBound_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _Lifetime_ | _TraitBound_
>
> _TraitBound_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `?`<sup>?</sup>
> [_ForLifetimes_](#higher-ranked-trait-bounds)<sup>?</sup> [_TypePath_]\
> &nbsp;&nbsp; | `(` `?`<sup>?</sup>
> [_ForLifetimes_](#higher-ranked-trait-bounds)<sup>?</sup> [_TypePath_] `)`
>
> _LifetimeBounds_ :\
> &nbsp;&nbsp; ( _Lifetime_ `+` )<sup>\*</sup> _Lifetime_<sup>?</sup>
>
> _Lifetime_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [LIFETIME_OR_LABEL]\
> &nbsp;&nbsp; | `'static`\
> &nbsp;&nbsp; | `'_`
{==+==}
> **<sup>语法</sup>**\
> _类型参数约束组_ :\
> &nbsp;&nbsp; _类型参数约束_ ( `+` _类型参数约束_ )<sup>\*</sup> `+`<sup>?</sup>
>
> _类型参数约束_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _生命周期_ | _Trait约束_
>
> _Trait约束_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `?`<sup>?</sup>
> [_For生命周期_](#higher-ranked-trait-bounds)<sup>?</sup> [_类型路径_][_TypePath_]\
> &nbsp;&nbsp; | `(` `?`<sup>?</sup>
> [_For生命周期_](#higher-ranked-trait-bounds)<sup>?</sup> [_类型路径_][_TypePath_] `)`
>
> _生命周期约束_ :\
> &nbsp;&nbsp; ( _生命周期_ `+` )<sup>\*</sup> _生命周期_<sup>?</sup>
>
> _生命周期_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [生命周期或标签][LIFETIME_OR_LABEL]\
> &nbsp;&nbsp; | `'static`\
> &nbsp;&nbsp; | `'_`
{==+==}


{==+==}
[Trait] and lifetime bounds provide a way for [generic items][generic] to
restrict which types and lifetimes are used as their parameters. Bounds can be
provided on any type in a [where clause]. There are also shorter forms for
certain common cases:

* Bounds written after declaring a [generic parameter][generic]:
  `fn f<A: Copy>() {}` is the same as `fn f<A>() where A: Copy {}`.
* In trait declarations as [supertraits]: `trait Circle : Shape {}` is
  equivalent to `trait Circle where Self : Shape {}`.
* In trait declarations as bounds on [associated types]:
  `trait A { type B: Copy; }` is equivalent to
  `trait A where Self::B: Copy { type B; }`.

Bounds on an item must be satisfied when using the item. When type checking and
borrow checking a generic item, the bounds can be used to determine that a
trait is implemented for a type. For example, given `Ty: Trait`

* In the body of a generic function, methods from `Trait` can be called on `Ty`
  values. Likewise associated constants on the `Trait` can be used.
* Associated types from `Trait` can be used.
* Generic functions and types with a `T: Trait` bounds can be used with `Ty`
  being used for `T`.
{==+==}
[Trait] 和生命周期约束提供了一种方式，让 [泛型条目][generic] 可以限制使用哪些类型和生命周期作为它们的参数。
约束可以在 [where 子句][where clause] 中对任何类型提供。对于某些常见情况，也有更短的形式：

* 在声明 [泛型参数][generic] 之后写入约束: `fn f<A: Copy>() {}` 等同于 `fn f<A>() where A: Copy {}` 。
* 在 trait 声明中作为 [supertraits] : `trait Circle : Shape {}` 等同于 `trait Circle where Self : Shape {}` 。
* 在 trait 声明中作为 [关联类型][associated types] 的约束: `trait A { type B: Copy; }` 等同于 `trait A where Self::B: Copy { type B; }` 。

在使用条目时必须满足条目上的约束。在对泛型条目进行类型检查和借用检查时，可以使用约束来确定类型是否实现了 trait 。例如，给定 `Ty: Trait` 

* 在泛型函数的主体中，可以在 `Ty` 值上调用 `Trait` 的方法。同样，可以使用 `Trait` 上的关联常量。
* 可以使用 `Trait` 的关联类型。
* 可以将具有 `T: Trait` 约束的泛型函数和类型用于 `T` 使用 `Ty` 。
{==+==}


{==+==}
```rust
# type Surface = i32;
trait Shape {
    fn draw(&self, surface: Surface);
    fn name() -> &'static str;
}

fn draw_twice<T: Shape>(surface: Surface, sh: T) {
    sh.draw(surface);           // Can call method because T: Shape
    sh.draw(surface);
}

fn copy_and_draw_twice<T: Copy>(surface: Surface, sh: T) where T: Shape {
    let shape_copy = sh;        // doesn't move sh because T: Copy
    draw_twice(surface, sh);    // Can use generic function because T: Shape
}

struct Figure<S: Shape>(S, S);

fn name_figure<U: Shape>(
    figure: Figure<U>,          // Type Figure<U> is well-formed because U: Shape
) {
    println!(
        "Figure of two {}",
        U::name(),              // Can use associated function
    );
}
```
{==+==}
```rust
# type Surface = i32;
trait Shape {
    fn draw(&self, surface: Surface);
    fn name() -> &'static str;
}

fn draw_twice<T: Shape>(surface: Surface, sh: T) {
    sh.draw(surface);           // 可以调用方法，因为T: Shape
    sh.draw(surface);
}

fn copy_and_draw_twice<T: Copy>(surface: Surface, sh: T) where T: Shape {
    let shape_copy = sh;        // 不移动 sh ，因为T: Copy
    draw_twice(surface, sh);    // 可以使用泛型函数，因为T: Shape
}

struct Figure<S: Shape>(S, S);

fn name_figure<U: Shape>(
    figure: Figure<U>,          // 类型 Figure<U> 是 well-formed ，因为U: Shape
) {
    println!(
        "Figure of two {}",
        U::name(),              // 可以使用关联函数
    );
}
```
{==+==}


{==+==}
Bounds that don't use the item's parameters or [higher-ranked lifetimes] are checked when the item is defined.
It is an error for such a bound to be false.

[`Copy`], [`Clone`], and [`Sized`] bounds are also checked for certain generic types when using the item, even if the use does not provide a concrete type.
It is an error to have `Copy` or `Clone` as a bound on a mutable reference, [trait object], or [slice].
It is an error to have `Sized` as a bound on a trait object or slice.
{==+==}
不涉及该条目参数或 [高阶生命周期][higher-ranked lifetimes] 的约束在定义该条目时进行检查。如果这样的约束为假，就会出现错误。

对于某些泛型类型，即使使用时不提供具体类型，也会对 [`Copy`] ， [`Clone`] 和 [`Sized`] 约束进行检查。
在可变引用， [trait 对象][trait object] 或 [slice] 上使用 `Copy` 或 `Clone`  作为约束是错误的。在 [trait 对象][trait object] 或 [切片][slice] 上使用 `Sized` 作为限制也是错误的。
{==+==}


{==+==}
```rust,compile_fail
struct A<'a, T>
where
    i32: Default,           // Allowed, but not useful
    i32: Iterator,          // Error: `i32` is not an iterator
    &'a mut T: Copy,        // (at use) Error: the trait bound is not satisfied
    [T]: Sized,             // (at use) Error: size cannot be known at compilation
{
    f: &'a T,
}
struct UsesA<'a, T>(A<'a, T>);
```
{==+==}
```rust,compile_fail
struct A<'a, T>
where
    i32: Default,           // 允许，但不实用
    i32: Iterator,          // 错误： `i32` 不是迭代器
    &'a mut T: Copy,        // (在使用时) 错误：无法满足 trait 约束
    [T]: Sized,             // (在使用时) 错误：大小无法在编译时确定
{
    f: &'a T,
}
struct UsesA<'a, T>(A<'a, T>);
```
{==+==}


{==+==}
Trait and lifetime bounds are also used to name [trait objects].
{==+==}
Trait 和 生命周期限制还用于命名 [trait 对象][trait objects] 。
{==+==}


{==+==}
## `?Sized`

`?` is only used to relax the implicit [`Sized`] trait bound for [type parameters] or [associated types].
`?Sized` may not be used as a bound for other types.

## Lifetime bounds

Lifetime bounds can be applied to types or to other lifetimes.
The bound `'a: 'b` is usually read as `'a` *outlives* `'b`.
`'a: 'b` means that `'a` lasts at least as long as `'b`, so a reference `&'a ()` is valid whenever `&'b ()` is valid.
{==+==}
## `?Sized`

`?` 仅用于为 [类型参数][type parameters] 或 [关联类型][associated types] 放宽隐含的 [`Sized`] trait 约束。 `?Sized` 不能用作其他类型的约束。

## 生命周期约束

生命周期限制可以应用于类型或其他生命周期。约束 `'a: 'b` 通常被读作 `'a` *比* `'b` 活得更久。
`'a: 'b` 意味着 `'a` 至少和 `'b` 一样长，因此当 `&'b ()` 有效时，引用 `&'a ()` 也是有效的。
{==+==}


{==+==}
```rust
fn f<'a, 'b>(x: &'a i32, mut y: &'b i32) where 'a: 'b {
    y = x;                      // &'a i32 is a subtype of &'b i32 because 'a: 'b
    let r: &'b &'a i32 = &&0;   // &'b &'a i32 is well formed because 'a: 'b
}
```
{==+==}
```rust
fn f<'a, 'b>(x: &'a i32, mut y: &'b i32) where 'a: 'b {
    y = x;                      // `&'a i32` 是 `&'b i32` 的子类型，因为 `'a: 'b` 。
    let r: &'b &'a i32 = &&0;   // `&'b &'a i32` 是良好形式的，因为 `'a: 'b` 。
}
```
{==+==}


{==+==}
`T: 'a` means that all lifetime parameters of `T` outlive `'a`.
For example, if `'a` is an unconstrained lifetime parameter, then `i32: 'static` and `&'static str: 'a` are satisfied, but `Vec<&'a ()>: 'static` is not.
{==+==}
`T: 'a` 表示 `T` 的所有生命周期参数都比 `'a` 更长。
例如，如果 `'a` 是一个未约束的生命周期参数，那么 `i32: 'static` 和 `&'static str: 'a` 是符合条件的，但是 `Vec<&'a ()>: 'static` 不符合条件。
{==+==}


{==+==}
## Higher-ranked trait bounds

> _ForLifetimes_ :\
> &nbsp;&nbsp; `for` [_GenericParams_]

Trait bounds may be *higher ranked* over lifetimes. These bounds specify a bound
that is true *for all* lifetimes. For example, a bound such as `for<'a> &'a T:
PartialEq<i32>` would require an implementation like
{==+==}
## 高阶 trait 约束

> _For生命周期_ :\
> &nbsp;&nbsp; `for` [_泛型参数组_][_GenericParams_]

Trait 约束可以对生命周期进行 *提阶* ，这些约束指定了一个对于 *所有* 生命周期都成立的约束。
例如， `for<'a> &'a T: PartialEq<i32>` 这样的约束需要一个这样的实现:
{==+==}


{==+==}
```rust
# struct T;
impl<'a> PartialEq<i32> for &'a T {
    // ...
#    fn eq(&self, other: &i32) -> bool {true}
}
```
{==+==}

{==+==}


{==+==}
and could then be used to compare a `&'a T` with any lifetime to an `i32`.

Only a higher-ranked bound can be used here, because the lifetime of the reference is shorter than any possible lifetime parameter on the function:
{==+==}
可以用它来将一个 `&'a T` 的生命周期与任意的 `i32` 进行比较。

只有一个更高阶的约束可以在这里使用，因为引用的生命周期比函数上可能存在的任何生命周期参数都要短：
{==+==}


{==+==}
```rust
fn call_on_ref_zero<F>(f: F) where for<'a> F: Fn(&'a i32) {
    let zero = 0;
    f(&zero);
}
```
{==+==}

{==+==}


{==+==}
Higher-ranked lifetimes may also be specified just before the trait: the only
difference is the scope of the lifetime parameter, which extends only to the
end of the following trait instead of the whole bound. This function is
equivalent to the last one.
{==+==}
更高阶的生命周期也可以在 trait 前面指定: 唯一的区别是生命周期参数的作用范围仅限于后面 trait 的末尾，而不是整个约束。这个函数与上一个函数是等价的。
{==+==}


{==+==}
```rust
fn call_on_ref_zero<F>(f: F) where F: for<'a> Fn(&'a i32) {
    let zero = 0;
    f(&zero);
}
```
{==+==}

{==+==}


{==+==}
[LIFETIME_OR_LABEL]: tokens.md#lifetimes-and-loop-labels
[_GenericParams_]: items/generics.md
[_TypePath_]: paths.md#paths-in-types
[`Clone`]: special-types-and-traits.md#clone
[`Copy`]: special-types-and-traits.md#copy
[`Sized`]: special-types-and-traits.md#sized

[arrays]: types/array.md
[associated types]: items/associated-items.md#associated-types
[supertraits]: items/traits.md#supertraits
[generic]: items/generics.md
[higher-ranked lifetimes]: #higher-ranked-trait-bounds
[slice]: types/slice.md
[Trait]: items/traits.md#trait-bounds
[trait object]: types/trait-object.md
[trait objects]: types/trait-object.md
[type parameters]: types/parameters.md
[where clause]: items/generics.md#where-clauses
{==+==}

{==+==}