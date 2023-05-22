{==+==}
## Behavior considered undefined

Rust code is incorrect if it exhibits any of the behaviors in the following
list. This includes code within `unsafe` blocks and `unsafe` functions.
`unsafe` only means that avoiding undefined behavior is on the programmer; it
does not change anything about the fact that Rust programs must never cause
undefined behavior.

It is the programmer's responsibility when writing `unsafe` code to ensure that
any safe code interacting with the `unsafe` code cannot trigger these
behaviors. `unsafe` code that satisfies this property for any safe client is
called *sound*; if `unsafe` code can be misused by safe code to exhibit
undefined behavior, it is *unsound*.

<div class="warning">

***Warning:*** The following list is not exhaustive. There is no formal model of
Rust's semantics for what is and is not allowed in unsafe code, so there may be
more behavior considered unsafe. The following list is just what we know for
sure is undefined behavior. Please read the [Rustonomicon] before writing unsafe
code.

</div>
{==+==}
## 未定义的行为

如果 Rust 代码表现出以下列表中的任何行为，则它是不正确的。这包括 `unsafe` 块和 `unsafe` 函数中的代码。 `unsafe` 只意味着避免未定义行为是由程序员负责；它并不改变 Rust 程序永远不应该导致未定义行为的事实。

在编写 `unsafe` 代码时，程序员的责任是确保与 `unsafe` 代码交互的任何安全代码都不会触发这些行为。对于任何安全客户端都满足这个属性的 `unsafe` 代码被称为 *健壮的* ；如果安全代码可以误用 `unsafe` 代码来表现出未定义的行为，那么它就是*不健壮的*。

<div class="warning">

***警告:*** 以下列表不是穷尽的。 Rust 中不允许哪些操作和不允许的行为没有正式的模型，因此可能还有更多被视为非安全的行为。以下列表仅列出了我们知道的肯定是未定义行为的内容。请在编写非安全代码之前阅读 [Rustonomicon]。

</div>
{==+==}


{==+==}
* Data races.
* Evaluating a [dereference expression] (`*expr`) on a raw pointer that is
  [dangling] or unaligned, even in [place expression context]
  (e.g. `addr_of!(&*expr)`).
* Breaking the [pointer aliasing rules]. `Box<T>`, `&mut T` and `&T` follow
  LLVM’s scoped [noalias] model, except if the `&T` contains an
  [`UnsafeCell<U>`]. References and boxes must not be [dangling] while they are
  live. The exact liveness duration is not specified, but some bounds exist:
  * For references, the liveness duration is upper-bounded by the syntactic
    lifetime assigned by the borrow checker; it cannot be live any *longer* than
    that lifetime.
  * Each time a reference or box is passed to or returned from a function, it is
    considered live.
  * When a reference (but not a `Box`!) is passed to a function, it is live at
    least as long as that function call, again except if the `&T` contains an
    [`UnsafeCell<U>`].
{==+==}
  * 数据竞争。
  * 在原始指针上评估解引用表达式 (`*expr`) ，即使在 [占位表达式上下文][place expression context] 上也是如此，该原始指针是 [dangling] 或不对齐的。(例如， `addr_of!(&*expr)` ).
  * 破坏 [指针别名规则][pointer aliasing rules] 。 `Box<T>` 、 `&mut T` 和 `&T` 遵循 LLVM 的作用域 [noalias] 模型，除非 `&T` 包含 [`UnsafeCell<U>`] 。当引用和 box 无效时，它们不能处于活动状态。确切的存活时间没有指定，但存在一些界限:
    * 对于引用，存活时间的上界是借用检查器分配的语法生命周期；它不能活得比那个生命周期更长。
    * 每次引用或 box 被传递给或从函数返回时，它都被视为处于活动状态。
    * 当引用 (但不是 `Box`! ) 被传递到函数时，它至少在那个函数调用期间是活动的，再次除非 `&T` 包含一个 [`UnsafeCell<U>`] 。
{==+==}


