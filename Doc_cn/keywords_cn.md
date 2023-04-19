{==+==}
# Keywords
{==+==}
# 关键字
{==+==}


{==+==}
Rust divides keywords into three categories:
{==+==}
Rust 关键字分为三类:
{==+==}


{==+==}
- [Keywords](#keywords)
- [关键字](#关键字)
  - [Strict keywords](#strict-keywords)
  - [严格字](#严格字)
  - [Reserved keywords](#reserved-keywords)
  - [保留字](#保留字)
  - [Weak keywords](#weak-keywords)
  - [松散字](#松散字)


{==+==}
## Strict keywords
{==+==}
## 严格字
{==+==}


{==+==}
These keywords can only be used in their correct contexts. They cannot
be used as the names of:
{==+==}
这些关键字只能在其正确的上下文中使用。不能用作以下内容的名称：
{==+==}


{==+==}
* [Items]
* [Variables] and function parameters
* Fields and [variants]
* [Type parameters]
* Lifetime parameters or [loop labels]
* [Macros] or [attributes]
* [Macro placeholders]
* [Crates]
{==+==}
* [条目][Items]
* [变量][Variables] 和函数参数
* 字段 和 [变体][variants]
* [类型参数][Type parameters]
* 生命周期参数 或 [循环标签][loop labels]
* [宏][Macros] 或 [属性][attributes]
* [宏占位符][Macro placeholders]
* [Crates]
{==+==}


{==+==}
> **<sup>Lexer:<sup>**\
> KW_AS             : `as`\
> KW_BREAK          : `break`\
> KW_CONST          : `const`\
> KW_CONTINUE       : `continue`\
> KW_CRATE          : `crate`\
> KW_ELSE           : `else`\
> KW_ENUM           : `enum`\
> KW_EXTERN         : `extern`\
> KW_FALSE          : `false`\
> KW_FN             : `fn`\
> KW_FOR            : `for`\
> KW_IF             : `if`\
> KW_IMPL           : `impl`\
> KW_IN             : `in`\
> KW_LET            : `let`\
> KW_LOOP           : `loop`\
> KW_MATCH          : `match`\
> KW_MOD            : `mod`\
> KW_MOVE           : `move`\
> KW_MUT            : `mut`\
> KW_PUB            : `pub`\
> KW_REF            : `ref`\
> KW_RETURN         : `return`\
> KW_SELFVALUE      : `self`\
> KW_SELFTYPE       : `Self`\
> KW_STATIC         : `static`\
> KW_STRUCT         : `struct`\
> KW_SUPER          : `super`\
> KW_TRAIT          : `trait`\
> KW_TRUE           : `true`\
> KW_TYPE           : `type`\
> KW_UNSAFE         : `unsafe`\
> KW_USE            : `use`\
> KW_WHERE          : `where`\
> KW_WHILE          : `while`
{==+==}
> **<sup>词法:<sup>**\
> KW_AS             : `as`\
> KW_BREAK          : `break`\
> KW_CONST          : `const`\
> KW_CONTINUE       : `continue`\
> KW_CRATE          : `crate`\
> KW_ELSE           : `else`\
> KW_ENUM           : `enum`\
> KW_EXTERN         : `extern`\
> KW_FALSE          : `false`\
> KW_FN             : `fn`\
> KW_FOR            : `for`\
> KW_IF             : `if`\
> KW_IMPL           : `impl`\
> KW_IN             : `in`\
> KW_LET            : `let`\
> KW_LOOP           : `loop`\
> KW_MATCH          : `match`\
> KW_MOD            : `mod`\
> KW_MOVE           : `move`\
> KW_MUT            : `mut`\
> KW_PUB            : `pub`\
> KW_REF            : `ref`\
> KW_RETURN         : `return`\
> KW_SELFVALUE      : `self`\
> KW_SELFTYPE       : `Self`\
> KW_STATIC         : `static`\
> KW_STRUCT         : `struct`\
> KW_SUPER          : `super`\
> KW_TRAIT          : `trait`\
> KW_TRUE           : `true`\
> KW_TYPE           : `type`\
> KW_UNSAFE         : `unsafe`\
> KW_USE            : `use`\
> KW_WHERE          : `where`\
> KW_WHILE          : `while`
{==+==}


{==+==}
The following keywords were added beginning in the 2018 edition.
{==+==}
以下关键字从 2018 版开始添加。
{==+==}


{==+==}
> **<sup>Lexer 2018+</sup>**\
> KW_ASYNC          : `async`\
> KW_AWAIT          : `await`\
> KW_DYN            : `dyn`
{==+==}
> **<sup>词法 2018+</sup>**\
> KW_ASYNC          : `async`\
> KW_AWAIT          : `await`\
> KW_DYN            : `dyn`
{==+==}


{==+==}
## Reserved keywords
{==+==}
## 保留字
{==+==}


{==+==}
These keywords aren't used yet, but they are reserved for future use. They have
the same restrictions as strict keywords. The reasoning behind this is to make
current programs forward compatible with future versions of Rust by forbidding
them to use these keywords.
{==+==}
这些关键字目前还没有被使用，但是它们被保留以供将来使用。
它们的使用规则与严格关键字相同。
这样做的原因是通过禁止当前程序使用这些关键字，从而，使其与 Rust 的将来版本保持向前兼容。
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> KW_ABSTRACT       : `abstract`\
> KW_BECOME         : `become`\
> KW_BOX            : `box`\
> KW_DO             : `do`\
> KW_FINAL          : `final`\
> KW_MACRO          : `macro`\
> KW_OVERRIDE       : `override`\
> KW_PRIV           : `priv`\
> KW_TYPEOF         : `typeof`\
> KW_UNSIZED        : `unsized`\
> KW_VIRTUAL        : `virtual`\
> KW_YIELD          : `yield`
{==+==}
> **<sup>词法</sup>**\
> KW_ABSTRACT       : `abstract`\
> KW_BECOME         : `become`\
> KW_BOX            : `box`\
> KW_DO             : `do`\
> KW_FINAL          : `final`\
> KW_MACRO          : `macro`\
> KW_OVERRIDE       : `override`\
> KW_PRIV           : `priv`\
> KW_TYPEOF         : `typeof`\
> KW_UNSIZED        : `unsized`\
> KW_VIRTUAL        : `virtual`\
> KW_YIELD          : `yield`
{==+==}


{==+==}
The following keywords are reserved beginning in the 2018 edition.
{==+==}
以下关键字从 2018 版本开始保留。
{==+==}


{==+==}
> **<sup>Lexer 2018+</sup>**\
> KW_TRY   : `try`
{==+==}
> **<sup>词法 2018+</sup>**\
> KW_TRY   : `try`
{==+==}


{==+==}
## Weak keywords
{==+==}
## 松散字
{==+==}


{==+==}
These keywords have special meaning only in certain contexts. For example, it
is possible to declare a variable or method with the name `union`.
{==+==}
这些关键字只有在特定上下文中才具有特殊意义。
例如，可以使用名称 `union` 声明变量或方法。
{==+==}


{==+==}
* `macro_rules` is used to create custom [macros].
* `union` is used to declare a [union] and is only a keyword when used in a
  union declaration.
* `'static` is used for the static lifetime and cannot be used as a [generic
  lifetime parameter] or [loop label]
{==+==}
* `macro_rules` 用于创建自定义 [宏][macros] 。
* `union` 用于声明 [联合体][union] ，只有在使用 union 声明时才是一个关键字。
* `'static` 用于静态生命周期，不能作为 [泛型生命周期参数][generic lifetime parameter] 或 [循环标签][loop label] 使用。
{==+==}


{==+==}
  ```compile_fail
  // error[E0262]: invalid lifetime parameter name: `'static`
  fn invalid_lifetime_parameter<'static>(s: &'static str) -> &'static str { s }
  ```
{==+==}

{==+==}


{==+==}
* In the 2015 edition, [`dyn`] is a keyword when used in a type position
  followed by a path that does not start with `::`.
{==+==}
* 在 2015 版中，[`dyn`] 当用在类型位置且随后的路径不以 `::` 开头时，是一个关键字。
{==+==}


{==+==}
  Beginning in the 2018 edition, `dyn` has been promoted to a strict keyword.
{==+==}
  从 2018 版开始， `dyn` 已提升为严格关键词。
{==+==}


{==+==}
> **<sup>Lexer</sup>**\
> KW_UNION          : `union`\
> KW_STATICLIFETIME : `'static`
>
> **<sup>Lexer 2015</sup>**\
> KW_DYN            : `dyn`
{==+==}
> **<sup>词法</sup>**\
> KW_UNION          : `union`\
> KW_STATICLIFETIME : `'static`
>
> **<sup>词法 2015</sup>**\
> KW_DYN            : `dyn`
{==+==}


{==+==}
[items]: items.md
[Variables]: variables.md
[Type parameters]: types/parameters.md
[loop labels]: expressions/loop-expr.md#loop-labels
[Macros]: macros.md
[attributes]: attributes.md
[Macro placeholders]: macros-by-example.md
[Crates]: crates-and-source-files.md
[union]: items/unions.md
[variants]: items/enumerations.md
[`dyn`]: types/trait-object.md
[loop label]: expressions/loop-expr.md#loop-labels
[generic lifetime parameter]: items/generics.md
{==+==}

{==+==}