{==+==}
# Special types and traits

Certain types and traits that exist in [the standard library] are known to the
Rust compiler. This chapter documents the special features of these types and
traits.

## `Box<T>`

[`Box<T>`] has a few special features that Rust doesn't currently allow for user
defined types.

* The [dereference operator] for `Box<T>` produces a place which can be moved
  from. This means that the `*` operator and the destructor of `Box<T>` are
  built-in to the language.
* [Methods] can take `Box<Self>` as a receiver.
* A trait may be implemented for `Box<T>` in the same crate as `T`, which the
  [orphan rules] prevent for other generic types.
{==+==}
# 特殊类型和 trait

某些在 [标准库][the standard library] 中存在的类型和 trait 对于 Rust 编译器是已知的。本章介绍这些类型和 trait 的特殊特性。

## `Box<T>`

[`Box<T>`] 具有 Rust 目前不允许用户定义类型拥有的一些特殊特性。
* `Box<T>` 的 [解引用运算符][dereference operator] 产生一个可以被移动的位置。这意味着 `*` 运算符和 `Box<T>` 的析构函数是内置到语言中的。
* [方法][Methods] 可以以 `Box<Self>` 作为接收者。
* 在与 `T` 相同的 crate 中可以为 `Box<T>` 实现 trait，而 [孤儿规则][orphan rules] 阻止了对其他泛型类型的实现。
{==+==}


{==+==}
## `Rc<T>`

[Methods] can take [`Rc<Self>`] as a receiver.

## `Arc<T>`

[Methods] can take [`Arc<Self>`] as a receiver.

## `Pin<P>`

[Methods] can take [`Pin<P>`] as a receiver.

## `UnsafeCell<T>`

[`std::cell::UnsafeCell<T>`] is used for [interior mutability]. It ensures that
the compiler doesn't perform optimisations that are incorrect for such types.
It also ensures that [`static` items] which have a type with interior
mutability aren't placed in memory marked as read only.
{==+==}
## `Rc<T>`

[`Rc<Self>`] 作为接收器也可以用于 [方法][Methods] 。

## `Arc<T>`

[`Arc<Self>`] 作为接收器也可以用于 [方法][Methods] 。

## `Pin<P>`

[`Pin<P>`] 作为接收器也可以用于 [方法][Methods] 。

## `UnsafeCell<T>`

[`std::cell::UnsafeCell<T>`] 用于 [内部可变性][interior mutability] 。它确保编译器不会为此类类型执行不正确的优化。
它还确保带有内部可变性的类型的 [`static` 条目][`static` items] 不会被放置在标记为只读的内存中。
{==+==}


{==+==}
## `PhantomData<T>`

