{==+==}
# Loops and other breakable expressions
{==+==}
# 循环和其他可中断的表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LoopExpression_ :\
> &nbsp;&nbsp; [_LoopLabel_]<sup>?</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_InfiniteLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_PredicateLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_PredicatePatternLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_IteratorLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_LabelBlockExpression_]\
> &nbsp;&nbsp; )
{==+==}
> **<sup>语法</sup>**\
> _循环表达式_ :\
> &nbsp;&nbsp; [_循环标签_][_LoopLabel_]<sup>?</sup> (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_无限循环表达式_][_InfiniteLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_断言循环表达式_][_PredicateLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_断言模式循环表达式_][_PredicatePatternLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_迭代循环表达式_][_IteratorLoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_标签块表达式_][_LabelBlockExpression_]\
> &nbsp;&nbsp; )
{==+==}


{==+==}
[_LoopLabel_]: #loop-labels
[_InfiniteLoopExpression_]: #infinite-loops
[_PredicateLoopExpression_]: #predicate-loops
[_PredicatePatternLoopExpression_]: #predicate-pattern-loops
[_IteratorLoopExpression_]: #iterator-loops
[_LabelBlockExpression_]: #labelled-block-expressions
{==+==}

{==+==}


{==+==}
Rust supports five loop expressions:

