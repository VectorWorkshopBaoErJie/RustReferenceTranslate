{==+==}
# Static items
{==+==}
# 静态条目
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _StaticItem_ :\
> &nbsp;&nbsp; `static` `mut`<sup>?</sup> [IDENTIFIER] `:` [_Type_]
>              ( `=` [_Expression_] )<sup>?</sup> `;`
{==+==}

{==+==}


{==+==}
A *static item* is similar to a [constant], except that it represents a precise
memory location in the program. All references to the static refer to the same
memory location. Static items have the `static` lifetime, which outlives all
other lifetimes in a Rust program. Static items do not call [`drop`] at the
end of the program.
{==+==}
 *静态条目* 与 [常量][constant] 类似，不同之处在于它表示程序中精确的内存位置。
 所有对静态条目的引用都指向同一内存位置。静态条目具有 `static` 生命周期，其生命周期超过 Rust 程序中的所有其他生命周期。静态条目在程序结束时不会调用 [`drop`] 函数。
{==+==}


{==+==}
The static initializer is a [constant expression] evaluated at compile time.
Static initializers may refer to other statics.
{==+==}
静态初始化器是一个在编译时计算的 [常量表达式][constant expression] 。
静态初始化器可能会引用其他的静态。
{==+==}


{==+==}
Non-`mut` static items that contain a type that is not [interior mutable] may
be placed in read-only memory.
{==+==}
非 `mut` 的静态条目，如果包含的类型不是 [内部可变类型][interior mutable] ，则可以被放置在只读内存中。
{==+==}


{==+==}
All access to a static is safe, but there are a number of restrictions on
statics:
{==+==}
所有对静态的访问都是安全的，但是对静态有一些限制:
{==+==}


{==+==}
* The type must have the `Sync` trait bound to allow thread-safe access.
* Constants cannot refer to statics.
{==+==}
* 该类型必须有 `Sync` trait ，以允许线程安全的访问。
* 常量不能引用静态。
{==+==}


{==+==}
The initializer expression must be omitted in an [external block], and must be
provided for free static items.
{==+==}
在 [外部块][external block] 中必须省略静态条目的初始化表达式，并且必须为自由静态条目提供初始化表达式。
{==+==}


{==+==}
## Statics & generics
{==+==}
## 静态 & 泛型
{==+==}


{==+==}
A static item defined in a generic scope (for example in a blanket or default
implementation) will result in exactly one static item being defined, as if
the static definition was pulled out of the current scope into the module.
There will *not* be one item per monomorphization.
{==+==}
在泛型作用域中定义的静态条目（例如在一个泛型实现的默认实现中）将恰好定义一个静态条目，就好像静态定义从当前作用域被提取到模块中一样。
不会为每个单态化生成一个静态条目。
{==+==}


{==+==}
This code:
{==+==}
代码：
{==+==}


{==+==}
```rust
use std::sync::atomic::{AtomicUsize, Ordering};

trait Tr {
    fn default_impl() {
        static COUNTER: AtomicUsize = AtomicUsize::new(0);
        println!("default_impl: counter was {}", COUNTER.fetch_add(1, Ordering::Relaxed));
    }

    fn blanket_impl();
}

struct Ty1 {}
struct Ty2 {}

impl<T> Tr for T {
    fn blanket_impl() {
        static COUNTER: AtomicUsize = AtomicUsize::new(0);
        println!("blanket_impl: counter was {}", COUNTER.fetch_add(1, Ordering::Relaxed));
    }
}

fn main() {
    <Ty1 as Tr>::default_impl();
    <Ty2 as Tr>::default_impl();
    <Ty1 as Tr>::blanket_impl();
    <Ty2 as Tr>::blanket_impl();
}
```
{==+==}

{==+==}


{==+==}
prints

```text
default_impl: counter was 0
default_impl: counter was 1
blanket_impl: counter was 0
blanket_impl: counter was 1
```
{==+==}

