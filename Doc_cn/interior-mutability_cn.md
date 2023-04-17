{==+==}
# Interior Mutability
{==+==}
# 内部可变性
{==+==}


{==+==}
Sometimes a type needs to be mutated while having multiple aliases. In Rust this
is achieved using a pattern called _interior mutability_. A type has interior
mutability if its internal state can be changed through a [shared reference] to
it. This goes against the usual [requirement][ub] that the value pointed to by a
shared reference is not mutated.

[`std::cell::UnsafeCell<T>`] type is the only allowed way to disable
this requirement. When `UnsafeCell<T>` is immutably aliased, it is still safe to
mutate, or obtain a mutable reference to, the `T` it contains. As with all
other types, it is undefined behavior to have multiple `&mut UnsafeCell<T>`
aliases.

Other types with interior mutability can be created by using `UnsafeCell<T>` as
a field. The standard library provides a variety of types that provide safe
interior mutability APIs. For example, [`std::cell::RefCell<T>`] uses run-time
borrow checks to ensure the usual rules around multiple references. The
[`std::sync::atomic`] module contains types that wrap a value that is only
accessed with atomic operations, allowing the value to be shared and mutated
across threads.
{==+==}
有时候一个类型需要在拥有多个别名的情况下进行改变。在 Rust 中，这可以通过一种称为 _内部可变性_ 模式来实现。
如果一个类型的内部状态可以通过对它的 [共享引用][shared reference] 进行改变，那么它就具有内部可变性。
这与通常的 [要求][ub] 相反，通常共享引用指向的值不会被改变。

[`std::cell::UnsafeCell<T>`] 类型是禁用这种要求的唯一方法。
当 `UnsafeCell<T>` 被不可变地别名化时，仍然可以安全地对其包含的 `T` 进行改变或获取可变引用。
与所有其他类型一样，拥有多个 `&mut UnsafeCell<T>` 别名是未定义的行为。

通过将 `UnsafeCell<T>` 用作字段，可以创建其他具有内部可变性的类型。
标准库提供了各种类型，提供安全的内部可变性 API 。
例如， [`std::cell::RefCell<T>`] 使用运行时借用检查来确保多个引用延伸的通常规则。
[`std::sync::atomic`] 模块包含了一些类型，这些类型包装了只能通过原子操作访问的值，允许这个值在线程之间共享和改变。
{==+==}


{==+==}
[shared reference]: types/pointer.md#shared-references-
[ub]: behavior-considered-undefined.md
[`std::cell::UnsafeCell<T>`]: ../std/cell/struct.UnsafeCell.html
[`std::cell::RefCell<T>`]: ../std/cell/struct.RefCell.html
[`std::sync::atomic`]: ../std/sync/atomic/index.html

{==+==}

{==+==}
