{==+==}
# Introduction
{==+==}
# 介绍
{==+==}


{==+==}
This book is the primary reference for the Rust programming language.
It provides three kinds of material:
{==+==}
本书是Rust编程语言的主要参考资料。
提供三个部分:
{==+==}


{==+==}
  - Chapters that informally describe each language construct and their use.
  - Chapters that informally describe the memory model, concurrency model, runtime services, linkage model, and debugging facilities.
  - Appendix chapters providing rationale and references to languages that influenced the design.
{==+==}
  - 简略的描述语言构成和用途的章节。
  - 简略的描述内存模型、并发模型、运行时服务、链接模型和调试工具的章节。
  - 附录章节提供影响设计的语言的原理和参考。
{==+==}


{==+==}
Warning:
This book is incomplete. Documenting everything takes a while.
See the [GitHub issues] for what is not documented in this book.
{==+==}
提醒:
这本书还不完整。记录所有内容还需要一段时间。
关于本书中没有记录的内容，请参见[GitHub issues]。
{==+==}


{==+==}
## Rust releases
{==+==}
## Rust 发布
{==+==}


{==+==}
Rust has a new language release every six weeks.
The first stable release of the language was Rust 1.0.0, followed by Rust 1.1.0 and so on.
Tools (`rustc`, `cargo`, etc.) and documentation ([Standard library], this book, etc.) are released with the language release.
{==+==}
Rust每六周有一个新的语言版本。
该语言的第一个稳定版本是Rust 1.0.0，随后是Rust 1.1.0，以此类推。
工具( `rustc` 、 `cargo` 等)和文档( [Standard library] 、本书等) 随语言发布而发布。
{==+==}


{==+==}
The latest release of this book, matching the latest Rust version, can always be found at <https://doc.rust-lang.org/reference/>.
Prior versions can be found by adding the Rust version before the "reference" directory.
For example, the Reference for Rust 1.49.0 is located at <https://doc.rust-lang.org/1.49.0/reference/>.
{==+==}
本书的最新版本，与最新的Rust版本相匹配，总是可以在 <https://doc.rust-lang.org/reference/> 找到。
之前的版本可以通过在 "reference" 目录前添加Rust版本来找到。
例如，Rust 1.49.0的参考位于<https://doc.rust-lang.org/1.49.0/reference/>。
{==+==}


{==+==}
## What *The Reference* is not
{==+==}
## *参考* 并非如此
{==+==}


{==+==}
This book does not serve as an introduction to the language.
Background familiarity with the language is assumed.
A separate [book] is available to help acquire such background familiarity.
{==+==}
本书不提供语言的介绍。
假设是熟悉语言的背景，有一本单独的 [book] 会更有帮助。
{==+==}


{==+==}
This book also does not serve as a reference to the [standard library] included in the language distribution.
Those libraries are documented separately by extracting documentation attributes from their source code.
Many of the features that one might expect to be language features are library features in Rust, so what you're looking for may be there, not here.
{==+==}
本书也不提供语言发行中包含的 [standard library] "标准库"的参考。
那些库的参考是通过从其源码中提取文档属性而单独记录的。
许多人可能认为的语言特性的功能，而在Rust中是库的特性，所以你要找的内容可能在那里，而不在本书中。
{==+==}


{==+==}
Similarly, this book does not usually document the specifics of `rustc` as a tool or of Cargo.
`rustc` has its own [book][rustc book].
Cargo has a [book][cargo book] that contains a [reference][cargo reference].
There are a few pages such as [linkage] that still describe how `rustc` works.
{==+==}
同样，这本书通常不记录 `rustc` 编译工具和 Cargo 的具体内容。
`rustc` 有自已的 [book][rustc book] 。
Cargo 有一本 [book][cargo book] ，其中包含具体[参考][cargo reference] 。
有一些页面，如 [linkage] ，也描述了 `rustc` 的工作方式。
{==+==}


{==+==}
This book also only serves as a reference to what is available in stable Rust.
For unstable features being worked on, see the [Unstable Book].
{==+==}
本书仅提供稳定版Rust的参考。
对于正在进行的不稳定的功能，请参见 [Unstable Book]。
{==+==}


