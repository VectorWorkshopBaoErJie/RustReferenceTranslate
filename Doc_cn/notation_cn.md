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
以下符号在 "词法" 和 "语法" 的代码表示段中使用：
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
| 符号              | 示例                               | 意义                                     |
|-------------------|------------------------------------|------------------------------------------|
| 大写字母           | KW_IF，INTEGER_LITERAL            | 表示词法分析器所生成的标记                |
| _驼峰斜体_        | _LetStatement_，_Item_             | 表示语法标记                              |
| `string`          | `x`，`while`，`*`                  | 表示精确的字符                            |
| \\x               | \\n，\\r，\\t，\\0                 | 表示此转义字符所代表的字符                |
| x<sup>?</sup>     | `pub`<sup>?</sup>                  | 表示可选项                                |
| x<sup>\*</sup>    | _外部属性_<sup>\*</sup>      | 表示 0 个或多个 x                          |
| x<sup>+</sup>     | _宏匹配_<sup>+</sup>           | 表示 1 个或多个 x                          |
| x<sup>a..b</sup>  | 十六进制数<sup>1..6</sup>           | 表示 x 重复 a 到 b 次                      |
| \|                | `u8` \| `u16`，块 \| 条目        | 两个选项中的一个                          |
| \[ ]              | \[`b` `B`]                         | 列举出中括号内的任意一个字符              |
| \[ - ]            | \[`a`-`z`]                         | 列举出指定范围内的任意一个字符            |
| ~\[ ]             | ~\[`b` `B`]                        | 列举除中括号内的任意一个字符之外的所有字符 |
| ~`string`         | ~`\n`，~`*/`                       | 列举除指定序列之外的所有字符              |
| ( )               | (`,` _参数_)<sup>?</sup>      | 分组                                      |

译注：中文翻译无法表达驼峰形式，仅用斜体表示。无法表达大小写形式。如有必要可查看翻译仓库中的条目。
{==+==}


{==+==}
## String table productions
{==+==}
## `string` 标记
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
语法中的一些规则，特别是 [一元运算符][unary operators] 、 [二元运算符][binary operators] 和 [关键字][keywords] ，采用了简化形式，以可打印的字符串列表的形式给出。
这些情况构成了与 [token][tokens] 令牌规则相关的规则子集，假定其编译驱动为确定性有限自动机 <abbr title="Deterministic Finite Automaton">DFA</abbr>
运行的语法分析器的词法分析阶段的结果，那么，该 DFA 操作于所有这些 `string` 表的分支上。
{==+==}


{==+==}
When such a string in `monospace` font occurs inside the grammar,
it is an implicit reference to a single member of such a string table
production. See [tokens] for more information.
{==+==}
当在语法中看到用 `monospace` 等宽字体显示的字符串时，它隐式地引用了该 `string` 表标记的一个成员。有关更多信息，请参见 [令牌][tokens] 。
{==+==}


{==+==}
[binary operators]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[keywords]: keywords.md
[tokens]: tokens.md
[unary operators]: expressions/operator-expr.md#borrow-operators
{==+==}

{==+==}