{==+==}
# Traits
{==+==}

{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Trait_ :\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> `trait` [IDENTIFIER]&nbsp;
>              [_GenericParams_]<sup>?</sup>
>              ( `:` [_TypeParamBounds_]<sup>?</sup> )<sup>?</sup>
>              [_WhereClause_]<sup>?</sup> `{`\
> &nbsp;&nbsp;&nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp;&nbsp;&nbsp; [_AssociatedItem_]<sup>\*</sup>\
> &nbsp;&nbsp; `}`
{==+==}

{==+==}


{==+==}
A _trait_ describes an abstract interface that types can implement. This
interface consists of [associated items], which come in three varieties:
{==+==}
_trait_ 定义一个类型可以实现的抽象接口，由 [关联条目][associated items] 组成，有三种:
{==+==}


{==+==}
- [functions](associated-items.md#associated-functions-and-methods)
- [types](associated-items.md#associated-types)
- [constants](associated-items.md#associated-constants)
{==+==}
- [functions](associated-items.md#associated-functions-and-methods) 函数
- [types](associated-items.md#associated-types) 类型
- [constants](associated-items.md#associated-constants) 常量
{==+==}


{==+==}
All traits define an implicit type parameter `Self` that refers to "the type
that is implementing this interface". Traits may also contain additional type
parameters. These type parameters, including `Self`, may be constrained by
other traits and so forth [as usual][generics].
{==+==}
所有 trait 都定义了一个隐式的类型参数 `Self` ，指向 "实现此接口的类型" 。
trait 可以包含附加的类型参数。这些类型参数，包括 `Self` ， 像通常 [泛型][generics] 一样可以受到其他trait的约束。
{==+==}


{==+==}
Traits are implemented for specific types through separate [implementations].
{==+==}
对于特定类型的 Trait 通过单独的 [implementations] 实现。
{==+==}


{==+==}
Trait functions may omit the function body by replacing it with a semicolon.
This indicates that the implementation must define the function. If the trait
function defines a body, this definition acts as a default for any
implementation which does not override it. Similarly, associated constants may
omit the equals sign and expression to indicate implementations must define
the constant value. Associated types must never define the type, the type may
only be specified in an implementation.
{==+==}
Trait 函数可以省略函数体，用分号来代替。表明具体实现必须定义该函数。
如果Trait函数定义了函数体，这个定义将是未覆盖时的默认实现。
同样地，Trait 常量可以省略等号和表达式，表明实现时必须定义常量值。
关联类型决不能定义类型，类型只能在实现中指定。
{==+==}


{==+==}
```rust
// Examples of associated trait items with and without definitions.
trait Example {
    const CONST_NO_DEFAULT: i32;
    const CONST_WITH_DEFAULT: i32 = 99;
    type TypeNoDefault;
    fn method_without_default(&self);
    fn method_with_default(&self) {}
}
```
{==+==}
```rust
// Examples of associated trait items with and without definitions.
// 有定义和无定义的关联trait条目的示例。
trait Example {
    const CONST_NO_DEFAULT: i32;
    const CONST_WITH_DEFAULT: i32 = 99;
    type TypeNoDefault;
    fn method_without_default(&self);
    fn method_with_default(&self) {}
}
```
{==+==}


{==+==}
Trait functions are not allowed to be [`async`] or [`const`].
{==+==}
Trait 函数不允许为 [`async`] 或 [`const`] 。
{==+==}


{==+==}
## Trait bounds

Generic items may use traits as [bounds] on their type parameters.
{==+==}
## Trait 绑定

泛型条目可以使用trait作为其类型参数的 [绑定][bounds] 。
{==+==}


{==+==}
## Generic Traits

Type parameters can be specified for a trait to make it generic. These appear
after the trait name, using the same syntax used in [generic functions].
{==+==}
## 泛型 Trait

可以为 trait 指定类型参数，使其成为泛型。
这些参数出现在 trait 名称之后，使用与 [泛型函数][generic functions] 相同的语法。
{==+==}


{==+==}
```rust
trait Seq<T> {
    fn len(&self) -> u32;
    fn elt_at(&self, n: u32) -> T;
    fn iter<F>(&self, f: F) where F: Fn(T);
}
```
{==+==}

{==+==}


{==+==}
## Object Safety

Object safe traits can be the base trait of a [trait object]. A trait is
*object safe* if it has the following qualities (defined in [RFC 255]):
{==+==}
## 对象安全

对象安全trait 可以是 [trait对象][trait object] 的基本trait 。
如果trait具有以下特性，它就是 *对象安全的*  (在 [RFC 255] 中定义):
{==+==}


{==+==}
* All [supertraits] must also be object safe.
* `Sized` must not be a [supertrait][supertraits]. In other words, it must not require `Self: Sized`.
* It must not have any associated constants.
* It must not have any associated types with generics.
* All associated functions must either be dispatchable from a trait object or be explicitly non-dispatchable:
    * Dispatchable functions require:
        * Not have any type parameters (although lifetime parameters are allowed),
        * Be a [method] that does not use `Self` except in the type of the receiver.
        * Have a receiver with one of the following types:
            * `&Self` (i.e. `&self`)
            * `&mut Self` (i.e `&mut self`)
            * [`Box<Self>`]
            * [`Rc<Self>`]
            * [`Arc<Self>`]
            * [`Pin<P>`] where `P` is one of the types above
        * Does not have a `where Self: Sized` bound (receiver type of `Self` (i.e. `self`) implies this).
    * Explicitly non-dispatchable functions require:
        * Have a `where Self: Sized` bound (receiver type of `Self` (i.e. `self`) implies this).
{==+==}
* 所有的 [supertraits] 也必须是对象安全的。
* `Sized` 不能是 [supertrait][supertraits] 。换句话说，必须不要求 `Self: Sized` 。
* 不能有任何关联常量。
* 不能有任何泛型关联类型。
* 所有关联函数必须是可从trait对象中可派发的，或者是明确不可派发的。
    * 可派发函数要求:
        * 没有任何类型参数 (允许生命周期参数),
        * 是除接收者类型，不使用 `Self` 的 [method] 。
        * 有具有以下类型的接收器:
            * `&Self` (即 `&self`)
            * `&mut Self` (即 `&mut self`)
            * [`Box<Self>`]
            * [`Rc<Self>`]
            * [`Arc<Self>`]
            * [`Pin<P>`] 其中 `P` 是上述类型之一
        * 没有 `where Self: Sized` 绑定 (接收器类型 `Self` (即 `self`) 暗指 this) 。
    * 显示不可派发函数要求:
        * 有 `where Self: Sized` 绑定 (接收器类型 `Self` (即 `self`) 暗指 this) 。
{==+==}


{==+==}
```rust
# use std::rc::Rc;
# use std::sync::Arc;
# use std::pin::Pin;
// Examples of object safe methods.
trait TraitMethods {
    fn by_ref(self: &Self) {}
    fn by_ref_mut(self: &mut Self) {}
    fn by_box(self: Box<Self>) {}
    fn by_rc(self: Rc<Self>) {}
    fn by_arc(self: Arc<Self>) {}
    fn by_pin(self: Pin<&Self>) {}
    fn with_lifetime<'a>(self: &'a Self) {}
    fn nested_pin(self: Pin<Arc<Self>>) {}
}
# struct S;
# impl TraitMethods for S {}
# let t: Box<dyn TraitMethods> = Box::new(S);
```
{==+==}
```rust
# use std::rc::Rc;
# use std::sync::Arc;
# use std::pin::Pin;
// 对象安全方法的示例。
trait TraitMethods {
    fn by_ref(self: &Self) {}
    fn by_ref_mut(self: &mut Self) {}
    fn by_box(self: Box<Self>) {}
    fn by_rc(self: Rc<Self>) {}
    fn by_arc(self: Arc<Self>) {}
    fn by_pin(self: Pin<&Self>) {}
    fn with_lifetime<'a>(self: &'a Self) {}
    fn nested_pin(self: Pin<Arc<Self>>) {}
}
# struct S;
# impl TraitMethods for S {}
# let t: Box<dyn TraitMethods> = Box::new(S);
```
{==+==}


{==+==}
```rust,compile_fail
// This trait is object-safe, but these methods cannot be dispatched on a trait object.
trait NonDispatchable {
    // Non-methods cannot be dispatched.
    fn foo() where Self: Sized {}
    // Self type isn't known until runtime.
    fn returns(&self) -> Self where Self: Sized;
    // `other` may be a different concrete type of the receiver.
    fn param(&self, other: Self) where Self: Sized {}
    // Generics are not compatible with vtables.
    fn typed<T>(&self, x: T) where Self: Sized {}
}

struct S;
impl NonDispatchable for S {
    fn returns(&self) -> Self where Self: Sized { S }
}
let obj: Box<dyn NonDispatchable> = Box::new(S);
obj.returns(); // ERROR: cannot call with Self return
obj.param(S);  // ERROR: cannot call with Self parameter
obj.typed(1);  // ERROR: cannot call with generic type
```
{==+==}
```rust,compile_fail
// 这个trait是对象安全的，但这些方法不能在trait对象上派发。
trait NonDispatchable {
    // 非方法，不能被派发。
    fn foo() where Self: Sized {}
    // Self类型只有在运行时才知道。
    fn returns(&self) -> Self where Self: Sized;
    // `other` 可能是接收者的不同的具体类型。
    fn param(&self, other: Self) where Self: Sized {}
    // 泛型与虚表不兼容。
    fn typed<T>(&self, x: T) where Self: Sized {}
}

struct S;
impl NonDispatchable for S {
    fn returns(&self) -> Self where Self: Sized { S }
}
let obj: Box<dyn NonDispatchable> = Box::new(S);
obj.returns(); // ERROR: 不能调用 Self 返回
obj.param(S);  // ERROR: 不能调用 Self 参数
obj.typed(1);  // ERROR: 不能调用泛型类型
```
{==+==}


{==+==}
```rust,compile_fail
# use std::rc::Rc;
// Examples of non-object safe traits.
trait NotObjectSafe {
    const CONST: i32 = 1;  // ERROR: cannot have associated const

    fn foo() {}  // ERROR: associated function without Sized
    fn returns(&self) -> Self; // ERROR: Self in return type
    fn typed<T>(&self, x: T) {} // ERROR: has generic type parameters
    fn nested(self: Rc<Box<Self>>) {} // ERROR: nested receiver not yet supported
}

struct S;
impl NotObjectSafe for S {
    fn returns(&self) -> Self { S }
}
let obj: Box<dyn NotObjectSafe> = Box::new(S); // ERROR
```
{==+==}
```rust,compile_fail
# use std::rc::Rc;
// 非对象安全trait的示例。
trait NotObjectSafe {
    const CONST: i32 = 1;  // ERROR: 不能有关联 const

    fn foo() {}  // ERROR: 关联函数没有Sized
    fn returns(&self) -> Self; // ERROR: Self 在返回类型
    fn typed<T>(&self, x: T) {} // ERROR: 有泛型类型参数
    fn nested(self: Rc<Box<Self>>) {} // ERROR: 尚不支持嵌套的接收器
}

struct S;
impl NotObjectSafe for S {
    fn returns(&self) -> Self { S }
}
let obj: Box<dyn NotObjectSafe> = Box::new(S); // ERROR
```
{==+==}


{==+==}
```rust,compile_fail
// Self: Sized traits are not object-safe.
trait TraitWithSize where Self: Sized {}

struct S;
impl TraitWithSize for S {}
let obj: Box<dyn TraitWithSize> = Box::new(S); // ERROR
```
{==+==}
```rust,compile_fail
// Self: Sized traits 不是 object-safe.
trait TraitWithSize where Self: Sized {}

struct S;
impl TraitWithSize for S {}
let obj: Box<dyn TraitWithSize> = Box::new(S); // ERROR
```
{==+==}


{==+==}
```rust,compile_fail
// Not object safe if `Self` is a type argument.
trait Super<A> {}
trait WithSelf: Super<Self> where Self: Sized {}

struct S;
impl<A> Super<A> for S {}
impl WithSelf for S {}
let obj: Box<dyn WithSelf> = Box::new(S); // ERROR: cannot use `Self` type parameter
```
{==+==}
```rust,compile_fail
// 如果 `Self` 是类型参数，则不是对象安全的。
trait Super<A> {}
trait WithSelf: Super<Self> where Self: Sized {}

struct S;
impl<A> Super<A> for S {}
impl WithSelf for S {}
let obj: Box<dyn WithSelf> = Box::new(S); // ERROR: 不能使用 `Self` 类型的参数
```
{==+==}


{==+==}
## Supertraits
{==+==}

{==+==}


{==+==}
**Supertraits** are traits that are required to be implemented for a type to
implement a specific trait. Furthermore, anywhere a [generic][generics] or [trait object]
is bounded by a trait, it has access to the associated items of its supertraits.
{==+==}
**Supertraits** are traits that are required to be implemented for a type to implement a specific trait. Furthermore, anywhere a [generic][generics] or [trait object] is bounded by a trait, it has access to the associated items of its supertraits.
**Supertraits** 是一个类型实现一个特定trait所需要实现的trait。
此外，在 [泛型][generics] 或 [trait对象][trait object] 被 trait 约束的位置，它可以访问其 Supertraits 的相关条目。
{==+==}


{==+==}
Supertraits are declared by trait bounds on the `Self` type of a trait and
transitively the supertraits of the traits declared in those trait bounds. It is
an error for a trait to be its own supertrait.
{==+==}
Supertraits 由 trait 的 `Self` 类型的 trait 绑定声明，并且是这些 trait 绑定中声明的 trait 的 Supertraits 。
如果 trait 是它自己的 Supertraits ，是一个错误。
{==+==}


{==+==}
The trait with a supertrait is called a **subtrait** of its supertrait.
{==+==}
具有 supertrait 的 trait 被称为其 supertrait 的 **subtrait** 。
{==+==}


{==+==}
The following is an example of declaring `Shape` to be a supertrait of `Circle`.
{==+==}
下面是一个声明 `Shape` 是 `Circle` 很形象的示例。
{==+==}


{==+==}
```rust
trait Shape { fn area(&self) -> f64; }
trait Circle : Shape { fn radius(&self) -> f64; }
```
{==+==}

{==+==}


{==+==}
And the following is the same example, except using [where clauses].
{==+==}
而下面是同一个例子，除了使用 [where 子句][where clauses] 。
{==+==}


{==+==}
```rust
trait Shape { fn area(&self) -> f64; }
trait Circle where Self: Shape { fn radius(&self) -> f64; }
```
{==+==}

{==+==}


{==+==}
This next example gives `radius` a default implementation using the `area`
function from `Shape`.
{==+==}
接下来的例子，给于 `radius` 一个默认的实现，使用 `Shape` 的 `area` 函数。
{==+==}


{==+==}
```rust
# trait Shape { fn area(&self) -> f64; }
trait Circle where Self: Shape {
    fn radius(&self) -> f64 {
        // A = pi * r^2
        // so algebraically,
        // r = sqrt(A / pi)
        (self.area() /std::f64::consts::PI).sqrt()
    }
}
```
{==+==}

{==+==}


{==+==}
This next example calls a supertrait method on a generic parameter.
{==+==}
接下来的示例，是在泛型参数上调用 supertrait 方法。
{==+==}


{==+==}
```rust
# trait Shape { fn area(&self) -> f64; }
# trait Circle : Shape { fn radius(&self) -> f64; }
fn print_area_and_radius<C: Circle>(c: C) {
    // Here we call the area method from the supertrait `Shape` of `Circle`.
    println!("Area: {}", c.area());
    println!("Radius: {}", c.radius());
}
```
{==+==}
```rust
# trait Shape { fn area(&self) -> f64; }
# trait Circle : Shape { fn radius(&self) -> f64; }
fn print_area_and_radius<C: Circle>(c: C) {
    // 这里从 `Circle` 的 supertrait `Shape` 中调用 area 方法。
    println!("Area: {}", c.area());
    println!("Radius: {}", c.radius());
}
```
{==+==}


{==+==}
Similarly, here is an example of calling supertrait methods on trait objects.
{==+==}
以下是一个在 trait 对象上调用 supertrait 方法的例子。
{==+==}


{==+==}
```rust
# trait Shape { fn area(&self) -> f64; }
# trait Circle : Shape { fn radius(&self) -> f64; }
# struct UnitCircle;
# impl Shape for UnitCircle { fn area(&self) -> f64 { std::f64::consts::PI } }
# impl Circle for UnitCircle { fn radius(&self) -> f64 { 1.0 } }
# let circle = UnitCircle;
let circle = Box::new(circle) as Box<dyn Circle>;
let nonsense = circle.radius() * circle.area();
```
{==+==}

{==+==}


{==+==}
## Unsafe traits
{==+==}

{==+==}


{==+==}
Traits items that begin with the `unsafe` keyword indicate that *implementing* the
trait may be [unsafe]. It is safe to use a correctly implemented unsafe trait.
The [trait implementation] must also begin with the `unsafe` keyword.
{==+==}
以 `unsafe` 关键字开头的 Trait 条目表明，*实现* 该特性可能是 [unsafe] 。使用正确实现的 unsafe trait 是安全的。
[trait 实现][trait implementation] 也必须以 `unsafe` 关键字开头。
{==+==}


{==+==}
[`Sync`] and [`Send`] are examples of unsafe traits.
{==+==}
[`Sync`] 和 [`Send`] 是 unsafe trait 的例子。
{==+==}


{==+==}
## Parameter patterns
{==+==}
## 参数模式
{==+==}


{==+==}
Function or method declarations without a body only allow [IDENTIFIER] or
`_` [wild card][WildcardPattern] patterns. `mut` [IDENTIFIER] is currently
allowed, but it is deprecated and will become a hard error in the future.
<!-- https://github.com/rust-lang/rust/issues/35203 -->
{==+==}
没有主体的函数或方法声明只允许 [IDENTIFIER] 或 `_` [wild card][WildcardPattern] 模式。
 `mut` [IDENTIFIER] 目前是允许的，但已废弃，可能会成为未来的错误。
<!-- https://github.com/rust-lang/rust/issues/35203 -->
{==+==}


{==+==}
In the 2015 edition, the pattern for a trait function or method parameter is
optional:
{==+==}
在2015版中，trait 函数或方法参数的模式是可选的。
{==+==}


{==+==}
```rust,edition2015
// 2015 Edition
trait T {
    fn f(i32);  // Parameter identifiers are not required.
}
```
{==+==}
```rust,edition2015
// 2015 版
trait T {
    fn f(i32);  // 不需要参数标识符。
}
```
{==+==}


{==+==}
The kinds of patterns for parameters is limited to one of the following:
{==+==}
参数的模式类型仅限于以下类型之一:
{==+==}


{==+==}
* [IDENTIFIER]
* `mut` [IDENTIFIER]
* [`_`][WildcardPattern]
* `&` [IDENTIFIER]
* `&&` [IDENTIFIER]
{==+==}

{==+==}


{==+==}
Beginning in the 2018 edition, function or method parameter patterns are no
longer optional. Also, all irrefutable patterns are allowed as long as there
is a body. Without a body, the limitations listed above are still in effect.
{==+==}
从2018版开始，函数或方法参数模式不再是可选的。
此外，只要有主体，所有不可驳斥的模式都是允许的。
如果没有主体，上述的限制仍然有效。
{==+==}


{==+==}
```rust
trait T {
    fn f1((a, b): (i32, i32)) {}
    fn f2(_: (i32, i32));  // Cannot use tuple pattern without a body.
}
```
{==+==}
```rust
trait T {
    fn f1((a, b): (i32, i32)) {}
    fn f2(_: (i32, i32));  // 不能使用没有主体的元组模式。
}
```
{==+==}


{==+==}
## Item visibility
{==+==}
## 条目可见性
{==+==}


{==+==}
Trait items syntactically allow a [_Visibility_] annotation, but this is
rejected when the trait is validated. This allows items to be parsed with a
unified syntax across different contexts where they are used. As an example,
an empty `vis` macro fragment specifier can be used for trait items, where the
macro rule may be used in other situations where visibility is allowed.
{==+==}
Trait 条目在语法上允许 [_Visibility_] 注解，但当 Trait 被验证时，将拒绝。
这允许条目在不同的上下文中以统一的语法进行解析。
作为例子，一个空的 `vis` 宏片段 specifier "指定器" 可以用于 trait 条目，在其他允许可见性的情况下可以使用宏规则。
{==+==}


{==+==}
```rust
macro_rules! create_method {
    ($vis:vis $name:ident) => {
        $vis fn $name(&self) {}
    };
}

trait T1 {
    // Empty `vis` is allowed.
    create_method! { method_of_t1 }
}

struct S;

impl S {
    // Visibility is allowed here.
    create_method! { pub method_of_s }
}

impl T1 for S {}

fn main() {
    let s = S;
    s.method_of_t1();
    s.method_of_s();
}
```
{==+==}
```rust
macro_rules! create_method {
    ($vis:vis $name:ident) => {
        $vis fn $name(&self) {}
    };
}

trait T1 {
    // 允许使用空的 `vis` 。
    create_method! { method_of_t1 }
}

struct S;

impl S {
    // 这里允许可见性。
    create_method! { pub method_of_s }
}

impl T1 for S {}

fn main() {
    let s = S;
    s.method_of_t1();
    s.method_of_s();
}
```
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[WildcardPattern]: ../patterns.md#wildcard-pattern
[_AssociatedItem_]: associated-items.md
[_GenericParams_]: generics.md
[_InnerAttribute_]: ../attributes.md
[_TypeParamBounds_]: ../trait-bounds.md
[_Visibility_]: ../visibility-and-privacy.md
[_WhereClause_]: generics.md#where-clauses
[bounds]: ../trait-bounds.md
[trait object]: ../types/trait-object.md
[RFC 255]: https://github.com/rust-lang/rfcs/blob/master/text/0255-object-safety.md
[associated items]: associated-items.md
[method]: associated-items.md#methods
[supertraits]: #supertraits
[implementations]: implementations.md
[generics]: generics.md
[where clauses]: generics.md#where-clauses
[generic functions]: functions.md#generic-functions
[unsafe]: ../unsafety.md
[trait implementation]: implementations.md#trait-implementations
[`Send`]: ../special-types-and-traits.md#send
[`Sync`]: ../special-types-and-traits.md#sync
[`Arc<Self>`]: ../special-types-and-traits.md#arct
[`Box<Self>`]: ../special-types-and-traits.md#boxt
[`Pin<P>`]: ../special-types-and-traits.md#pinp
[`Rc<Self>`]: ../special-types-and-traits.md#rct
[`async`]: functions.md#async-functions
[`const`]: functions.md#const-functions
{==+==}

{==+==}