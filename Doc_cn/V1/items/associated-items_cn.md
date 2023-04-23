{==+==}
# Associated Items
{==+==}
# 关联条目
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _AssociatedItem_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_MacroInvocationSemi_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | ( [_Visibility_]<sup>?</sup> ( [_TypeAlias_] | [_ConstantItem_] | [_Function_] ) )\
> &nbsp;&nbsp; )
{==+==}
> **<sup>语法</sup>**\
> _关联条目_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_宏调用语句_][_MacroInvocationSemi_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | ( [_可见性_][_Visibility_]<sup>?</sup> ( [_类型别名_][_TypeAlias_] | [_常量条目_][_ConstantItem_] | [_函数_][_Function_] ) )\
> &nbsp;&nbsp; )
{==+==}


{==+==}
*Associated Items* are the items declared in [traits] or defined in
[implementations]. They are called this because they are defined on an associate
type &mdash; the type in the implementation. They are a subset of the kinds of
items you can declare in a module. Specifically, there are [associated
functions] (including methods), [associated types], and [associated constants].
{==+==}
*关联条目* 是在 Rust 语言中通过 [traits] 或 [实现][implementations] 中的类型定义的条目。
它们被称为关联条目，是因为它们是在关联类型上定义的。
关联条目是你可以在模块中声明的条目的子集，具体包括 [关联函数][associated functions] (包括方法)、 [关联类型][associated types] 和 [关联常量][associated constants]。
{==+==}


{==+==}
[associated functions]: #associated-functions-and-methods
[associated types]: #associated-types
[associated constants]: #associated-constants
{==+==}

{==+==}


{==+==}
Associated items are useful when the associated item logically is related to the
associating item. For example, the `is_some` method on `Option` is intrinsically
related to Options, so should be associated.
{==+==}
关联条目在关联类型中定义，通常是与关联类型有逻辑关系。
例如， `is_some` 方法与 `Option` 类型本身有密切的关系，因此应该定义为关联条目。
{==+==}


{==+==}
Every associated item kind comes in two varieties: definitions that contain the
actual implementation and declarations that declare signatures for
definitions.
{==+==}
每个关联条目都有两种类型：定义和声明。定义包含实际的实现，而声明只声明定义的签名。
{==+==}


{==+==}
It is the declarations that make up the contract of traits and what is available
on generic types.
{==+==}
在 Rust 语言中，trait 的声明和在泛型类型上可用的内容构成了 trait 的约定。
{==+==}


{==+==}
## Associated functions and methods
{==+==}
## 关联函数和方法
{==+==}


{==+==}
*Associated functions* are [functions] associated with a type.
{==+==}
*关联函数* 是与类型相关的 [函数][functions] 。
{==+==}


{==+==}
An *associated function declaration* declares a signature for an associated
function definition. It is written as a function item, except the
function body is replaced with a `;`.
{==+==}
*关联函数声明* 声明了关联函数定义的签名。它采用函数条目的形式书写，但函数体被替换为 `;` 。
{==+==}


{==+==}
The identifier is the name of the function. The generics, parameter list,
return type, and where clause of the associated function must be the same as the
associated function declarations's.
{==+==}
标识符是函数的名称。关联函数的泛型、参数列表、返回类型和 where 子句必须与关联函数声明相同。
{==+==}


{==+==}
An *associated function definition* defines a function associated with another
type. It is written the same as a [function item].
{==+==}
*关联函数定义* 是定义与另一种类型关联的函数。它的写法与 [函数条目][function item] 相同。
{==+==}


{==+==}
An example of a common associated function is a `new` function that returns
a value of the type the associated function is associated with.
{==+==}
一个常见的关联函数例子是 `new` 函数，它返回与关联函数关联的类型的值。
{==+==}


{==+==}
```rust
struct Struct {
    field: i32
}

impl Struct {
    fn new() -> Struct {
        Struct {
            field: 0i32
        }
    }
}

fn main () {
    let _struct = Struct::new();
}
```
{==+==}

{==+==}


