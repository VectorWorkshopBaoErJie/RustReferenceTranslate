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
 *静态条目* 类似于 [constant] "常量" ，只是它代表了程序中一个精确的内存地址。
 所有对静态条目的引用都指同一个内存地址。
 静态条目有 `static` 生命周期，它比Rust程序中的其他生命周期都要长。静态条目不会在程序结束时调用 [`drop`] 。
{==+==}


{==+==}
The static initializer is a [constant expression] evaluated at compile time.
Static initializers may refer to other statics.
{==+==}
静态初始化器是一个在编译时评估的 [常量表达式][constant expression] 。
静态初始化器可以引用其他静态。
{==+==}


{==+==}
Non-`mut` static items that contain a type that is not [interior mutable] may
be placed in read-only memory.
{==+==}
包含非 [内部可变][interior mutable] 类型的非 `mut` 静态条目可以放置在只读内存中。
{==+==}


{==+==}
All access to a static is safe, but there are a number of restrictions on
statics:
{==+==}
对静态的所有访问都是安全的，但对静态有一些限制:
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
初始化表达式必须在 [外部块][external block] 中省略，并且必须为free静态条目提供。
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
在泛型作用域中定义的静态条目 (例如在 blanket 或默认 impl 中) 将只定义一个静态条目，如同静态定义从当前作用域移出到模块中。
每个单态化 *不会有* 条目。
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
如果静态条目用 `mut` 关键字声明，则表示允许修改。Rust的目标之一是解决并发性bug，而这显然会成为并发竞争条件或其他bug的来源。
因而，在读取或写入可变静态变量时，都需要一个 `unsafe` 块。注意，应确保对可变静态的修改对于在同一进程中运行的其他线程来说是安全的。
{==+==}


{==+==}
Mutable statics are still very useful, however. They can be used with C
libraries and can also be bound from C libraries in an `extern` block.
{==+==}
然而，可变静态仍然非常有用。它们可以与C语言库一起使用，可以在 `extern` 块中同C语言库绑定。
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
可变静态与普通静态有相同的限制，除了类型不需要实现 `Sync` trait 。
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
应该使用常量条目还是静态条目，这可能会令人困惑。
一般来说，常量条目应该比静态条目更受欢迎，除非以下情况之一。
{==+==}


{==+==}
* Large amounts of data are being stored
* The single-address property of statics is required.
* Interior mutability is required.
{==+==}
* 大量的数据正在被存储。
* 静态的单一地址属性是必需的。
* 内部可变性是必需的。
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