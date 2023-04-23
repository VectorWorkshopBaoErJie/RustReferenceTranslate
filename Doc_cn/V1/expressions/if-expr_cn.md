{==+==}
# `if` and `if let` expressions

## `if` expressions
{==+==}
# `if` 和 `if let` 表达式

## `if` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _IfExpression_ :\
> &nbsp;&nbsp; `if` [_Expression_]<sub>_except struct expression_</sub> [_BlockExpression_]\
> &nbsp;&nbsp; (`else` (
>   [_BlockExpression_]
> | _IfExpression_
> | _IfLetExpression_ ) )<sup>\?</sup>
{==+==}
> **<sup>语法</sup>**\
> _If表达式_ :\
> &nbsp;&nbsp; `if` [_表达式_][_Expression_]<sub>_不包括结构体表达式_</sub> [_块表达式_][_BlockExpression_]\
> &nbsp;&nbsp; (`else` (
>   [_块表达式_][_BlockExpression_]
> | _If表达式_
> | _IfLet表达式_ ) )<sup>\?</sup>
{==+==}


{==+==}
An `if` expression is a conditional branch in program control.
The syntax of an `if` expression is a condition operand, followed by a consequent block, any number of `else if` conditions and blocks, and an optional trailing `else` block.
The condition operands must have the [boolean type].
If a condition operand evaluates to `true`, the consequent block is executed and any subsequent `else if` or `else` block is skipped.
If a condition operand evaluates to `false`, the consequent block is skipped and any subsequent `else if` condition is evaluated.
If all `if` and `else if` conditions evaluate to `false` then any `else` block is executed.
An if expression evaluates to the same value as the executed block, or `()` if no block is evaluated.
An `if` expression must have the same type in all situations.
{==+==}
`if` 表达式是程序控制流中的条件分支。
`if` 表达式的语法是一个条件操作数，后面跟着一个结果块，任意数量的 `else if` 条件和块，以及一个可选的末尾 `else` 块。条件操作数必须具有 [布尔类型][boolean type] 。
如果条件操作数计算结果为 `true` ，则执行结果块，跳过任何后续的 `else if` 或 `else` 块。
如果条件操作数计算结果为 `false` ，则跳过结果块，评估任何后续的 `else if` 条件。
如果所有 `if` 和 `else if` 条件都计算结果为 `false` ，则执行任何 `else` 块。
`if` 表达式的计算结果与执行的块相同，如果没有执行块，则为 `()` 。
`if` 表达式在所有情况下必须具有相同的类型。
{==+==}


{==+==}
```rust
# let x = 3;
if x == 4 {
    println!("x is four");
} else if x == 3 {
    println!("x is three");
} else {
    println!("x is something else");
}

let y = if 12 * 15 > 150 {
    "Bigger"
} else {
    "Smaller"
};
assert_eq!(y, "Bigger");
```
{==+==}

{==+==}


{==+==}
## `if let` expressions
{==+==}
## `if let` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _IfLetExpression_ :\
> &nbsp;&nbsp; `if` `let` [_Pattern_] `=` [_Scrutinee_]<sub>_except lazy boolean operator expression_</sub>
>              [_BlockExpression_]\
> &nbsp;&nbsp; (`else` (
>   [_BlockExpression_]
> | _IfExpression_
> | _IfLetExpression_ ) )<sup>\?</sup>
{==+==}
> **<sup>语法</sup>**\
> _IfLet表达式_ :\
> &nbsp;&nbsp; `if` `let` [_模式_][_Pattern_] `=` [_被匹配项_][_Scrutinee_]<sub>_包括惰性布尔运算符表达式_</sub>
>              [_块表达式_][_BlockExpression_]\
> &nbsp;&nbsp; (`else` (
>   [_块表达式_][_BlockExpression_]
> | _If表达式_
> | _IfLet表达式_ ) )<sup>\?</sup>
{==+==}


{==+==}
An `if let` expression is semantically similar to an `if` expression but in place of a condition operand it expects the keyword `let` followed by a pattern, an `=` and a [scrutinee] operand.
If the value of the scrutinee matches the pattern, the corresponding block will execute.
Otherwise, flow proceeds to the following `else` block if it exists.
Like `if` expressions, `if let` expressions have a value determined by the block that is evaluated.
{==+==}
`if let` 表达式在语义上类似于 `if` 表达式，但是在条件操作数的位置上，它期望关键字 `let` 后跟一个模式、一个等号和一个[被匹配项][scrutinee] 。如果被检查的操作数的值匹配模式，则执行相应的代码块。
否则，如果存在 `else` 块，则流程将继续执行该块。
与 `if` 表达式类似， `if let` 表达式具有由执行的代码块确定的值。
{==+==}