{==+==}
When the associated function is declared on a trait, the function can also be
called with a [path] that is a path to the trait appended by the name of the
trait. When this happens, it is substituted for `<_ as Trait>::function_name`.
{==+==}
当关联函数在 trait 中声明时，可以使用 [路径][path] 调用该函数，该路径是附加 trait 名称的路径。
当发生这种情况时，它会替换为 `<_ as Trait>::function_name`。
{==+==}


{==+==}
```rust
trait Num {
    fn from_i32(n: i32) -> Self;
}

impl Num for f64 {
    fn from_i32(n: i32) -> f64 { n as f64 }
}

// These 4 are all equivalent in this case.
let _: f64 = Num::from_i32(42);
let _: f64 = <_ as Num>::from_i32(42);
let _: f64 = <f64 as Num>::from_i32(42);
let _: f64 = f64::from_i32(42);
```
{==+==}
```rust
trait Num {
    fn from_i32(n: i32) -> Self;
}

impl Num for f64 {
    fn from_i32(n: i32) -> f64 { n as f64 }
}

// 在这种情况下，这4个都是等价的。
let _: f64 = Num::from_i32(42);
let _: f64 = <_ as Num>::from_i32(42);
let _: f64 = <f64 as Num>::from_i32(42);
let _: f64 = f64::from_i32(42);
```
{==+==}


{==+==}
### Methods
{==+==}
### 方法
{==+==}


{==+==}
Associated functions whose first parameter is named `self` are called *methods*
and may be invoked using the [method call operator], for example, `x.foo()`, as
well as the usual function call notation.
{==+==}
第一个参数名为 `self` 的关联函数称为 *方法* ，可以使用 [方法调用运算符][method call operator] 例如 `x.foo()` ，以及通常的函数调用符号进行调用。
{==+==}


{==+==}
If the type of the `self` parameter is specified, it is limited to types resolving
to one generated by the following grammar (where `'lt` denotes some arbitrary
lifetime):
{==+==}
如果指定了 `self` 参数的类型，则限制解析为以下语法生成的类型之一 (其中 `'lt` 表示任意生命周期):
{==+==}


{==+==}
```text
P = &'lt S | &'lt mut S | Box<S> | Rc<S> | Arc<S> | Pin<P>
S = Self | P
```
{==+==}

{==+==}


{==+==}
The `Self` terminal in this grammar denotes a type resolving to the implementing type.
This can also include the contextual type alias `Self`, other type aliases,
or associated type projections resolving to the implementing type.
{==+==}
在这个语法中，`Self` 表示解析为实现类型的类型。
这也可以包括上下文类型别名 `Self `、其他类型别名或解析为实现类型的关联类型推导。
{==+==}


{==+==}
```rust
# use std::rc::Rc;
# use std::sync::Arc;
# use std::pin::Pin;
// Examples of methods implemented on struct `Example`.
{==+==}
```rust
# use std::rc::Rc;
# use std::sync::Arc;
# use std::pin::Pin;
// 在结构 `Example` 上实现方法的例子。
{==+==}


