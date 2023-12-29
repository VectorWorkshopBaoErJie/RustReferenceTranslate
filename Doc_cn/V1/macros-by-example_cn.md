{==+==}
# Macros By Example
{==+==}
# 实例宏
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MacroRulesDefinition_ :\
> &nbsp;&nbsp; `macro_rules` `!` [IDENTIFIER] _MacroRulesDef_
>
> _MacroRulesDef_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _MacroRules_ `)` `;`\
> &nbsp;&nbsp; | `[` _MacroRules_ `]` `;`\
> &nbsp;&nbsp; | `{` _MacroRules_ `}`
>
> _MacroRules_ :\
> &nbsp;&nbsp; _MacroRule_ ( `;` _MacroRule_ )<sup>\*</sup> `;`<sup>?</sup>
>
> _MacroRule_ :\
> &nbsp;&nbsp; _MacroMatcher_ `=>` _MacroTranscriber_
>
> _MacroMatcher_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _MacroMatch_<sup>\*</sup> `)`\
> &nbsp;&nbsp; | `[` _MacroMatch_<sup>\*</sup> `]`\
> &nbsp;&nbsp; | `{` _MacroMatch_<sup>\*</sup> `}`
>
> _MacroMatch_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Token_]<sub>_except `$` and [delimiters]_</sub>\
> &nbsp;&nbsp; | _MacroMatcher_\
> &nbsp;&nbsp; | `$` ( [IDENTIFIER_OR_KEYWORD] <sub>_except `crate`_</sub> | [RAW_IDENTIFIER] | `_` ) `:` _MacroFragSpec_\
> &nbsp;&nbsp; | `$` `(` _MacroMatch_<sup>+</sup> `)` _MacroRepSep_<sup>?</sup> _MacroRepOp_
>
> _MacroFragSpec_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `block` | `expr` | `ident` | `item` | `lifetime` | `literal`\
> &nbsp;&nbsp; | `meta` | `pat` | `pat_param` | `path` | `stmt` | `tt` | `ty` | `vis`
>
> _MacroRepSep_ :\
> &nbsp;&nbsp; [_Token_]<sub>_except [delimiters] and MacroRepOp_</sub>
>
> _MacroRepOp_ :\
> &nbsp;&nbsp; `*` | `+` | `?`
>
> _MacroTranscriber_ :\
> &nbsp;&nbsp; [_DelimTokenTree_]
{==+==}
> **<sup>语法</sup>**\
> _宏规则定义_ :\
> &nbsp;&nbsp; `macro_rules` `!` [标识符][IDENTIFIER] _宏规则组定界_
>
> _宏规则组定界_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _宏规则组_ `)` `;`\
> &nbsp;&nbsp; | `[` _宏规则组_ `]` `;`\
> &nbsp;&nbsp; | `{` _宏规则组_ `}`
>
> _宏规则组_ :\
> &nbsp;&nbsp; _宏规则_ ( `;` _宏规则_ )<sup>\*</sup> `;`<sup>?</sup>
>
> _宏规则_ :\
> &nbsp;&nbsp; _宏匹配器_ `=>` _宏转录器_
>
> _宏匹配器_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _宏匹配_<sup>\*</sup> `)`\
> &nbsp;&nbsp; | `[` _宏匹配_<sup>\*</sup> `]`\
> &nbsp;&nbsp; | `{` _宏匹配_<sup>\*</sup> `}`
>
> _宏匹配_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Token_]<sub>_不包括 `$` 和 [定界符号][delimiters] 之外的任意 token_</sub>\
> &nbsp;&nbsp; | _宏匹配器_\
> &nbsp;&nbsp; | `$` ( [标识符或关键字][IDENTIFIER_OR_KEYWORD] <sub>_不包括 `crate` 之外的任意标识符或关键字_</sub> | [原始标识符][RAW_IDENTIFIER] | `_` ) `:` _宏片段规格_\
> &nbsp;&nbsp; | `$` `(` _宏匹配_<sup>+</sup> `)` _宏表示分割_<sup>?</sup> _宏表示操作符_
>
> _宏片段规格_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `block` | `expr` | `ident` | `item` | `lifetime` | `literal`\
> &nbsp;&nbsp; | `meta` | `pat` | `pat_param` | `path` | `stmt` | `tt` | `ty` | `vis`
>
> _宏表示分割_ :\
> &nbsp;&nbsp; [_Token_]<sub>_不包括 [定界符号][delimiters] 和 宏表示操作符_ 之外的任意 token_</sub>
>
> _宏表示操作符_ :\
> &nbsp;&nbsp; `*` | `+` | `?`
>
> _宏转录器_ :\
> &nbsp;&nbsp; [_定界Token树_][_DelimTokenTree_]
{==+==}


{==+==}
`macro_rules` allows users to define syntax extension in a declarative way.  We
call such extensions "macros by example" or simply "macros".
{==+==}
允许用户以 `macro_rules` 声明定义语法扩展，称为 "实例宏" ，或者简称为 "宏" 。
{==+==}


