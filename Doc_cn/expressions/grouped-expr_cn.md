{==+==}
# Grouped expressions
{==+==}
# 分组表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _GroupedExpression_ :\
> &nbsp;&nbsp; `(` [_Expression_] `)`
{==+==}
> **<sup>语法</sup>**\
> _分组表达式_ :\
> &nbsp;&nbsp; `(` [_表达式_][_Expression_] `)`
{==+==}


{==+==}
A *parenthesized expression* wraps a single expression, evaluating to that expression.
The syntax for a parenthesized expression is a `(`, then an expression, called the *enclosed operand*, and then a `)`.

Parenthesized expressions evaluate to the value of the enclosed operand.
Unlike other expressions, parenthesized expressions are both [place expressions and value expressions][place].
When the enclosed operand is a place expression, it is a place expression and when the enclosed operand is a value expression, it is a value expression.

Parentheses can be used to explicitly modify the precedence order of subexpressions within an expression.

An example of a parenthesized expression:
{==+==}
*括号表达式* 将一个单独的表达式包裹起来，最终的结果是这个表达式的值。
括号表达式的语法是 `(` ，然后是一个表达式，称为 *内嵌操作数* ，最后是一个 `)` 。

括号表达式的值等同于内嵌操作数的值。与其他表达式不同的是，括号表达式既是 [占位表达式][place] 也是值表达式。
当内嵌操作数是占位表达式时，括号表达式也是占位表达式；当内嵌操作数是值表达式时，括号表达式也是值表达式。

括号可以用于显式修改表达式中子表达式的优先级顺序。

以下是括号表达式的示例:
{==+==}


{==+==}
```rust
let x: i32 = 2 + 3 * 4; // not parenthesized
let y: i32 = (2 + 3) * 4; // parenthesized
assert_eq!(x, 14);
assert_eq!(y, 20);
```
{==+==}
```rust
let x: i32 = 2 + 3 * 4;       // 未使用括号的表达式
let y: i32 = (2 + 3) * 4;     // 使用了括号的表达式
assert_eq!(x, 14);            // 验证 x 的值是否为 14
assert_eq!(y, 20);            // 验证 y 的值是否为 20
```
{==+==}


{==+==}
An example of a necessary use of parentheses is when calling a function pointer that is a member of a struct:
{==+==}
必须使用括号的一个例子是调用结构体成员的函数指针:
{==+==}


{==+==}
```rust
# struct A {
#    f: fn() -> &'static str
# }
# impl A {
#    fn f(&self) -> &'static str {
#        "The method f"
#    }
# }
# let a = A{f: || "The field f"};
#
assert_eq!( a.f (), "The method f");
assert_eq!((a.f)(), "The field f");
```
{==+==}

{==+==}


{==+==}
[_Expression_]: ../expressions.md
[place]: ../expressions.md#place-expressions-and-value-expressions
{==+==}

{==+==}