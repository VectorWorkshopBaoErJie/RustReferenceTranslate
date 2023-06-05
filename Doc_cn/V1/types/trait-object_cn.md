{==+==}
# Trait objects
{==+==}
# Trait 对象
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TraitObjectType_ :\
> &nbsp;&nbsp; `dyn`<sup>?</sup> [_TypeParamBounds_]
>
> _TraitObjectTypeOneBound_ :\
> &nbsp;&nbsp; `dyn`<sup>?</sup> [_TraitBound_]
{==+==}
> **<sup>语法</sup>**\
> _Trait对象类型_ :\
> &nbsp;&nbsp; `dyn`<sup>?</sup> [_类型参数约束组_]
>
> _Trait对象类型单约束_ :\
> &nbsp;&nbsp; `dyn`<sup>?</sup> [_Trait约束_][_TraitBound_]
{==+==}


{==+==}
A *trait object* is an opaque value of another type that implements a set of
traits. The set of traits is made up of an [object safe] *base trait* plus any
number of [auto traits].

Trait objects implement the base trait, its auto traits, and any [supertraits]
of the base trait.

Trait objects are written as the keyword `dyn` followed by a set of trait
bounds, but with the following restrictions on the trait bounds. All traits
except the first trait must be auto traits, there may not be more than one
lifetime, and opt-out bounds (e.g. `?Sized`) are not allowed. Furthermore,
paths to traits may be parenthesized.

For example, given a trait `Trait`, the following are all trait objects:
{==+==}
 *trait 对象* 是另一个类型的不透明值，该类型实现了一组 trait 。trait 集由一个 [对象安全][object safe] 的 *base trait* 和任意数量的 [auto trait] 组成。

Trait 对象实现了 base trait ，它的 auto traits 以及 base trait 的任何 [父级trait][supertraits] 。

 Trait 对象写作关键字 `dyn` 后跟一组 trait 约束 ，但对 trait 约束有以下限制。
除了第一个 trait 之外的所有 trait 都必须是 auto trait ，不能有多个生命周期，并且不允许可选附加约束 (例如 `?Sized` )。
此外，路径到 traits 可以括在括号中。

例如，给定 `Trait` ，以下都是 trait 对象:
{==+==}


{==+==}
* `dyn Trait`
* `dyn Trait + Send`
* `dyn Trait + Send + Sync`
* `dyn Trait + 'static`
* `dyn Trait + Send + 'static`
* `dyn Trait +`
* `dyn 'static + Trait`.
* `dyn (Trait)`
{==+==}

{==+==}


{==+==}
> **Edition Differences**: Before the 2021 edition, the `dyn` keyword may be
> omitted.
>
> Note: For clarity, it is recommended to always use the `dyn` keyword on your
> trait objects unless your codebase supports compiling with Rust 1.26 or lower.

> **Edition Differences**: In the 2015 edition, if the first bound of the
> trait object is a path that starts with `::`, then the `dyn` will be treated
> as a part of the path. The first path can be put in parenthesis to get
> around this. As such, if you want a trait object with the trait
> `::your_module::Trait`, you should write it as `dyn (::your_module::Trait)`.
>
> Beginning in the 2018 edition, `dyn` is a true keyword and is not allowed in
> paths, so the parentheses are not necessary.
{==+==}
> **版本差异**: 在 2021 版之前，可以省略 `dyn` 关键字。
>
> 注意: 为了清晰起见，建议在你的 trait 对象上始终使用 `dyn` 关键字，除非你的代码库支持使用 Rust 1.26 或更低版本进行编译。

> **版本差异**: 在 2015 版中，如果 trait 对象的第一个限定是以 `::` 开头的路径，那么 `dyn` 将被视为路径的一部分。你可以将第一个路径放在括号中来解决这个问题。
> 因此，如果你想要一个带有 trait `::your_module::Trait` 的 trait 对象，你应该将其写为 `dyn (::your_module::Trait)` 。
>
> 从 2018 版开始， `dyn` 是正式的关键字，不允许在路径中使用，因此不需要括号。
{==+==}


