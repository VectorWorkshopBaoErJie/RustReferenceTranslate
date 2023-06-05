{==+==}
# Call expressions
{==+==}
# 调用表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _CallExpression_ :\
> &nbsp;&nbsp; [_Expression_] `(` _CallParams_<sup>?</sup> `)`
>
> _CallParams_ :\
> &nbsp;&nbsp; [_Expression_]&nbsp;( `,` [_Expression_] )<sup>\*</sup> `,`<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _调用语法_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `(` _调用参数组_<sup>?</sup> `)`
>
> _调用参数组_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_]&nbsp;( `,` [_表达式_][_Expression_] )<sup>\*</sup> `,`<sup>?</sup>
{==+==}


{==+==}
A *call expression* calls a function.
The syntax of a call expression is an expression, called the *function operand*, followed by a parenthesized comma-separated list of expression, called the *argument operands*.
If the function eventually returns, then the expression completes.
For [non-function types], the expression `f(...)` uses the method on one of the [`std::ops::Fn`], [`std::ops::FnMut`] or [`std::ops::FnOnce`] traits, which differ in whether they take the type by reference, mutable reference, or take ownership respectively.
An automatic borrow will be taken if needed.
The function operand will also be [automatically dereferenced] as required.

Some examples of call expressions:
{==+==}
*调用表达式* 调用函数。
调用表达式的语法是一个称为 *函数操作数* 表达式，后面跟着一个带括号的逗号分隔的表达式列表，称为 *参数操作数* 。
如果函数最终返回，则表达式完成。
对于 [非函数类型][non-function types] ，表达式 `f(...)` 使用 [`std::ops::Fn`] 、 [`std::ops::FnMut`] 或 [`std::ops::FnOnce`] trait 中的方法，这些 trait 在是否通过引用、可变引用或取得所有权方面有所不同。
如果需要，将自动借用。函数操作数也将根据需要 [自动解引用][automatically dereferenced] 。

以下是一些调用表达式的例子:
{==+==}


{==+==}
```rust
# fn add(x: i32, y: i32) -> i32 { 0 }
let three: i32 = add(1i32, 2i32);
let name: &'static str = (|| "Rust")();
```
{==+==}

{==+==}


{==+==}
## Disambiguating Function Calls

All function calls are sugar for a more explicit [fully-qualified syntax].
Function calls may need to be fully qualified, depending on the ambiguity of a call in light of in-scope items.

> **Note**: In the past, the terms "Unambiguous Function Call Syntax", "Universal Function Call Syntax", or "UFCS", have been used in documentation, issues, RFCs, and other community writings.
> However, these terms lack descriptive power and potentially confuse the issue at hand.
> We mention them here for searchability's sake.

Several situations often occur which result in ambiguities about the receiver or referent of method or associated function calls.
These situations may include:

* Multiple in-scope traits define methods with the same name for the same types
* Auto-`deref` is undesirable; for example, distinguishing between methods on a smart pointer itself and the pointer's referent
* Methods which take no arguments, like [`default()`], and return properties of a type, like [`size_of()`]

To resolve the ambiguity, the programmer may refer to their desired method or function using more specific paths, types, or traits.

For example,
{==+==}
## 消除函数调用的歧义

所有函数调用都是 [完全限定语法][fully-qualified syntax] 的语法糖。
根据调用的歧义性以及作用域内的条目，函数调用可能需要完全限定。

> **注意**：在过去，术语 "无歧义函数调用语法" 、 "通用函数调用语法" 或 "UFCS" 已被用于文档、问题、 RFC 和其他社区资料。
> 但是，这些术语缺乏描述能力，可能会让问题变得更加混乱。
> 在这里提及是为了便于内容检索。

有几种常见情况可能导致方法或关联函数调用的接收者或引用产生歧义。
这些情况可能包括:

* 多个作用域内的 trait 为相同类型定义了同名方法
* 不希望自动解引用；例如，区分智能指针本身的方法和指针引用的方法
* 不带参数的方法，例如 [`default()`] ，返回类型的属性，例如 [`size_of()`]  

为了解决歧义，程序员可以使用更具体的路径、类型或 trait 来指定他们想要的方法或函数。

例如:
{==+==}


{==+==}
```rust
trait Pretty {
    fn print(&self);
}

trait Ugly {
  fn print(&self);
}

struct Foo;
impl Pretty for Foo {
    fn print(&self) {}
}

struct Bar;
impl Pretty for Bar {
    fn print(&self) {}
}
impl Ugly for Bar {
    fn print(&self) {}
}

fn main() {
    let f = Foo;
    let b = Bar;

    // we can do this because we only have one item called `print` for `Foo`s
    f.print();
    // more explicit, and, in the case of `Foo`, not necessary
    Foo::print(&f);
    // if you're not into the whole brevity thing
    <Foo as Pretty>::print(&f);

    // b.print(); // Error: multiple 'print' found
    // Bar::print(&b); // Still an error: multiple `print` found

    // necessary because of in-scope items defining `print`
    <Bar as Pretty>::print(&b);
}
```
{==+==}
```rust
trait Pretty {
    fn print(&self);
}

trait Ugly {
  fn print(&self);
}

struct Foo;
impl Pretty for Foo {
    fn print(&self) {}
}

struct Bar;
impl Pretty for Bar {
    fn print(&self) {}
}
impl Ugly for Bar {
    fn print(&self) {}
}

fn main() {
    let f = Foo;
    let b = Bar;

    // 可以这样做，因为只有一个叫做 `print` 的项适用于 `Foo`
    f.print();
    // 更明确的，而且在 `Foo` 的情况下并不必要
    Foo::print(&f);
    // 如果你不喜欢简略的语法
    <Foo as Pretty>::print(&f);

    // b.print(); // 错误：发现多个 'print'
    // Bar::print(&b); // 仍然是错误：发现多个 `print`

    // 由于作用域内的项目定义了 `print` ，因此这是必要的
    <Bar as Pretty>::print(&b);
}
```
{==+==}


{==+==}
Refer to [RFC 132] for further details and motivations.

[RFC 132]: https://github.com/rust-lang/rfcs/blob/master/text/0132-ufcs.md
[_Expression_]: ../expressions.md
[`default()`]: ../../std/default/trait.Default.html#tymethod.default
[`size_of()`]: ../../std/mem/fn.size_of.html
[`std::ops::FnMut`]: ../../std/ops/trait.FnMut.html
[`std::ops::FnOnce`]: ../../std/ops/trait.FnOnce.html
[`std::ops::Fn`]: ../../std/ops/trait.Fn.html
[automatically dereferenced]: field-expr.md#automatic-dereferencing
[fully-qualified syntax]: ../paths.md#qualified-paths
[non-function types]: ../types/function-item.md
{==+==}

{==+==}