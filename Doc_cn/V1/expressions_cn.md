{==+==}
# Expressions
{==+==}
# 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Expression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _ExpressionWithoutBlock_\
> &nbsp;&nbsp; | _ExpressionWithBlock_
>
> _ExpressionWithoutBlock_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup>[†](#expression-attributes)\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_LiteralExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_PathExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_OperatorExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_GroupedExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ArrayExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_AwaitExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_IndexExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_TupleExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_TupleIndexingExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_StructExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_CallExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_MethodCallExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_FieldExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ClosureExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_AsyncBlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ContinueExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_BreakExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_RangeExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ReturnExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_UnderscoreExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_MacroInvocation_]\
> &nbsp;&nbsp; )
>
> _ExpressionWithBlock_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup>[†](#expression-attributes)\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_BlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_UnsafeBlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_LoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_IfExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_IfLetExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_MatchExpression_]\
> &nbsp;&nbsp; )
{==+==}
> **<sup>语法</sup>**\
> _表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _无块表达式_\
> &nbsp;&nbsp; | _块表达式_
>
> _无块表达式_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup>[†](#expression-attributes)\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_字面值表达式_][_LiteralExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_路径表达式_][_PathExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_操作符表达式_][_OperatorExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_分组表达式_][_GroupedExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_数组表达式_][_ArrayExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Await表达式_][_AwaitExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_索引表达式_][_IndexExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_元组表达式_][_TupleExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_元组索引表达式_][_TupleIndexingExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_结构体表达式_][_StructExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_调用表达式_][_CallExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_方法调用表达式_][_MethodCallExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_字段表达式_][_FieldExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_闭包表达式_][_ClosureExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Async块表达式_][_AsyncBlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Continue表达式_][_ContinueExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Break表达式_][_BreakExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_区间表达式_][_RangeExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Return表达式_][_ReturnExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_下划线表达式_][_UnderscoreExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_宏调用表达式_][_MacroInvocation_]\
> &nbsp;&nbsp; )
>
> _块表达式_ :\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup>[†](#expression-attributes)\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [_块表达式_][_BlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Unsafe块表达式_][_UnsafeBlockExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Loop表达式_][_LoopExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_If表达式_][_IfExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_IfLet表达式_][_IfLetExpression_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Match表达式_][_MatchExpression_]\
> &nbsp;&nbsp; )
{==+==}


{==+==}
An expression may have two roles: it always produces a *value*, and it may have *effects* (otherwise known as "side effects").
An expression *evaluates to* a value, and has effects during *evaluation*.
Many expressions contain sub-expressions, called the *operands* of the expression.
The meaning of each kind of expression dictates several things:

* Whether or not to evaluate the operands when evaluating the expression
* The order in which to evaluate the operands
* How to combine the operands' values to obtain the value of the expression

In this way, the structure of expressions dictates the structure of execution.
Blocks are just another kind of expression, so blocks, statements, expressions, and blocks again can recursively nest inside each other to an arbitrary depth.

> **Note**: We give names to the operands of expressions so that we may discuss them, but these names are not stable and may be changed.
{==+==}
表达式具有两个作用: 总是产生一个值，并可能产生其他效果 (也称为 "副作用" )。
副作用在表达式计算为一个值的期间产生。
许多表达式包含子表达式，也称为表达式的操作数。表达式的具体语义决定了以下行为:

* 在计算表达式时是否进一步计算操作数
* 计算操作数的顺序
* 如何将操作数的值组合以获得表达式的值

块仍是表达式的一种，因此块、语句、表达式和块可以递归嵌套。

> **注意**: 给表达式的操作数命名可以便于讨论，但目前这些名称未稳定，可能会更改。
{==+==}


{==+==}
## Expression precedence

The precedence of Rust operators and expressions is ordered as follows, going from strong to weak.
Binary Operators at the same precedence level are grouped in the order given by their associativity.
{==+==}
## 表达式优先级

Rust 运算符和表达式的优先级按照以下顺序进行排列，从强到弱。
在相同优先级的二元运算符中，根据其结合性得出分组顺序。
{==+==}


