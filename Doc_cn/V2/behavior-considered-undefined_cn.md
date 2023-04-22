{==+==}
* Data races.
* Evaluating a [dereference expression] (`*expr`) on a raw pointer that is
  [dangling] or unaligned, even in [place expression context]
  (e.g. `addr_of!(*expr)`).
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
* 在使用 [解引用表达式][dereference expression] (`*expr`) 对一个 [悬垂指针][dangling] 或非对齐指针进行求值时，甚至在 [位置表达式上下文][place expression context] 中 (例如 `addr_of!(*expr)`) 。
* 违反 [指针别名规则][pointer aliasing rules] 。 `Box<T>` , `&mut T` 和 `&T` 遵循 LLVM 的有界 [无别名][noalias] 模型，除非 `&T` 包含 [`UnsafeCell<U>`]。 引用和 box 在其存在期间不能是 [悬垂][dangling] 的。确切的存活期未被指定，但存在某些限制:
  * 对于引用，存活期上限由借用检查器分配的语法生命周期确定；它的生存期不能比那个生命周期更长。
  * 每次引用或 box 传递给或从函数返回时，都被认为是活动的。
  * 当引用 (但不是 `Box`! ) 传递给函数时，它的生存期至少与该函数调用相同，除非 `&T` 包含一个 [`UnsafeCell<U>`] 。
{==+==}