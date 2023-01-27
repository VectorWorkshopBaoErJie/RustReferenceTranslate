{==+==}
# Conditional compilation
{==+==}
# 条件编译
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ConfigurationPredicate_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _ConfigurationOption_\
> &nbsp;&nbsp; | _ConfigurationAll_\
> &nbsp;&nbsp; | _ConfigurationAny_\
> &nbsp;&nbsp; | _ConfigurationNot_
>
> _ConfigurationOption_ :\
> &nbsp;&nbsp; [IDENTIFIER]&nbsp;(`=` ([STRING_LITERAL] | [RAW_STRING_LITERAL]))<sup>?</sup>
>
> _ConfigurationAll_\
> &nbsp;&nbsp; `all` `(` _ConfigurationPredicateList_<sup>?</sup> `)`
>
> _ConfigurationAny_\
> &nbsp;&nbsp; `any` `(` _ConfigurationPredicateList_<sup>?</sup> `)`
>
> _ConfigurationNot_\
> &nbsp;&nbsp; `not` `(` _ConfigurationPredicate_ `)`
>
> _ConfigurationPredicateList_\
> &nbsp;&nbsp; _ConfigurationPredicate_ (`,` _ConfigurationPredicate_)<sup>\*</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
*Conditionally compiled source code* is source code that may or may not be
considered a part of the source code depending on certain conditions. <!-- This
definition is sort of vacuous --> Source code can be conditionally compiled
using the [attributes] [`cfg`] and [`cfg_attr`] and the built-in [`cfg` macro].
These conditions are based on the target architecture of the compiled crate,
arbitrary values passed to the compiler, and a few other miscellaneous things
further described below in detail.
{==+==}
*条件编译源代码* 是指根据某些条件有些部分被认为是源代码的一部分，而有些认为不是。
<!-- This definition is sort of vacuous --> 
源代码可以使用 [attributes] [`cfg`] 和 [`cfg_attr`] 以及内置的 [`cfg` macro] 进行条件编译。
这些条件是指基于编译 crate 的目标架构、传递给编译器的任意值，以及其他内容，下面将进一步详细介绍。
{==+==}


{==+==}
Each form of conditional compilation takes a _configuration predicate_ that
evaluates to true or false. The predicate is one of the following:
{==+==}
每种形式的条件编译都需要有 _configuration predicate_ "配置述语"，评估其结果为真或假。该述语是以下之一:
{==+==}


{==+==}
* A configuration option. It is true if the option is set and false if it is
  unset.
* `all()` with a comma separated list of configuration predicates. It is false
  if at least one predicate is false. If there are no predicates, it is true.
* `any()` with a comma separated list of configuration predicates. It is true
  if at least one predicate is true. If there are no predicates, it is false.
* `not()` with a configuration predicate. It is true if its predicate is false
  and false if its predicate is true.
{==+==}
* 一个配置选项。如果该选项被设置，则为真，如果未被设置，则为假。
* `all()` 用逗号分隔的配置述语列表。如果至少有一个述语为假，则为假。如果没有述语，则为真。
* `any()` 与逗号分隔的配置述语列表。如果至少有一个述语为真，则为真。如果没有述语，则为假。
* `not()` 有一个配置述语。如果该述语为假，则为真，如果该述语为真，则为假。
{==+==}


{==+==}
_Configuration options_ are names and key-value pairs that are either set or
unset. Names are written as a single identifier such as, for example, `unix`.
Key-value pairs are written as an identifier, `=`, and then a string. For
example, `target_arch = "x86_64"` is a configuration option.
{==+==}
_配置选项_ 是名称和键值对，可以设置或不设置。
名称写为一个标识符，例如， `unix` 。
键值对写为一个标识符， `=` ，然后是一个字符串。
比如， `target_arch = "x86_64"` 是一个配置选项。
{==+==}


{==+==}
> **Note**: Whitespace around the `=` is ignored. `foo="bar"` and `foo = "bar"`
> are equivalent configuration options.
{==+==}
> **注意**: 忽略 `=` 周围空白。 `foo="bar"` 和 `foo = "bar"` 是等同的配置选项。
{==+==}


{==+==}
Keys are not unique in the set of key-value configuration options. For example,
both `feature = "std"` and `feature = "serde"` can be set at the same time.
{==+==}
在键值配置选项的集合中，键是不唯一的。例如，可以同时设置 `feature = "std"` 和 `feature = "serde"` 。
{==+==}


{==+==}
## Set Configuration Options
{==+==}
## 设置配置选项
{==+==}