{==+==}
struct Example;
type Alias = Example;
trait Trait { type Output; }
impl Trait for Example { type Output = Example; }
impl Example {
    fn by_value(self: Self) {}
    fn by_ref(self: &Self) {}
    fn by_ref_mut(self: &mut Self) {}
    fn by_box(self: Box<Self>) {}
    fn by_rc(self: Rc<Self>) {}
    fn by_arc(self: Arc<Self>) {}
    fn by_pin(self: Pin<&Self>) {}
    fn explicit_type(self: Arc<Example>) {}
    fn with_lifetime<'a>(self: &'a Self) {}
    fn nested<'a>(self: &mut &'a Arc<Rc<Box<Alias>>>) {}
    fn via_projection(self: <Example as Trait>::Output) {}
}
```
{==+==}

{==+==}


{==+==}
Shorthand syntax can be used without specifying a type, which have the
following equivalents:
{==+==}
可以使用不指定类型的简写语法，其等价于以下内容:
{==+==}


{==+==}
Shorthand             | Equivalent
----------------------|-----------
`self`                | `self: Self`
`&'lifetime self`     | `self: &'lifetime Self`
`&'lifetime mut self` | `self: &'lifetime mut Self`
{==+==}
简写             | 等同于
----------------------|-----------
`self`                | `self: Self`
`&'lifetime self`     | `self: &'lifetime Self`
`&'lifetime mut self` | `self: &'lifetime mut Self`
{==+==}


{==+==}
> **Note**: Lifetimes can be, and usually are, elided with this shorthand.
{==+==}
> **注意**: 生命周期通常也可以用这种简写语法。
{==+==}


{==+==}
If the `self` parameter is prefixed with `mut`, it becomes a mutable variable,
similar to regular parameters using a `mut` [identifier pattern]. For example:
{==+==}
如果 `self` 参数带有 `mut` 前缀，则它将变为可变变量，类似于使用 `mut` [标识符模式][identifier pattern] 的常规参数。例如:
{==+==}


{==+==}
```rust
trait Changer: Sized {
    fn change(mut self) {}
    fn modify(mut self: Box<Self>) {}
}
```
{==+==}

{==+==}


{==+==}
As an example of methods on a trait, consider the following:
{==+==}
以下是关于 trait 上方法的例子：
{==+==}


{==+==}
```rust
# type Surface = i32;
# type BoundingBox = i32;
trait Shape {
    fn draw(&self, surface: Surface);
    fn bounding_box(&self) -> BoundingBox;
}
```
{==+==}

{==+==}


{==+==}
This defines a trait with two methods. All values that have [implementations]
of this trait while the trait is in scope can have their `draw` and
`bounding_box` methods called.
{==+==}
这个例子定义了一个具有两个方法的 trait 。只要在该 trait 作用域内的时候，所有 [实现][implementations] 该 trait 的值都可以调用其 `draw` 和 `bounding_box` 方法。
{==+==}


{==+==}
```rust
# type Surface = i32;
# type BoundingBox = i32;
# trait Shape {
#     fn draw(&self, surface: Surface);
#     fn bounding_box(&self) -> BoundingBox;
# }
#
struct Circle {
    // ...
}

impl Shape for Circle {
    // ...
#   fn draw(&self, _: Surface) {}
#   fn bounding_box(&self) -> BoundingBox { 0i32 }
}

