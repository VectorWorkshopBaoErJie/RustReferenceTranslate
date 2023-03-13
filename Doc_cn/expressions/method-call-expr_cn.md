{==+==}
# Method-call expressions
{==+==}
# 方法调用表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MethodCallExpression_ :\
> &nbsp;&nbsp; [_Expression_] `.` [_PathExprSegment_] `(`[_CallParams_]<sup>?</sup> `)`
{==+==}

{==+==}


{==+==}
A _method call_ consists of an expression (the *receiver*) followed by a single dot, an expression path segment, and a parenthesized expression-list.
Method calls are resolved to associated [methods] on specific traits, either statically dispatching to a method if the exact `self`-type of the left-hand-side is known, or dynamically dispatching if the left-hand-side expression is an indirect [trait object](../types/trait-object.md).
{==+==}
一个 _方法调用_ 由一个表达式 (即 *接收者* ) 加上一个点 ( `.` ) 、一个表达式路径段和一个带括号的表达式列表组成。
方法调用会解析到特定 trait 上关联的方法，如果左侧表达式的确切 `self` 类型已知，则静态地调度到方法，否则，如果左侧表达式是一个间接的 trait 对象，则动态地调度。
{==+==}


{==+==}
```rust
let pi: Result<f32, _> = "3.14".parse();
let log_pi = pi.unwrap_or(1.0).log(2.72);
# assert!(1.14 < log_pi && log_pi < 1.15)
```
{==+==}

{==+==}


{==+==}
When looking up a method call, the receiver may be automatically dereferenced or borrowed in order to call a method.
This requires a more complex lookup process than for other functions, since there may be a number of possible methods to call.
The following procedure is used:

The first step is to build a list of candidate receiver types.
Obtain these by repeatedly [dereferencing][dereference] the receiver expression's type, adding each type encountered to the list, then finally attempting an [unsized coercion] at the end, and adding the result type if that is successful.
Then, for each candidate `T`, add `&T` and `&mut T` to the list immediately after `T`.

For instance, if the receiver has type `Box<[i32;2]>`, then the candidate types will be `Box<[i32;2]>`, `&Box<[i32;2]>`, `&mut Box<[i32;2]>`, `[i32; 2]` (by dereferencing), `&[i32; 2]`, `&mut [i32; 2]`, `[i32]` (by unsized coercion), `&[i32]`, and finally `&mut [i32]`.

Then, for each candidate type `T`, search for a [visible] method with a receiver of that type in the following places:

1. `T`'s inherent methods (methods implemented directly on `T`).
1. Any of the methods provided by a [visible] trait implemented by `T`.
   If `T` is a type parameter, methods provided by trait bounds on `T` are looked up first.
   Then all remaining methods in scope are looked up.
{==+==}
在查找方法调用时，为了调用方法，接收者可能会自动解引用或借用。与其他函数相比，这需要更复杂的查找过程，因为可能有许多可能要调用的方法。使用以下过程:

第一步是构建候选接收者类型列表。通过重复 [解引用][dereference] 接收者表达式的类型，将遇到的每种类型添加到列表中，然后在最后尝试 [不可缩放强制转换][unsized coercion] ，如果成功，则添加结果类型。然后，对于每个候选项 `T` ，在 `T` 之后立即添加 `&T` 和 `&mut T` 。

例如，如果接收者的类型为 `Box<[i32;2]>` ，则候选类型将是 `Box<[i32;2]>` 、 `&Box<[i32;2]>` 、 `&mut Box<[i32;2]>` 、 `[i32;2]` (通过解引用) 、 `&[i32;2]` 、`&mut [i32;2]` 、 `[i32]` （通过不可缩放强制转换）、 `&[i32]` 和最后的 `&mut [i32]` 。

然后，对于每个候选类型 `T` ，在以下位置搜索具有该类型接收者的 [可见][visible] 方法:

1. `T` 的内部方法 (直接在 `T` 上实现的方法)。
2. 由 `T` 实现的 [可见][visible] trait 提供的任何方法。如果 `T` 是类型参数，则首先查找 `T` 的 trait 约束提供的方法，然后查找所有剩余的作用域内方法。
{==+==}