{==+==}
Two trait object types alias each other if the base traits alias each other and
if the sets of auto traits are the same and the lifetime bounds are the same.
For example, `dyn Trait + Send + UnwindSafe` is the same as
`dyn Trait + UnwindSafe + Send`.

Due to the opaqueness of which concrete type the value is of, trait objects are
[dynamically sized types]. Like all
<abbr title="dynamically sized types">DSTs</abbr>, trait objects are used
behind some type of pointer; for example `&dyn SomeTrait` or
`Box<dyn SomeTrait>`. Each instance of a pointer to a trait object includes:

 - a pointer to an instance of a type `T` that implements `SomeTrait`
 - a _virtual method table_, often just called a _vtable_, which contains, for
   each method of `SomeTrait` and its [supertraits] that `T` implements, a
   pointer to `T`'s implementation (i.e. a function pointer).
{==+==}
如果 base trait 相互为别名， auto trait 集相同且生命周期约束相同，则两个 trait 对象类型为别名。例如， `dyn Trait + Send + UnwindSafe` 与 `dyn Trait + UnwindSafe + Send` 是相同的。

由于值的具体类型的不透明性，trait 对象是 [动态大小类型][dynamically sized types] 。与所有 <abbr title="dynamically sized types">DSTs</abbr> 一样，trait 对象在某种类型的指针后面使用，例如 `&dyn SomeTrait` 或 `Box<dyn SomeTrait>` 。
指向 trait 对象的指针实例包括：

- 指向实现 `SomeTrait` 的类型 `T` 的实例的指针
- 虚方法表 (通常简称为 _vtable_ ) ，它包含对于 `T` 实现的每个方法及其 [父级trait][supertraits] ，指向 `T` 的实现 (即函数指针) 的指针。
{==+==}


{==+==}
The purpose of trait objects is to permit "late binding" of methods. Calling a
method on a trait object results in virtual dispatch at runtime: that is, a
function pointer is loaded from the trait object vtable and invoked indirectly.
The actual implementation for each vtable entry can vary on an object-by-object
basis.

An example of a trait object:
{==+==}
Trait 对象的目的是允许方法的 "晚期绑定" 。在 Trait 对象上调用方法会导致运行时的虚拟调度：也就是说，函数指针从 Trait 对象的 vtable 中加载，并间接调用。每个 vtable 条目的实际实现可以在基于对象的基础上变化。

Trait 对象的一个例子：
{==+==}


{==+==}
```rust
trait Printable {
    fn stringify(&self) -> String;
}

impl Printable for i32 {
    fn stringify(&self) -> String { self.to_string() }
}

fn print(a: Box<dyn Printable>) {
    println!("{}", a.stringify());
}

fn main() {
    print(Box::new(10) as Box<dyn Printable>);
}
```
{==+==}

{==+==}


{==+==}
In this example, the trait `Printable` occurs as a trait object in both the
type signature of `print`, and the cast expression in `main`.
{==+==}
在这个例子中，trait `Printable` 出现在 `print` 函数的类型签名和 `main` 函数中的转换表达式中，表示它们都是 trait 对象。
{==+==}


{==+==}
## Trait Object Lifetime Bounds

Since a trait object can contain references, the lifetimes of those references
need to be expressed as part of the trait object. This lifetime is written as
`Trait + 'a`. There are [defaults] that allow this lifetime to usually be
inferred with a sensible choice.
{==+==}
## Trait 对象的生命周期约束

由于 Trait 对象可以包含引用，因此这些引用的生命周期需要作为 Trait 对象的一部分表示。这个生命周期的写法是 `Trait + 'a` 。有一些 [默认值][defaults] 可以允许此生命周期通常被推断为一个合理的选择。
{==+==}


{==+==}
[_TraitBound_]: ../trait-bounds.md
[_TypeParamBounds_]: ../trait-bounds.md
[auto traits]: ../special-types-and-traits.md#auto-traits
[defaults]: ../lifetime-elision.md#default-trait-object-lifetimes
[dynamically sized types]: ../dynamically-sized-types.md
[object safe]: ../items/traits.md#object-safety
[supertraits]: ../items/traits.md#supertraits
{==+==}

{==+==}