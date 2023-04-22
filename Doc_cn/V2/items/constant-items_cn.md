{==+==}
## Evaluation

[Free][free] constants are always [evaluated][const_eval] at compile-time to surface
panics. This happens even within an unused function:

```rust,compile_fail
// Compile-time panic
const PANIC: () = std::unimplemented!();

fn unused_generic_function<T>() {
    // A failing compile-time assertion
    const _: () = assert!(usize::BITS == 0);
}
```

[const_eval]: ../const_eval.md
{==+==}
## 评估

[自由][free] 常量总是在编译时被 [计算][const_eval]，以便暴露 panic。即使在未使用的函数中，也会发生这种情况:

```rust,compile_fail
// 编译时 panic
const PANIC: () = std::unimplemented!();

fn unused_generic_function<T>() {
    // 一个失败的编译时断言
    const _: () = assert!(usize::BITS == 0);
}
```

[const_eval]: ../const_eval.md
{==+==}