{==+==}
| Operator/Expression         | Associativity       |
|-----------------------------|---------------------|
| Paths                       |                     |
| Method calls                |                     |
| Field expressions           | left to right       |
| Function calls, array indexing |                  |
| `?`                         |                     |
| Unary `-` `*` `!` `&` `&mut` |                    |
| `as`                        | left to right       |
| `*` `/` `%`                 | left to right       |
| `+` `-`                     | left to right       |
| `<<` `>>`                   | left to right       |
| `&`                         | left to right       |
| `^`                         | left to right       |
| <code>&#124;</code>         | left to right       |
| `==` `!=` `<` `>` `<=` `>=` | Require parentheses |
| `&&`                        | left to right       |
| <code>&#124;&#124;</code>   | left to right       |
| `..` `..=`                  | Require parentheses |
| `=` `+=` `-=` `*=` `/=` `%=` <br> `&=` <code>&#124;=</code> `^=` `<<=` `>>=` | right to left |
| `return` `break` closures   |                     |
{==+==}
| 运算符/表达式                | 结合性                  |
|------------------------------|------------------------|
| 路径                         |                        |
| 方法调用                     |                        |
| 字段表达式                   | 从左到右                |
| 函数调用、数组索引           |                        |
| `?`                          |                        |
| 一元 `-` `*` `!` `&` `&mut`   |                        |
| `as`                         | 从左到右                |
| `*` `/` `%`                  | 从左到右                |
| `+` `-`                      | 从左到右                |
| `<<` `>>`                    | 从左到右                |
| `&`                          | 从左到右                |
| `^`                          | 从左到右                |
| <code>&#124;</code>          | 从左到右                |
| `==` `!=` `<` `>` `<=` `>=`  | 需要括号               |
| `&&`                         | 从左到右                |
| <code>&#124;&#124;</code>    | 从左到右                |
| `..` `..=`                   | 需要括号               |
| `=` `+=` `-=` `*=` `/=` `%=` <br> `&=` <code>&#124;=</code> `^=` `<<=` `>>=` | 从右到左 |
| `return` `break` 闭包         |                        |
{==+==}


{==+==}
## Evaluation order of operands

The following list of expressions all evaluate their operands the same way, as described after the list.
Other expressions either don't take operands or evaluate them conditionally as described on their respective pages.
{==+==}
## 操作数的求值顺序

以下表达式列表的所有表达式都以相同的方式对其操作数进行计算。
其他表达式要么不需要操作数，要么根据其各自的语法进行有条件的计算。
{==+==}


{==+==}
* Dereference expression
* Error propagation expression
* Negation expression
* Arithmetic and logical binary operators
* Comparison operators
* Type cast expression
* Grouped expression
* Array expression
* Await expression
* Index expression
* Tuple expression
* Tuple index expression
* Struct expression
* Call expression
* Method call expression
* Field expression
* Break expression
* Range expression
* Return expression
{==+==}
* 解引用表达式
* 错误传导表达式
* 取反表达式
* 算术和逻辑二元运算符
* 比较运算符
* 类型转换表达式
* 分组表达式
* 数组表达式
* `await` 表达式
* 索引表达式
* 元组表达式
* 元组索引表达式
* 结构体表达式
* 函数调用表达式
* 方法调用表达式
* 字段表达式
* `break` 表达式
* 范围表达式
* `return` 表达式
{==+==}


{==+==}
The operands of these expressions are evaluated prior to applying the effects of the expression.
Expressions taking multiple operands are evaluated left to right as written in the source code.

> **Note**: Which subexpressions are the operands of an expression is
> determined by expression precedence as per the previous section.
{==+==}
这些表达式的操作数在产生表达式副作用之前进行求值。
取多个操作数的表达式按照源代码中从左到右的顺序进行求值。

> **注意**: 其子表达式是否为一个表达式的操作数由前面的 "表达式优先级" 决定。
{==+==}


{==+==}
For example, the two `next` method calls will always be called in the same order:
{==+==}
例如，两个 `next` 方法调用总是按照它们在代码中出现的顺序被调用:
{==+==}


{==+==}
```rust
# // Using vec instead of array to avoid references
# // since there is no stable owned array iterator
# // at the time this example was written.
let mut one_two = vec![1, 2].into_iter();
assert_eq!(
    (1, 2),
    (one_two.next().unwrap(), one_two.next().unwrap())
);
```
{==+==}
```rust
# // 在这个例子中使用 `Vec` 而不是数组，是为了避免引用，因为在编写此示例时，数组的迭代器特性还没有稳定。
let mut one_two = vec![1, 2].into_iter();
assert_eq!(
    (1, 2),
    (one_two.next().unwrap(), one_two.next().unwrap())
);
```
{==+==}