# impl Circle {
#     fn new() -> Circle { Circle{} }
# }
#
let circle_shape = Circle::new();
let bounding_box = circle_shape.bounding_box();
```
{==+==}

{==+==}


{==+==}
> **Edition Differences**: In the 2015 edition, it is possible to declare trait
> methods with anonymous parameters (e.g. `fn foo(u8)`). This is deprecated and
> an error as of the 2018 edition. All parameters must have an argument name.
{==+==}
> **版本差异**: 在 2015 版中，可以使用匿名参数 (例如 `fn foo(u8)` ) 声明 trait 方法。但在 2018 版中，这已被弃用并且会导致错误。所有参数必须有参数名。
{==+==}


{==+==}
#### Attributes on method parameters
{==+==}
#### 方法参数的属性
{==+==}


{==+==}
Attributes on method parameters follow the same rules and restrictions as
[regular function parameters].
{==+==}
方法参数上的属性遵循与 [常规函数参数][regular function parameters] 相同的规则和限制。
{==+==}


{==+==}
## Associated Types
{==+==}
## 关联类型
{==+==}


{==+==}
*Associated types* are [type aliases] associated with another type. Associated
types cannot be defined in [inherent implementations] nor can they be given a
default implementation in traits.
{==+==}
*关联类型* 是与另一个类型相关联的 [类型别名][type aliases] 。关联类型不能在 [内部实现][inherent implementations] 中定义，也不能在 trait 中给出默认实现。
{==+==}


{==+==}
An *associated type declaration* declares a signature for associated type
definitions. It is written in one of the following forms, where `Assoc` is the
name of the associated type, `Params` is a comma-separated list of type,
lifetime or const parameters, `Bounds` is a plus-separated list of trait bounds
that the associated type must meet, and `WhereBounds` is a comma-separated list
of bounds that the parameters must meet:
{==+==}
 *关联类型声明* 声明了关联类型定义的签名。
 它以以下一种形式书写，其中 `Assoc` 是关联类型的名称，`Params` 是由逗号分隔的类型、生命周期或常量参数列表，
 `Bounds` 是关联类型必须满足的一组用加号分隔的 trait 约束列表， `WhereBounds` 是参数必须满足的一组用逗号分隔的约束列表:
{==+==}


{==+==}
<!-- ignore: illustrative example forms -->
```rust,ignore
type Assoc;
type Assoc: Bounds;
type Assoc<Params>;
type Assoc<Params>: Bounds;
type Assoc<Params> where WhereBounds;
type Assoc<Params>: Bounds where WhereBounds;
```
{==+==}

{==+==}


{==+==}
The identifier is the name of the declared type alias. The optional trait bounds
must be fulfilled by the implementations of the type alias.
There is an implicit [`Sized`] bound on associated types that can be relaxed using the special `?Sized` bound.
{==+==}
该标识符是已声明的类型别名的名称。可选的 trait 约束必须由类型别名的实现满足。
关联类型存在一个隐式 [`Sized`] 约束，可以使用特殊的 `?Sized` 约束放宽。
{==+==}


{==+==}
An *associated type definition* defines a type alias for the implementation
of a trait on a type. They are written similarly to an *associated type declaration*,
but cannot contain `Bounds`, but instead must contain a `Type`:
{==+==}
*关联类型定义* 为类型别名，用于为类型上的 trait 实现定义类型。它们的书写方式与 *关联类型声明* 类似，但不能包含 `Bounds`，而是必须包含一个 `Type` :
{==+==}


{==+==}
<!-- ignore: illustrative example forms -->
```rust,ignore
type Assoc = Type;
type Assoc<Params> = Type; // the type `Type` here may reference `Params`
type Assoc<Params> = Type where WhereBounds;
type Assoc<Params> where WhereBounds = Type; // deprecated, prefer the form above
```
{==+==}
<!-- ignore: illustrative example forms -->
```rust,ignore
type Assoc = Type;
type Assoc<Params> = Type; // 这里的 `Type` 可以引用 `Params` 
type Assoc<Params> = Type where WhereBounds;
type Assoc<Params> where WhereBounds = Type; // 已废弃，请选择上面的形式
```
{==+==}


{==+==}
If a type `Item` has an associated type `Assoc` from a trait `Trait`, then
`<Item as Trait>::Assoc` is a type that is an alias of the type specified in the
associated type definition. Furthermore, if `Item` is a type parameter, then
`Item::Assoc` can be used in type parameters.
{==+==}
如果一个类型 `Item` 拥有一个从 trait `Trait` 中获取的关联类型 `Assoc`，那么 `<Item as Trait>::Assoc` 就是一个类型，它是关联类型定义中指定的类型的别名。此外，如果 `Item` 是一个类型参数，那么 `Item::Assoc` 可以在类型参数中使用。
{==+==}


{==+==}
Associated types may include [generic parameters] and [where clauses]; these are
often referred to as *generic associated types*, or *GATs*. If the type `Thing`
has an associated type `Item` from a trait `Trait` with the generics `<'a>` , the
type can be named like `<Thing as Trait>::Item<'x>`, where `'x` is some lifetime
in scope. In this case, `'x` will be used wherever `'a` appears in the associated
type definitions on impls.
{==+==}
关联类型可以包括 [泛型参数][generic parameters] 和 [where 约束][where clauses] ；这些通常被称为 *泛型关联类型* 或 *GATs* 。
如果类型 `Thing` 从 trait `Trait` 中具有泛型 `<'a>` 的关联类型 `Item`，则该类型可以命名为 `<Thing as Trait>::Item<'x>`，其中 `'x` 是作用域内的某个生命周期。
在这种情况下，无论何时出现在 impl 上的关联类型定义中的 `'a` 都将使用 `'x` 。

