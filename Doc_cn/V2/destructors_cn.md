{==+==}
If multiple patterns are used in the same arm for a `match` expression, then an
unspecified pattern will be used to determine the drop order.

### Temporary scopes

The *temporary scope* of an expression is the scope that is used for the
temporary variable that holds the result of that expression when used in a
[place context], unless it is [promoted].

Apart from lifetime extension, the temporary scope of an expression is the
smallest scope that contains the expression and is one of the following:

* The entire function body.
* A statement.
* The body of an [`if`], [`while`] or [`loop`] expression.
* The `else` block of an `if` expression.
* The condition expression of an `if` or `while` expression, or a `match`
  guard.
* The body expression for a match arm.
* The second operand of a [lazy boolean expression].
{==+==}
如果在 `match` 表达式的同一分支中使用了多个模式，则使用未指定的模式来确定丢弃的顺序。

### 临时变量的作用域

一个表达式的 *临时作用域* 是在 [占位上下文][place context] 中使用该表达式时用于保存该表达式结果的临时变量的作用域，除非该表达式被 [提升][promoted]。

除了生命周期扩展之外，表达式的临时作用域是包含表达式的最小作用域，可以是以下之一:

* 整个函数体。
* 一个语句。
* [`if`]、[`while`] 或 [`loop`] 表达式的主体。
* `if` 表达式的 `else` 块。
* `if` 或 `while` 表达式的条件表达式，或 `match` 守卫。
* `match` 分支的主体表达式。
* 惰性布尔表达式的第二个操作数。
{==+==}
