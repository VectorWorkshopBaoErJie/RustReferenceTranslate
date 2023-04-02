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
*条件编译源代码* 是指根据某些条件来确定是否将其部分源代码不作为或者不作为源代码的一部分。
<!-- This definition is sort of vacuous --> 
可以使用 [attributes] [`cfg`] 和 [`cfg_attr`] 以及内置的 [`cfg` 宏][`cfg` macro] 条件编译源代码。
这些条件是基于编译的 crate 的目标架构、传递给编译器的任意值和其他一些细节进行确定的，下面将对其进行详细描述。
{==+==}


{==+==}
Each form of conditional compilation takes a _configuration predicate_ that
evaluates to true or false. The predicate is one of the following:
{==+==}
每种形式的条件编译都需要有配置谓词，该谓词评估为 true 或 false。谓词有以下几种形式：
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
* 配置选项。如果选项设置为 true ，则为真，否则为假。
* `all()`，带有逗号分隔的配置谓词列表。如果至少有一个谓词为 false ，则结果为 false 。如果没有谓词，则为 true 。
* `any()`，带有逗号分隔的配置谓词列表。如果至少有一个谓词为 true ，则结果为 true 。如果没有谓词，则为 false 。
* `not()`，带有一个配置谓词。如果其谓词为 false ，则为 true ，如果其谓词为 true ，则为 false 。
{==+==}


{==+==}
_Configuration options_ are names and key-value pairs that are either set or
unset. Names are written as a single identifier such as, for example, `unix`.
Key-value pairs are written as an identifier, `=`, and then a string. For
example, `target_arch = "x86_64"` is a configuration option.
{==+==}
_配置选项_ 是一些名称和键值对，可以被设置或不设置。
名称被写成单个标识符，例如 `unix` 。
键值对被写成一个标识符，后跟一个字符串 。
例如， `target_arch = "x86_64"` 就是一个配置选项。
{==+==}


{==+==}
> **Note**: Whitespace around the `=` is ignored. `foo="bar"` and `foo = "bar"`
> are equivalent configuration options.
{==+==}
> **注意**: 等号周围的空白将被忽略。 `foo="bar"` 和 `foo = "bar"` 是等价的配置选项。
{==+==}


{==+==}
Keys are not unique in the set of key-value configuration options. For example,
both `feature = "std"` and `feature = "serde"` can be set at the same time.
{==+==}
键在键值配置选项的集合中不是唯一的。例如，同时可以设置 `feature = "std"` 和 `feature = "serde"` 。
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
编译期间确定哪些配置选项被设置。某些选项是由编译数据设置的，称为编译器设置。
其他选项是任意设置的，基于在代码之外传递给编译器的输入设置。
无法从正在编译的 crate 的源代码中设置配置选项。
{==+==}


{==+==}
> **Note**: For `rustc`, arbitrary-set configuration options are set using the
> [`--cfg`] flag.
{==+==}
> **注意**: 对于 `rustc`，任意设置的配置选项可以使用 [`--cfg`] 标志设置。
{==+==}


{==+==}
> **Note**: Configuration options with the key `feature` are a convention used
> by [Cargo][cargo-feature] for specifying compile-time options and optional
> dependencies.
{==+==}
> **注意**: 键为 `feature` 的配置选项是 [Cargo][cargo-feature] 用于指定编译时选项和可选依赖的惯例。
{==+==}


{==+==}
Warning: It is possible for arbitrarily-set configuration options to have the
same value as compiler-set configuration options. For example, it is possible
to do `rustc --cfg "unix" program.rs` while compiling to a Windows target, and
have both `unix` and `windows` configuration options set at the same time. It
is unwise to actually do this.
{==+==}
警告：任意设置的配置选项可能与编译器设置的配置选项具有相同的值。
例如，可能会在编译为 Windows 目标时执行 `rustc --cfg "unix" program.rs`，并同时设置 `unix` 和 `windows` 配置选项。
实际上这样做是不明智的。
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
目标 CPU 架构与一组键值选项设置一起。该值类似于平台目标三元组的第一个元素，但并非完全相同。
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
为当前编译目标提供的每个平台特性设置键值选项。
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
详见 [`target_feature` attribute] 获取有关可用特性的更多详细信息。
`target_feature` 选项的另一个特性是 `crt-static` ，它指示可用 [静态 C 运行时库][static C runtime] 。
{==+==}


{==+==}
### `target_os`
{==+==}

{==+==}


{==+==}
Key-value option set once with the target's operating system. This value is
similar to the second and third element of the platform's target triple.
{==+==}
该选项是针对编译目标操作系统的键值选项，仅设定一次。该值类似于平台目标三元组的第二个和第三个元素。
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
`target_family` 是提供关于目标平台更一般描述的键值选项，例如目标平台通常属于的操作系统或体系结构系列。可以设置任意数量的 `target_family` 键值对。
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