关联类型可能包含 [泛型参数][generic parameters] 和 [where 约束][where clauses] ；这些通常被称为 *泛型关联类型* 或 GAT。
如果类型 `Thing` 有一个来自 `Trait` 的关联类型 `Item` ，具有泛型 `<'a>` ，则可以像 `<Thing as Trait> ::Item<'x>` 这样命名该类型，其中 `'x` 是某个有效作用域内的生命周期。
在这种情况下，`'x` 将在实现中的关联类型定义中的 `'a` 处使用。
{==+==}


{==+==}
```rust
trait AssociatedType {
    // Associated type declaration
    type Assoc;
}

struct Struct;

struct OtherStruct;

impl AssociatedType for Struct {
    // Associated type definition
    type Assoc = OtherStruct;
}

impl OtherStruct {
    fn new() -> OtherStruct {
        OtherStruct
    }
}

fn main() {
    // Usage of the associated type to refer to OtherStruct as <Struct as AssociatedType>::Assoc
    let _other_struct: OtherStruct = <Struct as AssociatedType>::Assoc::new();
}
```
{==+==}
```rust
trait AssociatedType {
    // 关联类型声明
    type Assoc;
}

struct Struct;

struct OtherStruct;

impl AssociatedType for Struct {
    // 关联类型定义
    type Assoc = OtherStruct;
}

impl OtherStruct {
    fn new() -> OtherStruct {
        OtherStruct
    }
}

fn main() {
    // 用关联类型来指代 OtherStruct 为 <Struct as AssociatedType>::Assoc
    let _other_struct: OtherStruct = <Struct as AssociatedType>::Assoc::new();
}
```
{==+==}


{==+==}
An example of associated types with generics and where clauses:
{==+==}
带有泛型和 where 子句的关联类型的示例:
{==+==}


{==+==}
```rust
struct ArrayLender<'a, T>(&'a mut [T; 16]);

trait Lend {
    // Generic associated type declaration
    type Lender<'a> where Self: 'a;
    fn lend<'a>(&'a mut self) -> Self::Lender<'a>;
}

impl<T> Lend for [T; 16] {
    // Generic associated type definition
    type Lender<'a> = ArrayLender<'a, T> where Self: 'a;

    fn lend<'a>(&'a mut self) -> Self::Lender<'a> {
        ArrayLender(self)
    }
}

fn borrow<'a, T: Lend>(array: &'a mut T) -> <T as Lend>::Lender<'a> {
    array.lend()
}


fn main() {
    let mut array = [0usize; 16];
    let lender = borrow(&mut array);
}
```
{==+==}
```rust
struct ArrayLender<'a, T>(&'a mut [T; 16]);

trait Lend {
    // 泛型关联类型声明
    type Lender<'a> where Self: 'a;
    fn lend<'a>(&'a mut self) -> Self::Lender<'a>;
}

impl<T> Lend for [T; 16] {
    // 泛型关联类型定义
    type Lender<'a> = ArrayLender<'a, T> where Self: 'a;

    fn lend<'a>(&'a mut self) -> Self::Lender<'a> {
        ArrayLender(self)
    }
}

fn borrow<'a, T: Lend>(array: &'a mut T) -> <T as Lend>::Lender<'a> {
    array.lend()
}


fn main() {
    let mut array = [0usize; 16];
    let lender = borrow(&mut array);
}
```
{==+==}


{==+==}
### Associated Types Container Example
{==+==}
### 关联类型容器实例
{==+==}


{==+==}
Consider the following example of a `Container` trait. Notice that the type is
available for use in the method signatures:
{==+==}
考虑以下 `Container` trait 的示例。请注意，此类型可用于方法签名中:
{==+==}


{==+==}
```rust
trait Container {
    type E;
    fn empty() -> Self;
    fn insert(&mut self, elem: Self::E);
}
```
{==+==}

{==+==}


{==+==}
In order for a type to implement this trait, it must not only provide
implementations for every method, but it must specify the type `E`. Here's an
implementation of `Container` for the standard library type `Vec`:
{==+==}
为了使类型实现此 trait ，它不仅必须为每个方法提供实现，还必须指定类型 `E` 。这里是标准库类型 `Vec` 的 `Container` 的实现:
{==+==}