*   A [`loop` expression](#infinite-loops) denotes an infinite loop.
*   A [`while` expression](#predicate-loops) loops until a predicate is false.
*   A [`while let` expression](#predicate-pattern-loops) tests a pattern.
*   A [`for` expression](#iterator-loops) extracts values from an iterator, looping until the iterator is empty.
*   A [labelled block expression](#labelled-block-expressions) runs a loop exactly once, but allows exiting the loop early with `break`.

All five types of loop support [`break` expressions](#break-expressions), and [labels](#loop-labels).
All except labelled block expressions support [`continue` expressions](#continue-expressions).
Only `loop` and labelled block expressions support [evaluation to non-trivial values](#break-and-loop-values).
{==+==}
Rust 支持五种循环表达式:

*   [`loop` 表达式](#infinite-loops) 表示无限循环。
*   [`while` 表达式](#predicate-loops) 循环直到断言为 false。
*   [`while let` 表达式](#predicate-pattern-loops) 测试模式。
*   [`for` 表达式](#iterator-loops) 从迭代器中提取值，循环直到迭代器为空。
*   [带标签的块表达式](#labelled-block-expressions) 只运行一次循环，但允许使用 `break` 提前退出循环。

这五种类型的循环都支持 [`break` 表达式](#break-expressions) 和 [标签](#loop-labels) 。
除了带标签的块表达式外，所有类型的循环都支持 [`continue` 表达式](#continue-expressions) 。
只有 `loop` 和带标签的块表达式支持 [计算非平凡值](#break-and-loop-values) 。
{==+==}


{==+==}
## Infinite loops
{==+==}
## 无限循环
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _InfiniteLoopExpression_ :\
> &nbsp;&nbsp; `loop` [_BlockExpression_]
{==+==}
> **<sup>语法</sup>**\
> _无限循环表达式_ :\
> &nbsp;&nbsp; `loop` [_块表达式_][_BlockExpression_]
{==+==}


{==+==}
A `loop` expression repeats execution of its body continuously:
`loop { println!("I live."); }`.

A `loop` expression without an associated `break` expression is diverging and has type [`!`](../types/never.md).
A `loop` expression containing associated [`break` expression(s)](#break-expressions) may terminate, and must have type compatible with the value of the `break` expression(s).
{==+==}
`loop` 表达式不断重复执行其主体:
`loop { println!("I live."); }` 。

没有相关的 `break` 表达式的 `loop` 表达式是发散的，并且具有 [`!`](../types/never.md) 类型。
包含相关 [`break` 表达式](#break-expressions) 的 `loop` 表达式可能会终止，并且必须具有与 `break` 表达式的值兼容的类型。
{==+==}


{==+==}
## Predicate loops
{==+==}
## 断言循环
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _PredicateLoopExpression_ :\
> &nbsp;&nbsp; `while` [_Expression_]<sub>_except struct expression_</sub> [_BlockExpression_]
{==+==}
> **<sup>语法</sup>**\
> _断言循环表达式_ :\
> &nbsp;&nbsp; `while` [_表达式_][_Expression_]<sub>_不包括结构体表达式_</sub> [_块表达式_][_BlockExpression_]
{==+==}


{==+==}
A `while` loop begins by evaluating the [boolean] loop conditional operand.
If the loop conditional operand evaluates to `true`, the loop body block executes, then control returns to the loop conditional operand.
If the loop conditional expression evaluates to `false`, the `while` expression completes.
{==+==}
`while` 循环首先会对布尔类型的循环条件进行求值。
如果循环条件为 `true` ，则会执行循环主体代码块，之后控制流会返回到循环条件，再次对其求值。
如果循环条件为 `false` ，则 `while` 循环终止执行。
{==+==}


{==+==}
An example:

```rust
let mut i = 0;

while i < 10 {
    println!("hello");
    i = i + 1;
}
```
{==+==}
一个例子:

```rust
let mut i = 0;

while i < 10 {
    println!("hello");
    i = i + 1;
}
```
{==+==}


{==+==}
## Predicate pattern loops
{==+==}
## 断言模式循环
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> [_PredicatePatternLoopExpression_] :\
> &nbsp;&nbsp; `while` `let` [_Pattern_] `=` [_Scrutinee_]<sub>_except lazy boolean operator expression_</sub>
>              [_BlockExpression_]
{==+==}
> **<sup>语法</sup>**\
> [_断言模式循环表达式_][_PredicatePatternLoopExpression_] :\
> &nbsp;&nbsp; `while` `let` [_模式_][_Pattern_] `=` [_被匹配项_][_Scrutinee_]<sub>_不包括惰性布尔运算符表达式_</sub>
>              [_块表达式_][_BlockExpression_]
{==+==}


{==+==}
A `while let` loop is semantically similar to a `while` loop but in place of a condition expression it expects the keyword `let` followed by a pattern, an `=`, a [scrutinee] expression and a block expression.
If the value of the scrutinee matches the pattern, the loop body block executes then control returns to the pattern matching statement.
Otherwise, the while expression completes.
{==+==}
 `while let` 循环在语义上类似于 `while` 循环，但是在条件表达式的位置上需要关键字 `let` ，后面跟着模式、等号、 [被匹配项][scrutinee] 和块表达式。
如果被匹配的表达式的值与模式匹配，那么循环体代码块就会被执行，然后控制流会返回到匹配模式的语句处。
否则， `while` 表达式会结束。
{==+==}


{==+==}
```rust
let mut x = vec![1, 2, 3];

while let Some(y) = x.pop() {
    println!("y = {}", y);
}

while let _ = 5 {
    println!("Irrefutable patterns are always true");
    break;
}
```
{==+==}

{==+==}


{==+==}
A `while let` loop is equivalent to a `loop` expression containing a [`match` expression] as follows.

<!-- ignore: expansion example -->
```rust,ignore
'label: while let PATS = EXPR {
    /* loop body */
}
```
{==+==}
 `while let` 循环等同于包含 [`match` 表达式][`match` expression] 的 `loop` 表达式，如下所示。

<!-- ignore: expansion example -->
```rust,ignore
'label: while let PATS = EXPR {
    /* loop body */
}
```
{==+==}


{==+==}
is equivalent to

<!-- ignore: expansion example -->
```rust,ignore
'label: loop {
    match EXPR {
        PATS => { /* loop body */ },
        _ => break,
    }
}
```
{==+==}
等价于

<!-- ignore: expansion example -->
```rust,ignore
'label: loop {
    match EXPR {
        PATS => { /* loop body */ },
        _ => break,
    }
}
```
{==+==}


{==+==}
Multiple patterns may be specified with the `|` operator.
This has the same semantics as with `|` in `match` expressions:
{==+==}
可以使用 `|` 运算符指定多个模式。
这与 `match` 表达式中的 `|` 具有相同的语义:
{==+==}


{==+==}
```rust
let mut vals = vec![2, 3, 1, 2, 2];
while let Some(v @ 1) | Some(v @ 2) = vals.pop() {
    // Prints 2, 2, then 1
    println!("{}", v);
}
```
{==+==}

{==+==}


{==+==}
As is the case in [`if let` expressions], the scrutinee cannot be a [lazy boolean operator expression][_LazyBooleanOperatorExpression_].
{==+==}
与 [`if let` 表达式][`if let` expressions] 一样，被匹配项不能是 [惰性布尔运算符表达式][_LazyBooleanOperatorExpression_] 。
{==+==}


{==+==}
## Iterator loops
{==+==}
## 迭代器循环
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _IteratorLoopExpression_ :\
> &nbsp;&nbsp; `for` [_Pattern_] `in` [_Expression_]<sub>_except struct expression_</sub>
>              [_BlockExpression_]
{==+==}
> **<sup>语法</sup>**\
> _迭代器循环表达式_ :\
> &nbsp;&nbsp; `for` [_模式_][_Pattern_] `in` [_表达式_][_Expression_]<sub>_不包括结构体表达式_</sub>
>              [_块表达式_][_BlockExpression_]
{==+==}


{==+==}
A `for` expression is a syntactic construct for looping over elements provided by an implementation of `std::iter::IntoIterator`.
If the iterator yields a value, that value is matched against the irrefutable pattern, the body of the loop is executed, and then control returns to the head of the `for` loop.
If the iterator is empty, the `for` expression completes.
{==+==}
`for` 表达式是一种语法结构，用于循环遍历 `std::iter::IntoIterator` 的实现提供的元素。
如果迭代器产生一个值，那么该值将与不可拒绝的模式匹配，执行循环体，然后控制返回到 `for` 循环的开头。
如果迭代器为空，则 `for` 表达式完成。
{==+==}


{==+==}
An example of a `for` loop over the contents of an array:
{==+==}
一个对数组内容进行 `for` 循环的例子:
{==+==}


{==+==}
```rust
let v = &["apples", "cake", "coffee"];

for text in v {
    println!("I like {}.", text);
}
```
{==+==}

{==+==}


{==+==}
An example of a for loop over a series of integers:
{==+==}
一个对一系列整数进行 `for` 循环的例子:
{==+==}


{==+==}
```rust
let mut sum = 0;
for n in 1..11 {
    sum += n;
}
assert_eq!(sum, 55);
```
{==+==}

{==+==}


{==+==}
A `for` loop is equivalent to a `loop` expression containing a [`match` expression] as follows:
{==+==}
`for` 循环等价于一个包含 [`match` 表达式][`match` expression] 的 `loop` 表达式，如下所示:
{==+==}


{==+==}
<!-- ignore: expansion example -->
```rust,ignore
'label: for PATTERN in iter_expr {
    /* loop body */
}
```
{==+==}

{==+==}


{==+==}
is equivalent to

<!-- ignore: expansion example -->
```rust,ignore
{
    let result = match IntoIterator::into_iter(iter_expr) {
        mut iter => 'label: loop {
            let mut next;
            match Iterator::next(&mut iter) {
                Option::Some(val) => next = val,
                Option::None => break,
            };
            let PATTERN = next;
            let () = { /* loop body */ };
        },
    };
    result
}
```
{==+==}
等价于

<!-- ignore: expansion example -->
```rust,ignore
{
    let result = match IntoIterator::into_iter(iter_expr) {
        mut iter => 'label: loop {
            let mut next;
            match Iterator::next(&mut iter) {
                Option::Some(val) => next = val,
                Option::None => break,
            };
            let PATTERN = next;
            let () = { /* loop body */ };
        },
    };
    result
}
```
{==+==}


{==+==}
`IntoIterator`, `Iterator`, and `Option` are always the standard library items here, not whatever those names resolve to in the current scope.
The variable names `next`, `iter`, and `val` are for exposition only, they do not actually have names the user can type.

> **Note**: that the outer `match` is used to ensure that any [temporary values] in `iter_expr` don't get dropped before the loop is finished.
> `next` is declared before being assigned because it results in types being inferred correctly more often.
{==+==}
`IntoIterator` 、 `Iterator` 和 `Option` 总是标准库中的条目，而不是当前作用域中解析出来的条目。
变量名 `next` 、 `iter` 和 `val` 仅用于说明，实际上用户无法键入这些名称。

> **注意**：外部 `match` 用于确保在循环完成之前，`iter_expr` 中的任何 [临时值][temporary values] 都不会被丢弃。
> `next` 在分配之前被声明，因为这样通常可以更正确地推断类型。
{==+==}


{==+==}
## Loop labels
{==+==}
## 循环标签
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LoopLabel_ :\
> &nbsp;&nbsp; [LIFETIME_OR_LABEL] `:`
{==+==}
> **<sup>语法</sup>**\
> _循环标签_ :\
> &nbsp;&nbsp; [生命周期或标签][LIFETIME_OR_LABEL] `:`
{==+==}


{==+==}
A loop expression may optionally have a _label_. The label is written as a lifetime preceding the loop expression, as in `'foo: loop { break 'foo; }`, `'bar: while false {}`, `'humbug: for _ in 0..0 {}`.
If a label is present, then labeled `break` and `continue` expressions nested within this loop may exit out of this loop or return control to its head.
See [break expressions](#break-expressions) and [continue expressions](#continue-expressions).

Labels follow the hygiene and shadowing rules of local variables. For example, this code will print "outer loop":
{==+==}
循环表达式可以选择性地使用标签。
标签在循环表达式之前写上一个生命周期，例如 `'foo: loop { break 'foo; }` 、 `'bar: while false {}` 、 `'humbug: for _ in 0..0 {}` 。
如果存在标签，则嵌套在此循环中的带有标签的 `break` 和 `continue` 表达式可以退出该循环或将控制返回到其头部。
请参见 [break 表达式](#break-expressions) 和 [continue 表达式](#continue-expressions) 。

标签遵循局部变量的卫生和隐藏规则。例如，以下代码将打印 "outer loop" :
{==+==}


{==+==}
```rust
'a: loop {
    'a: loop {
        break 'a;
    }
    print!("outer loop");
    break 'a;
}
```
{==+==}

{==+==}


{==+==}
## `break` expressions
{==+==}
## `break` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _BreakExpression_ :\
> &nbsp;&nbsp; `break` [LIFETIME_OR_LABEL]<sup>?</sup> [_Expression_]<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _Break表达式_ :\
> &nbsp;&nbsp; `break` [生命周期或标签][LIFETIME_OR_LABEL]<sup>?</sup> [_表达式_][_Expression_]<sup>?</sup>
{==+==}


{==+==}
When `break` is encountered, execution of the associated loop body is immediately terminated, for example:
{==+==}
当遇到 `break` 时，与之关联的循环体会立即终止执行，例如:
{==+==}


{==+==}
```rust
let mut last = 0;
for x in 1..100 {
    if x > 12 {
        break;
    }
    last = x;
}
assert_eq!(last, 12);
```
{==+==}

{==+==}


{==+==}
A `break` expression is normally associated with the innermost `loop`, `for` or `while` loop enclosing the `break` expression,
but a [label](#loop-labels) can be used to specify which enclosing loop is affected.
Example:
{==+==}
`break` 表达式通常与包含它的最内层 `loop` 、 `for` 或 `while` 循环相关联，但可以使用 [标签](#loop-labels) 来指定受影响的封闭循环。
例如:
{==+==}


{==+==}
```rust
'outer: loop {
    while true {
        break 'outer;
    }
}
```
{==+==}

{==+==}


{==+==}
A `break` expression is only permitted in the body of a loop, and has one of the forms `break`, `break 'label` or ([see below](#break-and-loop-values)) `break EXPR` or `break 'label EXPR`.
{==+==}
`break` 表达式仅允许在循环体中使用，并且有以下形式之一: `break` 、 `break 'label` 或者
 ([见下文](#break-and-loop-values)) `break EXPR` 或 `break 'label EXPR` 。
{==+==}


{==+==}
## Labelled block expressions
{==+==}
## 标签块表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LabelBlockExpression_ :\
> &nbsp;&nbsp; [_BlockExpression_]
{==+==}
> **<sup>语法</sup>**\
> _标签块表达式_ :\
> &nbsp;&nbsp; [_块表达式_][_BlockExpression_]
{==+==}


{==+==}
Labelled block expressions are exactly like block expressions, except that they allow using `break` expressions within the block.
Unlike other loops, `break` expressions within a label expression *must* have a label (i.e. the label is not optional).
Unlike other loops, labelled block expressions *must* begin with a label.
{==+==}
带标签的块表达式与普通块表达式完全相同，只不过它们允许在块内使用 `break` 表达式。
与其他循环不同，带标签的块表达式中的 `break` 表达式 *必须* 有一个标签 (即标签不是可选的) 。
此外，与其他循环不同，带标签的块表达式 *必须* 以标签开始。
{==+==}


{==+==}
## `continue` expressions
{==+==}
## `continue` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ContinueExpression_ :\
> &nbsp;&nbsp; `continue` [LIFETIME_OR_LABEL]<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _Continue 表达式_ :\
> &nbsp;&nbsp; `continue` [生命周期或标签][LIFETIME_OR_LABEL]<sup>?</sup>
{==+==}


{==+==}
When `continue` is encountered, the current iteration of the associated loop body is immediately terminated, returning control to the loop *head*.
In the case of a `while` loop, the head is the conditional expression controlling the loop.
In the case of a `for` loop, the head is the call-expression controlling the loop.

Like `break`, `continue` is normally associated with the innermost enclosing loop, but `continue 'label` may be used to specify the loop affected.
A `continue` expression is only permitted in the body of a loop.
{==+==}
当遇到 `continue` 关键字时，与其相关联的循环体的当前迭代会立即终止，控制权返回到循环 *头部* 。
在 `while` 循环中，头部是控制循环的条件表达式。在 `for` 循环中，头部是控制循环的调用表达式。

与 `break` 类似，`continue` 通常与最内层的封闭循环相关联，但可以使用 `continue 'label` 指定受影响的循环。
`continue` 表达式只允许出现在循环体中。
{==+==}


{==+==}
## `break` and loop values

When associated with a `loop`, a break expression may be used to return a value from that loop, via one of the forms `break EXPR` or `break 'label EXPR`, where `EXPR` is an expression whose result is returned from the `loop`.
For example:
{==+==}
## `break` 和循环值

在 Rust 中，当与 `loop` 关键字相关联时，可以使用 `break` 表达式通过 `break EXPR` 或 `break 'label EXPR` 的形式从循环中返回一个值，其中 `EXPR` 是返回给 `loop` 的表达式结果。
例如:
{==+==}


{==+==}
```rust
let (mut a, mut b) = (1, 1);
let result = loop {
    if b > 10 {
        break b;
    }
    let c = a + b;
    a = b;
    b = c;
};
// first number in Fibonacci sequence over 10:
assert_eq!(result, 13);
```
{==+==}
```rust
let (mut a, mut b) = (1, 1); // 初始化 a 和 b 为 1
let result = loop { // 进入循环
    if b > 10 { // 如果 b 大于 10
        break b; // 跳出循环并返回 b 的值
    }
    let c = a + b; 
    a = b; // 将 a 赋值为上一个 b 的值
    b = c; // 将 b 赋值为上一个 c 的值
};
// 斐波那契数列中第一个大于 10 的数是 13 :
assert_eq!(result, 13);
```
{==+==}


{==+==}
In the case a `loop` has an associated `break`, it is not considered diverging, and the `loop` must have a type compatible with each `break` expression.
`break` without an expression is considered identical to `break` with expression `()`.
{==+==}
在 `loop` 语句中，如果有关联的 `break` 语句，则不被视为发散，而且 `loop` 语句必须与每个 `break` 表达式兼容的类型。
未表明表达式的 `break` 语句的表达式被视为 `()` 。
{==+==}


{==+==}
[LIFETIME_OR_LABEL]: ../tokens.md#lifetimes-and-loop-labels
[_BlockExpression_]: block-expr.md
[_Expression_]: ../expressions.md
[_Pattern_]: ../patterns.md
[_Scrutinee_]: match-expr.md
[`match` expression]: match-expr.md
[boolean]: ../types/boolean.md
[scrutinee]: ../glossary.md#scrutinee
[temporary values]: ../expressions.md#temporaries
[_LazyBooleanOperatorExpression_]: operator-expr.md#lazy-boolean-operators
[`if let` expressions]: if-expr.md#if-let-expressions
{==+==}

{==+==}