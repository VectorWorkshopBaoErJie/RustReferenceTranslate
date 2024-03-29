{==+==}
# Statements and expressions

Rust is _primarily_ an expression language.
This means that most forms of value-producing or effect-causing evaluation are directed by the uniform syntax category of _expressions_.
Each kind of expression can typically _nest_ within each other kind of expression, and rules for evaluation of expressions involve specifying both the value produced by the expression and the order in which its sub-expressions are themselves evaluated.

In contrast, statements serve _mostly_ to contain and explicitly sequence expression evaluation.
{==+==}
# 语句和表达式

Rust 语言的 _主要_ 语法是表达式。大多数产生值或引起副作用的计算都由表达式完成。
表达式通常可以嵌套其他表达式，按各自的规则顺序进行计算。

语句则主要是显式地序列化了表达式计算的顺序。
{==+==}