{==+==}
Which configuration options are set is determined statically during the
compilation of the crate. Certain options are _compiler-set_ based on data
about the compilation. Other options are _arbitrarily-set_, set based on input
passed to the compiler outside of the code. It is not possible to set a
configuration option from within the source code of the crate being compiled.
{==+==}
设置哪些配置选项，是在编译crate的过程中静态确定的。
某些选项是 _编译设置_ 基于编译相关数据。
其他选项是 _任意设置_ 根据代码外传递给编译器的输入来设置。
不能为正在编译的crate的源代码中设置配置选项。
{==+==}


{==+==}
> **Note**: For `rustc`, arbitrary-set configuration options are set using the
> [`--cfg`] flag.
{==+==}
> **注意**: 对于 `rustc` 来说，任意设置的配置选项是使用 [--cfg`] 标志。
{==+==}


{==+==}
> **Note**: Configuration options with the key `feature` are a convention used
> by [Cargo][cargo-feature] for specifying compile-time options and optional
> dependencies.
{==+==}
> **注意**: 使用 [Cargo][cargo-feature] `feature` 键配置选项是一种惯例，用于指定编译时选项和可选的依赖。
{==+==}


{==+==}
Warning: It is possible for arbitrarily-set configuration options to have the
same value as compiler-set configuration options. For example, it is possible
to do `rustc --cfg "unix" program.rs` while compiling to a Windows target, and
have both `unix` and `windows` configuration options set at the same time. It
is unwise to actually do this.
{==+==}
警告: 任意设置的配置选项有可能与编译器设置的配置选项具有相同的值。
例如，有可能在编译到Windows目标时进行 `rustc --cfg "unix" program.rs` ，并同时设置 `unix` 和 `windows` 配置选项。
但这样做是不明智的。
{==+==}


{==+==}
### `target_arch`
{==+==}

{==+==}


{==+==}
Key-value option set once with the target's CPU architecture. The value is
similar to the first element of the platform's target triple, but not
identical.
{==+==}
用目标的CPU架构设置一次键值选项。该值与平台的目标三元组的第一个元素相似，但不完全相同。
{==+==}


{==+==}
Example values:

* `"x86"`
* `"x86_64"`
* `"mips"`
* `"powerpc"`
* `"powerpc64"`
* `"arm"`
* `"aarch64"`
{==+==}
示例值:

* `"x86"`
* `"x86_64"`
* `"mips"`
* `"powerpc"`
* `"powerpc64"`
* `"arm"`
* `"aarch64"`
{==+==}


{==+==}
### `target_feature`
{==+==}

{==+==}


{==+==}
Key-value option set for each platform feature available for the current
compilation target.
{==+==}
当前编译目标可用的每个平台特性的键值选项集合。
{==+==}


{==+==}
Example values:

* `"avx"`
* `"avx2"`
* `"crt-static"`
* `"rdrand"`
* `"sse"`
* `"sse2"`
* `"sse4.1"`
{==+==}
示例值:

* `"avx"`
* `"avx2"`
* `"crt-static"`
* `"rdrand"`
* `"sse"`
* `"sse2"`
* `"sse4.1"`
{==+==}


{==+==}
See the [`target_feature` attribute] for more details on the available
features. An additional feature of `crt-static` is available to the
`target_feature` option to indicate that a [static C runtime] is available.
{==+==}
参见 [`target_feature` attribute] 以了解更多关于可用特性的细节。
`target_feature` 选项有一个额外的特性 `crt-static` ，以表明有一个 [static C runtime] 静态C运行时可用。
{==+==}


{==+==}
### `target_os`
{==+==}

{==+==}


{==+==}
Key-value option set once with the target's operating system. This value is
similar to the second and third element of the platform's target triple.
{==+==}
对于目标操作系统设置一次的键值选项。这个值类似于平台的目标三元组中的第二和第三要素。
{==+==}


{==+==}
Example values:

* `"windows"`
* `"macos"`
* `"ios"`
* `"linux"`
* `"android"`
* `"freebsd"`
* `"dragonfly"`
* `"openbsd"`
* `"netbsd"`
{==+==}
示例值:

* `"windows"`
* `"macos"`
* `"ios"`
* `"linux"`
* `"android"`
* `"freebsd"`
* `"dragonfly"`
* `"openbsd"`
* `"netbsd"`
{==+==}


{==+==}
### `target_family`
{==+==}

{==+==}


{==+==}
Key-value option providing a more generic description of a target, such as the family of the
operating systems or architectures that the target generally falls into. Any number of
`target_family` key-value pairs can be set.
{==+==}
键值选项提供了对目标的更通用的描述，例如目标通常属于的操作系统或架构的家族。可以设置任意数量的 `target_family` 键值对。
{==+==}


{==+==}
Example values:

* `"unix"`
* `"windows"`
* `"wasm"`
{==+==}
示例值:

* `"unix"`
* `"windows"`
* `"wasm"`
{==+==}


{==+==}
### `unix` and `windows`

`unix` is set if `target_family = "unix"` is set and `windows` is set if
`target_family = "windows"` is set.
{==+==}
### `unix` 和 `windows`

如果设置了 `target_family = "unix"` ，则设置 `unix` ；如果设置了 `target_family = "windows"` ，则设置 `windows` 。
{==+==}


{==+==}
### `target_env`
{==+==}

{==+==}


{==+==}
Key-value option set with further disambiguating information about the target
platform with information about the ABI or `libc` used. For historical reasons,
this value is only defined as not the empty-string when actually needed for
disambiguation. Thus, for example, on many GNU platforms, this value will be
empty. This value is similar to the fourth element of the platform's target
triple. One difference is that embedded ABIs such as `gnueabihf` will simply
define `target_env` as `"gnu"`.
{==+==}
键值选项集合，包括目标平台的ABI或 `libc` 信息的进一步消除歧义。
由于历史原因，这个值只在实际需要消除歧义时定义为非空字符串。
因此，比如，在许多GNU平台上，这个值将是空的。
该值类似于平台的目标三元组的第四个元素。
一个区别是，嵌入式ABI，如 `gnueabihf` 会简单地将 `target_env` 定义为 `"gnu"` 。
{==+==}


{==+==}
Example values:

* `""`
* `"gnu"`
* `"msvc"`
* `"musl"`
* `"sgx"`
{==+==}
示例值:

* `""`
* `"gnu"`
* `"msvc"`
* `"musl"`
* `"sgx"`
{==+==}


{==+==}
### `target_endian`
{==+==}

{==+==}


{==+==}
Key-value option set once with either a value of "little" or "big" depending
on the endianness of the target's CPU.
{==+==}
根据目标CPU的字节顺序，用 "little" 或 "big" 的值来设置一次键值选项。
{==+==}


{==+==}
### `target_pointer_width`
{==+==}

{==+==}


{==+==}
Key-value option set once with the target's pointer width in bits.
{==+==}
键值选项设置一次，以比特的目标指针宽度。
{==+==}


{==+==}
Example values:

* `"16"`
* `"32"`
* `"64"`
{==+==}
示例值:

* `"16"`
* `"32"`
* `"64"`
{==+==}


{==+==}
### `target_vendor`
{==+==}

{==+==}


{==+==}
Key-value option set once with the vendor of the target.
{==+==}
用目标的vendor设置一次键值选项。
{==+==}


{==+==}
Example values:

* `"apple"`
* `"fortanix"`
* `"pc"`
* `"unknown"`
{==+==}
示例值:

* `"apple"`
* `"fortanix"`
* `"pc"`
* `"unknown"`
{==+==}


{==+==}
### `target_has_atomic`
{==+==}

{==+==}


{==+==}
Key-value option set for each bit width that the target supports
atomic loads, stores, and compare-and-swap operations.
{==+==}
为每个位宽设置的键值选项，目标支持原子加载、存储和比较与交换操作。
{==+==}


{==+==}
When this cfg is present, all of the stable [`core::sync::atomic`] APIs are available for
the relevant atomic width.
{==+==}
当这个cfg出现时，所有稳定的 [`core::sync::atomic`] API都可以用于相应的原子宽度。
{==+==}


{==+==}
[`core::sync::atomic`]: ../core/sync/atomic/index.html
{==+==}

{==+==}


{==+==}
Possible values:

* `"8"`
* `"16"`
* `"32"`
* `"64"`
* `"128"`
* `"ptr"`
{==+==}
可能值:

* `"8"`
* `"16"`
* `"32"`
* `"64"`
* `"128"`
* `"ptr"`
{==+==}


{==+==}
### `test`
{==+==}

{==+==}


{==+==}
Enabled when compiling the test harness. Done with `rustc` by using the
[`--test`] flag. See [Testing] for more on testing support.
{==+==}
在编译测试控制启用时。通过使用 [`--test`] 标志，在 `rustc` 中完成。更多关于测试支持的信息，请参见 [Testing] 。
{==+==}


{==+==}
### `debug_assertions`
{==+==}

{==+==}


{==+==}
Enabled by default when compiling without optimizations.
This can be used to enable extra debugging code in development but not in
production.  For example, it controls the behavior of the standard library's
[`debug_assert!`] macro.
{==+==}
在没有优化的情况下进行编译时，默认启用。
这可以用来在开发中启用额外的调试代码，但不能在生产中使用。
比如，它可以控制标准库的 [`debug_assert!`] 宏的行为。
{==+==}


{==+==}
### `proc_macro`
{==+==}

{==+==}


{==+==}
Set when the crate being compiled is being compiled with the `proc_macro`
[crate type].
{==+==}
当被编译的crate正在用 `proc_macro` [crate type] 进行编译时设置。
{==+==}


{==+==}
### `panic`
{==+==}

{==+==}


{==+==}
Key-value option set depending on the panic strategy. Note that more values may be added in the future.
{==+==}
根据恐慌策略设置的键值选项。注意，将来可能会增加更多的值。
{==+==}


{==+==}
Example values:

* `"abort"`
* `"unwind"`
{==+==}
示例值:

* `"abort"`
* `"unwind"`
{==+==}


{==+==}
## Forms of conditional compilation
{==+==}
## 条件编译的形式
{==+==}


{==+==}
### The `cfg` attribute
{==+==}
### `cfg` 属性
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _CfgAttrAttribute_ :\
> &nbsp;&nbsp; `cfg` `(` _ConfigurationPredicate_ `)`
{==+==}

{==+==}


{==+==}
<!-- should we say they're active attributes here? -->
{==+==}

{==+==}


{==+==}
The `cfg` [attribute] conditionally includes the thing it is attached to based
on a configuration predicate.
{==+==}
 `cfg` [attribute] 根据配置述语有条件地包含它所连接的内容。
{==+==}


{==+==}
It is written as `cfg`, `(`, a configuration predicate, and finally `)`.
{==+==}
它写成 `cfg` ， `(` ，配置述语，最后是 `)` 。
{==+==}


{==+==}
If the predicate is true, the thing is rewritten to not have the `cfg` attribute
on it. If the predicate is false, the thing is removed from the source code.
{==+==}
如果述语为真，该事物改写为没有 `cfg` 属性。如果述语为假，则该事物将从源代码中删除。
{==+==}


{==+==}
Some examples on functions:
{==+==}
一些关于功能的例子:
{==+==}


{==+==}
```rust
// The function is only included in the build when compiling for macOS
#[cfg(target_os = "macos")]
fn macos_only() {
  // ...
}

// This function is only included when either foo or bar is defined
#[cfg(any(foo, bar))]
fn needs_foo_or_bar() {
  // ...
}

// This function is only included when compiling for a unixish OS with a 32-bit
// architecture
#[cfg(all(unix, target_pointer_width = "32"))]
fn on_32bit_unix() {
  // ...
}

// This function is only included when foo is not defined
#[cfg(not(foo))]
fn needs_not_foo() {
  // ...
}

// This function is only included when the panic strategy is set to unwind
#[cfg(panic = "unwind")]
fn when_unwinding() {
  // ...
}

```
{==+==}
```rust
// 该函数仅在为macOS编译时包含在构建中。
#[cfg(target_os = "macos")]
fn macos_only() {
  // ...
}

// 只有在定义了foo或bar的情况下，才会包含这个函数
#[cfg(any(foo, bar))]
fn needs_foo_or_bar() {
  // ...
}

// 这个函数只在为32位架构的unixish操作系统编译时包含。
#[cfg(all(unix, target_pointer_width = "32"))]
fn on_32bit_unix() {
  // ...
}

// 只有在没有定义foo的情况下才包含这个函数
#[cfg(not(foo))]
fn needs_not_foo() {
  // ...
}

// 该函数仅在恐慌策略被设置为unwind时包含。
#[cfg(panic = "unwind")]
fn when_unwinding() {
  // ...
}

```
{==+==}


{==+==}
The `cfg` attribute is allowed anywhere attributes are allowed.
{==+==}
在任何允许属性的地方都允许`cfg` 属性。
{==+==}


{==+==}
### The `cfg_attr` attribute
{==+==}
### `cfg_attr` 属性
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _CfgAttrAttribute_ :\
> &nbsp;&nbsp; `cfg_attr` `(` _ConfigurationPredicate_ `,` _CfgAttrs_<sup>?</sup> `)`
>
> _CfgAttrs_ :\
> &nbsp;&nbsp; [_Attr_]&nbsp;(`,` [_Attr_])<sup>\*</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
The `cfg_attr` [attribute] conditionally includes [attributes] based on a
configuration predicate.
{==+==}
 `cfg_attr` [attribute] 有条件地包含基于配置述语的 [attributes] 。
{==+==}


{==+==}
When the configuration predicate is true, this attribute expands out to the
attributes listed after the predicate. For example, the following module will
either be found at `linux.rs` or `windows.rs` based on the target.
{==+==}
当配置述语为真时，该属性会展开为述语后面列出的属性。
比如，根据目标，以下模块将在 `linux.rs` 或 `windows.rs` 中找到。
{==+==}


{==+==}
<!-- ignore: `mod` needs multiple files -->
```rust,ignore
#[cfg_attr(target_os = "linux", path = "linux.rs")]
#[cfg_attr(windows, path = "windows.rs")]
mod os;
```
{==+==}

{==+==}


{==+==}
Zero, one, or more attributes may be listed. Multiple attributes will each be
expanded into separate attributes. For example:
{==+==}
可以列出零个、一个或多个属性。多个属性将分别被展开为单独的属性。比如:
{==+==}


{==+==}
<!-- ignore: fake attributes -->
```rust,ignore
#[cfg_attr(feature = "magic", sparkles, crackles)]
fn bewitched() {}
{==+==}

{==+==}


{==+==}
// When the `magic` feature flag is enabled, the above will expand to:
#[sparkles]
#[crackles]
fn bewitched() {}
```
{==+==}
// 当 `magic` 特性标志启用时，上述内容将展开为:
#[sparkles]
#[crackles]
fn bewitched() {}
```
{==+==}


{==+==}
> **Note**: The `cfg_attr` can expand to another `cfg_attr`. For example,
> `#[cfg_attr(target_os = "linux", cfg_attr(feature = "multithreaded", some_other_attribute))]`
> is valid. This example would be equivalent to
> `#[cfg_attr(all(target_os = "linux", feature ="multithreaded"), some_other_attribute)]`.
{==+==}
> **注意**: `cfg_attr` 可以展开到另一个 `cfg_attr` 。
> 比如, `#[cfg_attr(target_os = "linux", cfg_attr(feature = "multithreaded", some_other_attribute))]` 是有效的。
> 这个例子相当于 `#[cfg_attr(all(target_os = "linux", feature ="multithreaded"), some_other_attribute)]` 。
{==+==}


{==+==}
The `cfg_attr` attribute is allowed anywhere attributes are allowed.
{==+==}
在任何允许属性的地方都允许使用 `cfg_attr` 属性。
{==+==}


{==+==}
### The `cfg` macro
{==+==}
### `cfg` 宏
{==+==}


{==+==}
The built-in `cfg` macro takes in a single configuration predicate and evaluates
to the `true` literal when the predicate is true and the `false` literal when
it is false.
{==+==}
内置的 `cfg` 宏接收一个配置述语，当述语为真时评估为 `true` 字面值，为假时评估为 `false` 字面值。
{==+==}


{==+==}
For example:

```rust
let machine_kind = if cfg!(unix) {
  "unix"
} else if cfg!(windows) {
  "windows"
} else {
  "unknown"
};

println!("I'm running on a {} machine!", machine_kind);
```
{==+==}
示例:

```rust
let machine_kind = if cfg!(unix) {
  "unix"
} else if cfg!(windows) {
  "windows"
} else {
  "unknown"
};

println!("I'm running on a {} machine!", machine_kind);
```
{==+==}


{==+==}
[IDENTIFIER]: identifiers.md
[RAW_STRING_LITERAL]: tokens.md#raw-string-literals
[STRING_LITERAL]: tokens.md#string-literals
[Testing]: attributes/testing.md
[_Attr_]: attributes.md
[`--cfg`]: ../rustc/command-line-arguments.html#--cfg-configure-the-compilation-environment
[`--test`]: ../rustc/command-line-arguments.html#--test-build-a-test-harness
[`cfg`]: #the-cfg-attribute
[`cfg` macro]: #the-cfg-macro
[`cfg_attr`]: #the-cfg_attr-attribute
[`debug_assert!`]: ../std/macro.debug_assert.html
[`target_feature` attribute]: attributes/codegen.md#the-target_feature-attribute
[attribute]: attributes.md
[attributes]: attributes.md
[cargo-feature]: ../cargo/reference/features.html
[crate type]: linkage.md
[static C runtime]: linkage.md#static-and-dynamic-c-runtimes
{==+==}

{==+==}