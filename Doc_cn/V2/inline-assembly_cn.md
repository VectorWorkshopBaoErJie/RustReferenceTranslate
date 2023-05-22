{==+==}
## Syntax

The following ABNF specifies the general syntax:

```text
format_string := STRING_LITERAL / RAW_STRING_LITERAL
dir_spec := "in" / "out" / "lateout" / "inout" / "inlateout"
reg_spec := <register class> / "\"" <explicit register> "\""
operand_expr := expr / "_" / expr "=>" expr / expr "=>" "_"
reg_operand := [ident "="] dir_spec "(" reg_spec ")" operand_expr
clobber_abi := "clobber_abi(" <abi> *("," <abi>) [","] ")"
option := "pure" / "nomem" / "readonly" / "preserves_flags" / "noreturn" / "nostack" / "att_syntax" / "raw"
options := "options(" option *("," option) [","] ")"
operand := reg_operand / clobber_abi / options
asm := "asm!(" format_string *("," format_string) *("," operand) [","] ")"
global_asm := "global_asm!(" format_string *("," format_string) *("," operand) [","] ")"
```
{==+==}
## 语法

以下是常规语法的 ABNF 规范：

```text
format_string := STRING_LITERAL / RAW_STRING_LITERAL
dir_spec := "in" / "out" / "lateout" / "inout" / "inlateout"
reg_spec := <register class> / "\"" <explicit register> "\""
operand_expr := expr / "_" / expr "=>" expr / expr "=>" "_"
reg_operand := [ident "="] dir_spec "(" reg_spec ")" operand_expr
clobber_abi := "clobber_abi(" <abi> *("," <abi>) [","] ")"
option := "pure" / "nomem" / "readonly" / "preserves_flags" / "noreturn" / "nostack" / "att_syntax" / "raw"
options := "options(" option *("," option) [","] ")"
operand := reg_operand / clobber_abi / options
asm := "asm!(" format_string *("," format_string) *("," operand) [","] ")"
global_asm := "global_asm!(" format_string *("," format_string) *("," operand) [","] ")"
```
{==+==}


{==+==}
## Template string arguments

