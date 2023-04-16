{==+==}
# Comments
{==+==}
# 注释
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> LINE_COMMENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `//` (~\[`/` `!` `\n`] | `//`) ~`\n`<sup>\*</sup>\
> &nbsp;&nbsp; | `//`
>
> BLOCK_COMMENT :\
> &nbsp;&nbsp; &nbsp;&nbsp; `/*` (~\[`*` `!`] | `**` | _BlockCommentOrDoc_)
>      (_BlockCommentOrDoc_ | ~`*/`)<sup>\*</sup> `*/`\
> &nbsp;&nbsp; | `/**/`\
> &nbsp;&nbsp; | `/***/`
>
> INNER_LINE_DOC :\
> &nbsp;&nbsp; `//!` ~\[`\n` _IsolatedCR_]<sup>\*</sup>
>
> INNER_BLOCK_DOC :\
> &nbsp;&nbsp; `/*!` ( _BlockCommentOrDoc_ | ~\[`*/` _IsolatedCR_] )<sup>\*</sup> `*/`
>
> OUTER_LINE_DOC :\
> &nbsp;&nbsp; `///` (~`/` ~\[`\n` _IsolatedCR_]<sup>\*</sup>)<sup>?</sup>
>
> OUTER_BLOCK_DOC :\
> &nbsp;&nbsp; `/**` (~`*` | _BlockCommentOrDoc_ )
>              (_BlockCommentOrDoc_ | ~\[`*/` _IsolatedCR_])<sup>\*</sup> `*/`
>
> _BlockCommentOrDoc_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; BLOCK_COMMENT\
> &nbsp;&nbsp; | OUTER_BLOCK_DOC\
> &nbsp;&nbsp; | INNER_BLOCK_DOC
>
> _IsolatedCR_ :\
> &nbsp;&nbsp; _A `\r` not followed by a `\n`_
{==+==}
> **<sup>词法</sup>**\
> 行注释 :\
> &nbsp;&nbsp; &nbsp;&nbsp; `//` (~\[`/` `!` `\n`] | `//`) ~`\n`<sup>\*</sup>\
> &nbsp;&nbsp; | `//`
>
> 块注释 :\
> &nbsp;&nbsp; &nbsp;&nbsp; `/*` (~\[`*` `!`] | `**` | _行注释或文档_)
>      (_行注释或文档_ | ~`*/`)<sup>\*</sup> `*/`\
> &nbsp;&nbsp; | `/**/`\
> &nbsp;&nbsp; | `/***/`
>
> 内部行文档 :\
> &nbsp;&nbsp; `//!` ~\[`\n` _孤立CR_]<sup>\*</sup>
>
> 内部块文档 :\
> &nbsp;&nbsp; `/*!` ( _行注释或文档_ | ~\[`*/` _孤立CR_] )<sup>\*</sup> `*/`
>
> 外部行文档 :\
> &nbsp;&nbsp; `///` (~`/` ~\[`\n` _孤立CR_]<sup>\*</sup>)<sup>?</sup>
>
> 外部块文档 :\
> &nbsp;&nbsp; `/**` (~`*` | _行注释或文档_ )
>              (_行注释或文档_ | ~\[`*/` _孤立CR_])<sup>\*</sup> `*/`
>
> _行注释或文档_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; 块注释\
> &nbsp;&nbsp; | 外部块文档\
> &nbsp;&nbsp; | 内部块文档
>
> _孤立CR_ :\
> &nbsp;&nbsp; _ `\r` 不紧随 `\n`_
{==+==}


{==+==}
## Non-doc comments
{==+==}
## 非文档注释
{==+==}


{==+==}
Comments follow the general C++ style of line (`//`) and
block (`/* ... */`) comment forms. Nested block comments are supported.
{==+==}
注释遵循 C++ 风格的行 (`//`) 和 (`/* ... */`) 块注释的一般形式。支持嵌套的块注释。
{==+==}


{==+==}
Non-doc comments are interpreted as a form of whitespace.
{==+==}
非文档注释被解释为空白的一种形式。
{==+==}


{==+==}
## Doc comments
{==+==}
## 文档注释
{==+==}