{==+==}
Each macro by example has a name, and one or more _rules_. Each rule has two
parts: a _matcher_, describing the syntax that it matches, and a _transcriber_,
describing the syntax that will replace a successfully matched invocation. Both
the matcher and the transcriber must be surrounded by delimiters. Macros can
expand to expressions, statements, items (including traits, impls, and foreign
items), types, or patterns.
{==+==}
每个定义的实例宏都要有一个名称和一个以上 _规则_ 。
每个规则都要有两个部分：一个 _匹配器_ 部分，用于描述匹配语法，另一个 _转录器_ 部分，用于描述成功匹配并调用后将替换的语法。
匹配器和转录器都必须被包裹在定界符号中。
宏可以展开为表达式、语句、条目 (包括 trait 、实现和外部条目) 、类型或模式。
{==+==}


{==+==}
## Transcribing
{==+==}
## 转录
{==+==}


{==+==}
When a macro is invoked, the macro expander looks up macro invocations by name,
and tries each macro rule in turn. It transcribes the first successful match; if
this results in an error, then future matches are not tried. When matching, no
lookahead is performed; if the compiler cannot unambiguously determine how to
parse the macro invocation one token at a time, then it is an error. In the
following example, the compiler does not look ahead past the identifier to see
if the following token is a `)`, even though that would allow it to parse the
invocation unambiguously:
{==+==}
当调用宏时，宏展开器通过名称查找调用宏，依次尝试匹配其包含的宏规则。
在首个成功匹配处转录，如果导致错误，则不会尝试后续的规则。
在进行匹配时，不会对 Token 进行前瞻，如果编译器无法逐个明确如何解析，则会出错。
在下面的示例中，编译器不会前瞻标识符 $j ，不会前瞻是否已至 `)` 定界位置，虽然这能够更明确地解析:
{==+==}


{==+==}
```rust,compile_fail
macro_rules! ambiguity {
    ($($i:ident)* $j:ident) => { };
}

ambiguity!(error); // Error: local ambiguity
```
{==+==}
```rust,compile_fail
macro_rules! ambiguity {
    ($($i:ident)* $j:ident) => { };
}

ambiguity!(error); // Error: 局部歧义
```
{==+==}


{==+==}
In both the matcher and the transcriber, the `$` token is used to invoke special
behaviours from the macro engine (described below in [Metavariables] and
[Repetitions]). Tokens that aren't part of such an invocation are matched and
transcribed literally, with one exception. The exception is that the outer
delimiters for the matcher will match any pair of delimiters. Thus, for
instance, the matcher `(())` will match `{()}` but not `{{}}`. The character
`$` cannot be matched or transcribed literally.
{==+==}
在匹配器和转录器中， `$` 符号用于宏引擎实现特定行为 (具体在 [元变量][Metavariables] 和 [重复][Repetitions] 中描述)。
非该符号标记的 Token 会按字面进行匹配和转录，特例是匹配器的外部定界符将匹配任意一对定界符，
例如，匹配器 `(())` 将匹配 `{()}` ，但是不匹配 `{{}}` 。字符 `$` 不能按字面匹配或转录。
{==+==}


{==+==}
### Forwarding a matched fragment
{==+==}
### 转发匹配片段
{==+==}


{==+==}
When forwarding a matched fragment to another macro-by-example, matchers in
the second macro will see an opaque AST of the fragment type. The second macro
can't use literal tokens to match the fragments in the matcher, only a
fragment specifier of the same type. The `ident`, `lifetime`, and `tt`
fragment types are an exception, and *can* be matched by literal tokens. The
following illustrates this restriction:
{==+==}
在将匹配后的片段转发给内部的实例宏时，内部宏的匹配器将只能看到类型化的不透明 AST 片段。
内部宏无法简单通过字面 token 来匹配这个片段，只能使用相同类型规格的片段接收。
 `ident` 、 `lifetime` 和 `tt` 片段类型例外，可以按照字面 token 的方式匹配。
以下是一个示例：
{==+==}


{==+==}
```rust,compile_fail
macro_rules! foo {
    ($l:expr) => { bar!($l); }
// ERROR:               ^^ no rules expected this token in macro call
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```
{==+==}
```rust,compile_fail
macro_rules! foo {
    // expr 表达式片段与 3 的字面值规则不匹配，虽然此表达式是 3 的字面值表达式。
    ($l:expr) => { bar!($l); }
// ERROR:               ^^ 在宏调用中这个 token 没有预期的规则，
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```
{==+==}


{==+==}
The following illustrates how tokens can be directly matched after matching a
`tt` fragment:
{==+==}
以下是在匹配 `tt` Token 树片段后内部规则通过字面匹配 Token 的一个示例：
{==+==}


{==+==}
```rust
// compiles OK
macro_rules! foo {
    ($l:tt) => { bar!($l); }
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```
{==+==}
```rust
// 编译成功
macro_rules! foo {
    // `tt` Token 树片段直接作为字面 Token 与 3 的字面值表达式成功匹配。
    ($l:tt) => { bar!($l); }
}

macro_rules! bar {
    (3) => {}
}

foo!(3);
```
{==+==}