{==+==}
  All this also applies when values of these
  types are passed in a (nested) field of a compound type, but not behind
  pointer indirections.
* Mutating immutable data. All data inside a [`const`] item is immutable. Moreover, all
  data reached through a shared reference or data owned by an immutable binding
  is immutable, unless that data is contained within an [`UnsafeCell<U>`].
* Invoking undefined behavior via compiler intrinsics.
* Executing code compiled with platform features that the current platform
  does not support (see [`target_feature`]), *except* if the platform explicitly documents this to be safe.
* Calling a function with the wrong call ABI or unwinding from a function with the wrong unwind ABI.
* Producing an invalid value, even in private fields and locals. "Producing" a
  value happens any time a value is assigned to or read from a place, passed to
  a function/primitive operation or returned from a function/primitive
  operation.
  The following values are invalid (at their respective type):
  * A value other than `false` (`0`) or `true` (`1`) in a [`bool`].
  * A discriminant in an `enum` not included in the type definition.
  * A null `fn` pointer.
  * A value in a `char` which is a surrogate or above `char::MAX`.
  * A `!` (all values are invalid for this type).
  * An integer (`i*`/`u*`), floating point value (`f*`), or raw pointer obtained
    from [uninitialized memory][undef], or uninitialized memory in a `str`.
  * A reference or `Box<T>` that is [dangling], unaligned, or points to an invalid value.
  * Invalid metadata in a wide reference, `Box<T>`, or raw pointer:
    * `dyn Trait` metadata is invalid if it is not a pointer to a vtable for
      `Trait` that matches the actual dynamic trait the pointer or reference points to.
    * Slice metadata is invalid if the length is not a valid `usize`
      (i.e., it must not be read from uninitialized memory).
  * Invalid values for a type with a custom definition of invalid values.
    In the standard library, this affects [`NonNull<T>`] and [`NonZero*`].
{==+==}
  以下内容也适用于这些类型的值作为复合类型的 (嵌套) 字段传递，但不适用于指针间接引用的情况。
* 修改不可变数据。 [`const`] 中的所有数据都是不可变的。此外，通过共享引用访问的数据或由不可变绑定拥有的数据也是不可变的，除非该数据包含在 [`UnsafeCell<U>`] 中。
* 通过编译器内部函数调用产生未定义行为。
* 执行使用当前平台不支持的平台特性编译的代码 (请参阅 [`target_feature`]) ， *除非* 该平台明确记录此操作是安全的。
* 使用错误的调用 ABI 调用函数或从错误的取消 ABI 函数中回溯。
* 在私有字段和局部变量中生成无效值。 "生成" 值是指将值分配给或从位置读取值、将值传递给函数/原始操作或从函数/原始操作返回值。
  以下值是无效的 (在其各自的类型中) :
  * 在 [`bool`] 中除了 `false` (`0`) 或 `true` (`1`) 之外的值。
  * 枚举中的判别值不在类型定义中。
  * 空的 `fn` 指针。
  * `char` 中的值是代理或大于 `char::MAX` 。
  * `!` (所有值对于此类型都是无效的) 。
  * 从 [未初始化的内存][undef] 中获取的整数 ( `i*`/`u*` ) 、浮点数 ( `f*` ) 或裸指针，或在 `str` 中使用未初始化的内存。
  * 悬空引用或 `Box<T>` ，不对齐或指向无效值。
  * 宽指针、 `Box<T>` 或裸指针中的无效元数据:
    * 如果 `dyn Trait` 的元数据不是指向与指针或引用指向的实际动态特性相匹配的 `Trait` 的虚表的指针，则该元数据是无效的。
    * 如果长度不是有效的 `usize` (即不能从未初始化的内存中读取) ，则切片元数据无效。
  * 自定义无效值类型的无效值。在标准库中，这会影响 [`NonNull<T>`] 和 [`NonZero*`] 。
{==+==}


