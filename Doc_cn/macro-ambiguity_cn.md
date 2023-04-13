{==+==}
# Appendix: Macro Follow-Set Ambiguity Formal Specification
{==+==}
# 附录: 宏的Follow-Set歧义性形式化规范

该规范是为了帮助理解 Rust 宏展开期间的 Follow-Set 歧义性而设计的。 Follow-Set 是指给定上下文环境下宏可能展开的所有可能性集合。在某些情况下，这些集合可能会有歧义，这会导致宏展开出现问题。
{==+==}


{==+==}
This page documents the formal specification of the follow rules for [Macros
By Example]. They were originally specified in [RFC 550], from which the bulk
of this text is copied, and expanded upon in subsequent RFCs.
{==+==}
本页面记录了 [示例宏] 的正式规范，其中包括以下规则。
这些规则最初在 [RFC 550] 中进行了规定，本文大部分内容均来自该规范，并在随后的 RFC 中进行了扩展。
{==+==}


{==+==}
## Definitions & Conventions
{==+==}
## 定义和约定
{==+==}


{==+==}
  - `macro`: anything invokable as `foo!(...)` in source code.
  - `MBE`: macro-by-example, a macro defined by `macro_rules`.
  - `matcher`: the left-hand-side of a rule in a `macro_rules` invocation, or a
    subportion thereof.
  - `macro parser`: the bit of code in the Rust parser that will parse the
    input using a grammar derived from all of the matchers.
  - `fragment`: The class of Rust syntax that a given matcher will accept (or
    "match").
  - `repetition` : a fragment that follows a regular repeating pattern
  - `NT`: non-terminal, the various "meta-variables" or repetition matchers
    that can appear in a matcher, specified in MBE syntax with a leading `$`
    character.
{==+==}
  - `macro`: 在源代码中作为 `foo!(...)` 可调用的任何内容。
  - `MBE`: 由 `macro_rules` 定义的实例宏。
  - `matcher`: `macro_rules` 调用中规则的左侧或其子部分。
  - `macro parser`: Rust 解析器中将使用从所有匹配器派生的语法分析输入的代码部分。
  - `fragment`: 给定匹配器将接受 (或 "匹配" 的) Rust 语法类别。
  - `repetition`: 遵循规律重复模式的片段。
  - `NT`: 非终端，可以出现在匹配器中的各种 "元变量" 或重复匹配器，在 MBE 语法中使用前缀 $ 字符指定。
{==+==}


{==+==}
  - `simple NT`: a "meta-variable" non-terminal (further discussion below).
  - `complex NT`: a repetition matching non-terminal, specified via repetition
    operators (`*`, `+`, `?`).
  - `token`: an atomic element of a matcher; i.e. identifiers, operators,
    open/close delimiters, *and* simple NT's.
  - `token tree`: a tree structure formed from tokens (the leaves), complex
    NT's, and finite sequences of token trees.
  - `delimiter token`: a token that is meant to divide the end of one fragment
    and the start of the next fragment.
{==+==}
  - `simple NT`: 一种 "元变量" 非终端符 (下面会进一步讨论)。
  - `complex NT`: 通过重复运算符 (`*` 、 `+` 、 `?` ) 指定的重复匹配非终端符。
  - `token`: 匹配器的原子元素，即标识符、运算符、开/闭定界符、 *以及* 简单 NT 。
  - `token tree`: 由标记 (叶子)、复杂 NT 和标记树的有限序列形成的树结构。
  - `delimiter token`: 用于分隔一个片段的结尾和下一个片段的开头的标记。
{==+==}


{==+==}
  - `separator token`: an optional delimiter token in an complex NT that
    separates each pair of elements in the matched repetition.
  - `separated complex NT`: a complex NT that has its own separator token.
  - `delimited sequence`: a sequence of token trees with appropriate open- and
    close-delimiters at the start and end of the sequence.
  - `empty fragment`: The class of invisible Rust syntax that separates tokens,
    i.e. whitespace, or (in some lexical contexts), the empty token sequence.
  - `fragment specifier`: The identifier in a simple NT that specifies which
    fragment the NT accepts.
  - `language`: a context-free language.
{==+==}
  - `separator token`: 复杂 NT 中的可选分隔符标记，用于分隔匹配重复中每对元素。
  - `separated complex NT`: 具有自己的分隔符标记的复杂 NT 。
  - `delimited sequence`: 带有适当的开/闭定界符的标记树序列。
  - `empty fragment`: Rust 语言中分隔标记的无形语法类，即空格或 (在某些词法上下文中) 空标记序列。
  - `fragment specifier`: 简单 NT 中指定 NT 接受的片段的标识符。
  - `language`: 无上下文的语言。
{==+==}


{==+==}
Example:

```rust,compile_fail
macro_rules! i_am_an_mbe {
    (start $foo:expr $($i:ident),* end) => ($foo)
}
```
{==+==}

{==+==}


