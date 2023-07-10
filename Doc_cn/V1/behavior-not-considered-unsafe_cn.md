{==+==}
## Behavior not considered `unsafe`

The Rust compiler does not consider the following behaviors _unsafe_,
though a programmer may (should) find them undesirable, unexpected,
or erroneous.
{==+==}
## 不视为 `unsafe` 的行为

Rust 编译器并不视为以下行为是 _unsafe_ ，尽管程序员可能 (应该) 认为是不可取的、不符合预期或错误的。
{==+==}


{==+==}
##### Deadlocks
##### Leaks of memory and other resources
##### Exiting without calling destructors
##### Exposing randomized base addresses through pointer leaks
##### Integer overflow

If a program contains arithmetic overflow, the programmer has made an
error. In the following discussion, we maintain a distinction between
arithmetic overflow and wrapping arithmetic. The first is erroneous,
while the second is intentional.

When the programmer has enabled `debug_assert!` assertions (for
example, by enabling a non-optimized build), implementations must
insert dynamic checks that `panic` on overflow. Other kinds of builds
may result in `panics` or silently wrapped values on overflow, at the
implementation's discretion.

In the case of implicitly-wrapped overflow, implementations must
provide well-defined (even if still considered erroneous) results by
using two's complement overflow conventions.

The integral types provide inherent methods to allow programmers
explicitly to perform wrapping arithmetic. For example,
`i32::wrapping_add` provides two's complement, wrapping addition.

The standard library also provides a `Wrapping<T>` newtype which
ensures all standard arithmetic operations for `T` have wrapping
semantics.

See [RFC 560] for error conditions, rationale, and more details about
integer overflow.
{==+==}
##### 死锁
##### 内存和其他资源泄漏
##### 退出时未调用析构函数
##### 通过指针泄漏公开随机化的基址
##### 整数溢出

如果程序出现算术溢出，那么程序员就犯了一个错误。
在下面的讨论中，算术溢出和算术环绕之间有区别，前者是错误的，而后者是有意的。

当程序员启用了 `debug_assert!` 断言 (例如，启用了非优化构建) 时，实现必须插入动态检查以在溢出时引发 `panic` 。
其他类型的构建可能会在溢出时导致 `panics` 或静默地环绕值，由实现自行决定。

在隐式环绕的情况下，实现必须使用二进制补码溢出约定提供明确定义的结果 (即使仍然被认为是错误的) 。

整数类型提供了固有方法，允许程序员显式地执行算术环绕。例如， `i32::wrapping_add` 提供了二进制补码的环绕加法。

标准库还提供了一个 `Wrapping<T>` 的新类型，它确保 `T` 的所有标准算术操作都具有环绕语义。

有关整数溢出的错误条件、原理和更多细节，请参见 [RFC 560] 。
{==+==}


{==+==}
##### Logic errors

Safe code may impose extra logical constraints that can be checked
at neither compile-time nor runtime. If a program breaks such
a constraint, the behavior may be unspecified but will not result in
undefined behavior. This could include panics, incorrect results,
aborts, and non-termination. The behavior may also differ between
runs, builds, or kinds of build.

For example, implementing both `Hash` and `Eq` requires that values
considered equal have equal hashes. Another example are data structures
like `BinaryHeap`, `BTreeMap`, `BTreeSet`, `HashMap` and `HashSet`
which describe constraints on the modification of their keys while
they are in the data structure. Violating such constraints is not
considered unsafe, yet the program is considered erroneous and
its behavior unpredictable.
{==+==}
##### 逻辑错误

安全的代码可能会具有附加的逻辑约束，这些约束既无法在编译时检查，也无法在运行时检查。
如果程序违反了这些约束，则其行为可能是逻辑错误，但不会导致未定义行为。
这可能会包括恐慌、错误的结果、终止和非终端。
这种行为可能也会因为运行次数、构建方式或编译方式而不同。

例如，实现 `Hash` 和 `Eq` 都要求被认为相等的值具有相等的哈希值。
其一个例子是数据结构中，比如 `BinaryHeap` 、 `BTreeMap` 、 `BTreeSet` 、 `HashMap` 和 `HashSet` ，修改这些数据结构的键时有其约束。
如果违反约束则认为不是非安全的，但这时程序将产生错误，其行为不可预测。
{==+==}


{==+==}
[RFC 560]: https://github.com/rust-lang/rfcs/blob/master/text/0560-integer-overflow.md
{==+==}

{==+==}