{==+==}
Rust compilers, including `rustc`, will perform optimizations.
The reference does not specify what optimizations are allowed or disallowed.
Instead, think of the compiled program as a black box.
You can only probe by running it, feeding it input and observing its output.
Everything that happens that way must conform to what the reference says.
{==+==}
Rust 编译器，包括 `rustc` ，会进行优化。
该参考并没有指定允许或不允许哪些优化。
而是，把编译后的程序看作是黑盒。
你只能通过运行它，给它提供输入，并观察它的输出，来进行检查。
这样所产生的内容都必须符合参考中的规定。
{==+==}


{==+==}
Finally, this book is not normative.
It may include details that are specific to `rustc` itself, and should not be taken as a specification for the Rust language.
We intend to produce such a book someday, and until then, the reference is the closest thing we have to one.
{==+==}
最后，本书不是规范性的。
它可能包括 `rustc` 的特有细节，不应看作是 Rust 的语言规范。
我们打算制作这样一本书，在那之前，这本参考书的内容最接近。
{==+==}


{==+==}
## How to use this book
{==+==}
## 如何使用本书
{==+==}


{==+==}
This book does not assume you are reading this book sequentially.
Each chapter generally can be read standalone, but will cross-link to other chapters for facets of the language they refer to, but do not discuss.
{==+==}
本书并不假定你是按顺序阅读的。
每章一般可以独立阅读，但也会与其他章节交叉链接，从而了解所提到的、但没有具体讨论的语言的方面特征。
{==+==}


{==+==}
There are two main ways to read this document.
{==+==}
阅读本书有两种主要方式。
{==+==}


{==+==}
The first is to answer a specific question.
If you know which chapter answers that question, you can jump to that chapter in the table of contents.
Otherwise, you can press `s` or click the magnifying glass on the top bar to search for keywords related to your question.
For example, say you wanted to know when a temporary value created in a let statement is dropped.
If you didn't already know that the [lifetime of temporaries] is defined in the [expressions chapter], you could search "temporary let" and the first search result will take you to that section.
{==+==}
首先是解答具体的问题。
如果你知道哪一章解答了这个问题，你可以通过目录跳到该章。
或者，你可以按 `s` 键或点击顶部栏的放大镜来搜索与问题相关的关键词。
比如，假设你想知道在let语句中创建的临时值何时被删除。
如果你还不知道[expressions chapter] "表达式章节"中解释了 [lifetime of temporaries] "临时变量寿命"，你可以搜索 "temporary let"，第一个搜索结果会为你提示那一节。
{==+==}


{==+==}
The second is to generally improve your knowledge of a facet of the language.
In that case, just browse the table of contents until you see something you want to know more about, and just start reading.
If a link looks interesting, click it, and read about that section.
{==+==}
第二种是广泛的提高你对语言某个方面的知识。
这时，可以浏览了解目录内容。
点开感兴趣的链接，然后阅读该部分内容。
{==+==}


{==+==}
That said, there is no wrong way to read this book. Read it however you feel helps you best.
{==+==}
也就是说，阅读本书的方式不是确定的。用你觉得帮助最大的方式阅读吧。
{==+==}


{==+==}
### Conventions
{==+==}
### 惯例
{==+==}


{==+==}
Like all technical books, this book has certain conventions in how it displays information.
These conventions are documented here.
{==+==}
对于技术书籍，本书在如何表达信息方面有一些惯例。
这里记录了这些惯例。
{==+==}


{==+==}
* Statements that define a term contain that term in *italics*.
  Whenever that term is used outside of that chapter, it is usually a link to the section that has this definition.
{==+==}
* 解释一个术语的语句将该术语包含在 *斜体* 。
  每当该章之外使用该术语时，通常是链接指向有此定义的章节。
{==+==}


{==+==}
  An *example term* is an example of a term being defined.
{==+==}
  解释的term的一个例子 *example term* 。
{==+==}


{==+==}
* Differences in the language by which edition the crate is compiled under are in a blockquote that start with the words "Edition Differences:" in **bold**.
{==+==}
* 在语言上的差异是由哪个版次编译的crate，用版次差异 "Edition Differences:" 为开头的 **粗体** 引用块。
{==+==}


{==+==}
  > **Edition Differences**: In the 2015 edition, this syntax is valid that is disallowed as of the 2018 edition.
{==+==}
  > **Edition Differences**: 在2015版中，这种语法是有效的，从2018版开始不允许了。
{==+==}


