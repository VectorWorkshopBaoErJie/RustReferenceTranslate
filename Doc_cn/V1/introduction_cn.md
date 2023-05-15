{==+==}
# Introduction
{==+==}
# 介绍
{==+==}


{==+==}
This book is the primary reference for the Rust programming language.
It provides three kinds of material:
{==+==}
本书是 Rust 编程语言的主要参考资料，所提供的内容为三类：
{==+==}


{==+==}
  - Chapters that informally describe each language construct and their use.
  - Chapters that informally describe the memory model, concurrency model, runtime services, linkage model, and debugging facilities.
  - Appendix chapters providing rationale and references to languages that influenced the design.
{==+==}
  - 介绍语言构造及使用的章节。
  - 介绍内存模型、并发模型、运行时服务、链接模型和调试功能的章节。
  - 介绍相关语言设计基本原理和其他对设计有影响的参考资料的附录章节。
{==+==}


{==+==}
Warning:
This book is incomplete. Documenting everything takes a while.
See the [GitHub issues] for what is not documented in this book.
{==+==}
提醒:
本书还不完整，要记录所有内容还需要一段时间。
可以查看 [GitHub issues] 了解本书还未记录的内容。
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
Rust 语言每六周发布一个新的版本。
第一个稳定版本是 Rust 1.0.0 ，随后是 Rust 1.1.0 等等。
`rustc` 、 `cargo` 等工具和 [标准库][Standard library] 、本书等文档都随同语言版本一起发布。
{==+==}


{==+==}
The latest release of this book, matching the latest Rust version, can always be found at <https://doc.rust-lang.org/reference/>.
Prior versions can be found by adding the Rust version before the "reference" directory.
For example, the Reference for Rust 1.49.0 is located at <https://doc.rust-lang.org/1.49.0/reference/>.
{==+==}
本书原版新版本与最新的 Rust 版本匹配，可在 <https://doc.rust-lang.org/reference/> 找到。之前版本可通过在 "reference" 前添加 Rust 版本号找到。
例如 <https://doc.rust-lang.org/1.49.0/reference/> 。

【矢量工坊宝儿姐】注：当前的中文译本以2023年4月9日源 (Rust 1.69) 为基础，如有贡献、建议和问题，请至译本仓库<https://github.com/VectorWorkshopBaoErJie/RustReferenceTranslate/>
{==+==}


{==+==}
## What *The Reference* is not
{==+==}
## *参考* 不记录的内容
{==+==}


{==+==}
This book does not serve as an introduction to the language.
Background familiarity with the language is assumed.
A separate [book] is available to help acquire such background familiarity.
{==+==}
本书不是语言的简要介绍，如有需要，可以阅读 Rust 官方提供的 [入门文档][book] 。
{==+==}


{==+==}
This book also does not serve as a reference to the [standard library] included in the language distribution.
Those libraries are documented separately by extracting documentation attributes from their source code.
Many of the features that one might expect to be language features are library features in Rust, so what you're looking for may be there, not here.
{==+==}
本书中也没有语言发行版所包含 [标准库][standard library] 的参考内容。
标准库的参考是通过从源代码中提取文档属性，而单独记录。
许多 Rust 学习者所期望的语言特性可能包含于库中，因此可能要去标准库的参考中查找。
{==+==}


{==+==}
Similarly, this book does not usually document the specifics of `rustc` as a tool or of Cargo.
`rustc` has its own [book][rustc book].
Cargo has a [book][cargo book] that contains a [reference][cargo reference].
There are a few pages such as [linkage] that still describe how `rustc` works.
{==+==}
本书也通常不会记录 `rustc` 和 Cargo 工具的具体细节。
`rustc` 有其自己的 [文档][rustc book] 。
Cargo 也有一本 [文档][cargo book] ，其中包含 [参考][cargo reference]。
但有些，如 [链接][linkage] 页仍然对 `rustc` 的工作机制有所描述。
{==+==}


{==+==}
This book also only serves as a reference to what is available in stable Rust.
For unstable features being worked on, see the [Unstable Book].
{==+==}
本书仅作为 Rust 稳定版本的参考，对于正在开发中的特性，请参见 [未稳定性文档][Unstable Book]。
{==+==}


{==+==}
Rust compilers, including `rustc`, will perform optimizations.
The reference does not specify what optimizations are allowed or disallowed.
Instead, think of the compiled program as a black box.
You can only probe by running it, feeding it input and observing its output.
Everything that happens that way must conform to what the reference says.
{==+==}
Rust 编译器，包括 `rustc` ，会执行优化操作。
本书没有明确说明允许或禁止哪些优化。
对于编译后的程序，你需要通过运行程序、提供输入和观察输出的方式做 "黑盒" 测试，这样产生的效果应符合手册所阐述的内容。
{==+==}


{==+==}
Finally, this book is not normative.
It may include details that are specific to `rustc` itself, and should not be taken as a specification for the Rust language.
We intend to produce such a book someday, and until then, the reference is the closest thing we have to one.
{==+==}
当前，本书还没有达到完全规范和标准，还包含着一些依赖于 `rustc` 的特定细节，这些不应当成语言部分的规范。
我们打算在未来发布更加规范的参考。
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
本书并不需要顺序阅读。
通常可以去单独阅读每个章节，其中未展开讨论的内容通常会交叉链接到其他章节。
{==+==}