{==+==}
## Metavariables
{==+==}
## 元变量
{==+==}


{==+==}
In the matcher, `$` _name_ `:` _fragment-specifier_ matches a Rust syntax
fragment of the kind specified and binds it to the metavariable `$`_name_. Valid
fragment specifiers are:
{==+==}
在匹配器中， `$` _名称_ `:` _片段规格_ 定义元变量，用于匹配指定类型的语法片段，并与语法片段绑定。
有效的片段规格包括：

译注：这里 '片段规格' 的概念实际上与 '片段类型' 等价，使用规格这个词是与 rust 中的类型系统有所区分。
{==+==}


{==+==}
  * `item`: an [_Item_]
  * `block`: a [_BlockExpression_]
  * `stmt`: a [_Statement_] without the trailing semicolon (except for item
    statements that require semicolons)
  * `pat_param`: a [_PatternNoTopAlt_]
  * `pat`: at least any [_PatternNoTopAlt_], and possibly more depending on edition
  * `expr`: an [_Expression_]
  * `ty`: a [_Type_]
  * `ident`: an [IDENTIFIER_OR_KEYWORD] or [RAW_IDENTIFIER]
  * `path`: a [_TypePath_] style path
  * `tt`: a [_TokenTree_]&nbsp;(a single [token] or tokens in matching delimiters `()`, `[]`, or `{}`)
  * `meta`: an [_Attr_], the contents of an attribute
  * `lifetime`: a [LIFETIME_TOKEN]
  * `vis`: a possibly empty [_Visibility_] qualifier
  * `literal`: matches `-`<sup>?</sup>[_LiteralExpression_]
{==+==}
  * `item`: [_条目_][_Item_] 
  * `block`: [_块表达式_][_BlockExpression_] 
  * `stmt`: [_语句_][_Statement_] 不含尾部分号 (即，不包括需要分号的条目语句)
  * `pat_param`: [_模式非顶层项_][_PatternNoTopAlt_]
  * `pat`: 至少是任意 [_模式非顶层项_][_PatternNoTopAlt_], 更多取决于版次
  * `expr`: [_表达式_][_Expression_] 
  * `ty`: [_类型_][_Type_] 
  * `ident`: 一个 [标识符或关键字][IDENTIFIER_OR_KEYWORD] 或 [原始标识符][RAW_IDENTIFIER]
  * `path`: [_类型路径_][_TypePath_] 
  * `tt`: [_Token树_][_TokenTree_]&nbsp; (简单 [token] 或匹配在定界符号 `()` 、 `[]` 、 `{}` 中 token)
  * `meta`: 一个 [_Attr_], 属性的内容
  * `lifetime`:  [生命周期TOKEN][LIFETIME_TOKEN]
  * `vis`: 可空的 [_可见性_][_Visibility_] 限定词
  * `literal`: 匹配 `-`<sup>?</sup>[_字面值表达式_][_LiteralExpression_]
{==+==}


{==+==}
In the transcriber, metavariables are referred to simply by `$`_name_, since
the fragment kind is specified in the matcher. Metavariables are replaced with
the syntax element that matched them. The keyword metavariable `$crate` can be
used to refer to the current crate; see [Hygiene] below. Metavariables can be
transcribed more than once or not at all.
{==+==}
元变量的片段类型在匹配器中定义，在转录器中可以用 `$`_名称_ 来引用。关键字元变量 `$crate` 用于引用当前的 crate。
转录器中元变量将替换为匹配到的语法片段。
可以将元变量多次转录，或者并不进行转录。
参阅下面的 [卫生性][Hygiene] 。
{==+==}


{==+==}
For reasons of backwards compatibility, though `_` [is also an
expression][_UnderscoreExpression_], a standalone underscore is not matched by
the `expr` fragment specifier. However, `_` is matched by the `expr` fragment
specifier when it appears as a subexpression.
{==+==}
出于向后兼容的原因，单独的 `_` [下划线表达式][_UnderscoreExpression_] 不会被 `expr` 片段匹配。当 `_` 做为子表达式时可被 `expr` 片段匹配。
{==+==}


{==+==}
> **Edition Differences**: Starting with the 2021 edition, `pat` fragment-specifiers match top-level or-patterns (that is, they accept [_Pattern_]).
>
> Before the 2021 edition, they match exactly the same fragments as `pat_param` (that is, they accept [_PatternNoTopAlt_]).
>
> The relevant edition is the one in effect for the `macro_rules!` definition.
{==+==}
> **版次差异**: 从 2021 版次开始，`pat` 规格片段可以匹配顶层或模式 (即可接受 [_模式_][_Pattern_]) 。
>
> 在 2021 版次之前，则完全匹配与 `pat_param` 相同的片段 (即可接受 [_模式非顶层项_][_PatternNoTopAlt_] ) 。
>
> 这与 `macro_rules!` 定义生效的版次相关联。
{==+==}


{==+==}
## Repetitions
{==+==}
## 重复
{==+==}


