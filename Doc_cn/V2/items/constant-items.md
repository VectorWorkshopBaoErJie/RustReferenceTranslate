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

{==+==}
