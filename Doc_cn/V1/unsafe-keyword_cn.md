{==+==}
# The `unsafe` keyword

The `unsafe` keyword can occur in several different contexts:
unsafe functions (`unsafe fn`), unsafe blocks (`unsafe {}`), unsafe traits (`unsafe trait`), and unsafe trait implementations (`unsafe impl`).
It plays several different roles, depending on where it is used and whether the `unsafe_op_in_unsafe_fn` lint is enabled:
- it is used to mark code that *defines* extra safety conditions (`unsafe fn`, `unsafe trait`)
- it is used to mark code that needs to *satisfy* extra safety conditions (`unsafe {}`, `unsafe impl`, `unsafe fn` without [`unsafe_op_in_unsafe_fn`])

The following discusses each of these cases.
See the [keyword documentation][keyword] for some illustrative examples.

## Unsafe functions (`unsafe fn`)

Unsafe functions are functions that are not safe in all contexts and/or for all possible inputs.
We say they have *extra safety conditions*, which are requirements that must be upheld by all callers and that the compiler does not check.
For example, [`get_unchecked`] has the extra safety condition that the index must be in-bounds.
The unsafe function should come with documentation explaining what those extra safety conditions are.

Such a function must be prefixed with the keyword `unsafe` and can only be called from inside an `unsafe` block, or inside `unsafe fn` without the [`unsafe_op_in_unsafe_fn`] lint.
{==+==}
# `unsafe` 关键字

`unsafe` 关键字可以出现在几种不同的上下文: 非安全函数 (`unsafe fn`) ，非安全块 (`unsafe {}`) ，非安全 trait (`unsafe trait`) 和非安全 trait 实现 (`unsafe impl`) 中。
该关键字在不同的使用语境表达了不同的含义，以及是否启用 `unsafe_op_in_unsafe_fn` 代码分析: 

- 用于标记 *定义* 附加安全条件的代码 (`unsafe fn` ， `unsafe trait`) 。
- 用于标记需要 *满足* 附加安全条件的代码 (`unsafe {}` ， `unsafe impl` ，没有 [`unsafe_op_in_unsafe_fn`] 的 `unsafe fn`) 。

以下讨论具体情况。
参见 [关键字文档][keyword] 以获取一些相关信息。

## 非安全函数 (`unsafe fn`)

非安全函数是在所有上下文和/或所有可能的输入都非安全的函数。 *附加安全条件* 是指编译器不会检查所有调用者必须满足的要求。
例如， [`get_unchecked`] 具有附加安全条件，即索引必须在边界内。非安全函数应该配有说明文档，以进行解释说明。

这样的函数必须以关键字 `unsafe` 为前缀，并且只能在 `unsafe` 块内部调用，或者在没有 [`unsafe_op_in_unsafe_fn`] 代码分析的情况下在 `unsafe fn` 中调用。
{==+==}


{==+==}
## Unsafe blocks (`unsafe {}`)

A block of code can be prefixed with the `unsafe` keyword, to permit calling `unsafe` functions or dereferencing raw pointers.
By default, the body of an unsafe function is also considered to be an unsafe block;
this can be changed by enabling the [`unsafe_op_in_unsafe_fn`] lint.

By putting operations into an unsafe block, the programmer states that they have taken care of satisfying the extra safety conditions of all operations inside that block.

Unsafe blocks are the logical dual to unsafe functions:
where unsafe functions define a proof obligation that callers must uphold, unsafe blocks state that all relevant proof obligations have been discharged.
There are many ways to discharge proof obligations;
for example, there could be run-time checks or data structure invariants that guarantee that certain properties are definitely true, or the unsafe block could be inside an `unsafe fn` and use its own proof obligations to discharge the proof obligations of its callees.

Unsafe blocks are used to wrap foreign libraries, make direct use of hardware or implement features not directly present in the language.
For example, Rust provides the language features necessary to implement memory-safe concurrency in the language but the implementation of threads and message passing in the standard library uses unsafe blocks.

