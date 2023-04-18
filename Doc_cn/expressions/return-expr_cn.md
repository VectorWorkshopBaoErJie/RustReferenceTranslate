{==+==}
# `return` expressions
{==+==}
# `return` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ReturnExpression_ :\
> &nbsp;&nbsp; `return` [_Expression_]<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _Return表达式_ :\
> &nbsp;&nbsp; `return` [_表达式_][_Expression_]<sup>?</sup>
{==+==}


{==+==}
Return expressions are denoted with the keyword `return`.
Evaluating a `return` expression moves its argument into the designated output location for the current function call, destroys the current function activation frame, and transfers control to the caller frame.
{==+==}
`return` 表达式使用关键字 `return` 标识。
执行 `return` 表达式会将其参数移动到当前函数调用的指定输出位置，销毁当前函数激活帧，并将控制权转移到调用帧。
{==+==}


{==+==}
An example of a `return` expression:
{==+==}
`return` 表达式的一个例子:
{==+==}


{==+==}
```rust
fn max(a: i32, b: i32) -> i32 {
    if a > b {
        return a;
    }
    return b;
}
```
{==+==}

{==+==}


{==+==}
[_Expression_]: ../expressions.md
{==+==}

{==+==}