{==+==}
In both the matcher and transcriber, repetitions are indicated by placing the
tokens to be repeated inside `$(`…`)`, followed by a repetition operator,
optionally with a separator token between. The separator token can be any token
other than a delimiter or one of the repetition operators, but `;` and `,` are
the most common. For instance, `$( $i:ident ),*` represents any number of
identifiers separated by commas. Nested repetitions are permitted.
{==+==}
在匹配器和转录器中，重复语法是通过将要重复的 token 放在 `$(`…`)` 中，后跟重复运算符，可选地包含分隔符 token 。
分隔符 token 可以是除定界符或重复运算符外的任意 token ，但常用的是 `;` 和 `,` 。
例如， `$( $i:ident ),*` 表明由逗号分隔的任意数量的标识符。重复允许嵌套。
{==+==}


{==+==}
The repetition operators are:
{==+==}
重复运算符有:
{==+==}


{==+==}
- `*` — indicates any number of repetitions.
- `+` — indicates any number but at least one.
- `?` — indicates an optional fragment with zero or one occurrences.
{==+==}
- `*` — 表示任意数量的重复。
- `+` — 表示任意数字，但至少是 1 。
- `?` — 表示有 0 或 1 个产生的可选片段。
{==+==}


{==+==}
Since `?` represents at most one occurrence, it cannot be used with a
separator.
{==+==}
由于 `?` 表示最多出现一次，所以它不能与分隔符一起使用。
{==+==}


{==+==}
The repeated fragment both matches and transcribes to the specified number of
the fragment, separated by the separator token. Metavariables are matched to
every repetition of their corresponding fragment. For instance, the `$( $i:ident
),*` example above matches `$i` to all of the identifiers in the list.
{==+==}
重复的片段同时匹配和转录成指定数量的该片段，并由分隔符隔开。
元变量将匹配到对应片段的每个重复项。例如，上面示例的 `$( $i:ident ),*` 中元变量 `$i` 将匹配到列表中的所有标识符。
{==+==}


{==+==}
During transcription, additional restrictions apply to repetitions so that the
compiler knows how to expand them properly:
{==+==}
在转录过程中，重复操作受到额外的限制，以便能够正确地展开：
{==+==}


{==+==}
1.  A metavariable must appear in exactly the same number, kind, and nesting
    order of repetitions in the transcriber as it did in the matcher. So for the
    matcher `$( $i:ident ),*`, the transcribers `=> { $i }`,
    `=> { $( $( $i)* )* }`, and `=> { $( $i )+ }` are all illegal, but
    `=> { $( $i );* }` is correct and replaces a comma-separated list of
    identifiers with a semicolon-separated list.
{==+==}
1.  匹配器中的 '重复' ，在转录器中出现时，必须具有完全相同的数量、种类和嵌套顺序。
    因此，对于匹配器中 `$( $i:ident ),*` '重复' ，转录器中 `=> { $i }` 、`=> { $( $( $i)* )* }` 和 `=> { $( $i )+ }` 不一致的形式是不合法的，
    对于 `=> { $( $i );* }` 是正确的，这里只是将由逗号分隔的标识符列表替换为了分号分隔。
{==+==}


{==+==}
2.  Each repetition in the transcriber must contain at least one metavariable to
    decide how many times to expand it. If multiple metavariables appear in the
    same repetition, they must be bound to the same number of fragments. For
    instance, `( $( $i:ident ),* ; $( $j:ident ),* ) => (( $( ($i,$j) ),* ))` must
    bind the same number of `$i` fragments as `$j` fragments. This means that
    invoking the macro with `(a, b, c; d, e, f)` is legal and expands to
    `((a,d), (b,e), (c,f))`, but `(a, b, c; d, e)` is illegal because it does
    not have the same number. This requirement applies to every layer of nested
    repetitions.
{==+==}
2.  在转录器中，每个重复部分必须至少包含一个元变量，以决定展开它的次数。
    如果同一个重复部分中出现多个元变量时，则它们绑定的片段数量必须相同。
    例如，`( $( $i:ident ),* ; $( $j:ident ),* ) => (( $( ($i,$j) ),* ))` 其 `$i` 和 `$j` 必须绑定相同数量的片段。
    所以，以 `(a, b, c; d, e, f)` 这样的内容调用宏是合法的，将展开为 `((a,d), (b,e), (c,f))` ，但 `(a, b, c; d, e)` 却不行，因为数量不同。
    这个要求对于嵌套的重复同样适用。
{==+==}


{==+==}
## Scoping, Exporting, and Importing
{==+==}
## 作用域、导出和导入
{==+==}


{==+==}
For historical reasons, the scoping of macros by example does not work entirely
like items. Macros have two forms of scope: textual scope, and path-based scope.
Textual scope is based on the order that things appear in source files, or even
across multiple files, and is the default scoping. It is explained further below.
Path-based scope works exactly the same way that item scoping does. The scoping,
exporting, and importing of macros is controlled largely by attributes.
{==+==}
由于历史原因，实例宏的作用域机制与条目并不完全相同。
宏有两种作用域形式：文本作用域和基于路径的作用域。
文本作用域是基于宏在源文件中的顺序，甚至会跨越多个文件，并且这是默认的作用域。在下面会进一步解释。
基于路径的作用域与条目的作用域完全相同。宏的作用域、导出和导入主要由属性控制。
{==+==}