{==+==}
> Note: the lookup is done for each type in order, which can occasionally lead to surprising results.
> The below code will print "In trait impl!", because `&self` methods are looked up first, the trait method is found before the struct's `&mut self` method is found.
{==+==}
> 注意: 查找是按顺序对每种类型进行的，这可能偶尔会导致令人惊讶的结果。
> 下面的代码将打印 "In trait impl!" ，因为 `&self` 方法首先被查找，因此在找到结构体的 `&mut self` 方法之前就找到了 trait 方法。
{==+==}


{==+==}
> ```rust
> struct Foo {}
>
> trait Bar {
>   fn bar(&self);
> }
>
> impl Foo {
>   fn bar(&mut self) {
>     println!("In struct impl!")
>   }
> }
>
> impl Bar for Foo {
>   fn bar(&self) {
>     println!("In trait impl!")
>   }
> }
>
> fn main() {
>   let mut f = Foo{};
>   f.bar();
> }
> ```
{==+==}

{==+==}


{==+==}
If this results in multiple possible candidates, then it is an error, and the receiver must be [converted][disambiguate call] to an appropriate receiver type to make the method call.

This process does not take into account the mutability or lifetime of the receiver, or whether a method is `unsafe`.
Once a method is looked up, if it can't be called for one (or more) of those reasons, the result is a compiler error.

If a step is reached where there is more than one possible method, such as where generic methods or traits are considered the same, then it is a compiler error.
These cases require a [disambiguating function call syntax] for method and function invocation.

> **Edition Differences**: Before the 2021 edition, during the search for visible methods, if the candidate receiver type is an [array type], methods provided by the standard library [`IntoIterator`] trait are ignored.
>
> The edition used for this purpose is determined by the token representing the method name.
>
> This special case may be removed in the future.
{==+==}
如果这个过程导致有多个可能的候选方法，那么就会出现一个错误，接收者必须被转换为适当的接收者类型才能调用该方法。

这个过程不考虑接收者的可变性或生命周期，也不考虑方法是否是 `unsafe` 。一旦找到一个方法，如果出于其中一个或多个原因无法调用，结果就是编译器错误。

如果达到了一个步骤，其中有多个可能的方法，比如在考虑通用方法或 trait 时，那么这就是编译器错误。这些情况需要使用 [消除歧义的函数调用语法][disambiguating function call syntax] 来调用方法和函数。

> **版本差异**: 在 2021 版本之前，寻找可见方法时，如果候选接收者类型是 [数组类型][array type] ，则忽略标准库 [`IntoIterator`] trait 提供的方法。
> 
> 用于此目的的版本由表示方法名称的令牌确定。
> 
> 这种特殊情况可能会在将来被删除。
{==+==}


{==+==}
***Warning:*** For [trait objects], if there is an inherent method of the same name as a trait method, it will give a compiler error when trying to call the method in a method call expression.
Instead, you can call the method using [disambiguating function call syntax], in which case it calls the trait method, not the inherent method.
There is no way to call the inherent method.
Just don't define inherent methods on trait objects with the same name as a trait method and you'll be fine.
{==+==}
***警告:*** 对于 [trait对象][trait objects] ，如果存在与 trait 方法同名的内部方法，在方法调用表达式中尝试调用该方法会导致编译器错误。相反，可以使用 [消除歧义的函数调用语法][disambiguating function call syntax] 来调用该方法，这样会调用 trait 方法而不是内部方法。没有方法可以调用内部方法。不要在具有与trait方法同名的内部方法的trait对象上定义内部方法，这样就不会出现问题。
{==+==}


{==+==}
[_CallParams_]: call-expr.md
[_Expression_]: ../expressions.md
[_PathExprSegment_]: ../paths.md#paths-in-expressions
[visible]: ../visibility-and-privacy.md
[array type]: ../types/array.md
[trait objects]: ../types/trait-object.md
[disambiguate call]: call-expr.md#disambiguating-function-calls
[disambiguating function call syntax]: call-expr.md#disambiguating-function-calls
[dereference]: operator-expr.md#the-dereference-operator
[methods]: ../items/associated-items.md#methods
[unsized coercion]: ../type-coercions.md#unsized-coercions
[`IntoIterator`]: ../../std/iter/trait.IntoIterator.html
{==+==}

{==+==}