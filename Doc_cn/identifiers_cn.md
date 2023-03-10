{==+==}
# Identifiers
{==+==}
# 标识符
{==+==}


{==+==}
> **<sup>Lexer:<sup>**\
> IDENTIFIER_OR_KEYWORD :\
> &nbsp;&nbsp; &nbsp;&nbsp; XID_Start XID_Continue<sup>\*</sup>\
> &nbsp;&nbsp; | `_` XID_Continue<sup>+</sup>
>
> RAW_IDENTIFIER : `r#` IDENTIFIER_OR_KEYWORD <sub>*Except `crate`, `self`, `super`, `Self`*</sub>
>
> NON_KEYWORD_IDENTIFIER : IDENTIFIER_OR_KEYWORD <sub>*Except a [strict] or [reserved] keyword*</sub>
>
> IDENTIFIER :\
> NON_KEYWORD_IDENTIFIER | RAW_IDENTIFIER
{==+==}

{==+==}


{==+==}
<!-- When updating the version, update the UAX links, too. -->
Identifiers follow the specification in [Unicode Standard Annex #31][UAX31] for Unicode version 15.0, with the additions described below. Some examples of identifiers:
{==+==}
<!-- 更新版本时，也要更新UAX链接。 -->
标识符遵循 [Unicode 标准附录 #31][UAX31] 的规范，使用的是 Unicode 15.0 版本，此外还有下面的增强规则。以下是一些标识符的例子：
{==+==}


{==+==}
* `foo`
* `_identifier`
* `r#true`
* `Москва`
* `東京`
{==+==}

{==+==}


{==+==}
The profile used from UAX #31 is:
{==+==}
UAX #31 使用的编译设置是:
{==+==}


{==+==}
* Start := [`XID_Start`], plus the underscore character (U+005F)
* Continue := [`XID_Continue`]
* Medial := empty
{==+==}
* Start := [`XID_Start`], 加下划线字符 (U+005F)
* Continue := [`XID_Continue`]
* Medial := empty
{==+==}


{==+==}
with the additional constraint that a single underscore character is not an identifier.
{==+==}
附加约束条件是，单个下划线字符不是标识符。
{==+==}


{==+==}
> **Note**: Identifiers starting with an underscore are typically used to indicate an identifier that is intentionally unused, and will silence the unused warning in `rustc`.
{==+==}
> **注意**: 以下划线开头的标识符通常用于指示故意未使用的标识符，并将消除 `rustc` 中的未使用警告。
{==+==}


{==+==}
Identifiers may not be a [strict] or [reserved] keyword without the `r#` prefix described below in [raw identifiers](#raw-identifiers).
{==+==}
标识符不能是 [strict] "严格"或 [reserved] "保留" 的关键词，可以是下面 `r#` 前缀的 [raw identifiers](#raw-identifiers) "原始标识符"。
{==+==}


{==+==}
Zero width non-joiner (ZWNJ U+200C) and zero width joiner (ZWJ U+200D) characters are not allowed in identifiers.
{==+==}
零宽度不连字符 (ZWNJ U+200C) 和零宽度连字符 (ZWJ U+200D) 字符不能出现在标识符中。
{==+==}


{==+==}
Identifiers are restricted to the ASCII subset of [`XID_Start`] and [`XID_Continue`] in the following situations:
{==+==}
在以下情况下，标识符受到 ASCII 子集的 [XID_Start] 和 [XID_Continue] 的限制：
{==+==}


{==+==}
* [`extern crate`] declarations
* External crate names referenced in a [path]
* [Module] names loaded from the filesystem without a [`path` attribute]
* [`no_mangle`] attributed items
* Item names in [external blocks]
{==+==}
* [`extern crate`] 声明
* 在 [path] 中引用的外部 crate 的名称
* 从文件系统加载的没有 [`path` attribute] 的 [Module] 名称 。
* [`no_mangle`] 所属条目
* [external blocks] 中条目的名称
{==+==}


{==+==}
## Normalization
{==+==}
## 规范化
{==+==}


{==+==}
Identifiers are normalized using Normalization Form C (NFC) as defined in [Unicode Standard Annex #15][UAX15]. Two identifiers are equal if their NFC forms are equal.
{==+==}
标识符使用 [Unicode Standard Annex #15][UAX15] 中定义的规范化形式C(NFC)进行规范化。如果两个标识符的NFC形式相同，那么它们就是相等的。
{==+==}


{==+==}
[Procedural][proc-macro] and [declarative][mbe] macros receive normalized identifiers in their input.
{==+==}
[Procedural][proc-macro] 和 [declarative][mbe] 宏在其输入中接收规范化的标识符。
{==+==}


{==+==}
## Raw identifiers
{==+==}
## 原始标识符
{==+==}


{==+==}
A raw identifier is like a normal identifier, but prefixed by `r#`. (Note that
the `r#` prefix is not included as part of the actual identifier.)
Unlike a normal identifier, a raw identifier may be any strict or reserved
keyword except the ones listed above for `RAW_IDENTIFIER`.
{==+==}
原始标识符类似于普通标识符，但是前缀为 `r#`。
(注意，实际标识符不包括 `r#` 前缀。)
与普通标识符不同，原始标识符可以是任何严格字或保留字，除了列在 `RAW_IDENTIFIER` 上的那些关键字。
{==+==}


{==+==}
[`extern crate`]: items/extern-crates.md
[`no_mangle`]: abi.md#the-no_mangle-attribute
[`path` attribute]: items/modules.md#the-path-attribute
[`XID_Continue`]: http://unicode.org/cldr/utility/list-unicodeset.jsp?a=%5B%3AXID_Continue%3A%5D&abb=on&g=&i=
[`XID_Start`]:  http://unicode.org/cldr/utility/list-unicodeset.jsp?a=%5B%3AXID_Start%3A%5D&abb=on&g=&i=
[external blocks]: items/external-blocks.md
[mbe]: macros-by-example.md
[module]: items/modules.md
[path]: paths.md
[proc-macro]: procedural-macros.md
[reserved]: keywords.md#reserved-keywords
[strict]: keywords.md#strict-keywords
[UAX15]: https://www.unicode.org/reports/tr15/tr15-53.html
[UAX31]: https://www.unicode.org/reports/tr31/tr31-37.html
{==+==}

{==+==}