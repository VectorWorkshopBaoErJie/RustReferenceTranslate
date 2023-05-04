{==+==}
# Lifetime elision

Rust has rules that allow lifetimes to be elided in various places where the
compiler can infer a sensible default choice.

## Lifetime elision in functions

In order to make common patterns more ergonomic, lifetime arguments can be
*elided* in [function item], [function pointer], and [closure trait] signatures.
The following rules are used to infer lifetime parameters for elided lifetimes.
It is an error to elide lifetime parameters that cannot be inferred. The
placeholder lifetime, `'_`, can also be used to have a lifetime inferred in the
same way. For lifetimes in paths, using `'_` is preferred. Trait object
lifetimes follow different rules discussed
[below](#default-trait-object-lifetimes).

* Each elided lifetime in the parameters becomes a distinct lifetime parameter.
* If there is exactly one lifetime used in the parameters (elided or not), that
  lifetime is assigned to *all* elided output lifetimes.

In method signatures there is another rule

* If the receiver has type `&Self`  or `&mut Self`, then the lifetime of that
  reference to `Self` is assigned to all elided output lifetime parameters.
{==+==}
# 生命周期省略

在一些编译器可以推断出合理的默认选择的情况下，Rust 有允许省略生命周期的规则。

## 函数中的生命周期省略

为了让常见的模式更加人性化，生命周期参数可以在函数、函数指针和闭包 Trait 签名中被 *省略* 。
以下规则用于推断省略生命周期参数。省略无法推断的生命周期参数将是错误。
占位符生命周期 `'_` 也可以被用来以同样的方式推断生命周期。对于路径上的生命周期，使用 `'_` 很好的选择。
Trait 对象生命周期遵循不同的规则，下面会讨论。

* 在参数中每个被省略的生命周期都成为单独的。
* 如果在参数中只有一个生命周期 (不论是否被省略) ，那么该生命周期将分配到 *所有* 被省略的输出生命周期。

在方法签名中另有一个规则：

* 如果接收器的类型是 `&Self` 或 `&mut Self`，那么指向 `Self` 的这个引用的生命周期会被分配到所有省略的输出生命周期参数。
{==+==}


{==+==}
Examples:

```rust
# trait T {}
# trait ToCStr {}
# struct Thing<'a> {f: &'a i32}
# struct Command;
#
# trait Example {
fn print1(s: &str);                                   // elided
fn print2(s: &'_ str);                                // also elided
fn print3<'a>(s: &'a str);                            // expanded

fn debug1(lvl: usize, s: &str);                       // elided
fn debug2<'a>(lvl: usize, s: &'a str);                // expanded

fn substr1(s: &str, until: usize) -> &str;            // elided
fn substr2<'a>(s: &'a str, until: usize) -> &'a str;  // expanded

fn get_mut1(&mut self) -> &mut dyn T;                 // elided
fn get_mut2<'a>(&'a mut self) -> &'a mut dyn T;       // expanded

fn args1<T: ToCStr>(&mut self, args: &[T]) -> &mut Command;                  // elided
fn args2<'a, 'b, T: ToCStr>(&'a mut self, args: &'b [T]) -> &'a mut Command; // expanded

fn new1(buf: &mut [u8]) -> Thing<'_>;                 // elided - preferred
fn new2(buf: &mut [u8]) -> Thing;                     // elided
fn new3<'a>(buf: &'a mut [u8]) -> Thing<'a>;          // expanded
# }

type FunPtr1 = fn(&str) -> &str;                      // elided
type FunPtr2 = for<'a> fn(&'a str) -> &'a str;        // expanded

type FunTrait1 = dyn Fn(&str) -> &str;                // elided
type FunTrait2 = dyn for<'a> Fn(&'a str) -> &'a str;  // expanded
```
{==+==}
例子:

```rust
# trait T {}
# trait ToCStr {}
# struct Thing<'a> {f: &'a i32}
# struct Command;
#
# trait Example {
fn print1(s: &str);                                   // 省略生命周期
fn print2(s: &'_ str);                                // 同样省略
fn print3<'a>(s: &'a str);                            // 展开

fn debug1(lvl: usize, s: &str);                       // 省略生命周期
fn debug2<'a>(lvl: usize, s: &'a str);                // 展开

fn substr1(s: &str, until: usize) -> &str;            // 省略生命周期
fn substr2<'a>(s: &'a str, until: usize) -> &'a str;  // 展开

fn get_mut1(&mut self) -> &mut dyn T;                 // 省略生命周期
fn get_mut2<'a>(&'a mut self) -> &'a mut dyn T;       // 展开

fn args1<T: ToCStr>(&mut self, args: &[T]) -> &mut Command;                  // 省略生命周期
fn args2<'a, 'b, T: ToCStr>(&'a mut self, args: &'b [T]) -> &'a mut Command; // 展开

fn new1(buf: &mut [u8]) -> Thing<'_>;                 // 省略生命周期 - 推荐
fn new2(buf: &mut [u8]) -> Thing;                     // 省略生命周期
fn new3<'a>(buf: &'a mut [u8]) -> Thing<'a>;          // 展开
# }

type FunPtr1 = fn(&str) -> &str;                      // 省略生命周期
type FunPtr2 = for<'a> fn(&'a str) -> &'a str;        // 展开

type FunTrait1 = dyn Fn(&str) -> &str;                // 省略生命周期
type FunTrait2 = dyn for<'a> Fn(&'a str) -> &'a str;  // 展开
```
{==+==}


{==+==}
```rust,compile_fail
// The following examples show situations where it is not allowed to elide the
// lifetime parameter.

# trait Example {
// Cannot infer, because there are no parameters to infer from.
fn get_str() -> &str;                                 // ILLEGAL

// Cannot infer, ambiguous if it is borrowed from the first or second parameter.
fn frob(s: &str, t: &str) -> &str;                    // ILLEGAL
# }
```
{==+==}
```rust,compile_fail
// 以下示例展示了无法省略生命周期参数的情况。

# trait Example {
// 无法推断，因为没有参数可推断。
fn get_str() -> &str;                                 // ILLEGAL

// 无法推断，不清楚是从第一个还是第二个参数中借用。
fn frob(s: &str, t: &str) -> &str;                    // ILLEGAL
# }
```
{==+==}


{==+==}
## Default trait object lifetimes

The assumed lifetime of references held by a [trait object] is called its
_default object lifetime bound_. These were defined in [RFC 599] and amended in
[RFC 1156].

These default object lifetime bounds are used instead of the lifetime parameter
elision rules defined above when the lifetime bound is omitted entirely. If
`'_` is used as the lifetime bound then the bound follows the usual elision
rules.

If the trait object is used as a type argument of a generic type then the
containing type is first used to try to infer a bound.

* If there is a unique bound from the containing type then that is the default
* If there is more than one bound from the containing type then an explicit
  bound must be specified

If neither of those rules apply, then the bounds on the trait are used:

* If the trait is defined with a single lifetime _bound_ then that bound is
  used.
* If `'static` is used for any lifetime bound then `'static` is used.
* If the trait has no lifetime bounds, then the lifetime is inferred in
  expressions and is `'static` outside of expressions.
{==+==}
## 默认trait对象生命周期

由 [trait 对象][trait object] 持有的引用的假定生命周期称为其 _默认对象生命周期约束_ 。这些在 [RFC 599] 中定义并在 [RFC 1156] 中进行了修改。

这些默认对象生命周期约束在完全省略生命周期约束时使用，而不是使用上述省略生命周期参数的规则。如果将 `'_` 用作生命周期约束，则该约束遵循通常的省略规则。

如果 trait 对象用作泛型类型的类型参数，则首先使用包含类型来尝试推断约束。

* 如果包含类型存在唯一的约束，则使用该约束作为默认值。
* 如果来自包含类型的约束超过一个，则必须指定显式约束。

如果上述规则都不适用，则使用 trait 的约束：

* 如果 trait 使用单个生命周期约束定义，则使用该约束。
* 如果使用 `'static` 作为任何生命周期约束，则使用 `'static` 。
* 如果 trait 没有生命周期约束，则在表达式中推断生命周期，并在表达式外部使用 `'static` 。
{==+==}


{==+==}
```rust
// For the following trait...
trait Foo { }

// These two are the same because Box<T> has no lifetime bound on T
type T1 = Box<dyn Foo>;
type T2 = Box<dyn Foo + 'static>;

// ...and so are these:
impl dyn Foo {}
impl dyn Foo + 'static {}

// ...so are these, because &'a T requires T: 'a
type T3<'a> = &'a dyn Foo;
type T4<'a> = &'a (dyn Foo + 'a);

// std::cell::Ref<'a, T> also requires T: 'a, so these are the same
type T5<'a> = std::cell::Ref<'a, dyn Foo>;
type T6<'a> = std::cell::Ref<'a, dyn Foo + 'a>;
```
{==+==}
```rust
// 对于下面的 trait...
trait Foo { }

// 这两者是相同的，因为 Box<T> 没有 T 上的生命周期约束
type T1 = Box<dyn Foo>;
type T2 = Box<dyn Foo + 'static>;

// ... 这两者也相同：
impl dyn Foo {}
impl dyn Foo + 'static {}

// ... 这两者也相同，因为 &'a T 要求 T: 'a
type T3<'a> = &'a dyn Foo;
type T4<'a> = &'a (dyn Foo + 'a);

// std::cell::Ref<'a, T> 同样要求 T: 'a ，所以这两者相同
type T5<'a> = std::cell::Ref<'a, dyn Foo>;
type T6<'a> = std::cell::Ref<'a, dyn Foo + 'a>;
```
{==+==}


{==+==}
```rust,compile_fail
// This is an example of an error.
# trait Foo { }
struct TwoBounds<'a, 'b, T: ?Sized + 'a + 'b> {
    f1: &'a i32,
    f2: &'b i32,
    f3: T,
}
type T7<'a, 'b> = TwoBounds<'a, 'b, dyn Foo>;
//                                  ^^^^^^^
// Error: the lifetime bound for this object type cannot be deduced from context
```
{==+==}
```rust,compile_fail
// 这是一个错误的示例。
# trait Foo { }
struct TwoBounds<'a, 'b, T: ?Sized + 'a + 'b> {
    f1: &'a i32,
    f2: &'b i32,
    f3: T,
}
type T7<'a, 'b> = TwoBounds<'a, 'b, dyn Foo>;
//                                  ^^^^^^^
// 错误：无法从上下文中推断出对象类型的生命周期约束
```
{==+==}


{==+==}
Note that the innermost object sets the bound, so `&'a Box<dyn Foo>` is still
`&'a Box<dyn Foo + 'static>`.
{==+==}
请注意，最内层的对象设置了生命周期约束，因此 `&'a Box<dyn Foo>` 仍然等同于 `&'a Box<dyn Foo + 'static>` 。
{==+==}


{==+==}
```rust
// For the following trait...
trait Bar<'a>: 'a { }

// ...these two are the same:
type T1<'a> = Box<dyn Bar<'a>>;
type T2<'a> = Box<dyn Bar<'a> + 'a>;

// ...and so are these:
impl<'a> dyn Bar<'a> {}
impl<'a> dyn Bar<'a> + 'a {}
```
{==+==}
```rust
// 对于下面的 trait ...
trait Bar<'a>: 'a { }

// ...这两个是相同的:
type T1<'a> = Box<dyn Bar<'a>>;
type T2<'a> = Box<dyn Bar<'a> + 'a>;

// ...这两个也是相同的:
impl<'a> dyn Bar<'a> {}
impl<'a> dyn Bar<'a> + 'a {}
```
{==+==}


{==+==}
## `'static` lifetime elision

Both [constant] and [static] declarations of reference types have *implicit*
`'static` lifetimes unless an explicit lifetime is specified. As such, the
constant declarations involving `'static` above may be written without the
lifetimes.
{==+==}
## `'static` 生命周期省略

除非显式指定生命周期，否则引用类型的常量和静态声明都有 *隐含的* `'static` 生命周期。因此，上述涉及 `'static` 的常量声明可以不带生命周期。
{==+==}


{==+==}
```rust
// STRING: &'static str
const STRING: &str = "bitstring";

struct BitsNStrings<'a> {
    mybits: [u32; 2],
    mystring: &'a str,
}

// BITS_N_STRINGS: BitsNStrings<'static>
const BITS_N_STRINGS: BitsNStrings<'_> = BitsNStrings {
    mybits: [1, 2],
    mystring: STRING,
};
```
{==+==}

{==+==}


{==+==}
Note that if the `static` or `const` items include function or closure
references, which themselves include references, the compiler will first try
the standard elision rules. If it is unable to resolve the lifetimes by its
usual rules, then it will error. By way of example:
{==+==}
请注意，如果 `static` 或 `const` 条目包含函数或闭包引用，这些引用本身又包含引用，编译器将首先尝试标准省略规则。
如果它无法通过通常的规则解析生命周期，则会出现错误。以以下示例说明：
{==+==}


{==+==}
```rust
# struct Foo;
# struct Bar;
# struct Baz;
# fn somefunc(a: &Foo, b: &Bar, c: &Baz) -> usize {42}
// Resolved as `fn<'a>(&'a str) -> &'a str`.
const RESOLVED_SINGLE: fn(&str) -> &str = |x| x;

