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

{==+==}