{==+==}
* Notes that contain useful information about the state of the book or point out useful, but mostly out of scope, information are in blockquotes that start with the word "Note:" in **bold**.
{==+==}
* 含有关于书的状态的有用信息，或是指出有用的、但大多不在范围内的注释信息，在以 "Note:" 开头的 **粗体** 引用块中。
{==+==}


{==+==}
  > **Note**: This is an example note.
{==+==}
  > **Note**: 这是一个说明示例。
{==+==}


{==+==}
* Warnings that show unsound behavior in the language or possibly confusing interactions of language features are in a special warning box.
{==+==}
* 显示语言中不健全的行为或可能引起混淆的语言特性，其警告信息在特定的警告框中。
{==+==}


{==+==}
  Warning: This is an example warning.
{==+==}
  Warning: 这是一个警告的示例。
{==+==}


{==+==}
* Code snippets inline in the text are inside `<code>` tags.
{==+==}
* 文本中的内联代码片段位于 `<code>` 标签内。
{==+==}


{==+==}
  Longer code examples are in a syntax highlighted box that has controls for copying, executing, and showing hidden lines in the top right corner.
{==+==}
  较长的代码示例在一个语法高亮框中，右上角可控制复制、执行和显示隐藏行。
{==+==}


{==+==}
  ```rust
  # // This is a hidden line.
  fn main() {
      println!("This is a code example");
  }
  ```
{==+==}

{==+==}


{==+==}
  All examples are written for the latest edition unless otherwise stated.
{==+==}
  除非另有说明，所有示例都是最新版次编写的。
{==+==}


{==+==}
* The grammar and lexical structure is in blockquotes with either "Lexer" or "Syntax" in <sup>**bold superscript**</sup> as the first line.
{==+==}
* 语法和词法结构以 <sup>**bold superscript**</sup> 中的 "Lexer" 或 "Syntax" 为首行，采用引用块。
{==+==}


{==+==}
  > **<sup>Syntax</sup>**\
  > _ExampleGrammar_:\
  > &nbsp;&nbsp; &nbsp;&nbsp; `~` [_Expression_]\
  > &nbsp;&nbsp; | `box` [_Expression_]
{==+==}

{==+==}


{==+==}
  See [Notation] for more detail.
{==+==}
  更多信息见 [Notation] 。
{==+==}


{==+==}
## Contributing
{==+==}
## 贡献
{==+==}


{==+==}
We welcome contributions of all kinds.
{==+==}
我们非常欢迎各类贡献。
{==+==}


{==+==}
You can contribute to this book by opening an issue or sending a pull request to [the Rust Reference repository].
If this book does not answer your question, and you think its answer is in scope of it, please do not hesitate to [file an issue] or ask about it in the `t-lang/doc` stream on [Zulip].
Knowing what people use this book for the most helps direct our attention to making those sections the best that they can be.
We also want the reference to be as normative as possible, so if you see anything that is wrong or is non-normative but not specifically called out, please also [file an issue].
{==+==}
你可以通过向[the Rust Reference repository]开设问题或pull request来为本书做贡献。
如果本书没有回答你的问题，而你认为其答案应包含本书之中，请不要犹豫，[file an issue] 或在 [Zulip] 的 `t-lang/doc` stream 中询问。
了解本书中使用最多内容有助于更加注重，从使进一步完善这些部分。
我们也希望参考尽可能的规范化，所以如果你发现任何错误内容，或者非规范的但没有特别指出的内容，也请 [file an issue] "提一个issue"。
{==+==}


{==+==}
[book]: ../book/index.html
[github issues]: https://github.com/rust-lang/reference/issues
[standard library]: ../std/index.html
[the Rust Reference repository]: https://github.com/rust-lang/reference/
[Unstable Book]: https://doc.rust-lang.org/nightly/unstable-book/
[_Expression_]: expressions.md
[cargo book]: ../cargo/index.html
[cargo reference]: ../cargo/reference/index.html
[expressions chapter]: expressions.html
[file an issue]: https://github.com/rust-lang/reference/issues
[lifetime of temporaries]: expressions.html#temporaries
[linkage]: linkage.html
[rustc book]: ../rustc/index.html
[Notation]: notation.md
[Zulip]: https://rust-lang.zulipchat.com/#narrow/stream/237824-t-lang.2Fdoc
{==+==}

{==+==}