Rust's type system is a conservative approximation of the dynamic safety requirements, so in some cases there is a performance cost to using safe code.
For example, a doubly-linked list is not a tree structure and can only be represented with reference-counted pointers in safe code.
By using `unsafe` blocks to represent the reverse links as raw pointers, it can be implemented without reference counting.
(See ["Learn Rust With Entirely Too Many Linked Lists"](https://rust-unofficial.github.io/too-many-lists/) for a more in-depth exploration of this particular example.)
{==+==}
## 非安全块 (`unsafe {}`)

代码块可以加上 `unsafe` 关键字，以允许调用 `unsafe` 函数或引用原始指针。
默认情况下，非安全函数的函数体也被视为非安全块；这可以通过启用 [`unsafe_op_in_unsafe_fn`] 限制来改变。

通过将语句放入非安全块中，程序员声明他们已经满足了该块内所有语句的附加安全条件。

非安全块是非安全函数的逻辑对偶：非安全函数定义调用者必须满足的证明义务，而非安全块则声明所有相关证明义务已得到满足。
有许多方法可以满足证明义务；例如，可能存在运行时检查或数据结构不变，保证某些属性肯定为真，或者非安全块可以位于 `unsafe fn` 中，并使用其自己的证明义务来满足其调用方的证明义务。

非安全块用于包装外部库、直接使用硬件或实现语言中未直接出现的特性。
例如，Rust 提供了实现内存安全并发所需的语言特性，但标准库中的线程和消息传递的实现使用了非安全块。

Rust 的类型系统的动态安全要求有些保守，因此在某些情况下，使用安全代码会带来性能成本。
例如，双向链表不是树结构，只能在安全代码中使用引用计数指针来表示。
通过使用 `unsafe` 块来将反向链接表示为原始指针，可以在不使用引用计数的情况下实现。
 (有关此特定示例的更深入探索，请参见 ["使用多链表学习 Rust"](https://rust-unofficial.github.io/too-many-lists/) )
{==+==}


{==+==}
## Unsafe traits (`unsafe trait`)

An unsafe trait is a trait that comes with extra safety conditions that must be upheld by *implementations* of the trait.
The unsafe trait should come with documentation explaining what those extra safety conditions are.

Such a trait must be prefixed with the keyword `unsafe` and can only be implemented by `unsafe impl` blocks.

## Unsafe trait implementations (`unsafe impl`)

When implementing an unsafe trait, the implementation needs to be prefixed with the `unsafe` keyword.
By writing `unsafe impl`, the programmer states that they have taken care of satisfying the extra safety conditions required by the trait.

Unsafe trait implementations are the logical dual to unsafe traits: where unsafe traits define a proof obligation that implementations must uphold, unsafe implementations state that all relevant proof obligations have been discharged.
{==+==}
## 非安全 trait (`unsafe trait`)

非安全 trait 是一种需要实现附加安全条件的 trait，实现该 trait 必须遵守这些附加安全条件。
该非安全 trait 应该附带这些附加安全条件的说明文档。

这样的 trait 必须以关键字 `unsafe` 为前缀，并且只能由 `unsafe impl` 块实现。

## 非安全 trait 实现 (`unsafe impl`)

在实现非安全 trait 时，实现必须以 `unsafe` 关键字为前缀。通过编写 `unsafe impl`，程序员表明他们已经注意到了 trait 所需的附加安全条件。

非安全 trait 实现是非安全 trait 的逻辑对偶：非安全 trait 定义了实现必须遵守的证明义务，而非安全实现则声明已经履行了所有相关的证明义务。
{==+==}


{==+==}
[keyword]: ../std/keyword.unsafe.html
[`get_unchecked`]: ../std/primitive.slice.html#method.get_unchecked
[`unsafe_op_in_unsafe_fn`]: ../rustc/lints/listing/allowed-by-default.html#unsafe-op-in-unsafe-fn
{==+==}

{==+==}