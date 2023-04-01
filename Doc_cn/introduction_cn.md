{==+==}
# Introduction
{==+==}
# 介绍
{==+==}


{==+==}
This book is the primary reference for the Rust programming language.
It provides three kinds of material:
{==+==}
这本书是 Rust 编程语言的主要参考资料，提供了三种类型的内容：
{==+==}


{==+==}
  - Chapters that informally describe each language construct and their use.
  - Chapters that informally describe the memory model, concurrency model, runtime services, linkage model, and debugging facilities.
  - Appendix chapters providing rationale and references to languages that influenced the design.
{==+==}
  - 介绍语言构造及使用的章节。
  - 介绍内存模型、并发模型、运行时服务、链接模型和调试功能的章节。
  - 附录章节提供相关设计的基本原理和其他对设计有影响的语言的参考资料。
{==+==}


{==+==}
Warning:
This book is incomplete. Documenting everything takes a while.
See the [GitHub issues] for what is not documented in this book.
{==+==}
提醒:
该书是不完整的。记录所有内容还需要一段时间。
可以查看 [GitHub issues] 了解本书未记录的内容。
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
Rust 每六周发布一个新的语言版本。
第一个稳定版本是 Rust 1.0.0 ，之后是 Rust 1.1.0 等等。
工具( `rustc` 、 `cargo` 等) 和文档 ( [标准库][Standard library] 、本书等) 都随着语言版本一起发布。
{==+==}


{==+==}
The latest release of this book, matching the latest Rust version, can always be found at <https://doc.rust-lang.org/reference/>.
Prior versions can be found by adding the Rust version before the "reference" directory.
For example, the Reference for Rust 1.49.0 is located at <https://doc.rust-lang.org/1.49.0/reference/>.
{==+==}
本书的最新版本，与最新的 Rust 版本相匹配，始终可以在 <https://doc.rust-lang.org/reference/> 找到。
以前的版本可以通过在 "reference" 前添加 Rust 版本来找到。
例如， Rust 1.49.0 的参考资料位于 <https://doc.rust-lang.org/1.49.0/reference/> 。
{==+==}


{==+==}
## What *The Reference* is not
{==+==}
## *参考* 中没有的内容
{==+==}


{==+==}
This book does not serve as an introduction to the language.
Background familiarity with the language is assumed.
A separate [book] is available to help acquire such background familiarity.
{==+==}
本书不是 Rust 语言的入门教程，将假定读者已经对 Rust 有一定的了解。
如果需要学习 Rust，可以参考 Rust 官方提供的入门教程 [book] 。
{==+==}


{==+==}
This book also does not serve as a reference to the [standard library] included in the language distribution.
Those libraries are documented separately by extracting documentation attributes from their source code.
Many of the features that one might expect to be language features are library features in Rust, so what you're looking for may be there, not here.
{==+==}
本书也不作为语言发行版中所包含的 [标准库][standard library] 的参考文献。
这些库通过从源代码中提取文档属性来单独记录。
许多人可能期望是语言特性的功能在 Rust 中是库功能，所以您要查找的内容可能在标准库中，而不是本书中。
{==+==}


{==+==}
Similarly, this book does not usually document the specifics of `rustc` as a tool or of Cargo.
`rustc` has its own [book][rustc book].
Cargo has a [book][cargo book] that contains a [reference][cargo reference].
There are a few pages such as [linkage] that still describe how `rustc` works.
{==+==}
同样地，本书通常不会记录 `rustc` 和 Cargo 这两个工具的具体细节。
`rustc` 有其自己的 [book][rustc book] 。
Cargo 也有一本 [book][cargo book] ，其中包含 [reference][cargo reference]。
有一些页面如 [linkage] 仍然描述 `rustc` 的工作原理。
{==+==}


{==+==}
This book also only serves as a reference to what is available in stable Rust.
For unstable features being worked on, see the [Unstable Book].
{==+==}
本书仅作为稳定版本 Rust 可用功能的参考。
对于正在开发的不稳定特性，请参见 [Unstable Book]。
{==+==}


{==+==}
Rust compilers, including `rustc`, will perform optimizations.
The reference does not specify what optimizations are allowed or disallowed.
Instead, think of the compiled program as a black box.
You can only probe by running it, feeding it input and observing its output.
Everything that happens that way must conform to what the reference says.
{==+==}
Rust 编译器，包括 `rustc` ，会执行优化操作。
该参考手册没有明确指定允许或禁止哪些优化。
而是，将编译后的程序视为一个黑盒子。
您只能通过运行程序、提供输入和观察输出的方式来探测它。
这样发生的一切都必须符合参考手册所说的内容。
{==+==}


{==+==}
Finally, this book is not normative.
It may include details that are specific to `rustc` itself, and should not be taken as a specification for the Rust language.
We intend to produce such a book someday, and until then, the reference is the closest thing we have to one.
{==+==}
最后，这本书不是规范性的。
它可能包含一些特定于 `rustc` 的细节，不应将其视为 Rust 语言的规范。
我们打算有一天出版这样的书，但在那之前，该参考是拥有的最接近规范的东西。
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
本书不假设您是顺序阅读的。
每个章节通常都可以单独阅读，但会在其所涉及但未讨论的语言方面交叉链接到其他章节。
{==+==}


{==+==}
There are two main ways to read this document.
{==+==}
有两种主要的阅读本文的方式。
{==+==}