{==+==}
There are two main ways to read this document.
{==+==}
建议阅读本书的方式主要有两种。
{==+==}


{==+==}
The first is to answer a specific question.
If you know which chapter answers that question, you can jump to that chapter in the table of contents.
Otherwise, you can press `s` or click the magnifying glass on the top bar to search for keywords related to your question.
For example, say you wanted to know when a temporary value created in a let statement is dropped.
If you didn't already know that the [lifetime of temporaries] is defined in the [expressions chapter], you could search "temporary let" and the first search result will take you to that section.
{==+==}
一、查找特定的问题的答案。
可以按下 `s` 键或点击顶部栏上的放大镜搜索与问题相关的关键词。
{==+==}


{==+==}
The second is to generally improve your knowledge of a facet of the language.
In that case, just browse the table of contents until you see something you want to know more about, and just start reading.
If a link looks interesting, click it, and read about that section.
{==+==}
二、提高对语言某个方面的了解。
那么，只需浏览目录，展开阅读某一主题。
{==+==}


{==+==}
That said, there is no wrong way to read this book. Read it however you feel helps you best.
{==+==}
开卷有益，学而不怠，与君共勉。
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
本书作为技术类书籍，信息表达方式上有一些约定。
{==+==}


{==+==}
* Statements that define a term contain that term in *italics*.
  Whenever that term is used outside of that chapter, it is usually a link to the section that has this definition.
{==+==}
* 定义术语的语言句子中，该术语会以 *斜体* 的形式出现。
  在该章节之外的位置使用该术语时，通常以链接指向其定义部分。
{==+==}


{==+==}
  An *example term* is an example of a term being defined.
{==+==}
  如 *某某术语* 是指......这样的术语定义。
{==+==}


{==+==}
* Differences in the language by which edition the crate is compiled under are in a blockquote that start with the words "Edition Differences:" in **bold**.

{==+==}
* 在以 **粗体** 标识的块中，使用 "版次差异:" 这样的字眼来说明不同版本中编译的差异。
{==+==}


{==+==}
  > **Edition Differences**: In the 2015 edition, this syntax is valid that is disallowed as of the 2018 edition.
{==+==}
  > **版次差异**: 在 2015 版中，这个语法是有效的，但在 2018 版中不允许。
{==+==}


{==+==}
* Notes that contain useful information about the state of the book or point out useful, but mostly out of scope, information are in blockquotes that start with the word "Note:" in **bold**.
{==+==}
* "注意:" 为粗体的块，通常来注明关于书的状态，或者指出一些有用的而超出本书范围的信息。
{==+==}


{==+==}
  > **Note**: This is an example note.
{==+==}
  > **注意**: 这是一个标注的示例。
{==+==}


{==+==}
* Warnings that show unsound behavior in the language or possibly confusing interactions of language features are in a special warning box.
{==+==}
* 警告将显示为特殊的警告框的形式，用于标注语言中的不安全行为或可能引起混淆的语言特性。
{==+==}


{==+==}
  Warning: This is an example warning.
{==+==}
  警告: 这是一个警告的示例。
{==+==}


{==+==}
* Code snippets inline in the text are inside `<code>` tags.
{==+==}
* 正文中的内联代码片段包含在 `<code>` 标签内。
{==+==}


{==+==}
  Longer code examples are in a syntax highlighted box that has controls for copying, executing, and showing hidden lines in the top right corner.
{==+==}
  较长的代码示例会在一个语法高亮的框中展示，该框右上角有复制、执行和显示隐藏行的控制按钮。
{==+==}


{==+==}
  ```rust
  # // This is a hidden line.
  fn main() {
      println!("This is a code example");
  }
  ```
{==+==}
  ```rust
  # // 这是隐藏行。
  fn main() {
      println!("这是代码示例");
  }
  ```
{==+==}


{==+==}
  All examples are written for the latest edition unless otherwise stated.
{==+==}
  所有的示例都是针对 Rust 最新版本编写的，除非另有说明。
{==+==}


{==+==}
* The grammar and lexical structure is in blockquotes with either "Lexer" or "Syntax" in <sup>**bold superscript**</sup> as the first line.
{==+==}
* 语法和词法结构包含在块中，第一行以 <sup>**粗体上标**</sup> 的形式标注 "词法" 或 "语法" 。
{==+==}


{==+==}
  > **<sup>Syntax</sup>**\
  > _ExampleGrammar_:\
  > &nbsp;&nbsp; &nbsp;&nbsp; `~` [_Expression_]\
  > &nbsp;&nbsp; | `box` [_Expression_]
{==+==}
  > **<sup>语法</sup>**\
  > _示例语法_:\
  > &nbsp;&nbsp; &nbsp;&nbsp; `~` [_表达式_][_Expression_]\
  > &nbsp;&nbsp; | `box` [_表达式_][_Expression_]
{==+==}


{==+==}
  See [Notation] for more detail.
{==+==}
  更多信息参见 [符号约定][Notation] 。
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
你可以向 [Rust 参考仓库][the Rust Reference repository] 提交内容。对于发现的任何错误或不规范的问题，请 [提交 issue][file an issue] 。
如果你的问题在本书中没有得到解答，你认为该问题有必要包含在本书中，请 [提交 issue][file an issue] 或在 [Zulip] 的 `t-lang/doc` 频道提问。
通过这样的方式来了解本书最常使用的内容，有助于集中精力使这些部分变得更好。
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