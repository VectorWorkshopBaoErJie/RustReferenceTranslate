{==+==}
# Statements and expressions

Rust is _primarily_ an expression language.
This means that most forms of value-producing or effect-causing evaluation are directed by the uniform syntax category of _expressions_.
Each kind of expression can typically _nest_ within each other kind of expression, and rules for evaluation of expressions involve specifying both the value produced by the expression and the order in which its sub-expressions are themselves evaluated.

In contrast, statements serve _mostly_ to contain and explicitly sequence expression evaluation.
{==+==}
# 语句和表达式

Rust _主要_ 是一种表达式语言。这意味着大多数产生值或引起副作用的计算都由统一的语法类别——表达式指导。
每种表达式通常可以嵌套在其他表达式中，表达式的计算规则涉及指定表达式产生的值以及其子表达式本身的计算顺序。

相反，语句主要用于包含和显式地序列化表达式计算。
{==+==}