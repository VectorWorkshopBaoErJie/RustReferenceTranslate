{==+==}
# Whitespace
{==+==}
# 空白字符
{==+==}


{==+==}
Whitespace is any non-empty string containing only characters that have the
[`Pattern_White_Space`] Unicode property, namely:
{==+==}
空白字符是指非空字符串中包含的 [`Pattern_White_Space`] Unicode 性质的字符，即:
{==+==}


{==+==}
- `U+0009` (horizontal tab, `'\t'`)
- `U+000A` (line feed, `'\n'`)
- `U+000B` (vertical tab)
- `U+000C` (form feed)
- `U+000D` (carriage return, `'\r'`)
- `U+0020` (space, `' '`)
- `U+0085` (next line)
- `U+200E` (left-to-right mark)
- `U+200F` (right-to-left mark)
- `U+2028` (line separator)
- `U+2029` (paragraph separator)
{==+==}
- `U+0009` (水平制表符, `'\t'`)
- `U+000A` (换行符, `'\n'`)
- `U+000B` (垂直制表符)
- `U+000C` (换页符)
- `U+000D` (回车符, `'\r'`)
- `U+0020` (空格, `' '`)
- `U+0085` (下一行)
- `U+200E` (从左到右标记)
- `U+200F` (从右到左标记)
- `U+2028` (行分割符)
- `U+2029` (段落分隔符)
{==+==}


{==+==}
Rust is a "free-form" language, meaning that all forms of whitespace serve only
to separate _tokens_ in the grammar, and have no semantic significance.
{==+==}
Rust 是 "形式自由" 的语言，所有形式的空白字符仅仅用于分隔语法中的 _Token_ ，空白自身没有语义。
{==+==}


{==+==}
A Rust program has identical meaning if each whitespace element is replaced
with any other legal whitespace element, such as a single space character.
{==+==}
空白元素可以替换成任何其他合规的空白元素， Rust 编译器将认为具有相同的意义。
{==+==}


{==+==}
[`Pattern_White_Space`]: https://www.unicode.org/reports/tr31/
{==+==}

{==+==}