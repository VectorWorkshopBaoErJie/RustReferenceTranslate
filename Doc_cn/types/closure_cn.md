{==+==}
# Closure types
{==+==}
# 闭包类型
{==+==}


{==+==}
A [closure expression] produces a closure value with a unique, anonymous type
that cannot be written out. A closure type is approximately equivalent to a
struct which contains the captured variables. For instance, the following
closure:
{==+==}
[闭包表达式][closure expression] 生成一个闭包值，具有独特的匿名类型，无法显式书写。
闭包类型类似于包含捕获变量的结构体。例如，以下闭包:
{==+==}


{==+==}
```rust
fn f<F : FnOnce() -> String> (g: F) {
    println!("{}", g());
}

let mut s = String::from("foo");
let t = String::from("bar");

f(|| {
    s += &t;
    s
});
// Prints "foobar".
```
{==+==}

{==+==}


{==+==}
generates a closure type roughly like the following:
{==+==}
生成的闭包类型大致类似于以下结构体:
{==+==}


{==+==}
<!-- ignore: simplified, requires unboxed_closures, fn_traits -->
```rust,ignore
struct Closure<'a> {
    s : String,
    t : &'a String,
}

impl<'a> FnOnce<()> for Closure<'a> {
    type Output = String;
    fn call_once(self) -> String {
        self.s += &*self.t;
        self.s
    }
}
```
{==+==}

{==+==}


{==+==}
so that the call to `f` works as if it were:
{==+==}
因此，对 `f` 的调用将像以下代码一样工作:
{==+==}


{==+==}
<!-- ignore: continuation of above -->
```rust,ignore
f(Closure{s: s, t: &t});
```
{==+==}

{==+==}


{==+==}
## Capture modes

The compiler prefers to capture a closed-over variable by immutable borrow,
followed by unique immutable borrow (see below), by mutable borrow, and finally
by move. It will pick the first choice of these that is compatible with how the
captured variable is used inside the closure body. The compiler does not take
surrounding code into account, such as the lifetimes of involved variables, or
of the closure itself.

If the `move` keyword is used, then all captures are by move or, for `Copy`
types, by copy, regardless of whether a borrow would work. The `move` keyword is
usually used to allow the closure to outlive the captured values, such as if the
closure is being returned or used to spawn a new thread.

Composite types such as structs, tuples, and enums are always captured entirely,
not by individual fields. It may be necessary to borrow into a local variable in
order to capture a single field:
{==+==}
## 捕获模式

编译器优先选择对闭合变量进行不可变借用，然后是唯一不可变借用 (参见下文) ，然后是可变借用，最后才是移动方式。
编译器将选择与在闭包体内使用捕获变量的方式兼容的这些选项中的第一个。编译器不考虑周围的代码，比如涉及变量的生命周期或闭包本身的生命周期。

如果使用了 `move` 关键字，则所有的捕获都是通过移动，或者对于 `Copy` 类型是通过复制，而不管是否可以使用借用。
通常， `move` 关键字用于允许闭包超出捕获值的生命周期，例如，如果闭包正在被返回或用于生成新线程。

结构体、元组和枚举等复合类型始终被整体捕获，而不是逐个字段捕获。为了捕获单个字段，可能需要借用到一个本地变量中：
{==+==}


{==+==}
```rust
# use std::collections::HashSet;
#
struct SetVec {
    set: HashSet<u32>,
    vec: Vec<u32>
}

impl SetVec {
    fn populate(&mut self) {
        let vec = &mut self.vec;
        self.set.iter().for_each(|&n| {
            vec.push(n);
        })
    }
}
```
{==+==}

{==+==}


{==+==}
If, instead, the closure were to use `self.vec` directly, then it would attempt
to capture `self` by mutable reference. But since `self.set` is already
borrowed to iterate over, the code would not compile.
{==+==}
如果闭包直接使用 `self.vec` ，那么它将尝试通过可变引用来捕获 `self` 。
但是，由于 `self.set` 已经被借用以进行迭代，代码将无法编译。
{==+==}


{==+==}
## Unique immutable borrows in captures

Captures can occur by a special kind of borrow called a _unique immutable
borrow_, which cannot be used anywhere else in the language and cannot be
written out explicitly. It occurs when modifying the referent of a mutable
reference, as in the following example:
{==+==}
## 捕获中的唯一不可变借用

捕获可以通过一种称为唯一不可变借用的特殊借用方式发生，它无法在语言的任何其他地方使用，并且无法显式书写。
当修改可变引用的引用对象时，就会发生这种情况，例如以下示例：
{==+==}


{==+==}
```rust
let mut b = false;
let x = &mut b;
{
    let mut c = || { *x = true; };
    // The following line is an error:
    // let y = &x;
    c();
}
let z = &x;
```
{==+==}
```rust
let mut b = false;
let x = &mut b;
{
    let mut c = || { *x = true; };
    // 下面这一行是错误的:
    // let y = &x;
    c();
}
let z = &x;
```
{==+==}


