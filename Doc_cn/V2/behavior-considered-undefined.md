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

{==+==}