{==+==}
Line doc comments beginning with exactly _three_ slashes (`///`), and block
doc comments (`/** ... */`), both inner doc comments, are interpreted as a
special syntax for [`doc` attributes]. That is, they are equivalent to writing
`#[doc="..."]` around the body of the comment, i.e., `/// Foo` turns into
`#[doc="Foo"]` and `/** Bar */` turns into `#[doc="Bar"]`.
{==+==}
以三个斜线开始的行文档注释 (`///`)，以及块文档注释 (`/** ... */`)，都是内部文档注释，被解释为 [`doc` 属性][`doc` attributes] 的特殊语法。
也就是说，它们相当于在注释主体的周围写上 `#[doc="..."]` ，即， `/// Foo` 转换成 `#[doc="Foo"]` ， `/** Bar */` 转换成 `#[doc="Bar"` 。
{==+==}


{==+==}
Line comments beginning with `//!` and block comments `/*! ... */` are
doc comments that apply to the parent of the comment, rather than the item
that follows.  That is, they are equivalent to writing `#![doc="..."]` around
the body of the comment. `//!` comments are usually used to document
modules that occupy a source file.
{==+==}
以 `//!` 开头的行注释和 `/*! ... */` 块注释也是文档注释，应用于注释的父级，而不是之后条目。
也就是说，它们相当于在注释主体的周围写上 `#![doc="..."]` 。 `//!` 注释通常应用于记录拥有源文件的模块。
{==+==}


{==+==}
Isolated CRs (`\r`), i.e. not followed by LF (`\n`), are not allowed in doc
comments.
{==+==}
在文档注释中不允许孤立的 CR (`\r`) ，即后面没有 LF (`\n`) 。
{==+==}


{==+==}
## Examples
{==+==}
## 示例
{==+==}


{==+==}
```rust
//! A doc comment that applies to the implicit anonymous module of this crate
{==+==}
```rust
//! 应用于这个 crate 的隐式匿名模块的文档注释
{==+==}


{==+==}
pub mod outer_module {

    //!  - Inner line doc
    //!! - Still an inner line doc (but with a bang at the beginning)

    /*!  - Inner block doc */
    /*!! - Still an inner block doc (but with a bang at the beginning) */

    //   - Only a comment
    ///  - Outer line doc (exactly 3 slashes)
    //// - Only a comment

    /*   - Only a comment */
    /**  - Outer block doc (exactly) 2 asterisks */
    /*** - Only a comment */
{==+==}
pub mod outer_module {

    //!  - 内部行文档
    //!! - 仍是内部行文档 (但开始有感叹号)

    /*!  - 内部块文档 */
    /*!! - 仍是内部块文档 (但开始有感叹号) */

    //   - 仅是注释
    ///  - 外部行文档 (正好 3 斜线)
    //// - 仅是注释

    /*   - 仅是注释 */
    /**  - 外部块文档 (正好 2 星号) */
    /*** - 仅是注释 */
{==+==}


{==+==}
    pub mod inner_module {}

    pub mod nested_comments {
        /* In Rust /* we can /* nest comments */ */ */

        // All three types of block comments can contain or be nested inside
        // any other type:

        /*   /* */  /** */  /*! */  */
        /*!  /* */  /** */  /*! */  */
        /**  /* */  /** */  /*! */  */
        pub mod dummy_item {}
    }
{==+==}
    pub mod inner_module {}

    pub mod nested_comments {
        /* 在 Rust /* 可以 /* 嵌套注释 */ */ */

        // 所有三种类型的块注释都可以包含或嵌套任何其他类型的注释:

        /*   /* */  /** */  /*! */  */
        /*!  /* */  /** */  /*! */  */
        /**  /* */  /** */  /*! */  */
        pub mod dummy_item {}
    }
{==+==}


{==+==}
    pub mod degenerate_cases {
        // empty inner line doc
        //!

        // empty inner block doc
        /*!*/

        // empty line comment
        //

        // empty outer line doc
        ///

        // empty block comment
        /**/

        pub mod dummy_item {}

        // empty 2-asterisk block isn't a doc block, it is a block comment
        /***/

    }
{==+==}
    pub mod degenerate_cases {
        // 空内部行文档
        //!

        // 空内部块文档
        /*!*/

        // 空行注释
        //

        // 空外部行文档
        ///

        // 空块注释
        /**/

        pub mod dummy_item {}

        // 空的 2 星号块不是块文档，而是块注释。
        /***/

    }
{==+==}


{==+==}
    /* The next one isn't allowed because outer doc comments
       require an item that will receive the doc */

    /// Where is my item?
#   mod boo {}
}
```
{==+==}
    /* 下一个是不允许的，
       外部文档注释需要一个条目来接收文档 */

    /// 我的条目在哪里?
#   mod boo {}
}
```
{==+==}


{==+==}
[`doc` attributes]: ../rustdoc/the-doc-attribute.html
{==+==}

{==+==}