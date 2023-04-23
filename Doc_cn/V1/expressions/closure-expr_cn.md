{==+==}
# Closure expressions
{==+==}
# 闭包表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ClosureExpression_ :\
> &nbsp;&nbsp; `move`<sup>?</sup>\
> &nbsp;&nbsp; ( `||` | `|` _ClosureParameters_<sup>?</sup> `|` )\
> &nbsp;&nbsp; ([_Expression_] | `->` [_TypeNoBounds_]&nbsp;[_BlockExpression_])
>
> _ClosureParameters_ :\
> &nbsp;&nbsp; _ClosureParam_ (`,` _ClosureParam_)<sup>\*</sup> `,`<sup>?</sup>
>
> _ClosureParam_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> [_PatternNoTopAlt_]&nbsp;( `:` [_Type_] )<sup>?</sup>
{==+==}
> **<sup>语法</sup>**\
> _闭包表达式_ :\
> &nbsp;&nbsp; `move`<sup>?</sup>\
> &nbsp;&nbsp; ( `||` | `|` _闭包参数组_<sup>?</sup> `|` )\
> &nbsp;&nbsp; ([_表达式_][_Expression_] | `->` [_无约束类型组_][_TypeNoBounds_]&nbsp;[_块表达式_][_BlockExpression_])
>
> _闭包参数组_ :\
> &nbsp;&nbsp; _闭包参数_ (`,` _闭包参数_)<sup>\*</sup> `,`<sup>?</sup>
>
> _闭包参数_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> [_模式无顶层选项_][_PatternNoTopAlt_]&nbsp;( `:` [_类型_][_Type_] )<sup>?</sup>
{==+==}


{==+==}
A *closure expression*, also known as a lambda expression or a lambda, defines a [closure type] and evaluates to a value of that type.
The syntax for a closure expression is an optional `move` keyword, then a pipe-symbol-delimited (`|`) comma-separated list of [patterns], called the *closure parameters* each optionally followed by a `:` and a type, then an optional `->` and type, called the *return type*, and then an expression, called the *closure body operand*.
The optional type after each pattern is a type annotation for the pattern.
If there is a return type, the closure body must be a [block].
{==+==}
*闭包表达式* ，也称为 lambda 表达式或 lambda ，定义一个 [闭包类型][closure type] 并求值为该类型的值。
闭包表达式的语法是一个可选的 `move` 关键字，然后是一个由管道符号分隔 (`|`) 的逗号分隔的 [模式][patterns] 列表，称为*闭包参数*，每个参数后面可选地跟随一个 `:` 和一个类型，然后是一个可选的 `->` 和类型，称为 *返回类型* ，然后是一个表达式，称为 *闭包体操作数* 。每个模式后面的可选类型是该模式的类型注解。如果存在返回类型，则闭包体必须是一个 [块][block] 。
{==+==}


{==+==}
A closure expression denotes a function that maps a list of parameters onto the expression that follows the parameters.
Just like a [`let` binding], the closure parameters are irrefutable [patterns], whose type annotation is optional and will be inferred from context if not given.
Each closure expression has a unique, anonymous type.
{==+==}
闭包表达式表示一个函数，该函数将一系列参数映射到跟随参数的表达式。
就像 [`let` 绑定][`let` binding] 一样，闭包参数是无法拒绝的 [模式][patterns] ，其类型注解是可选的，如果未给出，将从上下文中推断。
每个闭包表达式都具有一个唯一的、匿名的类型。
{==+==}