{==+==}
When a macro is invoked by an unqualified identifier (not part of a multi-part
path), it is first looked up in textual scoping. If this does not yield any
results, then it is looked up in path-based scoping. If the macro's name is
qualified with a path, then it is only looked up in path-based scoping.
{==+==}
当以未限定的标识符 (指宏不是多部分组成的路径的一部分) 形式调用宏时，则首先在文本作用域中查找宏。
如果未找到结果，那么将按路径作用域查找。
如果宏的名称带有路径限定符，则仅在基于路径的作用域中查找。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
use lazy_static::lazy_static; // Path-based import.

macro_rules! lazy_static { // Textual definition.
    (lazy) => {};
}

lazy_static!{lazy} // Textual lookup finds our macro first.
self::lazy_static!{} // Path-based lookup ignores our macro, finds imported one.
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
use lazy_static::lazy_static; // 基于路径导入。

macro_rules! lazy_static { // 定义文本。
    (lazy) => {};
}

lazy_static!{lazy} // 首先在文本中找到了宏。
self::lazy_static!{} // 基于路径的查找忽略了文本中的宏，找到导入的宏。
```
{==+==}


{==+==}
### Textual Scope
{==+==}
### 文本作用域
{==+==}


{==+==}
Textual scope is based largely on the order that things appear in source files,
and works similarly to the scope of local variables declared with `let` except
it also applies at the module level. When `macro_rules!` is used to define a
macro, the macro enters the scope after the definition (note that it can still
be used recursively, since names are looked up from the invocation site), up
until its surrounding scope, typically a module, is closed. This can enter child
modules and even span across multiple files:
{==+==}
文本作用域主要基于宏在源文件中出现的顺序，机制与 `let` 声明的局部变量的作用域类似，但宏文本作用域将应用于模块级别。
当使用 `macro_rules!` 定义了宏，则宏在定义的以下部分进入作用域。需注意的是，由于名称是从调用位置查找，因而可以递归。 
在其围绕的作用域 (通常是模块) 关闭之前，宏可以进入子模块，甚至跨越多个文件：
{==+==}


{==+==}
<!-- ignore: requires external modules -->
```rust,ignore
//// src/lib.rs
mod has_macro {
    // m!{} // Error: m is not in scope.

    macro_rules! m {
        () => {};
    }
    m!{} // OK: appears after declaration of m.

    mod uses_macro;
}

// m!{} // Error: m is not in scope.

//// src/has_macro/uses_macro.rs

m!{} // OK: appears after declaration of m in src/lib.rs
```
{==+==}
<!-- ignore: requires external modules -->
```rust,ignore
//// src/lib.rs
mod has_macro {
    // m!{} // Error: m 不在作用域内。

    macro_rules! m {
        () => {};
    }
    m!{} // OK: 出现在 m 的声明之后。

    mod uses_macro;
}

// m!{} // Error: m 不在作用域内。

//// src/has_macro/uses_macro.rs

m!{} // OK: 在 src/lib.rs 中出现在 m 的声明之后。
```
{==+==}


{==+==}
It is not an error to define a macro multiple times; the most recent declaration
will shadow the previous one unless it has gone out of scope.
{==+==}
多次定义宏不会产生错误，如果未超出作用域，最近一次的声明会隐藏先前的声明。
{==+==}


{==+==}
```rust
macro_rules! m {
    (1) => {};
}

m!(1);

mod inner {
    m!(1);

    macro_rules! m {
        (2) => {};
    }
    // m!(1); // Error: no rule matches '1'
    m!(2);

    macro_rules! m {
        (3) => {};
    }
    m!(3);
}

m!(1);
```
{==+==}
```rust
macro_rules! m {
    (1) => {};
}

m!(1);

mod inner {
    m!(1);

    macro_rules! m {
        (2) => {};
    }
    // m!(1); // Error: 没有规则可以匹配 '1'
    m!(2);

    macro_rules! m {
        (3) => {};
    }
    m!(3);
}

m!(1);
```
{==+==}


{==+==}
Macros can be declared and used locally inside functions as well, and work
similarly:
{==+==}
宏也可以在函数内部声明和使用，工作方式与模块中声明类似:
{==+==}


{==+==}
```rust
fn foo() {
    // m!(); // Error: m is not in scope.
    macro_rules! m {
        () => {};
    }
    m!();
}


// m!(); // Error: m is not in scope.
```
{==+==}
```rust
fn foo() {
    // m!(); // Error: m 不在作用域内。
    macro_rules! m {
        () => {};
    }
    m!();
}