{==+==}
    > **Note**: `rustc` achieves this with the unstable
    > `rustc_layout_scalar_valid_range_*` attributes.
* Incorrect use of inline assembly. For more details, refer to the [rules] to
  follow when writing code that uses inline assembly.

**Note:** Uninitialized memory is also implicitly invalid for any type that has
a restricted set of valid values. In other words, the only cases in which
reading uninitialized memory is permitted are inside `union`s and in "padding"
(the gaps between the fields/elements of a type).

> **Note**: Undefined behavior affects the entire program. For example, calling
> a function in C that exhibits undefined behavior of C means your entire
> program contains undefined behaviour that can also affect the Rust code. And
> vice versa, undefined behavior in Rust can cause adverse affects on code
> executed by any FFI calls to other languages.
{==+==}
    > **注意**: `rustc` 使用未稳定的 `rustc_layout_scalar_valid_range_*` 属性来实现此功能。

* 不正确使用内联汇编。有关更多详细信息，请参阅使用内联汇编编写代码时要遵循的 [规则][rules] 。
  
**注意:** 对于任何具有受限制的有效值集的类型，未初始化的内存也是隐式无效的。换句话说，读取未初始化的内存仅在 `union` 内部和在类型的字段/元素之间 "填充" (间隙)中是允许的。

> **注意**: 未定义行为影响整个程序。例如，在C中调用表现出未定义行为的函数意味着你的整个程序包含未定义行为，这也可能影响 Rust 代码。反之，在 Rust 中发生未定义行为也会对调用其他语言的 FFI 调用执行的代码产生不利影响。
{==+==}


{==+==}
### Dangling pointers
[dangling]: #dangling-pointers

A reference/pointer is "dangling" if it is null or not all of the bytes it
points to are part of the same live allocation (so in particular they all have to be
part of *some* allocation). The span of bytes it points to is determined by the
pointer value and the size of the pointee type (using `size_of_val`).

If the size is 0, then the pointer must either point inside of a live allocation
(including pointing just after the last byte of the allocation), or it must be
directly constructed from a non-zero integer literal.

Note that dynamically sized types (such as slices and strings) point to their
entire range, so it is important that the length metadata is never too large. In
particular, the dynamic size of a Rust value (as determined by `size_of_val`)
must never exceed `isize::MAX`.
{==+==}
### 悬垂指针
[dangling]: #dangling-pointers

如果引用/指针为 null ，或者它所指向的字节不全属于同一个活跃分配 (因此特别注意它们必须全部属于 *某个* 分配) ，则该引用/指针是 "悬垂" 的。它所指向的字节跨度由指针值和指向类型的大小 (使用 `size_of_val` ) 确定。

如果大小为 0 ，则指针必须指向活动分配的内部 (包括指向分配的最后一个字节的后面) ，或者直接从非零整数字面值构建而来。

请注意，动态大小的类型 (例如切片和字符串) 指向它们的整个范围，因此重要的是长度元数据永远不要太大。特别是， Rust 值的动态大小(由 `size_of_val` 确定) 不能超过 `isize::MAX` 。
{==+==}


{==+==}
[`bool`]: types/boolean.md
[`const`]: items/constant-items.md
[noalias]: http://llvm.org/docs/LangRef.html#noalias
[pointer aliasing rules]: http://llvm.org/docs/LangRef.html#pointer-aliasing-rules
[undef]: http://llvm.org/docs/LangRef.html#undefined-values
[`target_feature`]: attributes/codegen.md#the-target_feature-attribute
[`UnsafeCell<U>`]: ../std/cell/struct.UnsafeCell.html
[Rustonomicon]: ../nomicon/index.html
[`NonNull<T>`]: ../core/ptr/struct.NonNull.html
[`NonZero*`]: ../core/num/index.html
[dereference expression]: expressions/operator-expr.md#the-dereference-operator
[place expression context]: expressions.md#place-expressions-and-value-expressions
[rules]: inline-assembly.md#rules-for-inline-assembly
{==+==}

{==+==}