{==+==}
```rust
# trait Container {
#     type E;
#     fn empty() -> Self;
#     fn insert(&mut self, elem: Self::E);
# }
impl<T> Container for Vec<T> {
    type E = T;
    fn empty() -> Vec<T> { Vec::new() }
    fn insert(&mut self, x: T) { self.push(x); }
}
```
{==+==}

{==+==}


{==+==}
### Relationship between `Bounds` and `WhereBounds`
{==+==}
### `Bounds` 和 `WhereBounds` 之间的关系
{==+==}


{==+==}
In this example:

```rust
# use std::fmt::Debug;
trait Example {
    type Output<T>: Ord where T: Debug;
}
```
{==+==}
在这个例子中:

```rust
# use std::fmt::Debug;
trait Example {
    type Output<T>: Ord where T: Debug;
}
```
{==+==}


{==+==}
Given a reference to the associated type like `<X as Example>::Output<Y>`, the associated type itself must be `Ord`, and the type `Y` must be `Debug`.
{==+==}
给定对关联类型的引用，如 `<X as Example>::Output<Y>` ，则关联类型本身必须是 `Ord` ，而类型 `Y` 必须是 `Debug` 。
{==+==}


{==+==}
### Required where clauses on generic associated types
{==+==}
### 要求在泛型关联类型上的where约束
{==+==}