The assembler template uses the same syntax as [format strings][format-syntax] (i.e. placeholders are specified by curly braces).
The corresponding arguments are accessed in order, by index, or by name.
However, implicit named arguments (introduced by [RFC #2795][rfc-2795]) are not supported.

An `asm!` invocation may have one or more template string arguments; an `asm!` with multiple template string arguments is treated as if all the strings were concatenated with a `\n` between them.
The expected usage is for each template string argument to correspond to a line of assembly code.
All template string arguments must appear before any other arguments.

As with format strings, positional arguments must appear before named arguments and explicit [register operands](#register-operands).

Explicit register operands cannot be used by placeholders in the template string.
All other named and positional operands must appear at least once in the template string, otherwise a compiler error is generated.

The exact assembly code syntax is target-specific and opaque to the compiler except for the way operands are substituted into the template string to form the code passed to the assembler.

Currently, all supported targets follow the assembly code syntax used by LLVM's internal assembler which usually corresponds to that of the GNU assembler (GAS).
On x86, the `.intel_syntax noprefix` mode of GAS is used by default.
On ARM, the `.syntax unified` mode is used.
These targets impose an additional restriction on the assembly code: any assembler state (e.g. the current section which can be changed with `.section`) must be restored to its original value at the end of the asm string.
Assembly code that does not conform to the GAS syntax will result in assembler-specific behavior.
Further constraints on the directives used by inline assembly are indicated by [Directives Support](#directives-support).

[format-syntax]: ../std/fmt/index.html#syntax
[rfc-2795]: https://github.com/rust-lang/rfcs/pull/2795
{==+==}
## 模板字符串参数

汇编模板使用与 [格式化字符串][format-syntax] 相同的语法 (即由大括号指定占位符) 。
相应的参数按顺序、索引或名称访问。但是，不支持隐式命名参数 (由 [RFC #2795][rfc-2795] 引入) 。

`asm!` 调用可能有一个或多个模板字符串参数；具有多个模板字符串参数的 `asm!` 被视为所有字符串在它们之间用 `\n` 连接。
预期的用法是，每个模板字符串参数对应于一个汇编代码行。所有模板字符串参数必须出现在任何其他参数之前。

与格式化字符串一样，位置参数必须出现在命名参数和显式 [寄存器操作数](#register-operands) 之前。

模板字符串中的占位符不能使用显式寄存器操作数。所有其他命名和位置操作数必须至少在模板字符串中出现一次，否则会生成编译器错误。

除了用于将操作数替换为传递给汇编器的代码的方式之外，确切的汇编代码语法是特定于目标的，并且对编译器不透明。

目前，所有受支持的目标都遵循LLVM内部汇编器使用的汇编代码语法，这通常对应于GNU汇编器 (GAS)的语法。
在x86上，默认情况下使用 GAS 的 `.intel_syntax noprefix` 模式。
在ARM上，使用 `.syntax unified` 模式。

这些目标对汇编代码施加了一个额外的限制: 必须在 asm 字符串的末尾将任何汇编器状态 (例如可以使用 `.section` 更改的当前部分) 恢复为其原始值。
不符合 GAS 语法的汇编代码将导致汇编器特定的行为。关于内联汇编使用的指令的进一步约束在 [指令支持](#directives-support) 中指示。

[format-syntax]: ../std/fmt/index.html#syntax
[rfc-2795]: https://github.com/rust-lang/rfcs/pull/2795
{==+==}


{==+==}
### Correctness and Validity

In addition to all of the previous rules, the string argument to `asm!` must ultimately become—
after all other arguments are evaluated, formatting is performed, and operands are translated—
assembly that is both syntactically correct and semantically valid for the target architecture.
The formatting rules allow the compiler to generate assembly with correct syntax.
Rules concerning operands permit valid translation of Rust operands into and out of `asm!`.
Adherence to these rules is necessary, but not sufficient, for the final expanded assembly to be
both correct and valid. For instance:

- arguments may be placed in positions which are syntactically incorrect after formatting
- an instruction may be correctly written, but given architecturally invalid operands
- an architecturally unspecified instruction may be assembled into unspecified code
- a set of instructions, each correct and valid, may cause undefined behavior if placed in immediate succession

As a result, these rules are _non-exhaustive_. The compiler is not required to check the
correctness and validity of the initial string nor the final assembly that is generated.
The assembler may check for correctness and validity but is not required to do so.
When using `asm!`, a typographical error may be sufficient to make a program unsound,
and the rules for assembly may include thousands of pages of architectural reference manuals.
Programmers should exercise appropriate care, as invoking this `unsafe` capability comes with
assuming the responsibility of not violating rules of both the compiler or the architecture.
{==+==}
### 正确性和有效性

除了之前的所有规则之外， `asm!` 的字符串参数在所有其他参数求值、格式化和操作数翻译后，必须最终成为对于目标架构既具有语法正确性又具有语义有效性的汇编代码。
格式化规则允许编译器生成正确语法的汇编代码。有关操作数的规则允许 Rust 操作数与 `asm!` 中的操作数之间有效的转换。
遵循这些规则是必要的，但不足以保证最终扩展的汇编代码是正确且有效的。例如：

- 参数可能被放置在格式化后语法上不正确的位置。
- 一个指令可能被正确书写，但给定不符合架构的操作数。
- 一个架构未指定的指令可能会被汇编成未指定的代码。
- 一组指令，每个指令都正确且有效，如果放置在紧接着的位置，可能会导致未定义行为。

因此，这些规则是 "非穷尽" 的。编译器不需要检查最终汇编代码的正确性和有效性。汇编器可以检查正确性和有效性，但并不是必需的。
当使用 `asm!` 时，一个排版错误就足以使程序非安全，并且汇编规则可能包括数千页的架构参考手册。
程序员应该谨慎使用，因为使用这种 "非安全" 功能意味着承担不违反编译器或架构规则的责任。
{==+==}


{==+==}
- `.2byte`
- `.4byte`
- `.8byte`
- `.align`
- `.alt_entry`
- `.ascii`
- `.asciz`
- `.balign`
- `.balignl`
- `.balignw`
- `.bss`
- `.byte`
- `.comm`
- `.data`
- `.def`
- `.double`
- `.endef`
- `.equ`
- `.equiv`
- `.eqv`
- `.fill`
- `.float`
- `.global`
- `.globl`
- `.inst`
- `.lcomm`
- `.long`
- `.octa`
- `.option`
- `.p2align`
- `.popsection`
- `.private_extern`
- `.pushsection`
- `.quad`
- `.scl`
- `.section`
- `.set`
- `.short`
- `.size`
- `.skip`
- `.sleb128`
- `.space`
- `.string`
- `.text`
- `.type`
- `.uleb128`
- `.word`
{==+==}

{==+==}