{==+==}
`(start $foo:expr $($i:ident),* end)` is a matcher. The whole matcher is a
delimited sequence (with open- and close-delimiters `(` and `)`), and `$foo`
and `$i` are simple NT's with `expr` and `ident` as their respective fragment
specifiers.
{==+==}
`(start $foo:expr $($i:ident),* end)` 是一个匹配器。
整个匹配器是一个带有开/闭定界符 `(` 和 `)` 的定界序列， `$foo` 和 `$i` 是具有 `expr` 和 `ident` 作为它们各自的片段指示符的简单 NT 。
{==+==}


{==+==}
`$(i:ident),*` is *also* an NT; it is a complex NT that matches a
comma-separated repetition of identifiers. The `,` is the separator token for
the complex NT; it occurs in between each pair of elements (if any) of the
matched fragment.
{==+==}
`$(i:ident),*` 也是一个 NT ; 它是一个复杂 NT ，用于匹配逗号分隔的标识符的重复。
`,` 是复杂 NT 的分隔符标记；它出现在匹配片段的每一对元素 (如果有的话) 之间。
{==+==}


{==+==}
Another example of a complex NT is `$(hi $e:expr ;)+`, which matches any
fragment of the form `hi <expr>; hi <expr>; ...` where `hi <expr>;` occurs at
least once. Note that this complex NT does not have a dedicated separator
token.
{==+==}
`$(hi $e:expr ;)+` 是一个复杂的非终端符(NT)的例子，它匹配形式为 `hi <expr>; hi <expr>; ...` 的片段，其中至少包含一次 `hi <expr>;` 。请注意，这个复杂的非终端符并没有专用的分隔符令牌。
{==+==}


{==+==}
(Note that Rust's parser ensures that delimited sequences always occur with
proper nesting of token tree structure and correct matching of open- and
close-delimiters.)
{==+==}
(注意， Rust 的解析器确保定界序列始终具有正确嵌套的标记树结构和正确匹配的开/闭定界符。)
{==+==}


{==+==}
We will tend to use the variable "M" to stand for a matcher, variables "t" and
"u" for arbitrary individual tokens, and the variables "tt" and "uu" for
arbitrary token trees. (The use of "tt" does present potential ambiguity with
its additional role as a fragment specifier; but it will be clear from context
which interpretation is meant.)
{==+==}
我们通常使用变量 "M" 代表一个匹配器，变量 "t" 和 "u" 代表任意单个标记，变量 "tt" 和 "uu" 代表任意标记树。( "tt" 的使用可能存在潜在的歧义，因为它还作为片段指示符的额外角色；但从上下文中可以清楚地知道意思。)
{==+==}


{==+==}
"SEP" will range over separator tokens, "OP" over the repetition operators
`*`, `+`, and `?`, "OPEN"/"CLOSE" over matching token pairs surrounding a
delimited sequence (e.g. `[` and `]`).
{==+==}
"SEP" 将遍历分隔符标记， "OP" 将遍历重复运算符 `*` 、 `+` 和 `?` ， "OPEN"/"CLOSE" 将遍历匹配定界序列的标记对 (例如 `[` 和 `]` )。
{==+==}


{==+==}
Greek letters "α" "β" "γ" "δ"  stand for potentially empty token-tree sequences.
(However, the Greek letter "ε" (epsilon) has a special role in the presentation
and does not stand for a token-tree sequence.)
{==+==}
希腊字母 "α" "β" "γ" "δ" 表示可能为空的标记树序列。
(然而，希腊字母 "ε" (epsilon) 在表述中具有特殊作用，不代表标记树序列。)
{==+==}


{==+==}
  * This Greek letter convention is usually just employed when the presence of
    a sequence is a technical detail; in particular, when we wish to *emphasize*
    that we are operating on a sequence of token-trees, we will use the notation
    "tt ..." for the sequence, not a Greek letter.
{==+==}
  * 这种希腊字母约定通常只在序列的存在是技术细节时使用；特别地，当我们希望 *强调* 我们正在操作标记树序列时，我们将使用符号 "tt ..." 表示序列，而不是希腊字母。
{==+==}


{==+==}
Note that a matcher is merely a token tree. A "simple NT", as mentioned above,
is an meta-variable NT; thus it is a non-repetition. For example, `$foo:ty` is
a simple NT but `$($foo:ty)+` is a complex NT.
{==+==}
请注意，匹配器仅仅是一个标记树。如上所述，"简单 NT" 是一个元变量 NT；因此它是一个非重复项。例如， `$foo:ty` 是一个简单的 NT ，但 `$($foo:ty)+` 是一个复杂的 NT。
{==+==}


{==+==}
Note also that in the context of this formalism, the term "token" generally
*includes* simple NTs.
{==+==}
还请注意，在这种形式化语境下，术语 "token" 通常包括简单的 NT。
{==+==}


