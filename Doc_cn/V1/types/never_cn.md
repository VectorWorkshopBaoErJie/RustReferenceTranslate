{==+==}
# Never type
{==+==}
# 永不类型
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _NeverType_ : `!`
{==+==}
> **<sup>语法</sup>**\
> _永不类型_ : `!`
{==+==}


{==+==}
The never type `!` is a type with no values, representing the result of
computations that never complete. Expressions of type `!` can be coerced into
any other type.
{==+==}
永不类型 `!` 是一种没有值的类型，表示永远不会计算出确切结果。
类型为 `!` 的表达式可以强制转换为其他任何类型。
{==+==}


{==+==}
<!-- ignore: unstable -->
```rust,ignore
let x: ! = panic!();
// Can be coerced into any type.
let y: u32 = x;
```
{==+==}
<!-- ignore: unstable -->
```rust,ignore
let x: ! = panic!();
// 可以强制转换为任何类型。
let y: u32 = x;
```
{==+==}


{==+==}
**NB.** The never type was expected to be stabilized in 1.41, but due
to some last minute regressions detected the stabilization was
temporarily reverted. The `!` type can only appear in function return
types presently. See [the tracking
issue](https://github.com/rust-lang/rust/issues/35121) for more
details.
{==+==}
**注意:** 预计在 1.41 版本中稳定的永不类型，但由于最后时间发现了一些回归问题，因此稳定性被暂时撤销。
目前， `!` 类型只能出现在函数返回类型中。有关更多详细信息，请参见 [问题跟踪](https://github.com/rust-lang/rust/issues/35121) 。
{==+==}