// m!(); // Error: m 不在作用域内。
```
{==+==}




{==+==}
### The `macro_use` attribute
{==+==}
### `macro_use` 属性
{==+==}


{==+==}
The *`macro_use` attribute* has two purposes. First, it can be used to make a
module's macro scope not end when the module is closed, by applying it to a
module:
{==+==}
*`macro_use` 属性* 有两个用法。一是，应用于模块后，使模块中宏的作用域在模块关闭后仍不结束:
{==+==}


{==+==}
```rust
#[macro_use]
mod inner {
    macro_rules! m {
        () => {};
    }
}

m!();
```
{==+==}
```rust
#[macro_use]
mod inner {
    macro_rules! m {
        () => {};
    }
}

// 这里使 m 的作用域延长到了模块之外
m!();
```
{==+==}


{==+==}
Second, it can be used to import macros from another crate, by attaching it to
an `extern crate` declaration appearing in the crate's root module. Macros
imported this way are imported into the [`macro_use` prelude], not textually,
which means that they can be shadowed by any other name. While macros imported
by `#[macro_use]` can be used before the import statement, in case of a
conflict, the last macro imported wins. Optionally, a list of macros to import
can be specified using the [_MetaListIdents_] syntax; this is not supported
when `#[macro_use]` is applied to a module.
{==+==}
二是，应用于 crate 根模块中的 `extern crate` 声明，从而从另一个 crate 导入宏。
这种方式会将宏导入到 [`macro_use` 预定义][`macro_use` prelude] 中，而不是文本形式的导入，这表示可在任意位置隐藏此宏名称。
虽然 `#[macro_use]` 导入的宏可以在导入语句之前使用，但名称冲突时，最后导入的宏优先。
可选语法是，可以使用 [元标识符列表][MetaListIdents] 指定要导入的宏列表。当将 `#[macro_use]` 应用于模块时，不支持此功能。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
#[macro_use(lazy_static)] // Or #[macro_use] to import all macros.
extern crate lazy_static;

lazy_static!{}
// self::lazy_static!{} // Error: lazy_static is not defined in `self`
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
#[macro_use(lazy_static)] // 仅 #[macro_use] 表示导入所有宏。
extern crate lazy_static;

lazy_static!{}
// self::lazy_static!{} // Error: lazy_static 在 `self` 未定义。
```
{==+==}


{==+==}
Macros to be imported with `#[macro_use]` must be exported with
`#[macro_export]`, which is described below.
{==+==}
使用 `#[macro_use]` 导入的宏导出时必须使用 `#[macro_export]` ，下面将对此进行描述。
{==+==}


{==+==}
### Path-Based Scope
{==+==}
### 基于路径作用域
{==+==}


{==+==}
By default, a macro has no path-based scope. However, if it has the
`#[macro_export]` attribute, then it is declared in the crate root scope and can
be referred to normally as such:
{==+==}
默认情况，宏没有基于路径的作用域。然而，如果赋予 `#[macro_export]` 属性，那么宏就被声明在 crate 根作用域内:
{==+==}


{==+==}
```rust
self::m!();
m!(); // OK: Path-based lookup finds m in the current module.

mod inner {
    super::m!();
    crate::m!();
}

mod mac {
    #[macro_export]
    macro_rules! m {
        () => {};
    }
}
```
{==+==}
```rust
self::m!();
m!(); // OK: 基于路径方式查找，在当前模块中找到了 m 。

mod inner {
    super::m!();
    crate::m!();
}

mod mac {
    #[macro_export]
    macro_rules! m {
        () => {};
    }
}
```
{==+==}


{==+==}
Macros labeled with `#[macro_export]` are always `pub` and can be referred to
by other crates, either by path or by `#[macro_use]` as described above.
{==+==}
 `#[macro_export]` 标记的宏总是 `pub` 的，可以通过路径或以上面描述的 `#[macro_use]` 方式引入其他 crate 中。
{==+==}


{==+==}
## Hygiene
{==+==}
## 卫生性
{==+==}