{==+==}
Finally, it is useful for the reader to keep in mind that according to the
definitions of this formalism, no simple NT matches the empty fragment, and
likewise no token matches the empty fragment of Rust syntax. (Thus, the *only*
NT that can match the empty fragment is a complex NT.) This is not actually
true, because the `vis` matcher can match an empty fragment. Thus, for the
purposes of the formalism, we will treat `$v:vis` as actually being
`$($v:vis)?`, with a requirement that the matcher match an empty fragment.
{==+==}
最后，读者应该记住，根据这个形式化的定义，没有简单的 NT 匹配空片段，同样，没有 token 匹配 Rust 语法的空片段。(因此，唯一可以匹配空片段的 NT 是复杂的 NT。) 这实际上并不正确，因为 `vis` 匹配器可以匹配空片段。因此，为了形式化的目的，我们将把 `$v:vis` 实际上视为 `$($v:vis)?` ，并要求匹配器匹配一个空片段。
{==+==}


{==+==}
### The Matcher Invariants
{==+==}
### 匹配器的不变性
{==+==}


{==+==}
To be valid, a matcher must meet the following three invariants. The definitions
of FIRST and FOLLOW are described later.
{==+==}
要有效，匹配器必须符合以下三个不变量。FIRST 和 FOLLOW 的定义将在后面描述。
{==+==}


{==+==}
1.  For any two successive token tree sequences in a matcher `M` (i.e. `M = ...
    tt uu ...`) with `uu ...` nonempty, we must have FOLLOW(`... tt`) ∪ {ε} ⊇
    FIRST(`uu ...`).
1.  For any separated complex NT in a matcher, `M = ... $(tt ...) SEP OP ...`,
    we must have `SEP` ∈ FOLLOW(`tt ...`).
1.  For an unseparated complex NT in a matcher, `M = ... $(tt ...) OP ...`, if
    OP = `*` or `+`, we must have FOLLOW(`tt ...`) ⊇ FIRST(`tt ...`).
{==+==}
1. 对于匹配器 `M` 中的任意两个连续的标记树序列 (即 `M = ... tt uu ...`，其中 `uu ...` 非空) ，我们必须有 FOLLOW(`... tt`) ∪ {ε} ⊇ FIRST(`uu ...`)。
2. 对于匹配器中的任何分隔的复杂 NT，`M = ... $(tt ...) SEP OP ...`，我们必须有 `SEP` ∈ FOLLOW(`tt ...`)。
3. 对于匹配器中的未分隔的复杂 NT，`M = ... $(tt ...) OP ...`，如果 OP = `*` 或 `+`，则我们必须有 FOLLOW(`tt ...`) ⊇ FIRST(`tt ...`)。
{==+==}


{==+==}
The first invariant says that whatever actual token that comes after a matcher,
if any, must be somewhere in the predetermined follow set.  This ensures that a
legal macro definition will continue to assign the same determination as to
where `... tt` ends and `uu ...` begins, even as new syntactic forms are added
to the language.
{==+==}
第一个不变量表示，无论匹配器后面有什么实际标记 (如果有的话) ，它都必须在预定的 FOLLOW 集中的某个位置。这确保了一个合法的宏定义将继续分配相同的决定，即 `... tt` 结束并且 `uu ...` 开始的位置，即使语言中添加了新的语法形式。
{==+==}


{==+==}
The second invariant says that a separated complex NT must use a separator token
that is part of the predetermined follow set for the internal contents of the
NT. This ensures that a legal macro definition will continue to parse an input
fragment into the same delimited sequence of `tt ...`'s, even as new syntactic
forms are added to the language.
{==+==}
第二个不变量表示，分隔的复杂 NT 必须使用作为 NT 内部内容预定 FOLLOW 集的分隔符标记。这确保了一个合法的宏定义将继续将输入片段解析为相同的 `tt ...` 的定界序列，即使语言中添加了新的语法形式。
{==+==}


{==+==}
The third invariant says that when we have a complex NT that can match two or
more copies of the same thing with no separation in between, it must be
permissible for them to be placed next to each other as per the first invariant.
This invariant also requires they be nonempty, which eliminates a possible
ambiguity.
{==+==}
第三个不变量表示，当我们有一个复杂的 NT 可以匹配两个或多个连续的相同元素而不需要分隔符时，根据第一个不变量，这些元素必须可以被放在一起。该不变量还要求它们必须是非空的，这消除了可能的歧义。
{==+==}


{==+==}
**NOTE: The third invariant is currently unenforced due to historical oversight
and significant reliance on the behaviour. It is currently undecided what to do
about this going forward. Macros that do not respect the behaviour may become
invalid in a future edition of Rust. See the [tracking issue].**
{==+==}
**注意: 由于历史遗漏和对该行为的重要依赖，目前未执行第三个不变量。目前尚未决定如何继续处理此问题。不遵守此行为的宏在 Rust 的未来版本中可能会变得无效。请参见 [问题跟踪][tracking issue]。**
{==+==}