{==+==}
```rust
let dish = ("Ham", "Eggs");

// this body will be skipped because the pattern is refuted
if let ("Bacon", b) = dish {
    println!("Bacon is served with {}", b);
} else {
    // This block is evaluated instead.
    println!("No bacon will be served");
}

// this body will execute
if let ("Ham", b) = dish {
    println!("Ham is served with {}", b);
}

if let _ = 5 {
    println!("Irrefutable patterns are always true");
}
```
{==+==}
```rust
let dish = ("Ham", "Eggs"); // 定义一个元组dish，包含两个字符串"Ham"和"Eggs"

// 由于模式不匹配，这个代码块将被跳过
if let ("Bacon", b) = dish { // 在dish上应用模式匹配，如果dish与("Bacon", b)相同，则执行代码块
    println!("Bacon is served with {}", b);
} else { 
    // 否则执行这个代码块
    println!("No bacon will be served"); // 打印"No bacon will be served"
}

// 由于模式匹配，这个代码块将被执行
if let ("Ham", b) = dish { // 在dish上应用模式匹配，如果dish与("Ham", b)相同，则执行代码块
    println!("Ham is served with {}", b); // 打印"Ham is served with Eggs"
}

if let _ = 5 { // 对5应用不可拒绝的模式，始终为真，执行代码块
    println!("Irrefutable patterns are always true"); // 打印"Irrefutable patterns are always true"
}
```
{==+==}


{==+==}
`if` and `if let` expressions can be intermixed:
{==+==}
`if` 和 `if let` 表达式可以混合使用:
{==+==}


{==+==}
```rust
let x = Some(3);
let a = if let Some(1) = x {
    1
} else if x == Some(2) {
    2
} else if let Some(y) = x {
    y
} else {
    -1
};
assert_eq!(a, 3);
```
{==+==}

{==+==}


{==+==}
An `if let` expression is equivalent to a [`match` expression] as follows:
{==+==}
一个 `if let` 表达式等价于一个如下所示的 [`match` 表达式][`match` expression] :
{==+==}


{==+==}
<!-- ignore: expansion example -->
```rust,ignore
if let PATS = EXPR {
    /* body */
} else {
    /*else */
}
```
{==+==}

{==+==}


{==+==}
is equivalent to
{==+==}
等价于
{==+==}


{==+==}
<!-- ignore: expansion example -->
```rust,ignore
match EXPR {
    PATS => { /* body */ },
    _ => { /* else */ },    // () if there is no else
}
```
{==+==}

{==+==}


{==+==}
Multiple patterns may be specified with the `|` operator. This has the same semantics as with `|` in `match` expressions:
{==+==}
可以使用 `|` 运算符指定多个模式。这与 `match` 表达式中的 `|` 运算符具有相同的语义:
{==+==}


{==+==}
```rust
enum E {
    X(u8),
    Y(u8),
    Z(u8),
}
let v = E::Y(12);
if let E::X(n) | E::Y(n) = v {
    assert_eq!(n, 12);
}
```
{==+==}

{==+==}


{==+==}
The expression cannot be a [lazy boolean operator expression][_LazyBooleanOperatorExpression_].
Use of a lazy boolean operator is ambiguous with a planned feature change of the language (the implementation of if-let chains - see [eRFC 2947][_eRFCIfLetChain_]).
When lazy boolean operator expression is desired, this can be achieved by using parenthesis as below:
{==+==}
该表达式不能是一个 [惰性布尔运算符表达式][_LazyBooleanOperatorExpression_]。
使用惰性布尔运算符会与语言计划更改的特性产生歧义 (即 if-let 链的实现 - 请参见 [eRFC 2947][_eRFCIfLetChain_] )。
当需要惰性布尔运算符表达式时，可以通过如下方式使用括号来实现：
{==+==}


{==+==}
<!-- ignore: pseudo code -->
```rust,ignore
// Before...
if let PAT = EXPR && EXPR { .. }

// After...
if let PAT = ( EXPR && EXPR ) { .. }

// Before...
if let PAT = EXPR || EXPR { .. }

// After...
if let PAT = ( EXPR || EXPR ) { .. }
```
{==+==}
<!-- ignore: pseudo code -->
```rust,ignore
// 之前...
if let PAT = EXPR && EXPR { .. } 

// 之后...
if let PAT = ( EXPR && EXPR ) { .. } 

// 之前...
if let PAT = EXPR || EXPR { .. } 

// 之后...
if let PAT = ( EXPR || EXPR ) { .. } 
```
{==+==}


{==+==}
[_BlockExpression_]: block-expr.md
[_Expression_]: ../expressions.md
[_LazyBooleanOperatorExpression_]: operator-expr.md#lazy-boolean-operators
[_Pattern_]: ../patterns.md
[_Scrutinee_]: match-expr.md
[_eRFCIfLetChain_]: https://github.com/rust-lang/rfcs/blob/master/text/2497-if-let-chains.md#rollout-plan-and-transitioning-to-rust-2018
[`match` expression]: match-expr.md
[boolean type]: ../types/boolean.md
[scrutinee]: ../glossary.md#scrutinee
{==+==}

{==+==}