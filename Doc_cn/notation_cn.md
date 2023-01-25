{==+==}
# Notation
{==+==}
# 符号
{==+==}


{==+==}
## Grammar
{==+==}
## 语法
{==+==}


{==+==}
The following notations are used by the *Lexer* and *Syntax* grammar snippets:
{==+==}
以下是 *Lexer* "词法分析" 和 *Syntax* "语法分析" 片段使用的符号。
{==+==}


{==+==}
| Notation          | Examples                      | Meaning                                   |
|-------------------|-------------------------------|-------------------------------------------|
| CAPITAL           | KW_IF, INTEGER_LITERAL        | A token produced by the lexer             |
| _ItalicCamelCase_ | _LetStatement_, _Item_        | A syntactical production                  |
| `string`          | `x`, `while`, `*`             | The exact character(s)                    |
| \\x               | \\n, \\r, \\t, \\0            | The character represented by this escape  |
| x<sup>?</sup>     | `pub`<sup>?</sup>             | An optional item                          |
| x<sup>\*</sup>    | _OuterAttribute_<sup>\*</sup> | 0 or more of x                            |
| x<sup>+</sup>     |  _MacroMatch_<sup>+</sup>     | 1 or more of x                            |
| x<sup>a..b</sup>  | HEX_DIGIT<sup>1..6</sup>      | a to b repetitions of x                   |
| \|                | `u8` \| `u16`, Block \| Item  | Either one or another                     |
| \[ ]               | \[`b` `B`]                     | Any of the characters listed              |
| \[ - ]             | \[`a`-`z`]                     | Any of the characters in the range        |
| ~\[ ]              | ~\[`b` `B`]                    | Any characters, except those listed       |
| ~`string`         | ~`\n`, ~`*/`                  | Any characters, except this sequence      |
| ( )               | (`,` _Parameter_)<sup>?</sup> | Groups items                              |
{==+==}

{==+==}


{==+==}
## String table productions
{==+==}
## 字符串表项
{==+==}


{==+==}
Some rules in the grammar &mdash; notably [unary operators], [binary
operators], and [keywords] &mdash; are given in a simplified form: as a listing
of printable strings. These cases form a subset of the rules regarding the
[token][tokens] rule, and are assumed to be the result of a lexical-analysis
phase feeding the parser, driven by a <abbr title="Deterministic Finite
Automaton">DFA</abbr>, operating over the disjunction of all such string table
entries.
{==+==}
语法中的一些规则 &mdash ; 特别是 [unary operators] "单目运算符"、 [binary operators] "二目运算法" 和 [keywords] "关键字" &mdash;
是提供的简化的形式：是可打印字符串的列表。
这些情况构成了相关 [token][tokens] 规则的一个子集,
被认为是词法分析阶段向解析器提供的结果,
由 <abbr title="Deterministic Finite Automaton">DFA</abbr> 驱动,
对所有此类字符串表项进行解析操作。
{==+==}


{==+==}
When such a string in `monospace` font occurs inside the grammar,
it is an implicit reference to a single member of such a string table
production. See [tokens] for more information.
{==+==}
当这种 `monospace` "等宽"字体的字符串在语法中出现时，其是对这种字符串表项的单个成员的隐式引用。更多信息见 [tokens] 。
{==+==}


{==+==}
[binary operators]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[keywords]: keywords.md
[tokens]: tokens.md
[unary operators]: expressions/operator-expr.md#borrow-operators
{==+==}

{==+==}