{==+==}
### FIRST and FOLLOW, informally
{==+==}
### FIRST 和 FOLLOW，非正式描述
{==+==}


{==+==}
A given matcher M maps to three sets: FIRST(M), LAST(M) and FOLLOW(M).
{==+==}
一个给定的匹配器 M 映射到三个集合: FIRST(M)、LAST(M) 和 FOLLOW(M)。
{==+==}


{==+==}
Each of the three sets is made up of tokens. FIRST(M) and LAST(M) may also
contain a distinguished non-token element ε ("epsilon"), which indicates that M
can match the empty fragment. (But FOLLOW(M) is always just a set of tokens.)
{==+==}
这三个集合都由标记组成。 FIRST(M) 和 LAST(M) 还可能包含一个特殊的非标记元素 ε ("epsilon") ，它表示 M 可以匹配空片段。(但 FOLLOW(M) 总是由一组标记组成。)
{==+==}


{==+==}
Informally:

  * FIRST(M): collects the tokens potentially used first when matching a
    fragment to M.
{==+==}
非正式地说：

  * FIRST(M)：在将片段与 M 匹配时，收集可能首先使用的标记。
{==+==}


{==+==}
  * LAST(M): collects the tokens potentially used last when matching a fragment
    to M.
{==+==}
  * LAST(M)：在将片段与 M 匹配时，收集可能最后使用的标记。
{==+==}


{==+==}
  * FOLLOW(M): the set of tokens allowed to follow immediately after some
    fragment matched by M.
{==+==}
  * FOLLOW(M)：在 M 匹配某个片段之后立即允许其后面紧跟的标记集合。
{==+==}


{==+==}
    In other words: t ∈ FOLLOW(M) if and only if there exists (potentially
    empty) token sequences α, β, γ, δ where:
{==+==}
    换句话说：当且仅当存在 (可能为空的) 标记序列 α β γ δ 时，t ∈ FOLLOW(M)，其中：
{==+==}


{==+==}
      * M matches β,

      * t matches γ, and

      * The concatenation α β γ δ is a parseable Rust program.
{==+==}
  * M 匹配 β，
  
  * t 匹配 γ ，以及
  
  * 连接 α β γ δ 是一个可解析的 Rust 程序。 
{==+==}


{==+==}
We use the shorthand ANYTOKEN to denote the set of all tokens (including simple
NTs). For example, if any token is legal after a matcher M, then FOLLOW(M) =
ANYTOKEN.
{==+==}
我们使用简写 ANYTOKEN 来表示所有标记 (包括简单的 NT )。例如，如果在匹配器 M 之后任何标记都是合法的，则 FOLLOW(M) = ANYTOKEN 。
{==+==}


