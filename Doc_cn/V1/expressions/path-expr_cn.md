{==+==}
# Path expressions
{==+==}
# 路径表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _PathExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_PathInExpression_]\
> &nbsp;&nbsp; | [_QualifiedPathInExpression_]
{==+==}
> **<sup>语法</sup>**\
> _路径表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式中路径_][_PathInExpression_]\
> &nbsp;&nbsp; | [_表达式中的限定路径_][_QualifiedPathInExpression_]
{==+==}


{==+==}
A [path] used as an expression context denotes either a local variable or an item.
Path expressions that resolve to local or static variables are [place expressions], other paths are [value expressions].
Using a [`static mut`] variable requires an [`unsafe` block].
{==+==}
在表达式上下文中使用的 [路径][path] 表示一个局部变量或一个条目。
解析为局部变量或静态变量的路径表达式是 [占位表达式][place expressions]，其他路径是 [值表达式][value expressions]。
使用 [`static mut`] 变量需要一个 [`unsafe` 块][`unsafe` block] 。
{==+==}


{==+==}
```rust
# mod globals {
#     pub static STATIC_VAR: i32 = 5;
#     pub static mut STATIC_MUT_VAR: i32 = 7;
# }
# let local_var = 3;
local_var;
globals::STATIC_VAR;
unsafe { globals::STATIC_MUT_VAR };
let some_constructor = Some::<i32>;
let push_integer = Vec::<i32>::push;
let slice_reverse = <[i32]>::reverse;
```
{==+==}

{==+==}


{==+==}
[_PathInExpression_]: ../paths.md#paths-in-expressions
[_QualifiedPathInExpression_]: ../paths.md#qualified-paths
[place expressions]: ../expressions.md#place-expressions-and-value-expressions
[value expressions]: ../expressions.md#place-expressions-and-value-expressions
[path]: ../paths.md
[`static mut`]: ../items/static-items.md#mutable-statics
[`unsafe` block]: block-expr.md#unsafe-blocks
{==+==}

{==+==}