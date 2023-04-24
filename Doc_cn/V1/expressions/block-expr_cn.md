{==+==}
# Block expressions
{==+==}
# 块表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _BlockExpression_ :\
> &nbsp;&nbsp; `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _Statements_<sup>?</sup>\
> &nbsp;&nbsp; `}`
>
> _Statements_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Statement_]<sup>\+</sup>\
> &nbsp;&nbsp; | [_Statement_]<sup>\+</sup> [_ExpressionWithoutBlock_]\
> &nbsp;&nbsp; | [_ExpressionWithoutBlock_]
{==+==}
> **<sup>语法</sup>**\
> _块表达式_ :\
> &nbsp;&nbsp; `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _语句组_<sup>?</sup>\
> &nbsp;&nbsp; `}`
>
> _语句组_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_语句_][_Statement_]<sup>\+</sup>\
> &nbsp;&nbsp; | [_语句_][_Statement_]<sup>\+</sup> [_无块表达式_][_ExpressionWithoutBlock_]\
> &nbsp;&nbsp; | [_无块表达式_][_ExpressionWithoutBlock_]
{==+==}


{==+==}
A *block expression*, or *block*, is a control flow expression and anonymous namespace scope for items and variable declarations.
As a control flow expression, a block sequentially executes its component non-item declaration statements and then its final optional expression.
As an anonymous namespace scope, item declarations are only in scope inside the block itself and variables declared by `let` statements are in scope from the next statement until the end of the block.

The syntax for a block is `{`, then any [inner attributes], then any number of [statements], then an optional expression, called the final operand, and finally a `}`.

Statements are usually required to be followed by a semicolon, with two exceptions:

1. Item declaration statements do not need to be followed by a semicolon.
2. Expression statements usually require a following semicolon except if its outer expression is a flow control expression.

Furthermore, extra semicolons between statements are allowed, but these semicolons do not affect semantics.

When evaluating a block expression, each statement, except for item declaration statements, is executed sequentially.
Then the final operand is executed, if given.

The type of a block is the type of the final operand, or `()` if the final operand is omitted.
{==+==}
*块表达式* 或 *块* 是一个控制流表达式和用于条目和变量声明的匿名命名空间作用域。
作为控制流表达式，块按顺序执行其组成的非条目声明语句，然后执行其最后的可选表达式。
作为匿名命名空间作用域，在块内部声明的条目声明在块本身中可用，而由 `let` 语句声明的变量从下一条语句开始在块结束之前处于作用域中。

块的语法是 `{` ，然后是任何 [内部属性][inner attributes] ，然后是任意数量的 [语句][statements] ，然后是一个可选的表达式，称为最后的操作数，最后是一个 `}` 。

语句通常需要在分号后面，有两个例外：

1. 条目声明语句不需要在分号后面。
2. 表达式语句通常需要一个后续分号，除非其外部表达式是控制流表达式。

此外，语句之间的额外分号是允许的，但是这些分号不影响语义。

在评估块表达式时，除条目声明语句外，每个语句都按顺序执行。
然后，如果给定了最后的操作数，则执行它。

块的类型是最后操作数的类型，如果省略了最后操作数，则为 `()` 。
{==+==}


{==+==}
```rust
# fn fn_call() {}
let _: () = {
    fn_call();
};

let five: i32 = {
    fn_call();
    5
};

assert_eq!(5, five);
```
{==+==}

{==+==}


{==+==}
> Note: As a control flow expression, if a block expression is the outer expression of an expression statement, the expected type is `()` unless it is followed immediately by a semicolon.

Blocks are always [value expressions] and evaluate the last operand in value expression context.

> **Note**: This can be used to force moving a value if really needed.
> For example, the following example fails on the call to `consume_self` because the struct was moved out of `s` in the block expression.
{==+==}
> 注意: 作为控制流表达式，如果块表达式是表达式语句的外部表达式，期望的类型是 `()` ，除非它紧跟着一个分号。

块始终是值表达式，并在值表达式上下文中评估最后一个操作数。

> **注意**: 如果确实需要，这可以用于强制移动值。例如，以下示例在调用 `consume_self` 时失败，因为结构体已在块表达式中移出了 `s` 。
{==+==}