{==+==}
(To review one's understanding of the above informal descriptions, the reader
at this point may want to jump ahead to the [examples of
FIRST/LAST](#examples-of-first-and-last) before reading their formal
definitions.)
{==+==}
 (为了回顾对上述非正式描述的理解，读者此时可能希望跳转到 [FIRST/LAST的示例](#examples-of-first-and-last) ，然后再阅读它们的正式定义。) 
{==+==}


{==+==}
### FIRST, LAST
{==+==}
### FIRST, LAST
{==+==}


{==+==}
Below are formal inductive definitions for FIRST and LAST.
{==+==}
以下是 FIRST 和 LAST 的正式归纳定义。
{==+==}


{==+==}
"A ∪ B" denotes set union, "A ∩ B" denotes set intersection, and "A \ B"
denotes set difference (i.e. all elements of A that are not present in B).
{==+==}
"A ∪ B" 表示并集， "A ∩ B" 表示交集， "A \ B" 表示差集 (即 A 中所有在 B 中不存在的元素)。
{==+==}


{==+==}
#### FIRST
{==+==}
#### FIRST
{==+==}


{==+==}
FIRST(M) is defined by case analysis on the sequence M and the structure of its
first token-tree (if any):
{==+==}
对于序列 M 及其第一个标记树的结构 (如果有) ，对 FIRST(M) 进行情况分析定义：
{==+==}


{==+==}
  * if M is the empty sequence, then FIRST(M) = { ε },

  * if M starts with a token t, then FIRST(M) = { t },
{==+==}
  * 如果 M 是空序列，则 FIRST(M) = { ε }，

  * 如果 M 以一个标记 t 开头，则 FIRST(M) = { t } ，
{==+==}


{==+==}
    (Note: this covers the case where M starts with a delimited token-tree
    sequence, `M = OPEN tt ... CLOSE ...`, in which case `t = OPEN` and thus
    FIRST(M) = { `OPEN` }.)
{==+==}
    (注意：这涵盖了 M 以定界的标记树序列开头的情况， `M = OPEN tt ... CLOSE ...` ，在这种情况下，`t = OPEN`，因此 FIRST(M) = { `OPEN` }。)
{==+==}


{==+==}
    (Note: this critically relies on the property that no simple NT matches the
    empty fragment.)
{==+==}
    (注意：这主要依赖于一个属性，即没有简单的 NT 可以匹配空片段。)
{==+==}


{==+==}
  * Otherwise, M is a token-tree sequence starting with a complex NT: `M = $( tt
    ... ) OP α`, or `M = $( tt ... ) SEP OP α`, (where `α` is the (potentially
    empty) sequence of token trees for the rest of the matcher).
{==+==}
  * 否则， M 是以复杂的 NT 开头的标记树序列： `M = $( tt ... ) OP α` 或 `M = $( tt ... ) SEP OP α` ， (其中 `α` 是匹配器的其余部分的 (可能为空的) 标记树序列)。
{==+==}


{==+==}
      * Let SEP\_SET(M) = { SEP } if SEP is present and ε ∈ FIRST(`tt ...`);
        otherwise SEP\_SET(M) = {}.
{==+==}
      * 如果 SEP 存在且 ε ∈ FIRST(`tt ...`) ，则让 SEP_SET(M) = { SEP }; 否则，SEP_SET(M) = {}。
{==+==}


{==+==}
  * Let ALPHA\_SET(M) = FIRST(`α`) if OP = `*` or `?` and ALPHA\_SET(M) = {} if
    OP = `+`.
  * FIRST(M) = (FIRST(`tt ...`) \\ {ε}) ∪ SEP\_SET(M) ∪ ALPHA\_SET(M).
{==+==}
  * 如果 OP = `*` 或 `?` ，则让 ALPHA\_SET(M) = FIRST(`α`) ，如果 OP = `+`，则 ALPHA\_SET(M) = {}。
  * FIRST(M) = (FIRST(`tt ...`) \\ {ε}) ∪ SEP\_SET(M) ∪ ALPHA\_SET(M) 。
{==+==}


{==+==}
The definition for complex NTs deserves some justification. SEP\_SET(M) defines
the possibility that the separator could be a valid first token for M, which
happens when there is a separator defined and the repeated fragment could be
empty. ALPHA\_SET(M) defines the possibility that the complex NT could be empty,
meaning that M's valid first tokens are those of the following token-tree
sequences `α`. This occurs when either `*` or `?` is used, in which case there
could be zero repetitions. In theory, this could also occur if `+` was used with
a potentially-empty repeating fragment, but this is forbidden by the third
invariant.
{==+==}
复杂非终端符的定义需要一些解释。 SEP_SET (M) 定义了分隔符可能是 M 的有效首个符号的可能性，当定义了分隔符并且重复片段可能为空时会发生这种情况。 ALPHA_SET (M) 定义了复杂非终端符可以为空的可能性，这意味着 M 的有效首个符号是以下 token-tree 序列 `α` 中的符号。当使用 `*` 或 `?` 时会发生这种情况，此时可能没有重复。理论上，如果使用 `+` 并带有潜在为空的重复片段，则也可能发生这种情况，但这被第三个不变量禁止。
{==+==}


{==+==}
From there, clearly FIRST(M) can include any token from SEP\_SET(M) or
ALPHA\_SET(M), and if the complex NT match is nonempty, then any token starting
FIRST(`tt ...`) could work too. The last piece to consider is ε. SEP\_SET(M) and
FIRST(`tt ...`) \ {ε} cannot contain ε, but ALPHA\_SET(M) could. Hence, this
definition allows M to accept ε if and only if ε ∈ ALPHA\_SET(M) does. This is
correct because for M to accept ε in the complex NT case, both the complex NT
and α must accept it. If OP = `+`, meaning that the complex NT cannot be empty,
then by definition ε ∉ ALPHA\_SET(M). Otherwise, the complex NT can accept zero
repetitions, and then ALPHA\_SET(M) = FOLLOW(`α`). So this definition is correct
with respect to \varepsilon as well.
{==+==}
从这里开始，显然 FIRST(M) 可以包括来自 SEP\_SET(M) 或 ALPHA\_SET(M) 的任何令牌，如果复合 NT 匹配非空，则任何以 FIRST(`tt ...`) 开头的令牌也可以使用。考虑的最后一个部分是 ε 。 SEP\_SET(M) 和 FIRST(`tt ...`) \ {ε}不能包含 ε，但 ALPHA\_SET(M) 可能会包含。
因此，这个定义允许 M 接受 ε ，当且仅当 ε∈ALPHA\_SET(M) 。这是正确的，因为对于 M 在复合 NT 情况下接受 ε ，复合 NT 和 α 都必须接受它。
如果 OP = `+` ，意味着复合 NT 不能为空，根据定义， ε∉ALPHA\_SET(M) 。
否则，复合 NT 可以接受零次重复，然后 ALPHA\_SET(M)=FOLLOW(`α`) 。因此，这个定义在 ε 方面是正确的。 
{==+==}


{==+==}
#### LAST
{==+==}
#### LAST
{==+==}


{==+==}
LAST(M), defined by case analysis on M itself (a sequence of token-trees):
{==+==}
LAST(M) 是根据 M 本身 (一个令牌树序列) 的情况分析定义的。
{==+==}


{==+==}
  * if M is the empty sequence, then LAST(M) = { ε }

  * if M is a singleton token t, then LAST(M) = { t }

  * if M is the singleton complex NT repeating zero or more times, `M = $( tt
    ... ) *`, or `M = $( tt ... ) SEP *`
{==+==}
  * 如果 M 是空序列，则 LAST(M) = { ε } 。
  * 如果 M 是单个令牌 t ，则 LAST(M) = { t } 。
  * 如果 M 是单一的复合 NT ，重复出现零次或多次，如 `M = $( tt ... ) *` 或 `M = $( tt ... ) SEP *`，则 LAST(M)={LAST('tt ...')}。
{==+==}


{==+==}
      * Let sep_set = { SEP } if SEP present; otherwise sep_set = {}.

      * if ε ∈ LAST(`tt ...`) then LAST(M) = LAST(`tt ...`) ∪ sep_set

      * otherwise, the sequence `tt ...` must be non-empty; LAST(M) = LAST(`tt
        ...`) ∪ {ε}.
{==+==}
      * 如果存在 SEP ，则令 sep_set = { SEP } ；否则，令 sep_set = {} 。
      * 如果 ε ∈ LAST(`tt ...`) ，则 LAST(M) = LAST(`tt ...`) ∪ sep_set 。
      * 否则，序列'tt ...'必须是非空的；LAST(M) = LAST(`tt ...`) ∪ {ε} 。
{==+==}


{==+==}
  * if M is the singleton complex NT repeating one or more times, `M = $( tt ...
    ) +`, or `M = $( tt ... ) SEP +`

      * Let sep_set = { SEP } if SEP present; otherwise sep_set = {}.

      * if ε ∈ LAST(`tt ...`) then LAST(M) = LAST(`tt ...`) ∪ sep_set

      * otherwise, the sequence `tt ...` must be non-empty; LAST(M) = LAST(`tt
        ...`)
{==+==}
  * 如果 M 是重复出现一次或多次的单一复合 NT ，如 `M = $( tt ... ) +` 或 `M = $( tt ... ) SEP +` ，
    * 如果存在 SEP ，则令 sep_set = { SEP } ；否则，令s ep_set = {} 。
    * 如果 ε ∈ LAST(`tt ...`) ，则 LAST(M) = LAST(`tt ...`) ∪ sep_set 。
    * 否则，序列 `tt ...` 必须是非空的；LAST(M) = LAST('tt ...')。
{==+==}


{==+==}
  * if M is the singleton complex NT repeating zero or one time, `M = $( tt ...)
    ?`, then LAST(M) = LAST(`tt ...`) ∪ {ε}.

  * if M is a delimited token-tree sequence `OPEN tt ... CLOSE`, then LAST(M) =
    { `CLOSE` }.
{==+==}
  * 如果 M 是重复出现零次或一次的单一复合 NT ，如 `M = $( tt ...) ?`，则 LAST(M) = LAST(`tt ...`) ∪ {ε} 。
  * 如果 M 是一个定界的标记树序列 `OPEN tt ... CLOSE` ，则 LAST(M) = { `CLOSE` }。
{==+==}


{==+==}
  * if M is a non-empty sequence of token-trees `tt uu ...`,

      * If ε ∈ LAST(`uu ...`), then LAST(M) = LAST(`tt`) ∪ (LAST(`uu ...`) \ { ε }).

      * Otherwise, the sequence `uu ...` must be non-empty; then LAST(M) =
        LAST(`uu ...`).
{==+==}
  * 如果 M 是一个非空的令牌树的序列 `tt uu ...` ，

      * 如果 ε ∈ LAST(`uu ...`) ，那么 LAST(M) = LAST(`tt`) ∪ (LAST(`uu ...`) \ { ε }) .

      * 否则，序列 `uu ...` 必须是非空的；那么 LAST(M) = LAST(`uu ...`) 。
{==+==}


{==+==}
### Examples of FIRST and LAST
{==+==}
### FIRST和LAST的例子
{==+==}


{==+==}
Below are some examples of FIRST and LAST.
(Note in particular how the special ε element is introduced and
eliminated based on the interaction between the pieces of the input.)
{==+==}
以下是一些 FIRST 和 LAST 的例子。
(特别注意，基于输入的不同部分之间的交互，特殊元素 ε 是如何被引入和消除的。)
{==+==}


{==+==}
Our first example is presented in a tree structure to elaborate on how
the analysis of the matcher composes. (Some of the simpler subtrees
have been elided.)
{==+==}
我们的第一个例子以树形结构呈现，以详细阐述匹配器的分析是如何组合的。 (一些简单的子树已省略。)
{==+==}


{==+==}
```text
INPUT:  $(  $d:ident   $e:expr   );*    $( $( h )* );*    $( f ; )+   g
            ~~~~~~~~   ~~~~~~~                ~
                |         |                   |
FIRST:   { $d:ident }  { $e:expr }          { h }


INPUT:  $(  $d:ident   $e:expr   );*    $( $( h )* );*    $( f ; )+
            ~~~~~~~~~~~~~~~~~~             ~~~~~~~           ~~~
                        |                      |               |
FIRST:          { $d:ident }               { h, ε }         { f }

INPUT:  $(  $d:ident   $e:expr   );*    $( $( h )* );*    $( f ; )+   g
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    ~~~~~~~~~~~~~~    ~~~~~~~~~   ~
                        |                       |              |       |
FIRST:        { $d:ident, ε }            {  h, ε, ;  }      { f }   { g }


INPUT:  $(  $d:ident   $e:expr   );*    $( $( h )* );*    $( f ; )+   g
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
                                        |
FIRST:                       { $d:ident, h, ;,  f }
```
{==+==}

{==+==}


{==+==}
Thus:

 * FIRST(`$($d:ident $e:expr );* $( $(h)* );* $( f ;)+ g`) = { `$d:ident`, `h`, `;`, `f` }
{==+==}
因此:

 * FIRST(`$($d:ident $e:expr );* $( $(h)* );* $( f ;)+ g`) = { `$d:ident`, `h`, `;`, `f` }
{==+==}


{==+==}
Note however that:

 * FIRST(`$($d:ident $e:expr );* $( $(h)* );* $($( f ;)+ g)*`) = { `$d:ident`, `h`, `;`, `f`, ε }
{==+==}
值得注意:

 * FIRST(`$($d:ident $e:expr );* $( $(h)* );* $($( f ;)+ g)*`) = { `$d:ident`, `h`, `;`, `f`, ε }
{==+==}


{==+==}
Here are similar examples but now for LAST.
{==+==}
以下是类似的例子，但现在是关于 LAST 的。
{==+==}


{==+==}
 * LAST(`$d:ident $e:expr`) = { `$e:expr` }
 * LAST(`$( $d:ident $e:expr );*`) = { `$e:expr`, ε }
 * LAST(`$( $d:ident $e:expr );* $(h)*`) = { `$e:expr`, ε, `h` }
 * LAST(`$( $d:ident $e:expr );* $(h)* $( f ;)+`) = { `;` }
 * LAST(`$( $d:ident $e:expr );* $(h)* $( f ;)+ g`) = { `g` }
{==+==}

{==+==}


{==+==}
### FOLLOW(M)
{==+==}

{==+==}


{==+==}
Finally, the definition for FOLLOW(M) is built up as follows. pat, expr, etc.
represent simple nonterminals with the given fragment specifier.
{==+==}
最后， FOLLOW(M) 的定义如下构建。 pat ， expr 等表示具有给定片段说明符的简单非终端符。
{==+==}


{==+==}
  * FOLLOW(pat) = {`=>`, `,`, `=`, `|`, `if`, `in`}`.

  * FOLLOW(expr) = FOLLOW(stmt) =  {`=>`, `,`, `;`}`.
{==+==}

{==+==}


{==+==}
  * FOLLOW(ty) = FOLLOW(path) = {`{`, `[`, `,`, `=>`, `:`, `=`, `>`, `>>`, `;`,
    `|`, `as`, `where`, block nonterminals}.

  * FOLLOW(vis) = {`,`l any keyword or identifier except a non-raw `priv`; any
    token that can begin a type; ident, ty, and path nonterminals}.
{==+==}
  * FOLLOW(ty) = FOLLOW(path) = {`{`, `[`, `,`, `=>`, `:`, `=`, `>`, `>>`, `;`,`|`, `as`, `where`, 块非终端符}。

  * FOLLOW(vis) = { `,` l 除非是非原始的 `priv` ，任何关键字或标识符; 可以开始类型的任何令牌; ident ， ty 和 path 非终端符}。
{==+==}


{==+==}
  * FOLLOW(t) = ANYTOKEN for any other simple token, including block, ident,
    tt, item, lifetime, literal and meta simple nonterminals, and all terminals.

  * FOLLOW(M), for any other M, is defined as the intersection, as t ranges over
    (LAST(M) \ {ε}), of FOLLOW(t).
{==+==}
  * 对于任何其他简单的 token ，包括 block 、 ident 、 tt 、 item 、 lifetime 、 literal 和 meta 的简单非终端符以及所有终端符， FOLLOW(t) = ANYTOKEN 。

  * 对于任何其他 M ， FOLLOW(M) 的定义是对 (LAST(M)\{ε}) 的所有 t 的 FOLLOW(t) 的交集，其中 t 在终端符中范围。
{==+==}


{==+==}
The tokens that can begin a type are, as of this writing, {`(`, `[`, `!`, `*`,
`&`, `&&`, `?`, lifetimes, `>`, `>>`, `::`, any non-keyword identifier, `super`,
`self`, `Self`, `extern`, `crate`, `$crate`, `_`, `for`, `impl`, `fn`, `unsafe`,
`typeof`, `dyn`}, although this list may not be complete because people won't
always remember to update the appendix when new ones are added.
{==+==}
可以开始类型的令牌是，截至本写作时，为 {`(`、`[`、`!`、`*`、`&`、`&&`、`?`、lifetime、`>`、`>>`、`::`、任何非关键字标识符、`super`、`self`、`Self`、`extern`、`crate`、`$crate`、`_`、`for`、`impl`、`fn`、`unsafe`、`typeof`、`dyn`}，尽管此列表可能不完整，因为人们不会总是记得在添加新令牌时更新附录。
{==+==}


{==+==}
Examples of FOLLOW for complex M:
{==+==}
复杂 M 的 FOLLOW 的例子:
{==+==}


{==+==}
 * FOLLOW(`$( $d:ident $e:expr )*`) = FOLLOW(`$e:expr`)
 * FOLLOW(`$( $d:ident $e:expr )* $(;)*`) = FOLLOW(`$e:expr`) ∩ ANYTOKEN = FOLLOW(`$e:expr`)
 * FOLLOW(`$( $d:ident $e:expr )* $(;)* $( f |)+`) = ANYTOKEN
{==+==}

{==+==}


{==+==}
### Examples of valid and invalid matchers
{==+==}
### 有效和无效匹配器的示例
{==+==}


{==+==}
With the above specification in hand, we can present arguments for
why particular matchers are legal and others are not.
{==+==}
有了上述规范，我们可以提出为什么特定匹配器合法而其他匹配器不合法的论据。
{==+==}


{==+==}
 * `($ty:ty < foo ,)` : illegal, because FIRST(`< foo ,`) = { `<` } ⊈ FOLLOW(`ty`)

 * `($ty:ty , foo <)` : legal, because FIRST(`, foo <`) = { `,` }  is ⊆ FOLLOW(`ty`).

 * `($pa:pat $pb:pat $ty:ty ,)` : illegal, because FIRST(`$pb:pat $ty:ty ,`) = { `$pb:pat` } ⊈ FOLLOW(`pat`), and also FIRST(`$ty:ty ,`) = { `$ty:ty` } ⊈ FOLLOW(`pat`).
{==+==}
 * `($ty:ty < foo ,)` : 不合法, 因为 FIRST(`< foo ,`) = { `<` } ⊈ FOLLOW(`ty`)

 * `($ty:ty , foo <)` : 合法, 因为 FIRST(`, foo <`) = { `,` }  is ⊆ FOLLOW(`ty`).

 * `($pa:pat $pb:pat $ty:ty ,)` : 不合法, 因为 FIRST(`$pb:pat $ty:ty ,`) = { `$pb:pat` } ⊈ FOLLOW(`pat`), 而且 FIRST(`$ty:ty ,`) = { `$ty:ty` } ⊈ FOLLOW(`pat`).
{==+==}


{==+==}
 * `( $($a:tt $b:tt)* ; )` : legal, because FIRST(`$b:tt`) = { `$b:tt` } is ⊆ FOLLOW(`tt`) = ANYTOKEN, as is FIRST(`;`) = { `;` }.

 * `( $($t:tt),* , $(t:tt),* )` : legal,  (though any attempt to actually use this macro will signal a local ambiguity error during expansion).
{==+==}
 * `( $($a:tt $b:tt)* ; )` : 合法, 因为 FIRST(`$b:tt`) = { `$b:tt` } is ⊆ FOLLOW(`tt`) = ANYTOKEN, as is FIRST(`;`) = { `;` }.

 * `( $($t:tt),* , $(t:tt),* )` : 合法,  (尽管任何尝试实际使用此宏都将在扩展期间发出本地歧义错误的信号。).
{==+==}


{==+==}
 * `($ty:ty $(; not sep)* -)` : illegal, because FIRST(`$(; not sep)* -`) = { `;`, `-` } is not in FOLLOW(`ty`).

 * `($($ty:ty)-+)` : illegal, because separator `-` is not in FOLLOW(`ty`).

 * `($($e:expr)*)` : illegal, because expr NTs are not in FOLLOW(expr NT).
{==+==}
* `($ty:ty $(; not sep)* -)` : 不合法，因为 FIRST(`$(; not sep)* -`) = { `;`, `-` } 不在 FOLLOW(`ty`) 中。

* `($($ty:ty)-+)`：不合法，因为分隔符 `-` 不在 FOLLOW(`ty`) 中。

* `($($e:expr)*)`：不合法，因为表达式 NT 不在 FOLLOW(expr NT) 中。
{==+==}


{==+==}
[Macros by Example]: macros-by-example.md
[RFC 550]: https://github.com/rust-lang/rfcs/blob/master/text/0550-macro-future-proofing.md
[tracking issue]: https://github.com/rust-lang/rust/issues/56575
{==+==}

{==+==}