{==+==}
Generic associated type declarations on traits currently may require a list of
where clauses, dependent on functions in the trait and how the GAT is used. These
rules may be loosened in the future; updates can be found [on the generic
associated types initiative repository](https://rust-lang.github.io/generic-associated-types-initiative/explainer/required_bounds.html).
{==+==}
trait 上的泛型关联类型声明当前可能需要一系列 where 子句 ，这取决于 trait 中的函数以及如何使用 GAT 。
未来这些规则可能会放宽;
更新内容可以在 [泛型关联类型提案库](https://rust-lang.github.io/generic-associated-types-initiative/explainer/required_bounds.html) 中找到。
{==+==}


{==+==}
In a few words, these where clauses are required in order to maximize the allowed
definitions of the associated type in impls. To do this, any clauses that *can be
proven to hold* on functions (using the parameters of the function or trait)
where a GAT appears as an input or output must also be written on the GAT itself.
{==+==}
简而言之，这些 `where` 子句是必需的，以便最大限度允许在 impls 中定义关联类型的范围。
为了做到这一点，在任何出现 GAT 作为输入或输出的函数 (使用函数或 trait 的参数) 上，任何 *可以证明持有* 子句也必须写在 GAT 本身上。
{==+==}


{==+==}
```rust
trait LendingIterator {
    type Item<'x> where Self: 'x;
    fn next<'a>(&'a mut self) -> Self::Item<'a>;
}
```
{==+==}

{==+==}


{==+==}
In the above, on the `next` function, we can prove that `Self: 'a`, because of
the implied bounds from `&'a mut self`; therefore, we must write the equivalent
bound on the GAT itself: `where Self: 'x`.
{==+==}
在上面的例子中，在 `next` 函数中，我们可以证明 `Self: 'a`，因为我们从 `&'a mut self` 推导出了这个隐含的限制。
因此，我们必须在 GAT 自身上写出等效的限制: `where Self: 'x` 。
{==+==}


{==+==}
When there are multiple functions in a trait that use the GAT, then the
*intersection* of the bounds from the different functions are used, rather than
the union.
{==+==}
当 trait 中有多个函数使用 GAT 时，会使用不同函数的边界的 *交集* ，而不是并集。
{==+==}


{==+==}
```rust
trait Check<T> {
    type Checker<'x>;
    fn create_checker<'a>(item: &'a T) -> Self::Checker<'a>;
    fn do_check(checker: Self::Checker<'_>);
}
```
{==+==}

{==+==}


{==+==}
In this example, no bounds are required on the `type Checker<'a>;`. While we
know that `T: 'a` on `create_checker`, we do not know that on `do_check`. However,
if `do_check` was commented out, then the `where T: 'x` bound would be required
on `Checker`.
{==+==}
在这个例子中， `type Checker<'a>;` 不需要任何约束。虽然我们知道在 `create_checker` 函数中 `T: 'a` ，但是我们不知道在 `do_check` 函数中。
然而，如果将 `do_check` 函数注释掉，那么 `Checker` 就需要一个 `where T: 'x` 的约束。
{==+==}


{==+==}
The bounds on associated types also propagate required where clauses.
{==+==}
关联类型上的约束也会传播所需的 where 子句。
{==+==}


{==+==}
```rust
trait Iterable {
    type Item<'a> where Self: 'a;
    type Iterator<'a>: Iterator<Item = Self::Item<'a>> where Self: 'a;
    fn iter<'a>(&'a self) -> Self::Iterator<'a>;
}
```
{==+==}

{==+==}


{==+==}
Here, `where Self: 'a` is required on `Item` because of `iter`. However, `Item`
is used in the bounds of `Iterator`, the `where Self: 'a` clause is also required
there.
{==+==}
在这里，由于 `iter` ， `Item` 上需要 `where Self: 'a` 。但是，由于 `Iterator` 的约束中使用了 `Item` ，因此也需要在那里写出 `where Self: 'a` 子句。
{==+==}


{==+==}
Finally, any explicit uses of `'static` on GATs in the trait do not count towards
the required bounds.
{==+==}
最后，在 trait 中对 GAT 进行显式使用时的 `'static` 不计入所需的约束。
{==+==}


{==+==}
```rust
trait StaticReturn {
    type Y<'a>;
    fn foo(&self) -> Self::Y<'static>;
}
```
{==+==}

{==+==}


{==+==}
## Associated Constants
{==+==}
## 关联常量
{==+==}


{==+==}
*Associated constants* are [constants] associated with a type.
{==+==}
*关联常量* 是与类型相关联的 [常量][constants] 。
{==+==}


{==+==}
An *associated constant declaration* declares a signature for associated
constant definitions. It is written as `const`, then an identifier,
then `:`, then a type, finished by a `;`.
{==+==}
 *关联常量声明* 声明了关联常量定义的签名。它写为 `const` ，然后是标识符，然后是 `:` ，然后是类型，最后以 `;` 结束。
{==+==}


{==+==}
The identifier is the name of the constant used in the path. The type is the
type that the definition has to implement.
{==+==}
标识符是路径中使用的常量的名称。类型是定义必须实现的类型。
{==+==}


{==+==}
An *associated constant definition* defines a constant associated with a
type. It is written the same as a [constant item].
{==+==}
*关联常量定义* 定义了与类型相关联的常量。它的写法与 [常量条目][constant item] 相同。
{==+==}


{==+==}
Associated constant definitions undergo [constant evaluation] only when
referenced. Further, definitions that include [generic parameters] are
evaluated after monomorphization.
{==+==}
关联常量定义仅在被引用时才会经过 [常量求值][constant evaluation] 。
此外，包括 [泛型参数][generic parameters] 的定义会在单态化后进行求值。
{==+==}


{==+==}
```rust,compile_fail
struct Struct;
struct GenericStruct<const ID: i32>;

impl Struct {
    // Definition not immediately evaluated
    const PANIC: () = panic!("compile-time panic");
}

impl<const ID: i32> GenericStruct<ID> {
    // Definition not immediately evaluated
    const NON_ZERO: () = if ID == 0 {
        panic!("contradiction")
    };
}

fn main() {
    // Referencing Struct::PANIC causes compilation error
    let _ = Struct::PANIC;

    // Fine, ID is not 0
    let _ = GenericStruct::<1>::NON_ZERO;

    // Compilation error from evaluating NON_ZERO with ID=0
    let _ = GenericStruct::<0>::NON_ZERO;
}
```
{==+==}
```rust,compile_fail
struct Struct;
struct GenericStruct<const ID: i32>;

impl Struct {
    // 定义没有立即评估
    const PANIC: () = panic!("compile-time panic");
}

impl<const ID: i32> GenericStruct<ID> {
    // 定义没有立即评估
    const NON_ZERO: () = if ID == 0 {
        panic!("contradiction")
    };
}

fn main() {
    // 引用 Struct::PANIC 会导致编译错误
    let _ = Struct::PANIC;

    // 可以, ID 不是 0
    let _ = GenericStruct::<1>::NON_ZERO;

    // 在 ID=0 的情况下评估 NON_ZERO 是编译错误
    let _ = GenericStruct::<0>::NON_ZERO;
}
```
{==+==}


{==+==}
### Associated Constants Examples
{==+==}
### 关联常量实例
{==+==}


{==+==}
A basic example:

```rust
trait ConstantId {
    const ID: i32;
}

struct Struct;

impl ConstantId for Struct {
    const ID: i32 = 1;
}

fn main() {
    assert_eq!(1, Struct::ID);
}
```
{==+==}
一个基本的例子:

```rust
trait ConstantId {
    const ID: i32;
}

struct Struct;

impl ConstantId for Struct {
    const ID: i32 = 1;
}

fn main() {
    assert_eq!(1, Struct::ID);
}
```
{==+==}


{==+==}
Using default values:

```rust
trait ConstantIdDefault {
    const ID: i32 = 1;
}

struct Struct;
struct OtherStruct;

impl ConstantIdDefault for Struct {}

impl ConstantIdDefault for OtherStruct {
    const ID: i32 = 5;
}

fn main() {
    assert_eq!(1, Struct::ID);
    assert_eq!(5, OtherStruct::ID);
}
```
{==+==}
使用默认值:

```rust
// 定义一个 trait `ConstantIdDefault`
trait ConstantIdDefault {
    // 声明一个常量 `ID`，默认值为 `1`
    const ID: i32 = 1;
}

// 定义结构体 `Struct`
struct Struct;
// 定义结构体 `OtherStruct`
struct OtherStruct;

// 为结构体 `Struct` 实现 `ConstantIdDefault` trait
impl ConstantIdDefault for Struct {}

// 为结构体 `OtherStruct` 实现 `ConstantIdDefault` trait，并重新定义 `ID` 常量的值为 `5`
impl ConstantIdDefault for OtherStruct {
    const ID: i32 = 5;
}

// 程序入口函数
fn main() {
    // 断言结构体 `Struct` 的 `ID` 常量值为 `1`
    assert_eq!(1, Struct::ID);
    // 断言结构体 `OtherStruct` 的 `ID` 常量值为 `5`
    assert_eq!(5, OtherStruct::ID);
}
```
{==+==}


{==+==}
[_ConstantItem_]: constant-items.md
[_Function_]: functions.md
[_MacroInvocationSemi_]: ../macros.md#macro-invocation
[_OuterAttribute_]: ../attributes.md
[_TypeAlias_]: type-aliases.md
[_Visibility_]: ../visibility-and-privacy.md
[`Arc<Self>`]: ../special-types-and-traits.md#arct
[`Box<Self>`]: ../special-types-and-traits.md#boxt
[`Pin<P>`]: ../special-types-and-traits.md#pinp
[`Rc<Self>`]: ../special-types-and-traits.md#rct
[`Sized`]: ../special-types-and-traits.md#sized
[traits]: traits.md
[type aliases]: type-aliases.md
[inherent implementations]: implementations.md#inherent-implementations
[identifier]: ../identifiers.md
[identifier pattern]: ../patterns.md#identifier-patterns
[implementations]: implementations.md
[type]: ../types.md#type-expressions
[constants]: constant-items.md
[constant item]: constant-items.md
[functions]: functions.md
[function item]: ../types/function-item.md
[method call operator]: ../expressions/method-call-expr.md
[path]: ../paths.md
[regular function parameters]: functions.md#attributes-on-function-parameters
[generic parameters]: generics.md
[where clauses]: generics.md#where-clauses
[constant evaluation]: ../const_eval.md
{==+==}

{==+==}