[`std::marker::PhantomData<T>`] is a zero-sized, minimum alignment, type that
is considered to own a `T` for the purposes of [variance], [drop check], and
[auto traits](#auto-traits).

## Operator Traits

The traits in [`std::ops`] and [`std::cmp`] are used to overload [operators],
[indexing expressions], and [call expressions].

## `Deref` and `DerefMut`

As well as overloading the unary `*` operator, [`Deref`] and [`DerefMut`] are
also used in [method resolution] and [deref coercions].

## `Drop`

The [`Drop`] trait provides a [destructor], to be run whenever a value of this
type is to be destroyed.

## `Copy`

The [`Copy`] trait changes the semantics of a type implementing it. Values
whose type implements `Copy` are copied rather than moved upon assignment.

`Copy` can only be implemented for types which do not implement `Drop`, and whose fields are all `Copy`.
For enums, this means all fields of all variants have to be `Copy`.
For unions, this means all variants have to be `Copy`.

`Copy` is implemented by the compiler for

* [Tuples] of `Copy` types
* [Function pointers]
* [Function items]
* [Closures] that capture no values or that only capture values of `Copy` types
{==+==}
## `PhantomData<T>`

[`std::marker::PhantomData<T>`] 是一种零大小、最小对齐方式的类型。从 [variance]、[drop check] 和 [auto traits](#auto-traits) 的角度来看，它被视为拥有一个类型为 `T` 的实例。

## 运算符 trait

[`std::ops`] 和 [`std::cmp`] 中的 trait 用于重载 [运算符][operators] 、 [索引表达式][indexing expressions] 和 [调用表达式][call expressions] 。

## `Deref` 和 `DerefMut`

除了重载一元操作符 `*` 之外，[`Deref`] 和 [`DerefMut`] 还用于 [方法解析][method resolution] 和 [解引用强制转换][deref coercions]。

## `Drop`

[`Drop`] trait 提供了一个 [析构器][destructor] ，当该类型的值即将被销毁时运行。

## `Copy`

[`Copy`] trait 改变了实现它的类型的语义。实现 `Copy` 的类型的值在赋值时会被复制而不是移动。只有不实现 `Drop` trait 并且其所有字段都是 `Copy` 类型的类型才能实现 `Copy` trait 。
对于枚举，这意味着所有变体的所有字段都必须是 `Copy` 类型。对于 union ，这意味着所有变体都必须是 `Copy` 类型。编译器为以下类型实现了 `Copy` trait ：

* `Copy` 类型的元组
* [函数指针][Function pointers]
* [函数条目][Function items]
* 没有捕获任何值或仅捕获 `Copy` 类型值的 [闭包][Closures]
{==+==}


{==+==}
## `Clone`

The [`Clone`] trait is a supertrait of `Copy`, so it also needs compiler
generated implementations. It is implemented by the compiler for the following
types:

* Types with a built-in `Copy` implementation (see above)
* [Tuples] of `Clone` types

## `Send`

The [`Send`] trait indicates that a value of this type is safe to send from one
thread to another.

## `Sync`

The [`Sync`] trait indicates that a value of this type is safe to share between
multiple threads. This trait must be implemented for all types used in
immutable [`static` items].

## `Termination`

The [`Termination`] trait indicates the acceptable return types for the [main function] and [test functions].
{==+==}
## `Clone`

[`Clone`] trait 是 `Copy` 的超级 trait ，因此它也需要编译器生成的实现。编译器为以下类型实现了此 trait：

* 有内置 `Copy` 实现的类型 (见上文)
* [`Clone`] 类型的元组

## `Send`

[`Send`] trait 表明此类型的值可以安全地从一个线程发送到另一个线程。

## `Sync`

[`Sync`] trait 表明此类型的值可以安全地在多个线程之间共享。所有用于不可变 [`static` 条目][`static` items] 的类型都必须实现此 trait 。

## `Termination`

[`Termination`] trait 指示 [main 函数][main function] 和 [test 函数][test functions] 的可接受返回类型。
{==+==}


{==+==}
## Auto traits

The [`Send`], [`Sync`], [`Unpin`], [`UnwindSafe`], and [`RefUnwindSafe`] traits are _auto
traits_. Auto traits have special properties.

If no explicit implementation or negative implementation is written out for an
auto trait for a given type, then the compiler implements it automatically
according to the following rules:

* `&T`, `&mut T`, `*const T`, `*mut T`, `[T; n]`, and `[T]` implement the trait
  if `T` does.
* Function item types and function pointers automatically implement the trait.
* Structs, enums, unions, and tuples implement the trait if all of their fields
  do.
* Closures implement the trait if the types of all of their captures do. A
  closure that captures a `T` by shared reference and a `U` by value implements
  any auto traits that both `&T` and `U` do.

For generic types (counting the built-in types above as generic over `T`), if a
generic implementation is available, then the compiler does not automatically
implement it for types that could use the implementation except that they do not
meet the requisite trait bounds. For instance, the standard library implements
`Send` for all `&T` where `T` is `Sync`; this means that the compiler will not
implement `Send` for `&T` if `T` is `Send` but not `Sync`.

Auto traits can also have negative implementations, shown as `impl !AutoTrait
for T` in the standard library documentation, that override the automatic
implementations. For example `*mut T` has a negative implementation of `Send`,
and so `*mut T` is not `Send`, even if `T` is. There is currently no stable way
to specify additional negative implementations; they exist only in the standard
library.

Auto traits may be added as an additional bound to any [trait object], even
though normally only one trait is allowed. For instance, `Box<dyn Debug + Send +
UnwindSafe>` is a valid type.
{==+==}
## 自动 trait

[`Send`]、[`Sync`]、[`Unpin`]、[`UnwindSafe`] 和 [`RefUnwindSafe`] trait 是 "自动 trait" 。自动 trait 具有特殊的属性。如果对于给定类型的自动 trait 没有显式实现或负实现，则编译器会根据以下规则自动实现：

* 如果类型 `T` 实现了 trait ，那么 `&T` `&mut T` `*const T` `*mut T` `[T; n]` 和 `[T]` 都会实现该 trait 。
* 函数条目类型和函数指针会自动实现该 trait 。
* 如果它们的所有字段都实现了该 trait ，则结构体、枚举、联合体和元组会实现该 trait 。
* 如果它们捕获的所有值的类型都实现了该 trait ，则闭包会实现该 trait 。

捕获一个 `T` 的共享引用和一个 `U` 的值的闭包会实现两个 `&T` 和 `U` 都实现的自动 trait 。
对于泛型类型 (将上面内置类型视为泛型的 `T` )，如果有通用实现，则编译器不会自动实现该 trait ，而是根据需要的 trait 限制为没有达到 trait 限制的类型实现。例如，标准库为所有 `T` 是 `Sync` 的 `&T` 实现了 `Send` ；这意味着，如果 `T` 是 `Send` 但不是 `Sync` ，则编译器不会为 `&T` 实现 `Send` 。
自动 trait 也可以有负实现，在标准库文档中表示为 `impl !AutoTrait for T` ，它们覆盖了自动实现。例如， `*mut T` 有一个 `Send` 的负实现，因此即使 `T` 是 `Send` ， `*mut T` 也不是 `Send` 。
目前没有稳定的方法指定额外的负实现；它们只存在于标准库中。自动 trait 可以作为 [trait 对象][trait object] 的附加限制添加到任何 trait 中，尽管通常只允许一个 trait 。
例如，`Box<dyn Debug + Send + UnwindSafe>`是一个有效的类型。
{==+==}


{==+==}
## `Sized`

The [`Sized`] trait indicates that the size of this type is known at compile-time; that is, it's not a [dynamically sized type].
[Type parameters] (except `Self` in traits) are `Sized` by default, as are [associated types].
`Sized` is always implemented automatically by the compiler, not by [implementation items].
These implicit `Sized` bounds may be relaxed by using the special `?Sized` bound.
{==+==}
## `Sized`

[`Sized`] trait 表示这个类型在编译时大小是已知的，即它不是 [动态大小类型][dynamically sized type] 。
类型参数 (除了trait 中的 `Self` ) 默认都是 `Sized` 的，关联类型也是。
`Sized` trait 总是由编译器自动实现，而不是由 [实现条目][implementation items] 实现的。
这些隐式的 `Sized` 约束可以通过使用特殊的 `?Sized` 约束进行放宽。
{==+==}


{==+==}
[`Arc<Self>`]: ../std/sync/struct.Arc.html
[`Box<T>`]: ../std/boxed/struct.Box.html
[`Clone`]: ../std/clone/trait.Clone.html
[`Copy`]: ../std/marker/trait.Copy.html
[`Deref`]: ../std/ops/trait.Deref.html
[`DerefMut`]: ../std/ops/trait.DerefMut.html
[`Drop`]: ../std/ops/trait.Drop.html
[`Pin<P>`]: ../std/pin/struct.Pin.html
[`Rc<Self>`]: ../std/rc/struct.Rc.html
[`RefUnwindSafe`]: ../std/panic/trait.RefUnwindSafe.html
[`Send`]: ../std/marker/trait.Send.html
[`Sized`]: ../std/marker/trait.Sized.html
[`std::cell::UnsafeCell<T>`]: ../std/cell/struct.UnsafeCell.html
[`std::cmp`]: ../std/cmp/index.html
[`std::marker::PhantomData<T>`]: ../std/marker/struct.PhantomData.html
[`std::ops`]: ../std/ops/index.html
[`Termination`]: ../std/process/trait.Termination.html
[`UnwindSafe`]: ../std/panic/trait.UnwindSafe.html
[`Sync`]: ../std/marker/trait.Sync.html
[`Unpin`]: ../std/marker/trait.Unpin.html

[Arrays]: types/array.md
[associated types]: items/associated-items.md#associated-types
[call expressions]: expressions/call-expr.md
[deref coercions]: type-coercions.md#coercion-types
[dereference operator]: expressions/operator-expr.md#the-dereference-operator
[destructor]: destructors.md
[drop check]: ../nomicon/dropck.html
[dynamically sized type]: dynamically-sized-types.md
[Function pointers]: types/function-pointer.md
[Function items]: types/function-item.md
[implementation items]: items/implementations.md
[indexing expressions]: expressions/array-expr.md#array-and-slice-indexing-expressions
[interior mutability]: interior-mutability.md
[main function]: crates-and-source-files.md#main-functions
[Methods]: items/associated-items.md#associated-functions-and-methods
[method resolution]: expressions/method-call-expr.md
[operators]: expressions/operator-expr.md
[orphan rules]: items/implementations.md#trait-implementation-coherence
[`static` items]: items/static-items.md
[test functions]: attributes/testing.md#the-test-attribute
[the standard library]: ../std/index.html
[trait object]: types/trait-object.md
[Tuples]: types/tuple.md
[Type parameters]: types/parameters.md
[variance]: subtyping.md#variance
[Closures]: types/closure.md
{==+==}

{==+==}