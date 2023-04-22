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
