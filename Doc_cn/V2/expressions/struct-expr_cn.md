{==+==}
Struct expressions with curly braces can't be used directly in a [loop] or [if] expression's head, or in the [scrutinee] of an [if let] or [match] expression.
However, struct expressions can be used in these situations if they are within another expression, for example inside [parentheses].

The field names can be decimal integer values to specify indices for constructing tuple structs.
This can be used with base structs to fill out the remaining indices not specified:
{==+==}
带大括号的结构体表达式不能直接用于 [loop] 或 [if] 表达式头部，也不能用于 [if let] 或 [match] 表达式的 [被匹配项][scrutinee]。
但是，如果结构体表达式在另一个表达式中，例如在 [括号][parentheses] 中，它们就可以在这些情况下使用。

字段名称可以是十进制整数值，用于指定用于构造元组结构体的索引。这可以与基础结构体一起使用，以填充未指定的其余索引:
{==+==}