如果设置了 `target_family = "unix"`，则会设置 `unix` ，如果设置了 `target_family = "windows"`，则会设置 `windows` 。
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
键值选项为目标平台提供进一步的区分信息，例如有关所使用的 ABI 或 `libc` 的信息。
由于历史原因，只有在实际需要区分时，此值才会被定义为非空字符串。
因此，例如，在许多 GNU 平台上，此值将为空。此值类似于平台目标三元组的第四个元素。
一个不同之处是，像 `gnueabihf` 这样的嵌入式 ABI 将简单地将 `target_env` 定义为 `"gnu"`。
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
键值选项，用于描述目标的字节序，取值为 "little" 或 "big" ，根据目标机器的 CPU 决定。
{==+==}


{==+==}
### `target_pointer_width`
{==+==}

{==+==}


{==+==}
Key-value option set once with the target's pointer width in bits.
{==+==}
键值选项只设置一次，其值为目标的指针宽度 (以位为单位) 。
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
键值选项设置一次，其键是目标的供应商 (vendor) 。
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
每个比特宽度支持原子加载、存储和比较交换操作的目标都设置了一个键值选项。
{==+==}


{==+==}
When this cfg is present, all of the stable [`core::sync::atomic`] APIs are available for
the relevant atomic width.
{==+==}
当这个 cfg 存在时，所有适用于相关原子宽度的稳定的 [`core::sync::atomic`] API 可用。
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
该选项在编译测试套件时启用，可以通过在使用 `rustc` 时加上 [`--test`] 标志来实现。
有关测试支持的更多信息，请参见 [Testing] 。
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
在没有开启优化的情况下，默认启用。
这可以在开发时启用额外的调试代码，但不用于生产。
例如，它控制标准库中的 [`debug_assert!`] 宏的行为。
{==+==}


{==+==}
### `proc_macro`
{==+==}

{==+==}


{==+==}
Set when the crate being compiled is being compiled with the `proc_macro`
[crate type].
{==+==}
当编译的 crate 使用 `proc_macro` [crate type] 时被设置。
{==+==}


{==+==}
### `panic`
{==+==}

{==+==}


{==+==}
Key-value option set depending on the panic strategy. Note that more values may be added in the future.
{==+==}
键值选项，根据 panic 策略设置而定。请注意，将来可能会添加更多值。
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
 `cfg` 属性是一个条件包含器，根据一系列配置判断来决定是否包含它所附着的目标。
{==+==}


{==+==}
It is written as `cfg`, `(`, a configuration predicate, and finally `)`.
{==+==}
它的语法是 `cfg` 、 `(` 、配置判断语句和 `)` 。
{==+==}


{==+==}
If the predicate is true, the thing is rewritten to not have the `cfg` attribute
on it. If the predicate is false, the thing is removed from the source code.
{==+==}
如果谓词为 true，则将该条件附加到的条目重新编写，不再具有 `cfg` 属性。
如果谓词为 false，则该条目将从源代码中删除。
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
// 该函数仅在为 macOS 编译时包含在构建中。
#[cfg(target_os = "macos")]
fn macos_only() {
  // ...
}

// 只有在定义了 foo 或 bar 的情况下，才会包含这个函数
#[cfg(any(foo, bar))]
fn needs_foo_or_bar() {
  // ...
}

// 这个函数只在为 32 位架构的 unixish 操作系统编译时包含。
#[cfg(all(unix, target_pointer_width = "32"))]
fn on_32bit_unix() {
  // ...
}

// 只有在没有定义 foo 的情况下才包含这个函数
#[cfg(not(foo))]
fn needs_not_foo() {
  // ...
}

// 该函数仅在恐慌策略被设置为 unwind 时包含。
#[cfg(panic = "unwind")]
fn when_unwinding() {
  // ...
}

```
{==+==}


{==+==}
The `cfg` attribute is allowed anywhere attributes are allowed.
{==+==}
`cfg` 属性允许在任何允许属性的位置使用。
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
`cfg_attr` 属性会根据条件谓词来选择性地包含其他属性。
{==+==}


{==+==}
When the configuration predicate is true, this attribute expands out to the
attributes listed after the predicate. For example, the following module will
either be found at `linux.rs` or `windows.rs` based on the target.
{==+==}
当配置谓词为真时，此属性将展开为谓词后列出的属性。
例如，以下模块将基于目标平台在 `linux.rs` 或 `windows.rs` 中找到：
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
可以列出零个、一个或多个属性。多个属性将会分别展开为单独的属性。比如:
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
> **注意**: `cfg_attr` 可以扩展为另一个 `cfg_attr` 。
> 比如, `#[cfg_attr(target_os = "linux", cfg_attr(feature = "multithreaded", some_other_attribute))]` 是有效的。
> 这个例子等价于 `#[cfg_attr(all(target_os = "linux", feature ="multithreaded"), some_other_attribute)]` 。
{==+==}


{==+==}
The `cfg_attr` attribute is allowed anywhere attributes are allowed.
{==+==}
`cfg_attr` 属性允许在任何允许其他属性的地方使用。
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
内置的 `cfg` 宏接收一个配置谓词并在谓词为真时求值为 `true` 字面值，在谓词为假时求值为 `false` 字面值。
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