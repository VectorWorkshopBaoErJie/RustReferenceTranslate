{==+==}
# Macros
{==+==}
# 宏
{==+==}


{==+==}
The functionality and syntax of Rust can be extended with custom definitions
called macros. They are given names, and invoked through a consistent
syntax: `some_extension!(...)`.
{==+==}
可以通过称为 macros "宏" 来扩展Rust的功能和语法。
给予宏名称，并通过一致的语法形式来调用: `some_extension!(...)` 。
{==+==}


{==+==}
There are two ways to define new macros:
{==+==}
有两种方式定义新宏:
{==+==}


{==+==}
* [Macros by Example] define new syntax in a higher-level, declarative way.
* [Procedural Macros] define function-like macros, custom derives, and custom
  attributes using functions that operate on input tokens.
{==+==}
* [Macros by Example] "实例宏" 以更高层次的、声明的方式定义新语法。
* [Procedural Macros] "过程宏" 以对输入token的进行操作的函数来定义类似函数的宏、自定义派生和自定义属性。
{==+==}


{==+==}
## Macro Invocation
{==+==}
## Macro 调用式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _MacroInvocation_ :\
> &nbsp;&nbsp; [_SimplePath_] `!` _DelimTokenTree_
>
> _DelimTokenTree_ :\
> &nbsp;&nbsp; &nbsp;&nbsp;  `(` _TokenTree_<sup>\*</sup> `)`\
> &nbsp;&nbsp; | `[` _TokenTree_<sup>\*</sup> `]`\
> &nbsp;&nbsp; | `{` _TokenTree_<sup>\*</sup> `}`
>
> _TokenTree_ :\
> &nbsp;&nbsp; [_Token_]<sub>_except [delimiters]_</sub> | _DelimTokenTree_
>
> _MacroInvocationSemi_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_SimplePath_] `!` `(` _TokenTree_<sup>\*</sup> `)` `;`\
> &nbsp;&nbsp; | [_SimplePath_] `!` `[` _TokenTree_<sup>\*</sup> `]` `;`\
> &nbsp;&nbsp; | [_SimplePath_] `!` `{` _TokenTree_<sup>\*</sup> `}`
{==+==}

{==+==}


{==+==}
A macro invocation expands a macro at compile time and replaces the
invocation with the result of the macro. Macros may be invoked in the
following situations:
{==+==}
宏调用式在编译时展开一个宏，以宏的结果取代调用。宏可以在以下情况下被调用。
{==+==}


{==+==}
* [Expressions] and [statements]
* [Patterns]
* [Types]
* [Items] including [associated items]
* [`macro_rules`] transcribers
* [External blocks]
{==+==}
* [Expressions] "表达式"和 [statements] "语句"
* [Patterns] "模式"
* [Types] "类型"
* [Items] "条目" 包括 [associated items] "关联条目"
* [`macro_rules`] "宏规则" 转换器
* [External blocks] "外部块"
{==+==}


{==+==}
When used as an item or a statement, the _MacroInvocationSemi_ form is used
where a semicolon is required at the end when not using curly braces.
[Visibility qualifiers] are never allowed before a macro invocation or
[`macro_rules`] definition.
{==+==}
在项目或语句使用时，使用 _MacroInvocationSemi_ 形式，在不使用大括号时，需要在结尾处使用分号。
在宏调用式或 [`macro_rules`] 定义之前，不允许使用 [Visibility qualifiers] "可见性限定"。
{==+==}


{==+==}
```rust
// Used as an expression.
let x = vec![1,2,3];

// Used as a statement.
println!("Hello!");

// Used in a pattern.
macro_rules! pat {
    ($i:ident) => (Some($i))
}

if let pat!(x) = Some(1) {
    assert_eq!(x, 1);
}

// Used in a type.
macro_rules! Tuple {
    { $A:ty, $B:ty } => { ($A, $B) };
}

type N2 = Tuple!(i32, i32);

// Used as an item.
# use std::cell::RefCell;
thread_local!(static FOO: RefCell<u32> = RefCell::new(1));

// Used as an associated item.
macro_rules! const_maker {
    ($t:ty, $v:tt) => { const CONST: $t = $v; };
}
trait T {
    const_maker!{i32, 7}
}

// Macro calls within macros.
macro_rules! example {
    () => { println!("Macro call in a macro!") };
}
// Outer macro `example` is expanded, then inner macro `println` is expanded.
example!();
```
{==+==}
```rust
// 用作表达式
let x = vec![1,2,3];

// 用作语句
println!("Hello!");

// 用在模式
macro_rules! pat {
    ($i:ident) => (Some($i))
}

if let pat!(x) = Some(1) {
    assert_eq!(x, 1);
}

// 用在类型
macro_rules! Tuple {
    { $A:ty, $B:ty } => { ($A, $B) };
}

type N2 = Tuple!(i32, i32);

// 用在条目
# use std::cell::RefCell;
thread_local!(static FOO: RefCell<u32> = RefCell::new(1));

// 用在关联条目
macro_rules! const_maker {
    ($t:ty, $v:tt) => { const CONST: $t = $v; };
}
trait T {
    const_maker!{i32, 7}
}

// 宏中的宏调用。
macro_rules! example {
    () => { println!("Macro call in a macro!") };
}
// 外部宏 `example` 被展开，然后内部宏 `println` 被展开。
example!();
```
{==+==}


{==+==}
[Macros by Example]: macros-by-example.md
[Procedural Macros]: procedural-macros.md
[_SimplePath_]: paths.md#simple-paths
[_Token_]: tokens.md
[associated items]: items/associated-items.md
[delimiters]: tokens.md#delimiters
[expressions]: expressions.md
[items]: items.md
[`macro_rules`]: macros-by-example.md
[patterns]: patterns.md
[statements]: statements.md
[types]: types.md
[visibility qualifiers]: visibility-and-privacy.md
[External blocks]: items/external-blocks.md
{==+==}

{==+==}