{==+==}
> **Note**: Since this is applied recursively, these expressions are also evaluated from innermost to outermost, ignoring siblings until there are no inner subexpressions.
{==+==}
> **注意**: 由于这个规则是递归的，所以这些表达式从内到外依次求值，直到没有更深层次的子表达式。在这个过程中会暂时忽略同级的表达式。
{==+==}


{==+==}
## Place Expressions and Value Expressions

Expressions are divided into two main categories: place expressions and value expressions;
there is also a third, minor category of expressions called assignee expressions.
Within each expression, operands may likewise occur in either place context or value context.
The evaluation of an expression depends both on its own category and the context it occurs within.

A *place expression* is an expression that represents a memory location.
These expressions are [paths] which refer to local variables, [static variables], [dereferences][deref] (`*expr`), [array indexing] expressions (`expr[expr]`), [field] references (`expr.f`) and parenthesized place expressions.
All other expressions are value expressions.

A *value expression* is an expression that represents an actual value.
{==+==}
## 占位表达式和值表达式

表达式从求值的特性上可区分为: 占位表达式和值表达式，以及次要的可赋值表达式。
在表达式中，操作数可以出现在占位上下文中或值上下文中。

*占位表达式* 是表示内存地址的表达式。这些表达式以 [路径][paths] 引用局部变量、 [静态变量][static variables] 、 [解引用][deref] 、 [数组索引][array indexing] 表达式 ( `expr[expr]` ) 、 [字段][field] 引用 (`expr.f`) 和带括号的占位表达式。其他表达式都是值表达式。

*值表达式* 是表示真实值的表达式。
{==+==}


{==+==}
The following contexts are *place expression* contexts:

* The left operand of a [compound assignment] expression.
* The operand of a unary [borrow], [address-of][addr-of] or [dereference][deref] operator.
* The operand of a field expression.
* The indexed operand of an array indexing expression.
* The operand of any [implicit borrow].
* The initializer of a [let statement].
* The [scrutinee] of an [`if let`], [`match`][match], or [`while let`]
  expression.
* The base of a [functional update] struct expression.

> Note: Historically, place expressions were called *lvalues* and value expressions were called *rvalues*.
{==+==}
以下为占位表达式上下文:

* [复合赋值][compound assignment] 表达式的左操作数。
* 单目 [借用][borrow] 、 [取地址][addr-of] 或 [解引用][deref] 操作符的操作数。
* 字段表达式的操作数。
* 数组索引表达式的索引操作数。
* 任何 [隐式借用][implicit borrow] 的操作数。
* [let 语句][let statement] 的初始化器。
* [`if let`] 、 [`match`][match] 或 [`while let`] 表达式的 [被匹配项][scrutinee] 。
* [结构体函数式更新][functional update] 表达式的基础。

译注： 基础这个概念在多处被使用，类似于基类，但 rust 中没传统继承这概念，使用基类将可能引起读者混淆。

> 注意: 从历史上看，占位表达式称为左值，值表达式称为右值。
{==+==}


{==+==}
An *assignee expression* is an expression that appears in the left operand of an [assignment][assign] expression.
Explicitly, the assignee expressions are:

- Place expressions.
- [Underscores][_UnderscoreExpression_].
- [Tuples][_TupleExpression_] of assignee expressions.
- [Slices][_ArrayExpression_] of assingee expressions.
- [Tuple structs][_StructExpression_] of assignee expressions.
- [Structs][_StructExpression_] of assignee expressions (with optionally named
  fields).
- [Unit structs][_StructExpression_].

Arbitrary parenthesisation is permitted inside assignee expressions.
{==+==}
*可赋值表达式* 是指出现在 [赋值][assign] 表达式的左操作数的表达式。
确切来说，可赋值表达式可以是：

- 占位表达式
- [_下划线表达式_][_UnderscoreExpression_]。
- 包含可赋值表达式的 [_元组表达式_][_TupleExpression_]。
- 包含可赋值表达式的 [_数组表达式_][_ArrayExpression_]。
- 包含可赋值表达式的 [_元组结构体表达式_][_StructExpression_]。
- 包含可赋值表达式的 [_结构体表达式_][_StructExpression_] (可包含命名字段) 。
- [_单元结构体_][_StructExpression_]。