{==+==}


{==+==}
## Mutable statics
{==+==}
## 可变静态
{==+==}


{==+==}
If a static item is declared with the `mut` keyword, then it is allowed to be
modified by the program. One of Rust's goals is to make concurrency bugs hard
to run into, and this is obviously a very large source of race conditions or
other bugs. For this reason, an `unsafe` block is required when either reading
or writing a mutable static variable. Care should be taken to ensure that
modifications to a mutable static are safe with respect to other threads
running in the same process.
{==+==}
如果静态条目用 `mut` 关键字声明，那么就程序允许修改它。
Rust 的一个目标是难以出现并发错误，这显然是导致竞态条件或其他错误的一个很大源头。
因此，在读取或写入可变静态变量时，需要使用 `unsafe` 块。
应该注意确保对可变静态变量的修改，对与运行在同一进程中的其他线程方面是安全的。
{==+==}


{==+==}
Mutable statics are still very useful, however. They can be used with C
libraries and can also be bound from C libraries in an `extern` block.
{==+==}
可变静态变量仍然非常有用。它们可以与 C 语言库一起使用，并且还可以在 `extern` 块中绑定来自 C 语言的库。
{==+==}


{==+==}
```rust
# fn atomic_add(_: &mut u32, _: u32) -> u32 { 2 }

static mut LEVELS: u32 = 0;

// This violates the idea of no shared state, and this doesn't internally
// protect against races, so this function is `unsafe`
unsafe fn bump_levels_unsafe1() -> u32 {
    let ret = LEVELS;
    LEVELS += 1;
    return ret;
}

// Assuming that we have an atomic_add function which returns the old value,
// this function is "safe" but the meaning of the return value may not be what
// callers expect, so it's still marked as `unsafe`
unsafe fn bump_levels_unsafe2() -> u32 {
    return atomic_add(&mut LEVELS, 1);
}
```
{==+==}
```rust
# fn atomic_add(_: &mut u32, _: u32) -> u32 { 2 }

static mut LEVELS: u32 = 0;

// 这违反了没有共享状态的理念，而且这在内部也不能防止竞争，所以这个函数是 `unsafe` 的。
unsafe fn bump_levels_unsafe1() -> u32 {
    let ret = LEVELS;
    LEVELS += 1;
    return ret;
}

// 假设我们有一个 atomic_add 函数，返回旧值，
// 这个函数是 "safe" ，但是返回值的含义可能不是调用者所期望的，所以它仍然被标记为 `unsafe` 。
unsafe fn bump_levels_unsafe2() -> u32 {
    return atomic_add(&mut LEVELS, 1);
}
```
{==+==}


{==+==}
Mutable statics have the same restrictions as normal statics, except that the
type does not have to implement the `Sync` trait.
{==+==}
可变静态变量与普通静态变量具有相同的限制，只是类型不必实现 `Sync` trait。
{==+==}


{==+==}
## Using Statics or Consts
{==+==}
## 使用静态或常量
{==+==}


{==+==}
It can be confusing whether or not you should use a constant item or a static
item. Constants should, in general, be preferred over statics unless one of the
following are true:
{==+==}
在选择使用常量条目或静态条目时可能会感到困惑。
通常情况下，应该优先使用常量而非静态条目，除非以下情况之一:
{==+==}


{==+==}
* Large amounts of data are being stored
* The single-address property of statics is required.
* Interior mutability is required.
{==+==}
* 需要存储大量的数据
* 需要静态的单地址特性。
* 需要内部可变性。
{==+==}


{==+==}
[constant]: constant-items.md
[`drop`]: ../destructors.md
[constant expression]: ../const_eval.md#constant-expressions
[external block]: external-blocks.md
[interior mutable]: ../interior-mutability.md
[IDENTIFIER]: ../identifiers.md
[_Type_]: ../types.md#type-expressions
[_Expression_]: ../expressions.md
{==+==}

{==+==}