{==+==}
> ```rust,compile_fail
> struct Struct;
>
> impl Struct {
>     fn consume_self(self) {}
>     fn borrow_self(&self) {}
> }
>
> fn move_by_block_expression() {
>     let s = Struct;
>
>     // Move the value out of `s` in the block expression.
>     (&{ s }).borrow_self();
>
>     // Fails to execute because `s` is moved out of.
>     s.consume_self();
> }
> ```
{==+==}
> ```rust,compile_fail
> struct Struct;
>
> impl Struct {
>     fn consume_self(self) {}
>     fn borrow_self(&self) {}
> }
>
> fn move_by_block_expression() {
>     let s = Struct;
>
>     // 在块表达式中将值从 `s` 移出。
>     (&{ s }).borrow_self();
>
>     // 无法执行，因为 `s` 已经被移出了。
>     s.consume_self();
> }
> ```
{==+==}


{==+==}
## `async` blocks

> **<sup>Syntax</sup>**\
> _AsyncBlockExpression_ :\
> &nbsp;&nbsp; `async` `move`<sup>?</sup> _BlockExpression_

An *async block* is a variant of a block expression which evaluates to a future.
The final expression of the block, if present, determines the result value of the future.

Executing an async block is similar to executing a closure expression:
its immediate effect is to produce and return an anonymous type.
Whereas closures return a type that implements one or more of the [`std::ops::Fn`] traits, however, the type returned for an async block implements the [`std::future::Future`] trait.
The actual data format for this type is unspecified.

> **Note:** The future type that rustc generates is roughly equivalent to an enum with one variant per `await` point, where each variant stores the data needed to resume from its corresponding point.

> **Edition differences**: Async blocks are only available beginning with Rust 2018.
{==+==}
## `async` 块

> **<sup>语法</sup>**\
> _Async块表达式_ :\
> &nbsp;&nbsp; `async` `move`<sup>?</sup> _块表达式_

异步块是块表达式的一种变体，它计算为一个 future。
块的最终表达式 (如果存在) 确定 future 的结果值。

执行异步块类似于执行闭包表达式: 它的立即效果是生成并返回一个匿名类型。
然而，闭包返回实现一个或多个 [`std::ops::Fn`] trait 的类型，异步块返回的类型实现了 [`std::future::Future`] trait。
这种类型的实际数据格式未指定。

> **注意:** rustc 生成的 future 类型大致相当于具有每个 `await` 点一个变体的枚举类型，其中每个变体存储从其相应点恢复所需的数据。
> **版本差异**: 异步块仅在Rust 2018及以后版本中可用。
{==+==}


{==+==}
### Capture modes

Async blocks capture variables from their environment using the same [capture modes] as closures.
Like closures, when written `async { .. }` the capture mode for each variable will be inferred from the content of the block.
`async move { .. }` blocks however will move all referenced variables into the resulting future.
{==+==}
### 捕获模式

异步块使用与闭包相同的 [捕获模式][capture modes] 从它们的环境中捕获变量。
与闭包一样，当写成 `async { .. }` 时，每个变量的捕获模式将从块的内容中推断出来。
然而， `async move { .. }` 块将移动所有被引用的变量到生成的 future 中。
{==+==}


{==+==}
### Async context

Because async blocks construct a future, they define an **async context** which can in turn contain [`await` expressions].
Async contexts are established by async blocks as well as the bodies of async functions, whose semantics are defined in terms of async blocks.
{==+==}
### 异步上下文

由于异步块构造了一个 future ，其定义了一个 **异步上下文** ，这个上下文可以包含 [`await` 表达式][`await` expressions] 。
异步上下文由异步块以及异步函数的函数体建立，后者的语义是以异步块为基础定义的。
{==+==}


{==+==}
### Control-flow operators

Async blocks act like a function boundary, much like closures.
Therefore, the `?` operator and `return` expressions both affect the output of the future, not the enclosing function or other context.
That is, `return <expr>` from within an async block will return the result of `<expr>` as the output of the future.
Similarly, if `<expr>?` propagates an error, that error is propagated as the result of the future.

Finally, the `break` and `continue` keywords cannot be used to branch out from an async block.
Therefore the following is illegal:

```rust,compile_fail
loop {
    async move {
        break; // error[E0267]: `break` inside of an `async` block
    }
}
```
{==+==}
### 控制流运算符

异步块类似于函数约束，就像闭包一样。
因此， `?` 运算符和 `return` 表达式都会影响 future 的输出，而不是封闭函数或其他上下文。
也就是说，从异步块中的 `return <expr>` 将返回 `<expr>` 的结果作为 future 的输出。
同样，如果 `<expr>?` 传播错误，则该错误将作为 future 的结果传播。

最后， `break` 和 `continue` 关键字不能用于从异步块中分支跳出。
因此以下代码是非法的:

```rust,compile_fail
loop {
    async move {
        break; // error[E0267]: `break` inside of an `async` block
    }
}
```
{==+==}


