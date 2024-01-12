{==+==}
# Impl trait
{==+==}

{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ImplTraitType_ : `impl` [_TypeParamBounds_]
>
> _ImplTraitTypeOneBound_ : `impl` [_TraitBound_]
{==+==}
> **<sup>语法</sup>**\
> _ImplTrait类型_ : `impl` [_类型参数约束组_][_TypeParamBounds_]
>
> _ImplTrait类型单约束_ : `impl` [_Trait约束_][_TraitBound_]
{==+==}


{==+==}
`impl Trait` provides ways to specify unnamed but concrete types that
implement a specific trait.
It can appear in two sorts of places: argument position (where it can act as an anonymous type parameter to functions), and return position (where it can act as an abstract return type).
{==+==}
`impl Trait` 提供了一种方式来指定匿名但具体实现了特定 trait 的类型。
它可以出现在两种位置: 一是参数位置 (作为函数的匿名类型参数) 二是返回位置 (作为抽象返回类型) 。
{==+==}


{==+==}
```rust
trait Trait {}
# impl Trait for () {}

// argument position: anonymous type parameter
fn foo(arg: impl Trait) {
}

// return position: abstract return type
fn bar() -> impl Trait {
}
```
{==+==}
```rust
trait Trait {}
# impl Trait for () {}

// 参数位置: 匿名类型参数
fn foo(arg: impl Trait) {
}

// 返回值位置: 抽象返回类型
fn bar() -> impl Trait {
}
```
{==+==}


{==+==}
## Anonymous type parameters

> Note: This is often called "impl Trait in argument position".
(The term "parameter" is more correct here, but "impl Trait in argument position" is the phrasing used during the development of this feature, and it remains in parts of the implementation.)

Functions can use `impl` followed by a set of trait bounds to declare a parameter as having an anonymous type.
The caller must provide a type that satisfies the bounds declared by the anonymous type parameter, and the function can only use the methods available through the trait bounds of the anonymous type parameter.

For example, these two forms are almost equivalent:
{==+==}
## 匿名类型参数

> 注意: 这经常被称为 "参数位置的impl Trait"。 (这里更准确的术语是 "参数"，但 "参数位置的impl Trait" 是这个特性开发期间使用的措辞，而且它仍然在实现的某些部分中保留着。)

函数可以使用 `impl` ，后面跟一组 trait 约束，来声明一个参数为匿名类型。调用方必须提供一个满足匿名类型参数所声明的 trait 约束的类型，而函数只能使用匿名类型参数的 trait 约束可用的方法。

例如，以下这两种形式几乎是等价的:
{==+==}


{==+==}
```rust,ignore
trait Trait {}

// generic type parameter
fn foo<T: Trait>(arg: T) {
}

// impl Trait in argument position
fn foo(arg: impl Trait) {
}
```
{==+==}
```rust,ignore
trait Trait {}

// 泛型类型参数
fn foo<T: Trait>(arg: T) {
}

// impl Trait 作为函数参数的匿名类型参数
fn foo(arg: impl Trait) {
}
```
{==+==}


{==+==}
That is, `impl Trait` in argument position is syntactic sugar for a generic type parameter like `<T: Trait>`, except that the type is anonymous and doesn't appear in the [_GenericParams_] list.

> **Note:**
> For function parameters, generic type parameters and `impl Trait` are not exactly equivalent.
> With a generic parameter such as `<T: Trait>`, the caller has the option to explicitly specify the generic argument for `T` at the call site using [_GenericArgs_], for example, `foo::<usize>(1)`.
> If `impl Trait` is the type of *any* function parameter, then the caller can't ever provide any generic arguments when calling that function.
This includes generic arguments for the return type or any const generics.
>
> Therefore, changing the function signature from either one to the other can constitute a breaking change for the callers of a function.
{==+==}
换句话说， `impl Trait` 作为函数参数是泛型类型参数 `<T: Trait>` 的语法糖，只不过该类型是匿名的并且不出现在 [_泛型参数组_][_GenericParams_] 列表中。

> **注意：**
> 对于函数参数而言，泛型类型参数和 `impl Trait` 并不完全等效。
> 对于泛型参数如 `<T: Trait>` ，调用方可以在调用时使用 [_泛型实参组_][_GenericArgs_] 显式指定 `T` 的泛型参数，例如 `foo::<usize>(1)` 。
> 如果 `impl Trait` 是 *任意* 函数参数的类型，那么调用方在调用该函数时不能提供任何泛型参数。
> 这包括返回类型或任何常量泛型参数。
>
> 因此，从其中一个函数签名更改到另一个函数签名可能会对函数的调用方构成破坏性更改。
{==+==}


{==+==}
## Abstract return types

> Note: This is often called "impl Trait in return position".

Functions can use `impl Trait` to return an abstract return type.
These types stand in for another concrete type where the caller may only use the methods declared by the specified `Trait`.
Each possible return value from the function must resolve to the same concrete type.

`impl Trait` in return position allows a function to return an unboxed abstract type.
This is particularly useful with [closures] and iterators.
For example, closures have a unique, un-writable type.
Previously, the only way to return a closure from a function was to use a [trait object]:
{==+==}
## 抽象返回类型

> 注意: 这通常被称为 "返回位置的 impl Trait" 。

函数可以使用 `impl Trait` 返回一个抽象返回类型。这些类型可以代表另一个具体类型，其中调用者只能使用指定 `Trait` 声明的方法。
函数的每个可能的返回值必须解析为相同的具体类型。返回位置的 `impl Trait` 允许函数返回未装箱的抽象类型。
这在处理 [闭包][closures] 和迭代器时特别有用。

例如，闭包具有独特的、无法重写的类型。以前，从函数返回闭包的唯一方法是使用 [trait 对象][trait object] ：
{==+==}


{==+==}
```rust
fn returns_closure() -> Box<dyn Fn(i32) -> i32> {
    Box::new(|x| x + 1)
}
```
{==+==}

{==+==}


{==+==}
This could incur performance penalties from heap allocation and dynamic dispatch.
It wasn't possible to fully specify the type of the closure, only to use the `Fn` trait.
That means that the trait object is necessary.
However, with `impl Trait`, it is possible to write this more simply:
{==+==}
这可能会带来堆分配和动态派发的性能损失。
无法完全指定闭包的类型，只能使用 `Fn` trait，意味着必需用 trait 对象。
但是，使用 `impl Trait` 可以更简单实现功能:
{==+==}


{==+==}
```rust
fn returns_closure() -> impl Fn(i32) -> i32 {
    |x| x + 1
}
```
{==+==}

{==+==}


{==+==}
which also avoids the drawbacks of using a boxed trait object.

Similarly, the concrete types of iterators could become very complex, incorporating the types of all previous iterators in a chain.
Returning `impl Iterator` means that a function only exposes the `Iterator` trait as a bound on its return type, instead of explicitly specifying all of the other iterator types involved.
{==+==}
这也避免了使用装箱 trait 对象的缺点。

同样，迭代器的具体类型可能变得非常复杂，包含了链中所有先前迭代器的类型。
返回 `impl Iterator` 意味着函数只公开 `Iterator` trait 作为其返回类型的约束，而不是明确指定涉及的所有其他迭代器类型。
{==+==}


{==+==}
### Differences between generics and `impl Trait` in return position

In argument position, `impl Trait` is very similar in semantics to a generic type parameter.
However, there are significant differences between the two in return position.
With `impl Trait`, unlike with a generic type parameter, the function chooses the return type, and the caller cannot choose the return type.

The function:
{==+==}
### 泛型和 `impl Trait` 在返回位置的差异

在参数位置上， `impl Trait` 在语义上与泛型类型参数非常相似。
然而，在返回位置上，两者之间存在显著的差异。
使用 `impl Trait` ，与泛型类型参数不同，函数选择返回类型，调用者无法选择返回类型。

该函数：
{==+==}


{==+==}
```rust,ignore
fn foo<T: Trait>() -> T {
```
{==+==}

{==+==}


{==+==}
allows the caller to determine the return type, `T`, and the function returns that type.

The function:
{==+==}
允许调用者确定返回类型 `T` ，并且函数返回该类型。

该函数：
{==+==}


{==+==}
```rust,ignore
fn foo() -> impl Trait {
```
{==+==}

{==+==}


{==+==}
doesn't allow the caller to determine the return type.
Instead, the function chooses the return type, but only promises that it will implement `Trait`.

## Limitations

`impl Trait` can only appear as a parameter or return type of a free or inherent function.
It cannot appear inside implementations of traits, nor can it be the type of a let binding or appear inside a type alias.
{==+==}
不允许调用者确定返回类型。而是函数选择返回类型，但只承诺将实现 `Trait` 。

## 限制

`impl Trait` 只能出现在自由函数或内部函数的参数或返回类型中。不能出现在 trait 的实现内部，也不能是 let 绑定的类型或出现在类型别名中。
{==+==}


{==+==}
[closures]: closure.md
[_GenericArgs_]: ../paths.md#paths-in-expressions
[_GenericParams_]: ../items/generics.md
[_TraitBound_]: ../trait-bounds.md
[trait object]: trait-object.md
[_TypeParamBounds_]: ../trait-bounds.md
{==+==}

{==+==}