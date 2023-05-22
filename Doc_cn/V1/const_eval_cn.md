{==+==}
# Constant evaluation

Constant evaluation is the process of computing the result of
[expressions] during compilation. Only a subset of all expressions
can be evaluated at compile-time.
{==+==}
# 常量求值

常量求值是在编译期间计算 [表达式][expressions] 结果的过程。
其条件是所有表达式的子集能够在编译时进行求值。
{==+==}


{==+==}
## Constant expressions

Certain forms of expressions, called constant expressions, can be evaluated at
compile time. In [const contexts](#const-context), these are the only allowed
expressions, and are always evaluated at compile time. In other places, such as
[let statements], constant expressions *may*
be, but are not guaranteed to be, evaluated at compile time. Behaviors such as
out of bounds [array indexing] or [overflow] are compiler errors if the value
must be evaluated at compile time (i.e. in const contexts). Otherwise, these
behaviors are warnings, but will likely panic at run-time.

The following expressions are constant expressions, so long as any operands are
also constant expressions and do not cause any [`Drop::drop`][destructors] calls
to be run.
{==+==}
## 常量表达式

常量表达式可以在编译时求值。在 [常量上下文](#const-context) 中，唯一允许的表达式，并且始终在编译时求值。
在其他位置，如 [let 语句][let statements] 中，常量表达式可能会被求值，但不能保证在编译时求值。
如果必须在编译时求值 (即在常量上下文中) ，则越界的 [数组索引][array indexing] 或 [溢出][overflow] 等行为会导致编译器错误。
否则，这些行为会导致警告，但可能会在运行时崩溃。

以下表达式是常量表达式，只要任何操作数也是常量表达式。并且不会导致 [`Drop::drop`][destructors] 调用。
{==+==}


{==+==}
* [Literals].
* [Const parameters].
* [Paths] to [functions] and [constants].
  Recursively defining constants is not allowed.
* Paths to [statics]. These are only allowed within the initializer of a static.
* [Tuple expressions].
* [Array expressions].
* [Struct] expressions.
* [Block expressions], including `unsafe` blocks.
    * [let statements] and thus irrefutable [patterns], including mutable bindings
    * [assignment expressions]
    * [compound assignment expressions]
    * [expression statements]
* [Field] expressions.
* Index expressions, [array indexing] or [slice] with a `usize`.
* [Range expressions].
* [Closure expressions] which don't capture variables from the environment.
* Built-in [negation], [arithmetic], [logical], [comparison] or [lazy boolean]
  operators used on integer and floating point types, `bool`, and `char`.
* Shared [borrow]s, except if applied to a type with [interior mutability].
* The [dereference operator] except for raw pointers.
* [Grouped] expressions.
* [Cast] expressions, except
  * pointer to address casts and
  * function pointer to address casts.
* Calls of [const functions] and const methods.
* [loop], [while] and [`while let`] expressions.
* [if], [`if let`] and [match] expressions.
{==+==}
* [字面值][Literals]。
* [常量参数][Const parameters]。
* [函数][functions] 和 [常量][constants] 的 [路径][Paths] 。不允许递归定义常量。
* [静态变量][statics] 的路径。这些只允许在静态变量的初始化器中使用。
* [元组表达式][Tuple expressions] 。
* [数组表达式][Array expressions] 。
* [结构体][Struct] 表达式。
* [块表达式][Block expressions] ，包括 `unsafe` 块。
    * [let 语句][let statements] 和因此不可拒绝的 [模式][patterns] ，包括可变绑定。
    * [赋值表达式][assignment expressions]
    * [复合赋值表达式][compound assignment expressions]
    * [表达式语句][expression statements]
* [字段][Field] 表达式。
* 索引表达式，使用 `usize` 的 [数组索引][array indexing] 或 [切片][slice] 。
* [区间表达式][Range expressions] 。
* 不捕获环境变量的 [闭包表达式][Closure expressions] 。
* 在整数和浮点数类型、 `bool` 和 `char` 上使用的内置 [否定][negation] 、 [算术][arithmetic] 、 [逻辑][logical] 、 [比较][comparison] 或 [惰性布尔][lazy boolean] 运算符。
* 共享 [借用][borrow] ，除非应用于具有 [内部可变性][interior mutability] 的类型。
* [解引用运算符][dereference operator] ，除了原始指针。
* [分组][Grouped] 表达式。
* [转换][Cast] 表达式，除了
  * 指针到地址转换和
  * 函数指针到地址转换。
* [const函数][const functions] 和 const 方法的调用。
* [loop] 、 [while] 和 [`while let`] 表达式。
* [if] 、 [`if let`] 和 [match] 表达式。
{==+==}


{==+==}
## Const context

A _const context_ is one of the following:

* [Array type length expressions]
* [Array repeat length expressions][array expressions]
* The initializer of
  * [constants]
  * [statics]
  * [enum discriminants]
* A [const generic argument]
{==+==}
## 常量上下文

_常量上下文_ 是以下情况之一:

* [数组类型长度表达式][Array type length expressions]
* [数组重复长度表达式][array expressions]
* [常量][constants] 、 [静态变量][statics] 或 [枚举判别值][enum discriminants] 的初始化器
* [常量泛型参数][const generic argument]
{==+==}


{==+==}
## Const Functions

A _const fn_ is a function that one is permitted to call from a const context. Declaring a function
`const` has no effect on any existing uses, it only restricts the types that arguments and the
return type may use, as well as prevent various expressions from being used within it. You can freely
do anything with a const function that you can do with a regular function.

When called from a const context, the function is interpreted by the
compiler at compile time. The interpretation happens in the
environment of the compilation target and not the host. So `usize` is
`32` bits if you are compiling against a `32` bit system, irrelevant
of whether you are building on a `64` bit or a `32` bit system.

Const functions have various restrictions to make sure that they can be
evaluated at compile-time. It is, for example, not possible to write a random
number generator as a const function. Calling a const function at compile-time
will always yield the same result as calling it at runtime, even when called
multiple times. There's one exception to this rule: if you are doing complex
floating point operations in extreme situations, then you might get (very
slightly) different results. It is advisable to not make array lengths and enum
discriminants depend on floating point computations.
{==+==}
## 常量函数

常量函数是指允许在常量上下文中调用的函数。
将函数声明为 `const` 不会影响函数现有的用途，只限制了参数和返回类型可以使用的类型和表达式。
可以像普通函数一样自由地使用常量函数进行相同的操作。

在常量上下文中调用函数时，编译器会在编译时解释该函数。解释环境为编译目标，而不是主机环境。
因此，如果你正在针对 32 位系统进行编译，则无论是在 64 位系统还是 32 位系统上构建， `usize` 都是 32 位。

常量函数有一些限制，以确保可以在编译时求值。例如，不可能将随机数生成器编写为常量函数。
在编译时调用常量函数将始终产生与在运行时调用它相同的结果，即使调用多次也是如此。
有一个例外：如果在极端情况下进行复杂的浮点数运算，则可能会得到(极微小的)不同结果。
建议不要使数组长度和枚举判别值依赖于浮点数计算。
{==+==}


{==+==}
Notable features that are allowed in const contexts but not in const functions include:

* floating point operations
  * floating point values are treated just like generic parameters without trait bounds beyond
  `Copy`. So you cannot do anything with them but copy/move them around.

Conversely, the following are possible in a const function, but not in a const context:

* Use of generic type and lifetime parameters.
  * Const contexts do allow limited use of [const generic parameters].
{==+==}
允许在常量上下文中使用但不允许在常量函数中使用的显著特性包括：

* 浮点运算
  * 浮点值与除了 `Copy` 之外的 trait 约束的泛型参数一样处理。因此，你无法对它们进行任何操作，只能复制/移动它们。

在常量函数中可以使用以下内容，但在常量上下文中不行：

* 使用泛型类型和生命周期参数。
  * 常量上下文允许有所限制的使用 [常量泛型参数][const generic parameters] 。
{==+==}


{==+==}
[arithmetic]:           expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[array expressions]:    expressions/array-expr.md
[array indexing]:       expressions/array-expr.md#array-and-slice-indexing-expressions
[array indexing]:       expressions/array-expr.md#array-and-slice-indexing-expressions
[array type length expressions]: types/array.md
[assignment expressions]: expressions/operator-expr.md#assignment-expressions
[compound assignment expressions]: expressions/operator-expr.md#compound-assignment-expressions
[block expressions]:    expressions/block-expr.md
[borrow]:               expressions/operator-expr.md#borrow-operators
[cast]:                 expressions/operator-expr.md#type-cast-expressions
[closure expressions]:  expressions/closure-expr.md
[comparison]:           expressions/operator-expr.md#comparison-operators
[const functions]:      items/functions.md#const-functions
[const generic argument]: items/generics.md#const-generics
[const generic parameters]: items/generics.md#const-generics
[constants]:            items/constant-items.md
[Const parameters]:     items/generics.md
[dereference operator]: expressions/operator-expr.md#the-dereference-operator
[destructors]:          destructors.md
[enum discriminants]:   items/enumerations.md#discriminants
[expression statements]: statements.md#expression-statements
[expressions]:          expressions.md
[field]:                expressions/field-expr.md
[functions]:            items/functions.md
[grouped]:              expressions/grouped-expr.md
[interior mutability]:  interior-mutability.md
[if]:                   expressions/if-expr.md#if-expressions
[`if let`]:             expressions/if-expr.md#if-let-expressions
[lazy boolean]:         expressions/operator-expr.md#lazy-boolean-operators
[let statements]:       statements.md#let-statements
[literals]:             expressions/literal-expr.md
[logical]:              expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[loop]:                 expressions/loop-expr.md#infinite-loops
[match]:                expressions/match-expr.md
[negation]:             expressions/operator-expr.md#negation-operators
[overflow]:             expressions/operator-expr.md#overflow
[paths]:                expressions/path-expr.md
[patterns]:             patterns.md
[range expressions]:    expressions/range-expr.md
[slice]:                types/slice.md
[statics]:              items/static-items.md
[struct]:               expressions/struct-expr.md
[tuple expressions]:    expressions/tuple-expr.md
[while]:                expressions/loop-expr.md#predicate-loops
[`while let`]:          expressions/loop-expr.md#predicate-pattern-loops
{==+==}

{==+==}