{==+==}
## `unsafe` blocks

> **<sup>Syntax</sup>**\
> _UnsafeBlockExpression_ :\
> &nbsp;&nbsp; `unsafe` _BlockExpression_

_See [`unsafe` block](../unsafe-blocks.md) for more information on when to use `unsafe`_

A block of code can be prefixed with the `unsafe` keyword to permit [unsafe operations].
Examples:
{==+==}
## `unsafe` 块

> **<sup>语法</sup>**\
> _Unsafe块表达式_ :\
> &nbsp;&nbsp; `unsafe` _块表达式_

在需要进行 [unsafe 操作][unsafe operations] 时，可以在一段代码块前加上 `unsafe` 关键字。详见 [`unsafe` 块](../unsafe-blocks.md) 。
示例:
{==+==}


{==+==}
```rust
unsafe {
    let b = [13u8, 17u8];
    let a = &b[0] as *const u8;
    assert_eq!(*a, 13);
    assert_eq!(*a.offset(1), 17);
}

# unsafe fn an_unsafe_fn() -> i32 { 10 }
let a = unsafe { an_unsafe_fn() };
```
{==+==}

{==+==}


{==+==}
## Labelled block expressions

Labelled block expressions are documented in the [Loops and other breakable expressions] section.
{==+==}
## 标记的块表达式

标记的块表达式在 [循环和其他可中断表达式][Loops and other breakable expressions] 部分进行了说明。
{==+==}


{==+==}
## Attributes on block expressions

[Inner attributes] are allowed directly after the opening brace of a block expression in the following situations:

* [Function] and [method] bodies.
* Loop bodies ([`loop`], [`while`], [`while let`], and [`for`]).
* Block expressions used as a [statement].
* Block expressions as elements of [array expressions], [tuple expressions],
  [call expressions], and tuple-style [struct] expressions.
* A block expression as the tail expression of another block expression.
<!-- Keep list in sync with expressions.md -->

The attributes that have meaning on a block expression are [`cfg`] and [the lint check attributes].

For example, this function returns `true` on unix platforms and `false` on other platforms.
{==+==}
## 块表达式上的属性

在以下情况下，可以在块表达式的左括号之后直接使用 [内部属性][Inner attributes] :

* [函数][Function] 和 [方法][method] 体。
* 循环体 ( [`loop`] 、 [`while`] 、 [`while let`] 和 [`for`] )。
* 作为 [语句][statement] 使用的块表达式。
* 块表达式作为 [数组表达式][array expressions] 、 [元组表达式][tuple expressions] 、 [调用表达式][call expressions] 和类似元组的 [结构体][struct] 表达式的元素。
* 另一个块表达式的尾表达式是块表达式。
<!-- Keep list in sync with expressions.md -->

具有在块表达式上的含义的属性包括 [`cfg`] 和 [代码分析检查属性][the lint check attributes] 。

例如，这个函数在 Unix 平台上返回 `true` ，在其他平台上返回 `false` 。
{==+==}


{==+==}
```rust
fn is_unix_platform() -> bool {
    #[cfg(unix)] { true }
    #[cfg(not(unix))] { false }
}
```
{==+==}

{==+==}


{==+==}
[_ExpressionWithoutBlock_]: ../expressions.md
[_InnerAttribute_]: ../attributes.md
[_Statement_]: ../statements.md
[`await` expressions]: await-expr.md
[`cfg`]: ../conditional-compilation.md
[`for`]: loop-expr.md#iterator-loops
[`loop`]: loop-expr.md#infinite-loops
[`std::ops::Fn`]: ../../std/ops/trait.Fn.html
[`std::future::Future`]: ../../std/future/trait.Future.html
[`while let`]: loop-expr.md#predicate-pattern-loops
[`while`]: loop-expr.md#predicate-loops
[array expressions]: array-expr.md
[call expressions]: call-expr.md
[capture modes]: ../types/closure.md#capture-modes
[function]: ../items/functions.md
[inner attributes]: ../attributes.md
[method]: ../items/associated-items.md#methods
[mutable reference]: ../types/pointer.md#mutables-references-
[shared references]: ../types/pointer.md#shared-references-
[statement]: ../statements.md
[statements]: ../statements.md
[struct]: struct-expr.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[tuple expressions]: tuple-expr.md
[unsafe operations]: ../unsafety.md
[value expressions]: ../expressions.md#place-expressions-and-value-expressions
[Loops and other breakable expressions]: loop-expr.md#labelled-block-expressions
{==+==}

{==+==}