{==+==}
The first is to answer a specific question.
If you know which chapter answers that question, you can jump to that chapter in the table of contents.
Otherwise, you can press `s` or click the magnifying glass on the top bar to search for keywords related to your question.
For example, say you wanted to know when a temporary value created in a let statement is dropped.
If you didn't already know that the [lifetime of temporaries] is defined in the [expressions chapter], you could search "temporary let" and the first search result will take you to that section.
{==+==}
第一种是回答特定的问题。
如果你知道哪个章节可以回答这个问题，可以在目录中跳转到该章节。
否则，你可以按下 `s` 键或点击顶部栏上的放大镜搜索与您的问题相关的关键词。
例如，假设你想知道在 let 语句中创建的临时值何时被丢弃。
如果你不知道 [临时值的生命周期][lifetime of temporaries] 在 [表达式章节][expressions chapter] 中定义，您可以搜索 "temporary let" ，第一个搜索结果将带您到该部分。
{==+==}


{==+==}
The second is to generally improve your knowledge of a facet of the language.
In that case, just browse the table of contents until you see something you want to know more about, and just start reading.
If a link looks interesting, click it, and read about that section.
{==+==}
第二种方法是一般性地提高对语言某个方面的了解。
在这种情况下，只需浏览目录，直到看到您想更多了解的某个主题，然后开始阅读。
如果一个链接看起来有趣，点击它，阅读该部分的内容。
{==+==}


{==+==}
That said, there is no wrong way to read this book. Read it however you feel helps you best.
{==+==}
说实话，阅读本书没有标准的方法。你可以按章节阅读，也可以按关键词查找，甚至可以通过随意点击链接来学习 Rust 。我们建议你选择一种最适合你的方式阅读本书。
{==+==}


{==+==}
### Conventions
{==+==}
### 约定
{==+==}


{==+==}
Like all technical books, this book has certain conventions in how it displays information.
These conventions are documented here.
{==+==}
本书与所有技术书籍一样，具有在显示信息方面的特定约定。
这里记录了这些约定。
{==+==}


{==+==}
* Statements that define a term contain that term in *italics*.
  Whenever that term is used outside of that chapter, it is usually a link to the section that has this definition.
{==+==}
* 定义术语的语句中，该术语会以 *斜体* 的形式出现。
  在该章节之外的任何地方使用该术语时，通常都是指向包含此定义部分的链接。
{==+==}


{==+==}
  An *example term* is an example of a term being defined.
{==+==}
  如 *例子术语* 这样术语定义。
{==+==}


{==+==}
* Differences in the language by which edition the crate is compiled under are in a blockquote that start with the words "Edition Differences:" in **bold**.

{==+==}
* 在以 **粗体** 标识的块引用中，使用 "Edition Differences:" 这些字眼来说明不同版本下编译的 crate 的语言差异。
{==+==}


{==+==}
  > **Edition Differences**: In the 2015 edition, this syntax is valid that is disallowed as of the 2018 edition.
{==+==}
  > **Edition Differences**: 在 2015 版中，这个语法是有效的，但在 2018 版中不允许。
{==+==}


{==+==}
* Notes that contain useful information about the state of the book or point out useful, but mostly out of scope, information are in blockquotes that start with the word "Note:" in **bold**.
{==+==}
* "Note:" 为粗体的块引用，包含有关书的状态或指出有用但大多数超出范围的信息。
{==+==}


{==+==}
  > **Note**: This is an example note.
{==+==}
  > **Note**: 这是一个说明示例。
{==+==}


{==+==}
* Warnings that show unsound behavior in the language or possibly confusing interactions of language features are in a special warning box.
{==+==}
* 警告信息将显示为特殊的警告框，用于显示语言中的不安全行为或可能引起混淆的语言特性之间的交互。
{==+==}


{==+==}
  Warning: This is an example warning.
{==+==}
  Warning: 这是一个警告的示例。
{==+==}


{==+==}
* Code snippets inline in the text are inside `<code>` tags.
{==+==}
* 正文中的内联代码片段包含在 `<code>` 标签内。
{==+==}


{==+==}
  Longer code examples are in a syntax highlighted box that has controls for copying, executing, and showing hidden lines in the top right corner.
{==+==}
  更长的代码示例在一个语法高亮的框中展示，该框右上角有复制、执行和显示隐藏行的控制按钮。
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
  所有的示例都是针对最新版本的 Rust 编写的，除非另有说明。
{==+==}


{==+==}
* The grammar and lexical structure is in blockquotes with either "Lexer" or "Syntax" in <sup>**bold superscript**</sup> as the first line.
{==+==}
* 语法和词法结构出现在引用块中，第一行以 <sup>**粗体上标**</sup> 的形式出现 "Lexer" 或 "Syntax" 。
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
我们欢迎各种贡献。
{==+==}


{==+==}
You can contribute to this book by opening an issue or sending a pull request to [the Rust Reference repository].
If this book does not answer your question, and you think its answer is in scope of it, please do not hesitate to [file an issue] or ask about it in the `t-lang/doc` stream on [Zulip].
Knowing what people use this book for the most helps direct our attention to making those sections the best that they can be.
We also want the reference to be as normative as possible, so if you see anything that is wrong or is non-normative but not specifically called out, please also [file an issue].
{==+==}
您可以通过打开一个 issue 或发送一个 pull request 到 [Rust Reference repository] 做出贡献。
如果这本书没有回答您的问题，并且您认为该问题在必要范围内，请不要犹豫， [提交一个 issue][file an issue] 或在 [Zulip] 上的 `t-lang/doc` 频道提问。
知道人们最常使用这本书的内容有助于我们把精力集中在使这些部分变得更好上。
我们还希望这本参考资料尽可能符合规范，因此如果您发现任何错误或非规范但没有特别指出的问题，请 [提交一个 issue][file an issue] 。
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