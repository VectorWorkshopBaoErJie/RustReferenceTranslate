{==+==}
# Inferred type
{==+==}
# 推断类型
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _InferredType_ : `_`
{==+==}

{==+==}


{==+==}
The inferred type asks the compiler to infer the type if possible based on the
surrounding information available. It cannot be used in item signatures. It is
often used in generic arguments:
{==+==}
推断类型是通过周围可用信息让编译器尽可能地推断类型。它不能用于条目签名中。通常在泛型参数中使用:
{==+==}


{==+==}
```rust
let x: Vec<_> = (0..10).collect();
```
{==+==}

{==+==}


{==+==}
<!--
  What else should be said here?
  The only documentation I am aware of is https://rustc-dev-guide.rust-lang.org/type-inference.html
  There should be a broader discussion of type inference somewhere.
-->
{==+==}

{==+==}