// Resolved as `Fn<'a, 'b, 'c>(&'a Foo, &'b Bar, &'c Baz) -> usize`.
const RESOLVED_MULTIPLE: &dyn Fn(&Foo, &Bar, &Baz) -> usize = &somefunc;
```
{==+==}
```rust
# struct Foo;
# struct Bar;
# struct Baz;
# fn somefunc(a: &Foo, b: &Bar, c: &Baz) -> usize {42}
// 解析为 `fn<'a>(&'a str) -> &'a str`.
const RESOLVED_SINGLE: fn(&str) -> &str = |x| x;

// 解析为 `Fn<'a, 'b, 'c>(&'a Foo, &'b Bar, &'c Baz) -> usize`.
const RESOLVED_MULTIPLE: &dyn Fn(&Foo, &Bar, &Baz) -> usize = &somefunc;
```
{==+==}


{==+==}
```rust,compile_fail
# struct Foo;
# struct Bar;
# struct Baz;
# fn somefunc<'a,'b>(a: &'a Foo, b: &'b Bar) -> &'a Baz {unimplemented!()}
// There is insufficient information to bound the return reference lifetime
// relative to the argument lifetimes, so this is an error.
const RESOLVED_STATIC: &dyn Fn(&Foo, &Bar) -> &Baz = &somefunc;
//                                            ^
// this function's return type contains a borrowed value, but the signature
// does not say whether it is borrowed from argument 1 or argument 2
```
{==+==}
```rust,compile_fail
# struct Foo;
# struct Bar;
# struct Baz;
# fn somefunc<'a,'b>(a: &'a Foo, b: &'b Bar) -> &'a Baz {unimplemented!()}
// 缺少信息来将返回的引用生命周期绑定到参数的生命周期，
// 所以会出现错误。
const RESOLVED_STATIC: &dyn Fn(&Foo, &Bar) -> &Baz = &somefunc;
//                                            ^
// 此函数的返回类型包含了一个借用的值，但是函数签名并未表明它是从参数 1 还是参数 2 借用的。
```
{==+==}


{==+==}
[closure trait]: types/closure.md
[constant]: items/constant-items.md
[function item]: types/function-item.md
[function pointer]: types/function-pointer.md
[RFC 599]: https://github.com/rust-lang/rfcs/blob/master/text/0599-default-object-bound.md
[RFC 1156]: https://github.com/rust-lang/rfcs/blob/master/text/1156-adjust-default-object-bounds.md
[static]: items/static-items.md
[trait object]: types/trait-object.md
{==+==}

{==+==}
