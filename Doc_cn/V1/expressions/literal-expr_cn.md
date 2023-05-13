{==+==}
# Literal expressions
{==+==}
# 字面值表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LiteralExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [CHAR_LITERAL]\
> &nbsp;&nbsp; | [STRING_LITERAL]\
> &nbsp;&nbsp; | [RAW_STRING_LITERAL]\
> &nbsp;&nbsp; | [BYTE_LITERAL]\
> &nbsp;&nbsp; | [BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | [RAW_BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | [INTEGER_LITERAL]\
> &nbsp;&nbsp; | [FLOAT_LITERAL]\
> &nbsp;&nbsp; | `true` | `false`
{==+==}
> **<sup>语法</sup>**\
> _字面值表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [字符字面值][CHAR_LITERAL]\
> &nbsp;&nbsp; | [字符串字面值][STRING_LITERAL]\
> &nbsp;&nbsp; | [原始字符串字面值][RAW_STRING_LITERAL]\
> &nbsp;&nbsp; | [字节字面值][BYTE_LITERAL]\
> &nbsp;&nbsp; | [字节字符串字面值][BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | [原始字节字符串字面值][RAW_BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | [整数字面值][INTEGER_LITERAL]\
> &nbsp;&nbsp; | [浮点数字面值][FLOAT_LITERAL]\
> &nbsp;&nbsp; | `true` | `false`
{==+==}


{==+==}
A _literal expression_ is an expression consisting of a single token, rather than a sequence of tokens, that immediately and directly denotes the value it evaluates to, rather than referring to it by name or some other evaluation rule.

A literal is a form of [constant expression], so is evaluated (primarily) at compile time.

Each of the lexical [literal][literal tokens] forms described earlier can make up a literal expression, as can the keywords `true` and `false`.
{==+==}
_字面值表达式_ 仅由一个单独的 token 组成，其直接指示计算结果值，而不是通过名称或其他计算规则的间接引用。 

字面值是 [常量表达式][constant expression] 的其中一种形式，因此在编译时计算 (主要) 。

先前描述的词法 [字面值][literal tokens] 每种形式都可以形成字面值表达式，关键字 `true` 和 `false` 也可以。
{==+==}


{==+==}
```rust
"hello";   // string type
'5';       // character type
5;         // integer type
```
{==+==}
```rust
"hello";   // 字符串类型
'5';       // 字符类型
5;         // 整数类型
```
{==+==}


{==+==}
## Character literal expressions

A character literal expression consists of a single [CHAR_LITERAL] token.

> **Note**: This section is incomplete.

## String literal expressions

A string literal expression consists of a single [STRING_LITERAL] or [RAW_STRING_LITERAL] token.

> **Note**: This section is incomplete.

## Byte literal expressions

A byte literal expression consists of a single [BYTE_LITERAL] token.

> **Note**: This section is incomplete.

## Byte string literal expressions

A string literal expression consists of a single [BYTE_STRING_LITERAL] or [RAW_BYTE_STRING_LITERAL] token.

> **Note**: This section is incomplete.
{==+==}
## 字符字面量表达式

字符字面量表达式由一个 [字符字面值][CHAR_LITERAL] token 构成。

> **注意**: 此部分内容不完整。

## 字符串字面量表达式

字符串字面量表达式由一个 [字符串字面值][STRING_LITERAL] 或 [原始字符串字面值][RAW_STRING_LITERAL] token 构成。

> **注意**: 此部分内容不完整。

## 字节字面量表达式

字节字面量表达式由一个 [字节字面值][BYTE_LITERAL] token 牌构成。

> **注意**: 此部分内容不完整。

## 字节字符串字面量表达式

字节字符串字面量表达式由一个 [字节字符串字面值][BYTE_STRING_LITERAL] 或 [原始字节字符串字面值][RAW_BYTE_STRING_LITERAL] token 构成。

> **注意**: 此部分内容不完整。
{==+==}


{==+==}
## Integer literal expressions

An integer literal expression consists of a single [INTEGER_LITERAL] token.

If the token has a [suffix], the suffix must be the name of one of the [primitive integer types][numeric types]: `u8`, `i8`, `u16`, `i16`, `u32`, `i32`, `u64`, `i64`, `u128`, `i128`, `usize`, or `isize`, and the expression has that type.

If the token has no suffix, the expression's type is determined by type inference:

* If an integer type can be _uniquely_ determined from the surrounding program context, the expression has that type.

* If the program context under-constrains the type, it defaults to the signed 32-bit integer `i32`.

* If the program context over-constrains the type, it is considered a static type error.

Examples of integer literal expressions:
{==+==}
## 整型字面值表达式

整型字面值表达式由一个 [整数字面值][INTEGER_LITERAL] token 组成。

如果 token 有 [后缀][suffix] ，则后缀必须是 [基本整数类型][numeric types] 中的一个名称： `u8` 、 `i8` 、 `u16` 、 `i16` 、 `u32` 、 `i32` 、 `u64` 、 `i64` 、 `u128` 、 `i128` 、 `usize` 或 `isize` ，且表达式具有该类型。

如果 token 没有后缀，则该表达式的类型由类型推断确定:

* 如果整型类型可以从周围的程序上下文中确定，则表达式具有该类型。

* 如果程序上下文中的整型类型不足以确定类型，则默认为有符号的 32 位整型 `i32`。

* 如果程序上下文中的整型类型过多，则被视为静态类型错误。

整型字面量表达式的例子:
{==+==}


{==+==}
```rust
123;                               // type i32
123i32;                            // type i32
123u32;                            // type u32
123_u32;                           // type u32
let a: u64 = 123;                  // type u64

0xff;                              // type i32
0xff_u8;                           // type u8

0o70;                              // type i32
0o70_i16;                          // type i16

0b1111_1111_1001_0000;             // type i32
0b1111_1111_1001_0000i64;          // type i64

0usize;                            // type usize
```
{==+==}

{==+==}


{==+==}
The value of the expression is determined from the string representation of the token as follows:

* An integer radix is chosen by inspecting the first two characters of the string, as follows:

    * `0b` indicates radix 2
    * `0o` indicates radix 8
    * `0x` indicates radix 16
    * otherwise the radix is 10.

* If the radix is not 10, the first two characters are removed from the string.

* Any suffix is removed from the string.

* Any underscores are removed from the string.

* The string is converted to a `u128` value as if by [`u128::from_str_radix`] with the chosen radix.
If the value does not fit in `u128`, it is a compiler error.

* The `u128` value is converted to the expression's type via a [numeric cast].

> **Note**: The final cast will truncate the value of the literal if it does not fit in the expression's type.
> `rustc` includes a [lint check] named `overflowing_literals`, defaulting to `deny`, which rejects expressions where this occurs.

> **Note**: `-1i8`, for example, is an application of the [negation operator] to the literal expression `1i8`, not a single integer literal expression.
> See [Overflow] for notes on representing the most negative value for a signed type.
{==+==}
该表达式的值根据 token 的字符串表示如下确定：

* 通过检查字符串的前两个字符，选择整数基数，如下：

    * `0b` 表示基数为 2。
    * `0o` 表示基数为 8。
    * `0x` 表示基数为 16。
    * 否则，基数为 10。

* 如果基数不是 10，则从字符串中删除前两个字符。

* 从字符串中删除任何后缀。

* 从字符串中删除任何下划线。

* 将字符串转换为 `u128` 值，就像通过 [`u128::from_str_radix`] 选择的基数一样。如果值不适合 `u128`，则会出现编译器错误。

* 通过 [数字转换][numeric cast] 将 `u128` 值转换为表达式的类型。

> **注意**: 如果该字面值的值超出了该类型的表示范围，最终转换将截断该字面值。 `rustc` 包括一个名为 `overflowing_literals` 的 [代码分析检查] [lint check] ，默认为 `deny`，拒绝其中出现的表达式。

> **注意**: 例如， `-1i8` 是对字面值表达式 `1i8` 应用了 [取反运算符][negation operator] ，而不是一个整数字面值表达式。有关表示有符号类型的最大负值的注解，请参见 [溢出][Overflow] 。
{==+==}


{==+==}
## Floating-point literal expressions

A floating-point literal expression has one of two forms:
 * a single [FLOAT_LITERAL] token
 * a single [INTEGER_LITERAL] token which has a suffix and no radix indicator

If the token has a [suffix], the suffix must be the name of one of the [primitive floating-point types][floating-point types]: `f32` or `f64`, and the expression has that type.

If the token has no suffix, the expression's type is determined by type inference:

* If a floating-point type can be _uniquely_ determined from the surrounding program context, the expression has that type.

* If the program context under-constrains the type, it defaults to `f64`.

* If the program context over-constrains the type, it is considered a static type error.

Examples of floating-point literal expressions:
{==+==}
## 浮点数字面值表达式

浮点数字面值表达式具有以下两种形式:

 * 单个 [浮点数字面值][FLOAT_LITERAL] token
 * 单个 [整数字面值][INTEGER_LITERAL] token，该 token 具有后缀但没有基数指示符

如果 token 具有 [后缀][suffix] ，则后缀必须是 [浮点类型][floating-point types] `f32` 或 `f64` 之一，且表达式具有该类型。

如果 token 没有后缀，则表达式的类型由类型推断确定：

* 如果可以从周围的程序上下文中 _唯一_ 确定浮点类型，则表达式具有该类型。

* 如果程序上下文不足以确定类型，则默认为 `f64` 。

* 如果程序上下文类型约束冲突，则被视为静态类型错误。

浮点数字面值表达式的示例:
{==+==}


{==+==}
```rust
123.0f64;        // type f64
0.1f64;          // type f64
0.1f32;          // type f32
12E+99_f64;      // type f64
5f32;            // type f32
let x: f64 = 2.; // type f64
```
{==+==}

{==+==}


{==+==}
The value of the expression is determined from the string representation of the token as follows:

* Any suffix is removed from the string.

* Any underscores are removed from the string.

* The string is converted to the expression's type as if by [`f32::from_str`] or [`f64::from_str`].

> **Note**: `-1.0`, for example, is an application of the [negation operator] to the literal expression `1.0`, not a single floating-point literal expression.

> **Note**: `inf` and `NaN` are not literal tokens.
> The [`f32::INFINITY`], [`f64::INFINITY`], [`f32::NAN`], and [`f64::NAN`] constants can be used instead of literal expressions.
> In `rustc`, a literal large enough to be evaluated as infinite will trigger the `overflowing_literals` lint check.
{==+==}
表达式的值根据 token 的字符串表示形式确定，具体如下:

* 从字符串中删除任何后缀。
* 从字符串中删除任何下划线。
* 将字符串转换为表达式的类型，如同通过 [`f32::from_str`] 或 [`f64::from_str`] 处理。

> **注意**: 例如， `-1.0` 是对字面表达式 `1.0` 应用 [取反运算符][negation operator] ，而不是单个浮点字面值表达式。

> **注意**: `inf` 和 `NaN` 不是字面值 token 。
> 可以使用 [`f32::INFINITY`] 、 [`f64::INFINITY`] 、 [`f32::NAN`] 和 [`f64::NAN`] 常量来代替字面值表达式。
> 在 `rustc` 中，被评估为无限大的字面值将触发 `overflowing_literals` 代码分析检查。
{==+==}


{==+==}
## Boolean literal expressions

A boolean literal expression consists of one of the keywords `true` or `false`.

The expression's type is the primitive [boolean type], and its value is:
 * true if the keyword is `true`
 * false if the keyword is `false`
{==+==}
## 布尔字面值表达式

一个布尔字面值表达式由 `true` 或 `false` 中的一个关键字组成。

该表达式的类型为原始的 [布尔类型][boolean type] ，其值是:
 * 如果关键字是 `true`，则为真
 * 如果关键字是 `false`，则为假
{==+==}


{==+==}
[boolean type]: ../types/boolean.md
[constant expression]: ../const_eval.md#constant-expressions
[floating-point types]: ../types/numeric.md#floating-point-types
[lint check]: ../attributes/diagnostics.md#lint-check-attributes
[literal tokens]: ../tokens.md#literals
[numeric cast]: operator-expr.md#numeric-cast
[numeric types]: ../types/numeric.md
[suffix]: ../tokens.md#suffixes
[negation operator]: operator-expr.md#negation-operators
[overflow]: operator-expr.md#overflow
[`f32::from_str`]: ../../core/primitive.f32.md#method.from_str
[`f32::INFINITY`]: ../../core/primitive.f32.md#associatedconstant.INFINITY
[`f32::NAN`]: ../../core/primitive.f32.md#associatedconstant.NAN
[`f64::from_str`]: ../../core/primitive.f64.md#method.from_str
[`f64::INFINITY`]: ../../core/primitive.f64.md#associatedconstant.INFINITY
[`f64::NAN`]: ../../core/primitive.f64.md#associatedconstant.NAN
[`u128::from_str_radix`]: ../../core/primitive.u128.md#method.from_str_radix
[CHAR_LITERAL]: ../tokens.md#character-literals
[STRING_LITERAL]: ../tokens.md#string-literals
[RAW_STRING_LITERAL]: ../tokens.md#raw-string-literals
[BYTE_LITERAL]: ../tokens.md#byte-literals
[BYTE_STRING_LITERAL]: ../tokens.md#byte-string-literals
[RAW_BYTE_STRING_LITERAL]: ../tokens.md#raw-byte-string-literals
[INTEGER_LITERAL]: ../tokens.md#integer-literals
[FLOAT_LITERAL]: ../tokens.md#floating-point-literals
{==+==}

{==+==}