{==+==}
# Statements

> **<sup>Syntax</sup>**\
> _Statement_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `;`\
> &nbsp;&nbsp; | [_Item_]\
> &nbsp;&nbsp; | [_LetStatement_]\
> &nbsp;&nbsp; | [_ExpressionStatement_]\
> &nbsp;&nbsp; | [_MacroInvocationSemi_]
{==+==}
# 语句

> **<sup>语法</sup>**\
> _语句_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `;`\
> &nbsp;&nbsp; | [_条目_][_Item_]\
> &nbsp;&nbsp; | [_Let语句_][_LetStatement_]\
> &nbsp;&nbsp; | [_表达式语句_][_ExpressionStatement_]\
> &nbsp;&nbsp; | [_宏调用语句_][_MacroInvocationSemi_]
{==+==}


{==+==}
A *statement* is a component of a [block], which is in turn a component of an outer [expression] or [function].

Rust has two kinds of statement: [declaration statements](#declaration-statements) and [expression statements](#expression-statements).
{==+==}
*语句* 是 [块][block] 的组成部分，而块又是之外 [表达式][expression] 或 [函数][function] 的组成部分。

Rust 有两种语句：[声明语句](#declaration-statements) 和 [表达式语句](#expression-statements) 。
{==+==}


{==+==}
## Declaration statements

A *declaration statement* is one that introduces one or more *names* into the enclosing statement block.
The declared names may denote new variables or new [items][item].

The two kinds of declaration statements are item declarations and `let` statements.
{==+==}
## 声明语句

*声明语句* 引入一个或多个 *名称* 到闭合语句块中。
声明的名称可能表示新变量或新 [条目][item] 。

声明语句有两种类型: `let` 语句和条目声明。
{==+==}


{==+==}
### Item declarations

An *item declaration statement* has a syntactic form identical to an [item declaration][item] within a [module].
Declaring an item within a statement block restricts its scope to the block containing the statement.
The item is not given a [canonical path] nor are any sub-items it may declare.
The exception to this is that associated items defined by [implementations] are still accessible in outer scopes as long as the item and, if applicable, trait are accessible.
It is otherwise identical in meaning to declaring the item inside a module.

There is no implicit capture of the containing function's generic parameters, parameters, and local variables.
For example, `inner` may not access `outer_var`.
{==+==}
### 条目声明

*条目声明语句* 的语法形式与 [模块][module] 中的 [条目声明][item] 相同。
在语句块中声明一个条目，将其作用域限制为包含该语句的块。
该条目不会被赋予 [规范路径][canonical path] ，也不会声明任何子条目。
唯一的例外是通过 [实现][implementations] 定义的关联条目，只要该条目和 (如果适用) trait 可访问，它们仍然可以在外部作用域中访问。
否则，它与在模块中声明该条目的含义相同。

不会隐式捕获包含函数的泛型参数、参数和局部变量。
例如， `inner` 无法访问 `outer_var` 。
{==+==}


{==+==}
```rust
fn outer() {
  let outer_var = true;

  fn inner() { /* outer_var is not in scope here */ }

  inner();
}
```
{==+==}
```rust
fn outer() {
  let outer_var = true;

  fn inner() { /* outer_var 在这里不在作用域内。 */ }

  inner();
}
```
{==+==}


{==+==}
### `let` statements
{==+==}
### `let` 语句
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LetStatement_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> `let` [_PatternNoTopAlt_]
>     ( `:` [_Type_] )<sup>?</sup> (`=` [_Expression_] [†](#let-else-restriction)
>     ( `else` [_BlockExpression_]) <sup>?</sup> ) <sup>?</sup> `;`
>
> <span id="let-else-restriction">† When an `else` block is specified, the
> _Expression_ must not be a [_LazyBooleanExpression_], or end with a `}`.</span>
{==+==}
> **<sup>语法</sup>**\
> _Let语句_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup> `let` [_模式非顶层选项_][_PatternNoTopAlt_]
>     ( `:` [_类型_][_Type_] )<sup>?</sup> (`=` [_表达式_][_Expression_] [†](#let-else-restriction)
>     ( `else` [_块表达式_][_BlockExpression_]) <sup>?</sup> ) <sup>?</sup> `;`
>
> <span id="let-else-restriction">† 当指定了 `else` 块时，表达式不能是 [_惰性布尔表达式_][_LazyBooleanExpression_] ，也不能以 `}` 结尾。</span>
{==+==}


{==+==}
A *`let` statement* introduces a new set of [variables], given by a [pattern].
The pattern is followed optionally by a type annotation and then either ends, or is followed by an initializer expression plus an optional `else` block.
When no type annotation is given, the compiler will infer the type, or signal an error if insufficient type information is available for definite inference.
Any variables introduced by a variable declaration are visible from the point of declaration until the end of the enclosing block scope, except when they are shadowed by another variable declaration.

If an `else` block is not present, the pattern must be irrefutable.
If an `else` block is present, the pattern may be refutable.
If the pattern does not match (this requires it to be refutable), the `else` block is executed.
The `else` block must always diverge (evaluate to the [never type]).
{==+==}
`let` 语句引入了一组由 [模式][pattern] 给定的新 [变量][variables] 。模式可选地后跟一个类型注释，然后以初始化表达式结束，或者跟随可选的 `else` 块。
当没有给出类型注释时，编译器将推断类型，如果没有足够的类型信息进行明确推断，则会发出错误信号。
任何由变量声明引入的变量在声明点到包含块范围的结尾之间都可见，除非它们被另一个变量声明隐藏。

如果不存在 `else` 块，则模式必须是不可反驳的。
如果存在 `else` 块，则模式可以是可反驳的。
如果模式不匹配 (这需要它是可反驳的) ，则执行 `else` 块。
`else` 块必须始终发散 (求值为 [永不类型][never type] )。

译注："反驳" (refutable) 和 "不可反驳" (irrefutable) 表示模式是否能够在任何情况下都成功匹配。
"发散" (diverge) 指的是一个表达式无法正常终止并返回值的情况。
{==+==}


{==+==}
```rust
let (mut v, w) = (vec![1, 2, 3], 42); // The bindings may be mut or const
let Some(t) = v.pop() else { // Refutable patterns require an else block
    panic!(); // The else block must diverge
};
let [u, v] = [v[0], v[1]] else { // This pattern is irrefutable, so the compiler
                                 // will lint as the else block is redundant.
    panic!();
};
```
{==+==}
```rust
let (mut v, w) = (vec![1, 2, 3], 42); // 绑定可以是可变或常量
let Some(t) = v.pop() else { // 可反驳的模式需要一个 else 块
    panic!(); // else 块必须发散
};
let [u, v] = [v[0], v[1]] else { // 这个模式是不可反驳的，所以编译器会作为 else 块是冗余的进行代码分析检查
    panic!();
};
```
{==+==}


{==+==}
## Expression statements

> **<sup>Syntax</sup>**\
> _ExpressionStatement_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_ExpressionWithoutBlock_][expression] `;`\
> &nbsp;&nbsp; | [_ExpressionWithBlock_][expression] `;`<sup>?</sup>

An *expression statement* is one that evaluates an [expression] and ignores its result.
As a rule, an expression statement's purpose is to trigger the effects of evaluating its expression.

An expression that consists of only a [block expression][block] or control flow expression, if used in a context where a statement is permitted, can omit the trailing semicolon.
This can cause an ambiguity between it being parsed as a standalone statement and as a part of another expression;
in this case, it is parsed as a statement.
The type of [_ExpressionWithBlock_][expression] expressions when used as statements must be the unit type.
{==+==}
## 表达式语句

> **<sup>语法</sup>**\
> _表达式语句_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_无块表达式_][expression] `;`\
> &nbsp;&nbsp; | [_块表达式_][expression] `;`<sup>?</sup>

一个表达式语句是指执行一个表达式并忽略其结果的语句。通常，表达式语句的目的是触发其表达式的副作用。

如果一个表达式只包含一个块表达式或控制流表达式，并且在允许语句的上下文中使用，它可以省略尾随的分号。
这会导致在将其解析为独立语句和作为另一个表达式的一部分之间产生歧义。在这种情况下，它会被解析为语句。
当作为语句使用时，[_块表达式_][expression] 表达式的类型必须是单元类型。
{==+==}


{==+==}
```rust
# let mut v = vec![1, 2, 3];
v.pop();          // Ignore the element returned from pop
if v.is_empty() {
    v.push(5);
} else {
    v.remove(0);
}                 // Semicolon can be omitted.
[1];              // Separate expression statement, not an indexing expression.
```
{==+==}
```rust
let mut v = vec![1, 2, 3];
v.pop();          // 忽略 pop 返回的元素
if v.is_empty() {
    v.push(5);
} else {
    v.remove(0);
}                 // 分号可以省略。
[1];              // 独立的表达式语句，不是索引表达式。
```
{==+==}


{==+==}
When the trailing semicolon is omitted, the result must be type `()`.
{==+==}
当省略了语句结尾的分号时，其结果必须是类型为 `()`。
{==+==}


{==+==}
```rust
// bad: the block's type is i32, not ()
// Error: expected `()` because of default return type
// if true {
//   1
// }

// good: the block's type is i32
if true {
  1
} else {
  2
};
```
{==+==}
```rust
// 错误: 块表达式类型是 i32, 不是 ()
// Error: expected `()` because of default return type
// if true {
//   1
// }

// 成功: 块表达式类型是 i32
if true {
  1
} else {
  2
};
```
{==+==}


{==+==}
## Attributes on Statements

Statements accept [outer attributes].
The attributes that have meaning on a statement are [`cfg`], and [the lint check attributes].
{==+==}
## 语句上的属性

语句可以接受 [外围属性][outer attributes] 。
在语句上具有意义的属性包括 [`cfg`] 和 [代码分析检查属性][the lint check attributes] 。
{==+==}


{==+==}
[block]: expressions/block-expr.md
[expression]: expressions.md
[function]: items/functions.md
[item]: items.md
[module]: items/modules.md
[never type]: types/never.md
[canonical path]: paths.md#canonical-paths
[implementations]: items/implementations.md
[variables]: variables.md
[outer attributes]: attributes.md
[`cfg`]: conditional-compilation.md
[the lint check attributes]: attributes/diagnostics.md#lint-check-attributes
[pattern]: patterns.md
[_BlockExpression_]: expressions/block-expr.md
[_ExpressionStatement_]: #expression-statements
[_Expression_]: expressions.md
[_Item_]: items.md
[_LazyBooleanExpression_]: expressions/operator-expr.md#lazy-boolean-operators
[_LetStatement_]: #let-statements
[_MacroInvocationSemi_]: macros.md#macro-invocation
[_OuterAttribute_]: attributes.md
[_PatternNoTopAlt_]: patterns.md
[_Type_]: types.md
{==+==}

{==+==}