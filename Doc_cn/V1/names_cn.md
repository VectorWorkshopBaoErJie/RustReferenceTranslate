{==+==}
# Names

An *entity* is a language construct that can be referred to in some way within
the source program, usually via a [path]. Entities include [types], [items],
[generic parameters], [variable bindings], [loop labels], [lifetimes],
[fields], [attributes], and [lints].

A *declaration* is a syntactical construct that can introduce a *name* to
refer to an entity. Entity names are valid within a [*scope*] — a region of
source text where that name may be referenced.

Some entities are [explicitly declared](#explicitly-declared-entities) in the
source code, and some are [implicitly declared](#implicitly-declared-entities)
as part of the language or compiler extensions.

[*Paths*] are used to refer to an entity, possibly in another scope. Lifetimes
and loop labels use a [dedicated syntax][lifetimes-and-loop-labels] using a
leading quote.

Names are segregated into different [*namespaces*], allowing entities in
different namespaces to share the same name without conflict.

[*Name resolution*] is the compile-time process of tying paths, identifiers,
and labels to entity declarations.

Access to certain names may be restricted based on their [*visibility*].
{==+==}
# 名称

*实体* 是语言结构，可以通过某种方式在源程序中引用，通常是通过 [路径][path] 。
实体包括 [类型][types] 、 [条目][items] 、 [泛型参数][generic parameters] 、 [变量绑定][variable bindings] 、 [循环标签][loop labels] 、 [生命周期][lifetimes] 、 [字段][fields] 、 [属性][attributes] 和 [代码分析][lints] 。

*声明* 是语言结构，可以引入 *名称* 来引用一个实体。
实体名称在 [*作用域*][*scope*] 内是有效的 —— 源文本的一个区域，可以在其中引用该名称。
一些实体在源代码中 [显式声明](#explicitly-declared-entities)，一些实体则作为语言或编译器扩展的一部分 [隐式声明](#implicitly-declared-entities)。

[*路径*][*Paths*] 用于引用实体，可在另一个作用域中。
生命周期和循环标签使用 [专用语法][lifetimes-and-loop-labels]，使用前导引号。

名称被分为不同的 [*命名空间*][*namespaces*] ，允许不同命名空间中的实体共享相同的名称而不冲突。

[*名称解析*][*Name resolution*] 是将路径、标识符和标签与实体声明联系起来的编译时过程。

根据 [*可见性*][*visibility*] ，访问某些名称可能会受到限制。
{==+==}


{==+==}
## Explicitly declared entities

Entities that explicitly introduce a name in the source code are:

* [Items]:
    * [Module declarations]
    * [External crate declarations]
    * [Use declarations]
    * [Function declarations] and [function parameters]
    * [Type aliases]
    * [struct], [union], [enum], enum variant declarations, and their named
      fields
    * [Constant item declarations]
    * [Static item declarations]
    * [Trait item declarations] and their [associated items]
    * [External block items]
    * [`macro_rules` declarations] and [matcher metavariables]
    * [Implementation] associated items
* [Expressions]:
    * [Closure] parameters
    * [`while let`] pattern bindings
    * [`for`] pattern bindings
    * [`if let`] pattern bindings
    * [`match`] pattern bindings
    * [Loop labels]
* [Generic parameters]
* [Higher ranked trait bounds]
* [`let` statement] pattern bindings
* The [`macro_use` attribute] can introduce macro names from another crate
* The [`macro_export` attribute] can introduce an alias for the macro into the crate root

Additionally, [macro invocations] and [attributes] can introduce names by
expanding to one of the above items.
{==+==}
## 显式声明的实体

在源代码中显式引入名称的实体包括：

* [条目][Items]：
    * [模块声明][Module declarations]
    * [外部 crate 声明][External crate declarations]
    * [使用声明][Use declarations]
    * [函数声明][Function declarations] 和 [函数参数][function parameters]
    * [类型别名][Type aliases]
    * [结构体][struct] 、 [联合体][union] 、 [枚举][enum] 、枚举变体声明及其命名字段
    * [常量条目声明][Constant item declarations]
    * [静态条目声明][Static item declarations]
    * [Trait 条目声明][Trait item declarations] 及其 [关联条目][associated items]
    * [外部块条目][External block items]
    * [`macro_rules` 声明][`macro_rules` declarations] 和 [匹配器元变量][matcher metavariables]
    * [实现][Implementation] 的关联条目
* [表达式][Expressions]：
    * [闭包][Closure] 参数
    * [`while let`] 模式绑定
    * [`for`] 模式绑定
    * [`if let`] 模式绑定
    * [`match`] 模式绑定
    * [Loop 标签][Loop labels]
* [泛型参数][Generic parameters]
* [高阶 trait 约束][Higher ranked trait bounds]
* [`let` 语句][`let` statement] 的模式绑定
* [`macro_use` 属性][`macro_use` attribute] 可以引入另一个 crate 中的宏名称
* [`macro_export` 属性][`macro_export` attribute] 可以将宏的别名引入 crate 根

此外， [宏调用][macro invocations] 和 [属性][attributes] 可以通过扩展为上述项之一而引入名称。
{==+==}


{==+==}
## Implicitly declared entities

The following entities are implicitly defined by the language, or are
introduced by compiler options and extensions:

* [Language prelude]:
    * [Boolean type] — `bool`
    * [Textual types] — `char` and `str`
    * [Integer types] — `i8`, `i16`, `i32`, `i64`, `i128`, `u8`, `u16`, `u32`, `u64`, `u128`
    * [Machine-dependent integer types] — `usize` and `isize`
    * [floating-point types] — `f32` and `f64`
* [Built-in attributes]
* [Standard library prelude] items, attributes, and macros
* [Standard library][extern-prelude] crates in the root module
* [External crates][extern-prelude] linked by the compiler
* [Tool attributes]
* [Lints] and [tool lint attributes]
* [Derive helper attributes] are valid within an item without being explicitly imported
* The [`'static`] lifetime

Additionally, the crate root module does not have a name, but can be referred
to with certain [path qualifiers] or aliases.
{==+==}
## 隐式声明的实体

以下实体是由语言隐式定义的，或者是由编译器选项和扩展引入的:

* [语言预定义][Language prelude]:
    * [布尔类型][Boolean type] — `bool`
    * [文本类型][Textual types] — `char` 和 `str`
    * [整数类型][Integer types] — `i8`、`i16`、`i32`、`i64`、`i128`、`u8`、`u16`、`u32`、`u64`、`u128`
    * [机器相关整数类型][Machine-dependent integer types] — `usize` 和 `isize`
    * [浮点类型][floating-point types] — `f32` 和 `f64`
* [内置属性][Built-in attributes]
* [标准库预定义][Standard library prelude] 条目、属性和宏
* 根模块中的 [标准库][extern-prelude] crate
* 编译器链接的 [外部 crate][extern-prelude]
* [工具属性][Tool attributes]
* [代码分析][Lints] 和 [工具 Lint 属性][tool lint attributes]
* [衍生助手属性][Derive helper attributes] 在条目内有效，无需显式导入
* [`'static`] 生命周期

此外，crate 根模块没有名称，但可以用特定 [路径限定符][path qualifiers] 或别名来引用。
{==+==}


{==+==}
[*Name resolution*]: names/name-resolution.md
[*namespaces*]: names/namespaces.md
[*paths*]: paths.md
[*scope*]: names/scopes.md
[*visibility*]: visibility-and-privacy.md
[`'static`]: keywords.md#weak-keywords
[`for`]: expressions/loop-expr.md#iterator-loops
[`if let`]: expressions/if-expr.md#if-let-expressions
[`let` statement]: statements.md#let-statements
[`macro_export` attribute]: macros-by-example.md#path-based-scope
[`macro_rules` declarations]: macros-by-example.md
[`macro_use` attribute]: macros-by-example.md#the-macro_use-attribute
[`match`]: expressions/match-expr.md
[`while let`]: expressions/loop-expr.md#predicate-pattern-loops
[associated items]: items/associated-items.md
[attributes]: attributes.md
[Boolean type]: types/boolean.md
[Built-in attributes]: attributes.md#built-in-attributes-index
[Closure]: expressions/closure-expr.md
[Constant item declarations]: items/constant-items.md
[Derive helper attributes]: procedural-macros.md#derive-macro-helper-attributes
[enum]: items/enumerations.md
[Expressions]: expressions.md
[extern-prelude]: names/preludes.md#extern-prelude
[External block items]: items/external-blocks.md
[External crate declarations]: items/extern-crates.md
[fields]: expressions/field-expr.md
[floating-point types]: types/numeric.md#floating-point-types
[Function declarations]: items/functions.md
[function parameters]: items/functions.md#function-parameters
[Generic parameters]: items/generics.md
[Higher ranked trait bounds]: trait-bounds.md#higher-ranked-trait-bounds
[Implementation]: items/implementations.md
[Integer types]: types/numeric.md#integer-types
[Items]: items.md
[Language prelude]: names/preludes.md#language-prelude
[lifetimes-and-loop-labels]: tokens.md#lifetimes-and-loop-labels
[lifetimes]: tokens.md#lifetimes-and-loop-labels
[Lints]: attributes/diagnostics.md#lint-check-attributes
[Loop labels]: expressions/loop-expr.md#loop-labels
[Machine-dependent integer types]: types/numeric.md#machine-dependent-integer-types
[macro invocations]: macros.md#macro-invocation
[matcher metavariables]: macros-by-example.md#metavariables
[Module declarations]: items/modules.md
[path]: paths.md
[path qualifiers]: paths.md#path-qualifiers
[Standard library prelude]: names/preludes.md#standard-library-prelude
[Static item declarations]: items/static-items.md
[struct]: items/structs.md
[Textual types]: types/textual.md
[Tool attributes]: attributes.md#tool-attributes
[tool lint attributes]: attributes/diagnostics.md#tool-lint-attributes
[Trait item declarations]: items/traits.md
[Type aliases]: items/type-aliases.md
[types]: types.md
[union]: items/unions.md
[Use declarations]: items/use-declarations.md
[variable bindings]: patterns.md
{==+==}

{==+==}