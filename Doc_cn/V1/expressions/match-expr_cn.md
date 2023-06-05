{==+==}
# `match` expressions
{==+==}
# `match` 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MatchExpression_ :\
> &nbsp;&nbsp; `match` _Scrutinee_ `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _MatchArms_<sup>?</sup>\
> &nbsp;&nbsp; `}`
>
>_Scrutinee_ :\
> &nbsp;&nbsp; [_Expression_]<sub>_except struct expression_</sub>
>
> _MatchArms_ :\
> &nbsp;&nbsp; ( _MatchArm_ `=>`
>                             ( [_ExpressionWithoutBlock_][_Expression_] `,`
>                             | [_ExpressionWithBlock_][_Expression_] `,`<sup>?</sup> )
>                           )<sup>\*</sup>\
> &nbsp;&nbsp; _MatchArm_ `=>` [_Expression_] `,`<sup>?</sup>
>
> _MatchArm_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> [_Pattern_] _MatchArmGuard_<sup>?</sup>
>
> _MatchArmGuard_ :\
> &nbsp;&nbsp; `if` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _Match表达式_ :\
> &nbsp;&nbsp; `match` _被匹配项_ `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _Match分支组_<sup>?</sup>\
> &nbsp;&nbsp; `}`
>
>_被匹配项_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_]<sub>_不包括结构体表达式_</sub>
>
> _Match分支组_ :\
> &nbsp;&nbsp; ( _Match分支_ `=>`
>                             ( [_无块表达式_][_Expression_] `,`
>                             | [_块表达式_][_Expression_] `,`<sup>?</sup> )
>                           )<sup>\*</sup>\
> &nbsp;&nbsp; _Match分支_ `=>` [_表达式_][_Expression_] `,`<sup>?</sup>
>
> _Match分支_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> [_模式_][_Pattern_] _Match分支守卫_<sup>?</sup>
>
> _Match分支守卫_ :\
> &nbsp;&nbsp; `if` [_表达式_][_Expression_]
{==+==}


{==+==}
A *`match` expression* branches on a pattern.
The exact form of matching that occurs depends on the [pattern].
A `match` expression has a *[scrutinee] expression*, which is the value to compare to the patterns.
The scrutinee expression and the patterns must have the same type.
{==+==}
 *`match` 表达式* 根据模式而进行分支。匹配所发生的确切形式取决于 [模式][pattern] 。
 `match` 表达式有一个 *[被匹配项][scrutinee] 表达式*，与模式进行比较。
被匹配表达式和模式必须类型相同。
{==+==}


{==+==}
A `match` behaves differently depending on whether or not the scrutinee expression is a [place expression or value expression][place expression].
If the scrutinee expression is a [value expression], it is first evaluated into a temporary location, and the resulting value is sequentially compared to the patterns in the arms until a match is found.
The first arm with a matching pattern is chosen as the branch target of the `match`, any variables bound by the pattern are assigned to local variables in the arm's block, and control enters the block.
{==+==}
`match` 的行为取决于被匹配表达式是一个 [占位表达式还是值表达式][place expression]。
如果被匹配表达式是一个 [值表达式][value expression] ，它首先被求值到一个临时位置，然后将结果值按顺序与每个分支中的模式进行比较，直到找到匹配的模式。
第一个模式匹配的分支作为 `match` 的目标分支，任何由模式绑定的变量被分配为分支块局部变量，进入块中。
{==+==}


{==+==}
When the scrutinee expression is a [place expression], the match does not allocate a temporary location;
however, a by-value binding may copy or move from the memory location.
When possible, it is preferable to match on place expressions, as the lifetime of these matches inherits the lifetime of the place expression rather than being restricted to the inside of the match.

An example of a `match` expression:
{==+==}
当被匹配表达式是一个 [占位表达式][place expression] 时，`match` 不会分配临时地址；但是，按值绑定时可能会从内存位置复制或移动。
如果可能的话，最好匹配占位表达式，因为这些匹配项的生命周期继承了占位表达式的生命周期，而不是被限制在 `match` 内部。

下面是一个 `match` 表达式的示例：
{==+==}


{==+==}
```rust
let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    4 => println!("four"),
    5 => println!("five"),
    _ => println!("something else"),
}
```
{==+==}

{==+==}


{==+==}
Variables bound within the pattern are scoped to the match guard and the arm's expression.
The [binding mode] (move, copy, or reference) depends on the pattern.

Multiple match patterns may be joined with the `|` operator.
Each pattern will be tested in left-to-right sequence until a successful match is found.
{==+==}
在模式中绑定的变量的作用域限定在 `match` 守卫及分支表达式中。[绑定模式][binding mode] (移动、复制或引用) 取决于模式。

多个匹配模式可以用 `|` 运算符连接起来。每个模式将按照从左到右的顺序进行测试，直到找到成功匹配的模式为止。
{==+==}


