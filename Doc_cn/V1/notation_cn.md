{==+==}
# Notation
{==+==}
# 符号约定
{==+==}


{==+==}
## Grammar
{==+==}
## 语法
{==+==}


{==+==}
The following notations are used by the *Lexer* and *Syntax* grammar snippets:
{==+==}
以下符号约定在 "词法" 和 "语法" 的代码表示段中使用：
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
| 符号约定           | 示例                               | 意义                                     |
|-------------------|------------------------------------|------------------------------------------|
| 大写字母           | KW_IF，INTEGER_LITERAL            | 表示词法分析器所生成的 token                |
| _驼峰斜体_        | _LetStatement_，_Item_             | 表示语法产生式                              |
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
## 表中 `string` 产生式
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
在语法中，一些规则，尤其是 [一元运算符][unary operators] 、 [二元运算符][binary operators] 和 [关键字][keywords] ，用简化表示：以易阅读的字符串清单的形式。
这些情况形成了有关 [token][tokens] 规则的子集，假定它们是由一个驱动着词法分析阶段的确定性有限自动机<abbr title="Deterministic Finite Automaton">DFA</abbr>操作生成的，则在所有此类表中字符串词条的分离分支上进行操作。
{==+==}


{==+==}
When such a string in `monospace` font occurs inside the grammar,
it is an implicit reference to a single member of such a string table
production. See [tokens] for more information.
{==+==}
当在语法中看到用 `monospace` 等宽字体显示的字符串时，它隐式地指代表中 `string` 产生式的一个成员。有关更多信息，请参见 [令牌][tokens] 。
{==+==}


{==+==}
[binary operators]: expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[keywords]: keywords.md
[tokens]: tokens.md
[unary operators]: expressions/operator-expr.md#borrow-operators
{==+==}

{==+==}