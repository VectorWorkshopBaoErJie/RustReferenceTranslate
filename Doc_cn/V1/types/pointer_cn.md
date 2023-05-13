{==+==}
# Pointer types
{==+==}
# 指针类型
{==+==}


{==+==}
All pointers are explicit first-class values.
They can be moved or copied, stored into data structs, and returned from functions.
{==+==}
所有指针都是显式的第一类值。它们可以移动或复制，存储到数据结构中，并从函数返回。
{==+==}


{==+==}
## References (`&` and `&mut`)
{==+==}
## 引用 (`&` 和 `&mut`)
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ReferenceType_ :\
> &nbsp;&nbsp; `&` [_Lifetime_]<sup>?</sup> `mut`<sup>?</sup> [_TypeNoBounds_]
{==+==}
> **<sup>语法</sup>**\
> _引用类型_ :\
> &nbsp;&nbsp; `&` [_生命周期_][_Lifetime_]<sup>?</sup> `mut`<sup>?</sup> [_无约束类型组_][_TypeNoBounds_]
{==+==}


{==+==}
### Shared references (`&`)

Shared references point to memory which is owned by some other value.
When a shared reference to a value is created, it prevents direct mutation of the value.
[Interior mutability] provides an exception for this in certain circumstances.
As the name suggests, any number of shared references to a value may exist.
A shared reference type is written `&type`, or `&'a type` when you need to specify an explicit lifetime.
Copying a reference is a "shallow" operation:
it involves only copying the pointer itself, that is, pointers are `Copy`.
Releasing a reference has no effect on the value it points to, but referencing of a [temporary value] will keep it alive during the scope of the reference itself.
{==+==}
### 共享引用 (`&`)

共享引用指向由某个其他值拥有的内存。创建一个值的共享引用时，它会防止直接修改该值。 [内部可变性][Interior mutability] 在某些情况下提供了一个例外。
顾名思义，可能存在对一个值的任意数量的共享引用。共享引用类型写作 `&type` ，或者在需要指定显式生命周期时写作 `&'a type` 。
复制引用是一个 "浅层" 操作：它只涉及到指针本身的复制，也就是说，指针是 `Copy` 的。
释放引用对它所指向的值没有影响，但引用 [临时值][temporary value] 将在引用本身的作用域中保持其活动状态。
{==+==}


{==+==}
### Mutable references (`&mut`)

Mutable references point to memory which is owned by some other value.
A mutable reference type is written `&mut type` or `&'a mut type`.
A mutable reference (that hasn't been borrowed) is the only way to access the value it points to, so is not `Copy`.
{==+==}
### 可变引用 (`&mut`)

可变引用指向由某个其他值拥有的内存。可变引用类型写作 `&mut type` 或 `&'a mut type` 。可变引用 (未被借用的) 是访问它所指向的值的唯一方式，因此不是 `Copy` 。
{==+==}


{==+==}
## Raw pointers (`*const` and `*mut`)
{==+==}
## 原始指针 (`*const` and `*mut`)
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _RawPointerType_ :\
> &nbsp;&nbsp; `*` ( `mut` | `const` ) [_TypeNoBounds_]
{==+==}
> **<sup>语法</sup>**\
> _原始指针类型_ :\
> &nbsp;&nbsp; `*` ( `mut` | `const` ) [_无约束类型组_][_TypeNoBounds_]
{==+==}


{==+==}
Raw pointers are pointers without safety or liveness guarantees.
Raw pointers are written as `*const T` or `*mut T`.
For example `*const i32` means a raw pointer to a 32-bit integer.
Copying or dropping a raw pointer has no effect on the lifecycle of any other value.
Dereferencing a raw pointer is an [`unsafe` operation].
This can also be used to convert a raw pointer to a reference by reborrowing it (`&*` or `&mut *`).
Raw pointers are generally discouraged;
they exist to support interoperability with foreign code, and writing performance-critical or low-level functions.
{==+==}
原始指针是没有安全性或生存性保证的指针。原始指针写作 `*const T` 或 `*mut T` 。
例如， `*const i32` 意味着一个指向 32 位整数的原始指针。复制或丢弃原始指针不会对任何其他值的生命周期产生影响。
解引用原始指针是一个 [`unsafe` 操作][`unsafe` operation] 。这也可以用于通过重新借用它 (`&*` 或 `&mut *`) 将原始指针转换为引用。
通常不建议使用原始指针；其存在是为了支持与外部代码的互操作性，以及编写关键性能或低级别的函数。
{==+==}


{==+==}
When comparing raw pointers they are compared by their address, rather than by what they point to.
When comparing raw pointers to [dynamically sized types] they also have their additional data compared.

Raw pointers can be created directly using [`core::ptr::addr_of!`] for `*const` pointers and [`core::ptr::addr_of_mut!`] for `*mut` pointers.
{==+==}
当比较原始指针时，是通过它们的地址进行比较，而不是通过它们所指向的内容进行比较。当将原始指针与 [动态大小类型][dynamically sized types] 进行比较时，它们还会比较它们的附加数据。

可以使用 [`core::ptr::addr_of!`] 创建 `*const` 指针和 [`core::ptr::addr_of_mut!`] 创建 `*mut` 指针来直接创建原始指针。
{==+==}


{==+==}
## Smart Pointers

The standard library contains additional 'smart pointer' types beyond references and raw pointers.
{==+==}
## 智能指针

标准库包含除了引用和原始指针之外的其他 '智能指针' 类型。
{==+==}


{==+==}
[`core::ptr::addr_of!`]: ../../core/ptr/macro.addr_of.html
[`core::ptr::addr_of_mut!`]: ../../core/ptr/macro.addr_of_mut.html
[Interior mutability]: ../interior-mutability.md
[_Lifetime_]: ../trait-bounds.md
[_TypeNoBounds_]: ../types.md#type-expressions
[`unsafe` operation]: ../unsafety.md
[dynamically sized types]: ../dynamically-sized-types.md
[temporary value]: ../expressions.md#temporaries
{==+==}

{==+==}