{==+==}
```rust
let x = 9;
let message = match x {
    0 | 1  => "not many",
    2 ..= 9 => "a few",
    _      => "lots"
};

assert_eq!(message, "a few");

// Demonstration of pattern match order.
struct S(i32, i32);

match S(1, 2) {
    S(z @ 1, _) | S(_, z @ 2) => assert_eq!(z, 1),
    _ => panic!(),
}
```
{==+==}
```rust
let x = 9;
let message = match x {
    0 | 1  => "not many",     // 如果 x 是 0 或 1，将 "not many" 赋值给 message
    2 ..= 9 => "a few",       // 如果 x 在 2 到 9 之间（包括 2 和 9），将 "a few" 赋值给 message
    _      => "lots"         // 否则将 "lots" 赋值给 message
};

assert_eq!(message, "a few");

// 演示模式匹配的顺序。
struct S(i32, i32);

match S(1, 2) {
    // 如果 S 的第一个元素是 1 或者第二个元素是 2，则将 z 绑定为 1，并且通过断言来检查 z 的值。
    S(z @ 1, _) | S(_, z @ 2) => assert_eq!(z, 1),  
    // 否则发生错误
    _ => panic!(),
}
```
{==+==}


{==+==}
> Note: The `2..=9` is a [Range Pattern], not a [Range Expression].
> Thus, only those types of ranges supported by range patterns can be used in match arms.

Every binding in each `|` separated pattern must appear in all of the patterns in the arm.
Every binding of the same name must have the same type, and have the same binding mode.
{==+==}
> 注意: `2..=9` 是 [区间模式][Range Pattern] ，不是 [区间表达式][Range Expression] 。因此，在匹配分支中只能使用由区间模式支持的这些类型的区间。

每个 `|` 分隔的模式中的所有绑定都必须出现在分支的所有模式中。每个同名绑定必须类型相同，且绑定模式相同。
{==+==}


{==+==}
## Match guards

Match arms can accept _match guards_ to further refine the criteria for matching a case.
Pattern guards appear after the pattern and consist of a `bool`-typed expression following the `if` keyword.

When the pattern matches successfully, the pattern guard expression is executed.
If the expression evaluates to true, the pattern is successfully matched against.
Otherwise, the next pattern, including other matches with the `|` operator in the same arm, is tested.
{==+==}
## 匹配守卫

`match` 分支可以接受 _匹配守卫_ 来进一步细化匹配条件。
模式守卫出现在模式之后，由 `if` 关键字后面的 `bool` 类型表达式组成。

当模式成功匹配时，模式守卫表达式会被执行。如果表达式的结果为 true，则模式成功匹配。
否则，将测试下一个模式，包括同一分支中使用 `|` 运算符的其他匹配。
{==+==}


{==+==}
```rust
# let maybe_digit = Some(0);
# fn process_digit(i: i32) { }
# fn process_other(i: i32) { }
let message = match maybe_digit {
    Some(x) if x < 10 => process_digit(x),
    Some(x) => process_other(x),
    None => panic!(),
};
```
{==+==}

{==+==}


{==+==}
> Note: Multiple matches using the `|` operator can cause the pattern guard and the side effects it has to execute multiple times.
> For example:
{==+==}
> 注意：使用 `|` 操作符的多个匹配项可能导致模式守卫及其所产生的副作用执行多次。
> 例如：
{==+==}


{==+==}
> ```rust
> # use std::cell::Cell;
> let i : Cell<i32> = Cell::new(0);
> match 1 {
>     1 | _ if { i.set(i.get() + 1); false } => {}
>     _ => {}
> }
> assert_eq!(i.get(), 2);
> ```
{==+==}

{==+==}


{==+==}
A pattern guard may refer to the variables bound within the pattern they follow.
Before evaluating the guard, a shared reference is taken to the part of the scrutinee the variable matches on.
While evaluating the guard, this shared reference is then used when accessing the variable.
Only when the guard evaluates to true is the value moved, or copied, from the scrutinee into the variable.
This allows shared borrows to be used inside guards without moving out of the scrutinee in case guard fails to match.
Moreover, by holding a shared reference while evaluating the guard, mutation inside guards is also prevented.
{==+==}
模式守卫可以引用其所跟随的模式中绑定的变量。
在评估模式守卫之前，会对匹配变量的部分获取共享引用。
在评估模式守卫时，这个共享引用被用于访问该变量。
只有当模式守卫计算结果为 true 时，才会将值从待匹配项移动或复制到该变量中。
这允许在守卫中使用共享借用，如果守卫不匹配，则不会移动待匹配项。
此外，通过在评估守卫时保持共享引用，还可以防止在守卫内进行修改。
{==+==}


{==+==}
## Attributes on match arms

Outer attributes are allowed on match arms.
The only attributes that have meaning on match arms are [`cfg`] and the [lint check attributes].

[Inner attributes] are allowed directly after the opening brace of the match expression in the same expression contexts as [attributes on block expressions].
{==+==}
## 匹配分支上的属性

可以在匹配分支上使用外围属性。
在匹配分支上具有意义的外围属性只有 [`cfg`] 和 [代码分析检查属性][lint check attributes] 。
[内部属性][Inner attributes] 可以直接放置在匹配表达式的左括号后面，在与 [块表达式上的属性][attributes on block expressions] 相同的表达式上下文中。
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[value expression]: ../expressions.md#place-expressions-and-value-expressions
[_InnerAttribute_]: ../attributes.md
[_OuterAttribute_]: ../attributes.md
[`cfg`]: ../conditional-compilation.md
[lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[Range Expression]: range-expr.md

[_Pattern_]: ../patterns.md
[pattern]: ../patterns.md
[Inner attributes]: ../attributes.md
[Range Pattern]: ../patterns.md#range-patterns
[attributes on block expressions]: block-expr.md#attributes-on-block-expressions
[binding mode]: ../patterns.md#binding-modes
[scrutinee]: ../glossary.md#scrutinee
{==+==}

{==+==}