{==+==}
In this case, borrowing `x` mutably is not possible, because `x` is not `mut`.
But at the same time, borrowing `x` immutably would make the assignment illegal,
because a `& &mut` reference might not be unique, so it cannot safely be used to
modify a value. So a unique immutable borrow is used: it borrows `x` immutably,
but like a mutable borrow, it must be unique. In the above example, uncommenting
the declaration of `y` will produce an error because it would violate the
uniqueness of the closure's borrow of `x`; the declaration of z is valid because
the closure's lifetime has expired at the end of the block, releasing the borrow.
{==+==}
在这种情况下，无法将 `x` 借为可变引用，因为 `x` 不是可变的。
但与此同时，借用 `x` 为不可变引用将使赋值操作非法，因为 `& &mut` 引用可能不是唯一的，因此不能安全地用于修改值。
因此，使用了一种独特的不可变借用：它以不可变方式借用 `x` ，但像可变借用一样，必须是唯一的。
在上面的示例中，取消注释 `y` 的声明将导致错误，因为它将违反闭包对 `x` 的借用的唯一性； `z` 的声明是有效的，因为闭包的生命周期在块的末尾已过期，释放了借用。
{==+==}


{==+==}
## Call traits and coercions

Closure types all implement [`FnOnce`], indicating that they can be called once
by consuming ownership of the closure. Additionally, some closures implement
more specific call traits:
{==+==}
## 调用trait和类型强制转换

闭包类型都实现了 [`FnOnce`] ，表示它们可以通过消费闭包所有权来被调用一次。此外，一些闭包还实现了更具体的调用trait:
{==+==}


{==+==}
* A closure which does not move out of any captured variables implements
  [`FnMut`], indicating that it can be called by mutable reference.

* A closure which does not mutate or move out of any captured variables
  implements [`Fn`], indicating that it can be called by shared reference.

> Note: `move` closures may still implement [`Fn`] or [`FnMut`], even though
> they capture variables by move. This is because the traits implemented by a
> closure type are determined by what the closure does with captured values,
> not how it captures them.

*Non-capturing closures* are closures that don't capture anything from their
environment. They can be coerced to function pointers (e.g., `fn()`)
with the matching signature.
{==+==}
* 一个不会移动任何捕获变量的闭包实现了 [`FnMut`] ，表示它可以通过可变引用来调用。
* 一个不会修改或移动捕获变量的闭包实现了 [`Fn`] ，表示它可以通过共享引用来调用。

> 注意: `move` 闭包仍然可能实现 [`Fn`] 或 [`FnMut`] ，即使它们通过移动捕获变量。
> 这是因为闭包类型实现的trait是由闭包对捕获值的操作方式决定的，而不是它们如何捕获它们。

*非捕获闭包* 是不从其环境捕获任何内容的闭包。它们可以强制转换为与匹配签名的函数指针 (例如, `fn()` ) 。
{==+==}


{==+==}
```rust
let add = |x, y| x + y;

let mut x = add(5,7);

type Binop = fn(i32, i32) -> i32;
let bo: Binop = add;
x = bo(5,7);
```
{==+==}

{==+==}


{==+==}
## Other traits

All closure types implement [`Sized`]. Additionally, closure types implement the
following traits if allowed to do so by the types of the captures it stores:
{==+==}
## 其他 traits

所有闭包类型都实现了 [`Sized`] 。此外，如果允许所存储的捕获类型这样做，则闭包类型会实现以下 traits：
{==+==}


{==+==}
* [`Clone`]
* [`Copy`]
* [`Sync`]
* [`Send`]

The rules for [`Send`] and [`Sync`] match those for normal struct types, while
[`Clone`] and [`Copy`] behave as if [derived]. For [`Clone`], the order of
cloning of the captured variables is left unspecified.

Because captures are often by reference, the following general rules arise:

* A closure is [`Sync`] if all captured variables are [`Sync`].
* A closure is [`Send`] if all variables captured by non-unique immutable
  reference are [`Sync`], and all values captured by unique immutable or mutable
  reference, copy, or move are [`Send`].
* A closure is [`Clone`] or [`Copy`] if it does not capture any values by
  unique immutable or mutable reference, and if all values it captures by copy
  or move are [`Clone`] or [`Copy`], respectively.
{==+==}
* [`Clone`]
* [`Copy`]
* [`Sync`]
* [`Send`]

[`Send`] 和 [`Sync`] 的规则与普通结构体类型相同，而 [`Clone`] 和 [`Copy`] 的行为则类似于 [派生](https://doc.rust-lang.org/reference/items/derive.html) 。
对于 [`Clone`] ，复制捕获变量的顺序未指定。

由于捕获通常是通过引用进行的，因此会出现以下一般规则：

* 如果所有捕获的变量都是 [`Sync`] ，则闭包是 [`Sync`] 的。
* 如果所有非唯一不可变引用捕获的变量都是 [`Sync`] ，并且所有唯一不可变或可变引用、复制或移动捕获的值都是 [`Send`] ，则闭包是 [`Send`] 的。
* 如果闭包未通过唯一不可变或可变引用捕获任何值，并且它通过复制或移动捕获的所有值都是 [`Clone`] 或 [`Copy`] 的，则闭包是 [`Clone`] 或 [`Copy`] 的。
{==+==}


{==+==}
[`Clone`]: ../special-types-and-traits.md#clone
[`Copy`]: ../special-types-and-traits.md#copy
[`FnMut`]: ../../std/ops/trait.FnMut.html
[`FnOnce`]: ../../std/ops/trait.FnOnce.html
[`Fn`]: ../../std/ops/trait.Fn.html
[`Send`]: ../special-types-and-traits.md#send
[`Sized`]: ../special-types-and-traits.md#sized
[`Sync`]: ../special-types-and-traits.md#sync
[closure expression]: ../expressions/closure-expr.md
[derived]: ../attributes/derive.md
{==+==}

{==+==}