在可赋值表达式中允许使用任意括号。
{==+==}


{==+==}
### Moved and copied types

When a place expression is evaluated in a value expression context, or is bound by value in a pattern, it denotes the value held _in_ that memory location.
If the type of that value implements [`Copy`], then the value will be copied.
In the remaining situations, if that type is [`Sized`], then it may be possible to move the value.
Only the following place expressions may be moved out of:

* [Variables] which are not currently borrowed.
* [Temporary values](#temporaries).
* [Fields][field] of a place expression which can be moved out of and don't implement [`Drop`].
* The result of [dereferencing][deref] an expression with type [`Box<T>`] and that can also be moved out of.

After moving out of a place expression that evaluates to a local variable, the location is deinitialized and cannot be read from again until it is reinitialized.
In all other cases, trying to use a place expression in a value expression context is an error.
{==+==}
### 移动和复制类型

当在值表达式上下文中计算占位表达式，或者通过值在模式中绑定占位表达式时，它表示该内存位置所存储的值。
如果该值的类型实现了 [`Copy`] ，那么该值将被复制。
其它情况，如果该类型是 [`Sized`]，那么可能移动该值。
只有以下占位表达式可以 '移出' ：

* [变量][Variables]，当前未被借用。
* [临时值](#temporaries)。
* 未实现 [`Drop`] 的占位表达式的 [字段][field] 可以移出。
* 具有类型 [`Box<T>`] 的表达式的 [解引用][deref] 结果可以移出。

在计算时从局部变量的占位表达式中移动出后，该占位将被取消初始化，不能再次读取，直到重新初始化。
在其他情况，尝试在值表达式上下文中使用占位表达式将产生错误。
{==+==}


{==+==}
### Mutability

For a place expression to be [assigned][assign] to, mutably [borrowed][borrow], [implicitly mutably borrowed], or bound to a pattern containing `ref mut`, it must be _mutable_.
We call these *mutable place expressions*.
In contrast, other place expressions are called *immutable place expressions*.

The following expressions can be mutable place expression contexts:
{==+==}
### 可变性

要将一个占位表达式赋值、进行可变借用、进行隐式可变借用或绑定到包含 `ref mut` 的模式中时，则必须是可变的。
这被称为可变占位表达式，其他称为不可变占位表达式。

下面的表达式可以是可变占位表达式上下文:
{==+==}


{==+==}
* Mutable [variables] which are not currently borrowed.
* [Mutable `static` items].
* [Temporary values].
* [Fields][field]: this evaluates the subexpression in a mutable place expression context.
* [Dereferences][deref] of a `*mut T` pointer.
* Dereference of a variable, or field of a variable, with type `&mut T`.
  Note: This is an exception to the requirement of the next rule.
* Dereferences of a type that implements `DerefMut`:
  this then requires that the value being dereferenced is evaluated in a mutable place expression context.
* [Array indexing] of a type that implements `IndexMut`:
  this then evaluates the value being indexed, but not the index, in mutable place expression context.
{==+==}
* 当前没有被借用的可变 [变量][variables] 。
* [可变 `static` 条目][Mutable `static` items] 。
* [临时值][Temporary values] 。
* [字段][field]: 这将在可变占位表达式上下文中计算子表达式。
* `*mut T` 指针的 [解引用][deref] 。
* 类型为 `&mut T` 的变量或变量字段的解引用。注意：这是对下一条规则的例外情况。
* 实现 `DerefMut` 的类型的解引用: 这要求被解引用的值在可变占位表达式上下文中求值。
* 实现 `IndexMut` 的类型的 [数组索引][Array indexing]: 这将在可变占位表达式上下文中计算被索引的值，但不包括索引。
{==+==}


{==+==}
### Temporaries

When using a value expression in most place expression contexts, a temporary unnamed memory location is created and initialized to that value.
The expression evaluates to that location instead, except if [promoted] to a `static`.
The [drop scope] of the temporary is usually the end of the enclosing statement.
{==+==}
### 临时值

在大多数占位表达式上下文中使用值表达式时，会创建一个临时的无名内存位置，并将其初始化为该值。
表达式计算为该位置，除非它被 [提升][promoted] 为 `static` 静态的。
临时值的 [丢弃作用域][drop scope] 通常是封闭语句的末尾。
{==+==}


{==+==}
### Implicit Borrows

Certain expressions will treat an expression as a place expression by implicitly borrowing it.
For example, it is possible to compare two unsized [slices][slice] for equality directly, because the `==` operator implicitly borrows its operands:
{==+==}
### 隐式借用

某些表达式将会通过隐式借用将一个表达式作为占位表达式。
比如，可以直接比较两个非固定大小的 [切片][slice] 是否相等，因为 `==` 运算符会隐式地借用它的操作数:
{==+==}


{==+==}
```rust
# let c = [1, 2, 3];
# let d = vec![1, 2, 3];
let a: &[i32];
let b: &[i32];
# a = &c;
# b = &d;
// ...
*a == *b;
// Equivalent form:
::std::cmp::PartialEq::eq(&*a, &*b);
```
{==+==}
```rust
# let c = [1, 2, 3];
# let d = vec![1, 2, 3];
let a: &[i32];
let b: &[i32];
# a = &c;
# b = &d;
// ...
*a == *b;
// 等价形式:
::std::cmp::PartialEq::eq(&*a, &*b);
```
{==+==}


{==+==}
Implicit borrows may be taken in the following expressions:

* Left operand in [method-call] expressions.
* Left operand in [field] expressions.
* Left operand in [call expressions].
* Left operand in [array indexing] expressions.
* Operand of the [dereference operator][deref] (`*`).
* Operands of [comparison].
* Left operands of the [compound assignment].
{==+==}
隐式借用可以出现在以下表达式中:

* [方法调用][method-call] 表达式中的左操作数。
* [字段][field] 表达式中的左操作数。
* [函数调用][call expressions] 表达式中的左操作数。
* [数组索引][array indexing] 表达式中的左操作数。
* [解引用操作符][deref] (`*`) 的操作数。
* [比较][comparison] 的操作数。
* [复合赋值][compound assignment] 的左操作数。
{==+==}


{==+==}
## Overloading Traits

Many of the following operators and expressions can also be overloaded for other types using traits in `std::ops` or `std::cmp`.
These traits also exist in `core::ops` and `core::cmp` with the same names.
{==+==}
## trait 重载

许多以下的运算符和表达式可以使用 `std::ops` 或 `std::cmp` 中的 trait 重载为其他类型可用。
这些 trait 在 `core::ops` 和 `core::cmp` 中也有相同的名称。
{==+==}


{==+==}
## Expression Attributes

[Outer attributes][_OuterAttribute_] before an expression are allowed only in a few specific cases:

* Before an expression used as a [statement].
* Elements of [array expressions], [tuple expressions], [call expressions], and tuple-style [struct] expressions.
* The tail expression of [block expressions].
<!-- Keep list in sync with block-expr.md -->

They are never allowed before:
* [Range][_RangeExpression_] expressions.
* Binary operator expressions ([_ArithmeticOrLogicalExpression_], [_ComparisonExpression_], [_LazyBooleanExpression_], [_TypeCastExpression_], [_AssignmentExpression_], [_CompoundAssignmentExpression_]).
{==+==}
## 表达式属性

在表达式前面的 [_外围属性_][_OuterAttribute_] 只允许在以下几种情况下使用：

* 用作 [语句][statement] 的表达式之前。
* [数组表达式][array expressions]，[元组表达式][tuple expressions]，[调用表达式][call expressions]和元组风格的 [结构体][struct] 表达式的元素。
* [块表达式][block expressions] 的尾部表达式。
<!-- Keep list in sync with block-expr.md -->

不允许在以下情况下使用:
* [区间][_RangeExpression_] 表达式。
* 二元运算符表达式 ([_算术或逻辑表达式_][_ArithmeticOrLogicalExpression_]，[_比较表达式_][_ComparisonExpression_]，[_懒惰布尔表达式_][_LazyBooleanExpression_]，[_类型转换表达式_][_TypeCastExpression_]，[_赋值表达式_][_AssignmentExpression_]，[_复合赋值表达式_][_CompoundAssignmentExpression_]) 。
{==+==}


{==+==}
[block expressions]:    expressions/block-expr.md
[call expressions]:     expressions/call-expr.md
[field]:                expressions/field-expr.md
[functional update]:    expressions/struct-expr.md#functional-update-syntax
[`if let`]:             expressions/if-expr.md#if-let-expressions
[match]:                expressions/match-expr.md
[method-call]:          expressions/method-call-expr.md
[paths]:                expressions/path-expr.md
[struct]:               expressions/struct-expr.md
[tuple expressions]:    expressions/tuple-expr.md
[`while let`]:          expressions/loop-expr.md#predicate-pattern-loops

[array expressions]:    expressions/array-expr.md
[array indexing]:       expressions/array-expr.md#array-and-slice-indexing-expressions

[assign]:               expressions/operator-expr.md#assignment-expressions
[borrow]:               expressions/operator-expr.md#borrow-operators
[addr-of]:              expressions/operator-expr.md#raw-address-of-operators
[comparison]:           expressions/operator-expr.md#comparison-operators
[compound assignment]:  expressions/operator-expr.md#compound-assignment-expressions
[deref]:                expressions/operator-expr.md#the-dereference-operator

[destructors]:          destructors.md
[drop scope]:           destructors.md#drop-scopes
{==+==}

{==+==}


{==+==}
[`Box<T>`]:             ../std/boxed/struct.Box.html
[`Copy`]:               special-types-and-traits.md#copy
[`Drop`]:               special-types-and-traits.md#drop
[`Sized`]:              special-types-and-traits.md#sized
[implicit borrow]:      #implicit-borrows
[implicitly mutably borrowed]: #implicit-borrows
[interior mutability]:  interior-mutability.md
[let statement]:        statements.md#let-statements
[Mutable `static` items]: items/static-items.md#mutable-statics
[scrutinee]:            glossary.md#scrutinee
[promoted]:             destructors.md#constant-promotion
[slice]:                types/slice.md
[statement]:            statements.md
[static variables]:     items/static-items.md
[Temporary values]:     #temporaries
[Variables]:            variables.md
{==+==}

{==+==}


{==+==}
[_ArithmeticOrLogicalExpression_]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[_ArrayExpression_]:              expressions/array-expr.md
[_AsyncBlockExpression_]:         expressions/block-expr.md#async-blocks
[_AwaitExpression_]:              expressions/await-expr.md
[_AssignmentExpression_]:         expressions/operator-expr.md#assignment-expressions
[_BlockExpression_]:              expressions/block-expr.md
[_BreakExpression_]:              expressions/loop-expr.md#break-expressions
[_CallExpression_]:               expressions/call-expr.md
[_ClosureExpression_]:            expressions/closure-expr.md
[_ComparisonExpression_]:         expressions/operator-expr.md#comparison-operators
[_CompoundAssignmentExpression_]: expressions/operator-expr.md#compound-assignment-expressions
[_ContinueExpression_]:           expressions/loop-expr.md#continue-expressions
[_FieldExpression_]:              expressions/field-expr.md
[_GroupedExpression_]:            expressions/grouped-expr.md
[_IfExpression_]:                 expressions/if-expr.md#if-expressions
[_IfLetExpression_]:              expressions/if-expr.md#if-let-expressions
[_IndexExpression_]:              expressions/array-expr.md#array-and-slice-indexing-expressions
[_LazyBooleanExpression_]:        expressions/operator-expr.md#lazy-boolean-operators
[_LiteralExpression_]:            expressions/literal-expr.md
[_LoopExpression_]:               expressions/loop-expr.md
[_MacroInvocation_]:              macros.md#macro-invocation
[_MatchExpression_]:              expressions/match-expr.md
[_MethodCallExpression_]:         expressions/method-call-expr.md
[_OperatorExpression_]:           expressions/operator-expr.md
[_OuterAttribute_]:               attributes.md
[_PathExpression_]:               expressions/path-expr.md
[_RangeExpression_]:              expressions/range-expr.md
[_ReturnExpression_]:             expressions/return-expr.md
[_StructExpression_]:             expressions/struct-expr.md
[_TupleExpression_]:              expressions/tuple-expr.md
[_TupleIndexingExpression_]:      expressions/tuple-expr.md#tuple-indexing-expressions
[_TypeCastExpression_]:           expressions/operator-expr.md#type-cast-expressions
[_UnderscoreExpression_]:         expressions/underscore-expr.md
[_UnsafeBlockExpression_]:        expressions/block-expr.md#unsafe-blocks
{==+==}

{==+==}