{==+==}
Significantly, closure expressions _capture their environment_, which regular [function definitions] do not.
Without the `move` keyword, the closure expression [infers how it captures each variable from its environment](../types/closure.md#capture-modes), preferring to capture by shared reference, effectively borrowing all outer variables mentioned inside the closure's body.
If needed the compiler will infer that instead mutable references should be taken, or that the values should be moved or copied (depending on their type) from the environment.
A closure can be forced to capture its environment by copying or moving values by prefixing it with the `move` keyword.
This is often used to ensure that the closure's lifetime is `'static`.
{==+==}
值得注意的是，闭包表达式 _捕获其环境_ ，而普通的 [函数定义][function definitions] 不会。
如果没有 `move` 关键字，闭包表达式 [推断它如何从其环境中捕获每个变量](../types/closure.md#capture-modes) ，优先通过共享引用来捕获，实际上借用闭包体内提到的所有外部变量。
如果需要，编译器将推断应该取而代之的是可变引用，或者应该从环境中移动或复制值 (取决于它们的类型) 。
通过在闭包前面加上 `move` 关键字，可以强制闭包通过复制或移动值来捕获其环境。
这通常用于确保闭包的生命周期为 `'static` 。
{==+==}


{==+==}
## Closure trait implementations

Which traits the closure type implement depends on how variables are captured and the types of the captured variables.
See the [call traits and coercions] chapter for how and when a closure implements `Fn`, `FnMut`, and `FnOnce`.
The closure type implements [`Send`] and [`Sync`] if the type of every captured variable also implements the trait.
{==+==}
## 闭包类型实现的 trait

闭包类型实现哪些 trait 取决于如何捕获变量和捕获的变量的类型。
请参考 [调用 trait 和强制转换][call traits and coercions] 章节了解闭包实现 `Fn` 、 `FnMut` 和 `FnOnce` 的情况和时间。
如果每个捕获的变量的类型也实现了 trait ，闭包类型实现 [`Send`] 和 [`Sync`] 。
{==+==}


{==+==}
## Example

In this example, we define a function `ten_times` that takes a higher-order function argument, and we then call it with a closure expression as an argument, followed by a closure expression that moves values from its environment.
{==+==}
## 示例

在这个例子中，我们定义了一个函数 `ten_times` ，它接受一个高阶函数参数，然后我们调用它并传入一个闭包表达式作为参数，接着传入一个从环境中移动值的闭包表达式。
{==+==}


{==+==}
```rust
fn ten_times<F>(f: F) where F: Fn(i32) {
    for index in 0..10 {
        f(index);
    }
}

ten_times(|j| println!("hello, {}", j));
// With type annotations
ten_times(|j: i32| -> () { println!("hello, {}", j) });

let word = "konnichiwa".to_owned();
ten_times(move |j| println!("{}, {}", word, j));
```
{==+==}
```rust
fn ten_times<F>(f: F) where F: Fn(i32) {
    // 遍历 0..10，对每个值调用 f 函数
    for index in 0..10 {
        f(index);
    }
}

// 调用 ten_times 函数，并传入一个匿名函数作为参数，该匿名函数打印出 "hello" 和输入值
ten_times(|j| println!("hello, {}", j));
// 带有类型注解的调用方式，和上一行等价
ten_times(|j: i32| -> () { println!("hello, {}", j) });

let word = "konnichiwa".to_owned();
// 调用 ten_times 函数，并传入一个匿名函数作为参数，该匿名函数打印出 word 和输入值
// 由于匿名函数要使用外部变量 word，所以前面加上 move 关键字
ten_times(move |j| println!("{}, {}", word, j));
```
{==+==}


{==+==}
## Attributes on closure parameters

Attributes on closure parameters follow the same rules and restrictions as [regular function parameters].
{==+==}
## 在闭包参数上的属性

在闭包参数上的属性遵循与 [普通函数参数][regular function parameters] 相同的规则和限制。
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[_BlockExpression_]: block-expr.md
[_TypeNoBounds_]: ../types.md#type-expressions
[_PatternNoTopAlt_]: ../patterns.md
[_Type_]: ../types.md#type-expressions
[`let` binding]: ../statements.md#let-statements
[`Send`]: ../special-types-and-traits.md#send
[`Sync`]: ../special-types-and-traits.md#sync
[_OuterAttribute_]: ../attributes.md
[block]: block-expr.md
[call traits and coercions]: ../types/closure.md#call-traits-and-coercions
[closure type]: ../types/closure.md
[function definitions]: ../items/functions.md
[patterns]: ../patterns.md
[regular function parameters]: ../items/functions.md#attributes-on-function-parameters
{==+==}

{==+==}