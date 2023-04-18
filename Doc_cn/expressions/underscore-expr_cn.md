{==+==}
# `_` expressions
{==+==}
# `_` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _UnderscoreExpression_ :\
> &nbsp;&nbsp; `_`
{==+==}
> **<sup>语法</sup>**\
> _下划线表达式_ :\
> &nbsp;&nbsp; `_`
{==+==}


{==+==}
Underscore expressions, denoted with the symbol `_`, are used to signify a
placeholder in a destructuring assignment. They may only appear in the left-hand
side of an assignment.

An example of an `_` expression:
{==+==}
下划线表达式，用符号 `_` 表示，在解构赋值中用作占位符。它们只能出现在赋值的左侧。

一个 `_` 表达式的例子:
{==+==}


{==+==}
```rust
let p = (1, 2);
let mut a = 0;
(_, a) = p;
```
{==+==}

{==+==}