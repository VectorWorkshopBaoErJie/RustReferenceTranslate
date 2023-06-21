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
Rust 可以通过自定义宏来扩展功能和语法。
宏被赋予名称，可通过一致的语法调用: `some_extension!(...)`。
{==+==}


{==+==}
There are two ways to define new macros:
{==+==}
有两种定义新宏的方式：
{==+==}


{==+==}
* [Macros by Example] define new syntax in a higher-level, declarative way.
* [Procedural Macros] define function-like macros, custom derives, and custom
  attributes using functions that operate on input tokens.
{==+==}
* [实例宏][Macros by Example] 以高阶声明的方式定义新的语法。
* [过程宏][Procedural Macros] 以定义操作输入 Token 的函数，实现自定义函数式宏，以及衍生和属性。
{==+==}


{==+==}
## Macro Invocation
{==+==}
## 宏调用
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
> **<sup>语法</sup>**\
> _宏调用_ :\
> &nbsp;&nbsp; [_简单路径_][_SimplePath_] `!` _定界Token树_
>
> _定界Token树_ :\
> &nbsp;&nbsp; &nbsp;&nbsp;  `(` _Token树_<sup>\*</sup> `)`\
> &nbsp;&nbsp; | `[` _Token树_<sup>\*</sup> `]`\
> &nbsp;&nbsp; | `{` _Token树_<sup>\*</sup> `}`
>
> _Token树_ :\
> &nbsp;&nbsp; [_Token_]<sub>_不包括 [定界符号][delimiters]_</sub> | _定界Token树_
>
> _宏调用语句_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_简单路径_][_SimplePath_] `!` `(` _Token树_<sup>\*</sup> `)` `;`\
> &nbsp;&nbsp; | [_简单路径_][_SimplePath_] `!` `[` _Token树_<sup>\*</sup> `]` `;`\
> &nbsp;&nbsp; | [_简单路径_][_SimplePath_] `!` `{` _Token树_<sup>\*</sup> `}`
{==+==}


{==+==}
A macro invocation expands a macro at compile time and replaces the
invocation with the result of the macro. Macros may be invoked in the
following situations:
{==+==}
宏调用是在编译期发生，将展开宏，用宏的结果替换调用。
可在以下语法中调用宏：
{==+==}


{==+==}
* [Expressions] and [statements]
* [Patterns]
* [Types]
* [Items] including [associated items]
* [`macro_rules`] transcribers
* [External blocks]
{==+==}
* [表达式][Expressions] 和 [语句][statements] 
* [模式][Patterns]
* [类型][Types]
* [条目][Items] 包括 [关联条目][associated items]
* [`macro_rules`] 转录器
* [外部块][External blocks] 
{==+==}


{==+==}
When used as an item or a statement, the _MacroInvocationSemi_ form is used
where a semicolon is required at the end when not using curly braces.
[Visibility qualifiers] are never allowed before a macro invocation or
[`macro_rules`] definition.
{==+==}
当宏用作条目或语句时，使用 _宏调用语句_ 语法，当未使用花括号时需要在末尾加上分号。
在宏调用或 [`macro_rules`] 定义之前，拒绝 [可见性限定符][Visibility qualifiers] 。
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
// 首先展开外部宏 `example` ，然后展开内部宏 `println` 。
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