{==+==}
By default, all identifiers referred to in a macro are expanded as-is, and are
looked up at the macro's invocation site. This can lead to issues if a macro
refers to an item or macro which isn't in scope at the invocation site. To
alleviate this, the `$crate` metavariable can be used at the start of a path to
force lookup to occur inside the crate defining the macro.
{==+==}
默认情况下，在宏中引用的所有标识符都会按原样展开，并从宏调用的位置查找。
如果宏引用的条目或宏，在调用位置不在作用域内，则可能会出现问题。
为了缓解这个问题，可以在路径中使用 `$crate` 元变量，以强制在定义宏的 crate 内部进行查找。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
//// Definitions in the `helper_macro` crate.
#[macro_export]
macro_rules! helped {
    // () => { helper!() } // This might lead to an error due to 'helper' not being in scope.
    () => { $crate::helper!() }
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// Usage in another crate.
// Note that `helper_macro::helper` is not imported!
use helper_macro::helped;

fn unit() {
    helped!();
}
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
//// 定义在 `helper_macro` crate 中。
#[macro_export]
macro_rules! helped {
    // () => { helper!() } // 由于 'helper' 不在作用域中，这可能会导致错误。
    () => { $crate::helper!() }
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// 在另一个 crate 里使用。
// 请注意，未导入 `helper_macro::helper` 。
use helper_macro::helped;

fn unit() {
    helped!();
}
```
{==+==}


{==+==}
Note that, because `$crate` refers to the current crate, it must be used with a
fully qualified module path when referring to non-macro items:
{==+==}
注意，因为 `$crate` 指的是当前的 crate ，所以当引用非宏条目时，必须使用完全限定的模块路径。
{==+==}


{==+==}
```rust
pub mod inner {
    #[macro_export]
    macro_rules! call_foo {
        () => { $crate::inner::foo() };
    }

    pub fn foo() {}
}
```
{==+==}

{==+==}


{==+==}
Additionally, even though `$crate` allows a macro to refer to items within its
own crate when expanding, its use has no effect on visibility. An item or macro
referred to must still be visible from the invocation site. In the following
example, any attempt to invoke `call_foo!()` from outside its crate will fail
because `foo()` is not public.
{==+==}
此外，尽管 `$crate` 允许宏在展开时引用其所在 crate 内的条目，但并不会影响被引用条目的可见性。
被引用的条目或宏必须从调用位置可见。
在下面的示例中，从 crate 外部尝试调用 `call_foo!()` 将失败，因为 `foo()` 不是公开的。
{==+==}


{==+==}
```rust
#[macro_export]
macro_rules! call_foo {
    () => { $crate::foo() };
}

fn foo() {}
```
{==+==}

{==+==}


{==+==}
> **Version & Edition Differences**: Prior to Rust 1.30, `$crate` and
> `local_inner_macros` (below) were unsupported. They were added alongside
> path-based imports of macros (described above), to ensure that helper macros
> did not need to be manually imported by users of a macro-exporting crate.
> Crates written for earlier versions of Rust that use helper macros need to be
> modified to use `$crate` or `local_inner_macros` to work well with path-based
> imports.
{==+==}
> 在 Rust 1.30 之前，不支持 `$crate` 和 `local_inner_macros` (下面会描述) 。
> 这两个语法与基于路径导入宏的语法一起添加，是为保证一些工具宏不需要用户手动导入。
> 使用工具宏的早期 Rust 版本编写的 crate 需要进行修改后，以使用 `$crate` 或 `local_inner_macros` 。
{==+==}


{==+==}
When a macro is exported, the `#[macro_export]` attribute can have the
`local_inner_macros` keyword added to automatically prefix all contained macro
invocations with `$crate::`. This is intended primarily as a tool to migrate
code written before `$crate` was added to the language to work with Rust 2018's
path-based imports of macros. Its use is discouraged in new code.
{==+==}
当一个宏被导出时，可以在 `#[macro_export]` 属性中添加 `local_inner_macros` 关键字，以自动在所有包含的宏调用中添加 `$crate::` 前缀。
这主要是为了迁移在 `$crate` 添加到语言之前编写的代码，使其能够与 Rust 2018 的基于路径的宏导入一起使用。
不建议在新代码中使用此功能。
{==+==}


{==+==}
```rust
#[macro_export(local_inner_macros)]
macro_rules! helped {
    () => { helper!() } // Automatically converted to $crate::helper!().
}

#[macro_export]
macro_rules! helper {
    () => { () }
}
```
{==+==}
```rust
#[macro_export(local_inner_macros)]
macro_rules! helped {
    () => { helper!() } // 自动转换为 $crate::helper!() 。
}

#[macro_export]
macro_rules! helper {
    () => { () }
}
```
{==+==}


{==+==}
## Follow-set Ambiguity Restrictions
{==+==}
## 后继符冲突限制
{==+==}


{==+==}
The parser used by the macro system is reasonably powerful, but it is limited in
order to prevent ambiguity in current or future versions of the language. In
particular, in addition to the rule about ambiguous expansions, a nonterminal
matched by a metavariable must be followed by a token which has been decided can
be safely used after that kind of match.
{==+==}
宏系统使用的解析器相当强大，但出于防止当前或未来版本的语言产生歧义的考虑，对于宏有一些限制。
特别是，除了有关模糊展开的规则之外，由元变量匹配的非终结符号必须后跟一个 token ，此 token 决定可以安全地在该类型匹配之后使用。
{==+==}


{==+==}
As an example, a macro matcher like `$i:expr [ , ]` could in theory be accepted
in Rust today, since `[,]` cannot be part of a legal expression and therefore
the parse would always be unambiguous. However, because `[` can start trailing
expressions, `[` is not a character which can safely be ruled out as coming
after an expression. If `[,]` were accepted in a later version of Rust, this
matcher would become ambiguous or would misparse, breaking working code.
Matchers like `$i:expr,` or `$i:expr;` would be legal, however, because `,` and
`;` are legal expression separators. The specific rules are:
{==+==}
举例来说，像 `$i:expr [ , ]` 这样的宏匹配器理论上是可以接受的，因为 `[,]` 不能成为合规的表达式的一部分，因此解析不会有歧义。
但是，由于 `[` 可以作为尾随的表达式的开始，因此 `[` 不是一个可以安全地在表达式之后排除的字符。
如果在以后的 Rust 版本中接受了 `[,]` ，这个匹配器就会变成有歧义或者解析错误，从而破坏工作中的代码。
对于，像 `$i:expr,` 或 `$i:expr;` 这样的匹配器是合规的，因为 `,` 和 `;` 是合规的表达式分隔符。
具体的规则如下：
{==+==}


{==+==}
  * `expr` and `stmt` may only be followed by one of: `=>`, `,`, or `;`.
  * `pat_param` may only be followed by one of: `=>`, `,`, `=`, `|`, `if`, or `in`.
  * `pat` may only be followed by one of: `=>`, `,`, `=`, `if`, or `in`.
  * `path` and `ty` may only be followed by one of: `=>`, `,`, `=`, `|`, `;`,
    `:`, `>`, `>>`, `[`, `{`, `as`, `where`, or a macro variable of `block`
    fragment specifier.
  * `vis` may only be followed by one of: `,`, an identifier other than a
    non-raw `priv`, any token that can begin a type, or a metavariable with a
    `ident`, `ty`, or `path` fragment specifier.
  * All other fragment specifiers have no restrictions.
{==+==}
  * `expr` 和 `stmt` 后面只能跟着一个: `=>` 、 `,`、 `;` 。
  * `pat_param` 后面只能跟着一个: `=>` 、 `,` 、 `=` 、 `|` 、 `if` 、 `in` 。
  * `pat` 后面只能跟着一个: `=>` 、 `,` 、 `=` 、 `if` 、 `in` 。
  * `path` 和 `ty` 后面只能跟着一个: `=>` 、 `,` 、 `=` 、 `|` 、 `;` 、 `:` 、 `>` 、 `>>` 、 `[` 、 `{` 、 `as` 、 `where` ， 或 `block` 片段规格的元变量 。
  * `vis` 后面只能跟着一个: `,`, 一个非原始的 `priv` 以外的标识符，任何可以开始一个类型的标记，或者一个带有 `ident` 、 `ty` 或 `path` 片段规格的元变量。
  * 对所有其他片段规格没有限制。
{==+==}


{==+==}
> **Edition Differences**: Before the 2021 edition, `pat` may also be followed by `|`.
{==+==}
> **版次差异**: 在 2021 版次之前， `pat` 后面还可以跟着 `|`。
{==+==}


{==+==}
When repetitions are involved, then the rules apply to every possible number of
expansions, taking separators into account. This means:
{==+==}
当涉及到重复时，规则适用于每次可能的展开，同时要考虑分隔符。这意味着：
{==+==}


{==+==}
  * If the repetition includes a separator, that separator must be able to
    follow the contents of the repetition.
  * If the repetition can repeat multiple times (`*` or `+`), then the contents
    must be able to follow themselves.
  * The contents of the repetition must be able to follow whatever comes
    before, and whatever comes after must be able to follow the contents of the
    repetition.
  * If the repetition can match zero times (`*` or `?`), then whatever comes
    after must be able to follow whatever comes before.
{==+==}
  * 如果重复包括一个分隔符，则该分隔符必须能够跟在重复内容之后。
  * 如果重复可以重复多次 (`*` 或 `+`) ，则其内容必须能够跟随它们自身。
  * 重复的内容必须能够跟随之前的任何内容，而跟随重复内容的任何内容后面都必须能够跟随。
  * 如果重复可以匹配零次 (`*` 或 `?`) ，则后面必须能够跟随前面。
{==+==}


{==+==}
For more detail, see the [formal specification].
{==+==}
更多细节，见 [形式说明][formal specification] 。
{==+==}


{==+==}
[Hygiene]: #hygiene
[IDENTIFIER]: identifiers.md
[IDENTIFIER_OR_KEYWORD]: identifiers.md
[RAW_IDENTIFIER]: identifiers.md
[LIFETIME_TOKEN]: tokens.md#lifetimes-and-loop-labels
[Metavariables]: #metavariables
[Repetitions]: #repetitions
[_Attr_]: attributes.md
[_BlockExpression_]: expressions/block-expr.md
[_DelimTokenTree_]: macros.md
[_Expression_]: expressions.md
[_Item_]: items.md
[_LiteralExpression_]: expressions/literal-expr.md
[_MetaListIdents_]: attributes.md#meta-item-attribute-syntax
[_Pattern_]: patterns.md
[_PatternNoTopAlt_]: patterns.md
[_Statement_]: statements.md
[_TokenTree_]: macros.md#macro-invocation
[_Token_]: tokens.md
[delimiters]: tokens.md#delimiters
[_TypePath_]: paths.md#paths-in-types
[_Type_]: types.md#type-expressions
[_UnderscoreExpression_]: expressions/underscore-expr.md
[_Visibility_]: visibility-and-privacy.md
[formal specification]: macro-ambiguity.md
[token]: tokens.md
[`macro_use` prelude]: names/preludes.md#macro_use-prelude
{==+==}

{==+==}