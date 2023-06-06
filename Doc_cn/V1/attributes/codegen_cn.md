{==+==}
# Code generation attributes
{==+==}
# 代码生成属性
{==+==}


{==+==}
The following [attributes] are used for controlling code generation.
{==+==}
以下 [属性][attributes] 用于控制代码生成。
{==+==}


{==+==}
## Optimization hints

The `cold` and `inline` [attributes] give suggestions to generate code in a
way that may be faster than what it would do without the hint. The attributes
are only hints, and may be ignored.

Both attributes can be used on [functions]. When applied to a function in a
[trait], they apply only to that function when used as a default function for
a trait implementation and not to all trait implementations. The attributes
have no effect on a trait function without a body.
{==+==}
## 优化提示

 `cold` 和 `inline` [属性][attributes] 给出一些编译建议，期望生成更快的代码。
但仅是提示，可能会被编译器忽略。

这两个属性可应用于 [函数][functions] 。
当应用于 [trait] 中的函数时，仅适用于作为 trait 实现的默认函数，而不是所有 trait 实现。
这些属性对于没有函数体的 trait 函数没有效果。
{==+==}


{==+==}
### The `inline` attribute

The *`inline` [attribute]* suggests that a copy of the attributed function
should be placed in the caller, rather than generating code to call the
function where it is defined.

> ***Note***: The `rustc` compiler automatically inlines functions based on
> internal heuristics. Incorrectly inlining functions can make the program
> slower, so this attribute should be used with care.

There are three ways to use the inline attribute:
{==+==}
### `inline` 属性

* `inline` 属性* 建议将函数副本放置于调用者中，而不是在相应位置生成调用该函数的代码。

> ***注意***: `rustc` 编译器基于内部启发式算法自动内联函数。不正确地内联函数可能会使程序变慢，因而此属性应谨慎使用。

有三种使用 `inline` 属性的方式:
{==+==}


{==+==}
* `#[inline]` *suggests* performing an inline expansion.
* `#[inline(always)]` *suggests* that an inline expansion should always be
  performed.
* `#[inline(never)]` *suggests* that an inline expansion should never be
  performed.

> ***Note***: `#[inline]` in every form is a hint, with no *requirements*
> on the language to place a copy of the attributed function in the caller.
{==+==}
* `#[inline]` *建议* 进行内联展开。
* `#[inline(always)]` *建议* 总是进行内联展开。
* `#[inline(never)]` *建议* 永远不进行内联展开。

> ***注意***: 无论形式如何， `#[inline]` 都仅是提示，对于是否将带有该属性的函数副本放置在调用者中，并不作 *强制* 。
{==+==}


{==+==}
### The `cold` attribute

The *`cold` [attribute]* suggests that the attributed function is unlikely to
be called.
{==+==}
### `cold` 属性

 *`cold` [属性][attribute]* 表示带有该属性的函数不太可能被调用。
{==+==}


{==+==}
## The `no_builtins` attribute

The *`no_builtins` [attribute]* may be applied at the crate level to disable
optimizing certain code patterns to invocations of library functions that are
assumed to exist.
{==+==}
## `no_builtins` 属性

 *`no_builtins` [属性][attribute]* 可以用于禁用优化特定的代码模式以调用预期存在的库函数。
 该属性可以在 crate 级别应用。
{==+==}


{==+==}
## The `target_feature` attribute

The *`target_feature` [attribute]* may be applied to a function to
enable code generation of that function for specific platform architecture
features. It uses the [_MetaListNameValueStr_] syntax with a single key of
`enable` whose value is a string of comma-separated feature names to enable.
{==+==}
## `target_feature` 属性

 *`target_feature` [属性][attribute]* 可以应用于函数，以启用为特定平台架构特性生成该函数的代码。
它使用带有单个 `enable` 键的 [_元列表名称值字符串_][_MetaListNameValueStr_] 语法，其值是逗号分隔的要启用的特性名称字符串。
{==+==}


{==+==}
```rust
# #[cfg(target_feature = "avx2")]
#[target_feature(enable = "avx2")]
unsafe fn foo_avx2() {}
```
{==+==}

{==+==}


{==+==}
Each [target architecture] has a set of features that may be enabled. It is an
error to specify a feature for a target architecture that the crate is not
being compiled for.

It is [undefined behavior] to call a function that is compiled with a feature
that is not supported on the current platform the code is running on, *except*
if the platform explicitly documents this to be safe.

Functions marked with `target_feature` are not inlined into a context that
does not support the given features. The `#[inline(always)]` attribute may not
be used with a `target_feature` attribute.
{==+==}
每个 [目标架构][target architecture] 都有一组可启用的特性。
对于进行编译的 crate，如果指定了不受支持的目标架构特性，会出现错误。

对于使用不受当前平台支持的特性进行编译的函数，进行调用是 [未定义行为][undefined behavior] ，除非平台明确说明这是安全的。

使用 `target_feature` 标注的函数不会被内联到不支持所给定特性的上下文中。
 `#[inline(always)]` 属性不能与 `target_feature` 属性一起使用。
{==+==}


{==+==}
### Available features

The following is a list of the available feature names.
{==+==}
### 可用特性

以下是可用的特性名称列表。
{==+==}


{==+==}
#### `x86` or `x86_64`

Executing code with unsupported features is undefined behavior on this platform.
Hence this platform requires that `#[target_feature]` is only applied to [`unsafe`
functions][unsafe function].
{==+==}
####  `x86` 或 `x86_64`

在此平台上执行不支持的特性是未定义的行为。
因此，该平台要求 `#[target_feature]` 仅应用于 [`unsafe` 函数][unsafe function] 。
{==+==}


{==+==}
Feature     | Implicitly Enables | Description
------------|--------------------|-------------------
`adx`       |          | [ADX] — Multi-Precision Add-Carry Instruction Extensions
`aes`       | `sse2`   | [AES] — Advanced Encryption Standard
`avx`       | `sse4.2` | [AVX] — Advanced Vector Extensions
`avx2`      | `avx`    | [AVX2] — Advanced Vector Extensions 2
`bmi1`      |          | [BMI1] — Bit Manipulation Instruction Sets
`bmi2`      |          | [BMI2] — Bit Manipulation Instruction Sets 2
`fma`       | `avx`    | [FMA3] — Three-operand fused multiply-add
`fxsr`      |          | [`fxsave`] and [`fxrstor`] — Save and restore x87 FPU, MMX Technology, and SSE State
`lzcnt`     |          | [`lzcnt`] — Leading zeros count
`pclmulqdq` | `sse2`   | [`pclmulqdq`] — Packed carry-less multiplication quadword
`popcnt`    |          | [`popcnt`] — Count of bits set to 1
`rdrand`    |          | [`rdrand`] — Read random number
`rdseed`    |          | [`rdseed`] — Read random seed
`sha`       | `sse2`   | [SHA] — Secure Hash Algorithm
`sse`       |          | [SSE] — Streaming <abbr title="Single Instruction Multiple Data">SIMD</abbr> Extensions
`sse2`      | `sse`    | [SSE2] — Streaming SIMD Extensions 2
`sse3`      | `sse2`   | [SSE3] — Streaming SIMD Extensions 3
`sse4.1`    | `ssse3`  | [SSE4.1] — Streaming SIMD Extensions 4.1
`sse4.2`    | `sse4.1` | [SSE4.2] — Streaming SIMD Extensions 4.2
`ssse3`     | `sse3`   | [SSSE3] — Supplemental Streaming SIMD Extensions 3
`xsave`     |          | [`xsave`] — Save processor extended states
`xsavec`    |          | [`xsavec`] — Save processor extended states with compaction
`xsaveopt`  |          | [`xsaveopt`] — Save processor extended states optimized
`xsaves`    |          | [`xsaves`] — Save processor extended states supervisor
{==+==}
特性 | 隐式启用 | 描述
--- | --- | ---
`adx` | | [ADX] — 多精度加进位指令扩展
`aes` | `sse2` | [AES] — 高级加密标准
`avx` | `sse4.2` | [AVX] — 高级矢量扩展
`avx2` | `avx` | [AVX2] — 高级矢量扩展 2
`bmi1` | | [BMI1] — 位操作指令集
`bmi2` | | [BMI2] — 位操作指令集 2
`fma` | `avx` | [FMA3] — 三操作数融合乘加
`fxsr` | | [`fxsave`] 和 [`fxrstor`] — 保存和恢复 x87 FPU、MMX 技术和 SSE 状态
`lzcnt` | | [`lzcnt`] — 领先的零数目
`pclmulqdq` | `sse2` | [`pclmulqdq`] — 打包无进位乘法的乘积四倍字
`popcnt` | | [`popcnt`] — 位值为 1 的位数
`rdrand` | | [`rdrand`] — 读取随机数
`rdseed` | | [`rdseed`] — 读取随机种子
`sha` | `sse2` | [SHA] — 安全哈希算法
`sse` | | [SSE] — 流处理 SIMD 扩展
`sse2` | `sse` | [SSE2] — 流处理 SIMD 扩展 2
`sse3` | `sse2` | [SSE3] — 流处理 SIMD 扩展 3
`sse4.1` | `ssse3` | [SSE4.1] — 流处理 SIMD 扩展 4.1
`sse4.2` | `sse4.1` | [SSE4.2] — 流处理 SIMD 扩展 4.2
`ssse3` | `sse3` | [SSSE3] — 补充流处理 SIMD 扩展 3
`xsave` | | [`xsave`] — 保存处理器扩展状态
`xsavec` | | [`xsavec`] — 保存带压缩的处理器扩展状态
`xsaveopt` | | [`xsaveopt`] — 优化保存处理器扩展状态
`xsaves` | | [`xsaves`] — 以管理模式保存处理器扩展状态
{==+==}


{==+==}
<!-- Keep links near each table to make it easier to move and update. -->
{==+==}

{==+==}


{==+==}
[ADX]: https://en.wikipedia.org/wiki/Intel_ADX
[AES]: https://en.wikipedia.org/wiki/AES_instruction_set
[AVX]: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions
[AVX2]: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2
[BMI1]: https://en.wikipedia.org/wiki/Bit_Manipulation_Instruction_Sets
[BMI2]: https://en.wikipedia.org/wiki/Bit_Manipulation_Instruction_Sets#BMI2
[FMA3]: https://en.wikipedia.org/wiki/FMA_instruction_set
[`fxsave`]: https://www.felixcloutier.com/x86/fxsave
[`fxrstor`]: https://www.felixcloutier.com/x86/fxrstor
[`lzcnt`]: https://www.felixcloutier.com/x86/lzcnt
[`pclmulqdq`]: https://www.felixcloutier.com/x86/pclmulqdq
[`popcnt`]: https://www.felixcloutier.com/x86/popcnt
[`rdrand`]: https://en.wikipedia.org/wiki/RdRand
[`rdseed`]: https://en.wikipedia.org/wiki/RdRand
[SHA]: https://en.wikipedia.org/wiki/Intel_SHA_extensions
[SSE]: https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions
[SSE2]: https://en.wikipedia.org/wiki/SSE2
[SSE3]: https://en.wikipedia.org/wiki/SSE3
[SSE4.1]: https://en.wikipedia.org/wiki/SSE4#SSE4.1
[SSE4.2]: https://en.wikipedia.org/wiki/SSE4#SSE4.2
[SSSE3]: https://en.wikipedia.org/wiki/SSSE3
[`xsave`]: https://www.felixcloutier.com/x86/xsave
[`xsavec`]: https://www.felixcloutier.com/x86/xsavec
[`xsaveopt`]: https://www.felixcloutier.com/x86/xsaveopt
[`xsaves`]: https://www.felixcloutier.com/x86/xsaves
{==+==}

{==+==}


{==+==}
#### `aarch64`

This platform requires that `#[target_feature]` is only applied to [`unsafe`
functions][unsafe function].

Further documentation on these features can be found in the [ARM Architecture
Reference Manual], or elsewhere on [developer.arm.com].

[ARM Architecture Reference Manual]: https://developer.arm.com/documentation/ddi0487/latest
[developer.arm.com]: https://developer.arm.com

> ***Note***: The following pairs of features should both be marked as enabled
> or disabled together if used:
> - `paca` and `pacg`, which LLVM currently implements as one feature.
{==+==}
#### `aarch64`

这个平台要求 `#[target_feature]` 仅能应用于 [`unsafe`函数][unsafe function] 。

更多关于这些特性的文档可以在 [ARM 架构参考手册][ARM Architecture Reference Manual] 或 [developer.arm.com] 中找到。

[ARM Architecture Reference Manual]: https://developer.arm.com/documentation/ddi0487/latest
[developer.arm.com]: https://developer.arm.com

> ***注意***: 如果使用以下特性对，应该一起标记为已启用或已禁用:
> `paca` 和 `pacg` ，LLVM 当前将它们实现为一个特性。
{==+==}


{==+==}
Feature        | Implicitly Enables | Feature Name
---------------|--------------------|-------------------
`aes`          | `neon`         | FEAT_AES - Advanced <abbr title="Single Instruction Multiple Data">SIMD</abbr> AES instructions
`bf16`         |                | FEAT_BF16 - BFloat16 instructions
`bti`          |                | FEAT_BTI - Branch Target Identification
`crc`          |                | FEAT_CRC - CRC32 checksum instructions
`dit`          |                | FEAT_DIT - Data Independent Timing instructions
`dotprod`      |                | FEAT_DotProd - Advanced SIMD Int8 dot product instructions
`dpb`          |                | FEAT_DPB - Data cache clean to point of persistence
`dpb2`         |                | FEAT_DPB2 - Data cache clean to point of deep persistence
`f32mm`        | `sve`          | FEAT_F32MM - SVE single-precision FP matrix multiply instruction
`f64mm`        | `sve`          | FEAT_F64MM - SVE double-precision FP matrix multiply instruction
`fcma`         | `neon`         | FEAT_FCMA - Floating point complex number support
`fhm`          | `fp16`         | FEAT_FHM - Half-precision FP FMLAL instructions
`flagm`        |                | FEAT_FlagM - Conditional flag manipulation
`fp16`         | `neon`         | FEAT_FP16 - Half-precision FP data processing
`frintts`      |                | FEAT_FRINTTS - Floating-point to int helper instructions
`i8mm`         |                | FEAT_I8MM - Int8 Matrix Multiplication
`jsconv`       | `neon`         | FEAT_JSCVT - JavaScript conversion instruction
`lse`          |                | FEAT_LSE - Large System Extension
`lor`          |                | FEAT_LOR - Limited Ordering Regions extension
`mte`          |                | FEAT_MTE - Memory Tagging Extension
`neon`         |                | FEAT_FP & FEAT_AdvSIMD - Floating Point and Advanced SIMD extension
`pan`          |                | FEAT_PAN - Privileged Access-Never extension
`paca`         |                | FEAT_PAuth - Pointer Authentication (address authentication)
`pacg`         |                | FEAT_PAuth - Pointer Authentication (generic authentication)
`pmuv3`        |                | FEAT_PMUv3 - Performance Monitors extension (v3)
`rand`         |                | FEAT_RNG - Random Number Generator
`ras`          |                | FEAT_RAS - Reliability, Availability and Serviceability extension
`rcpc`         |                | FEAT_LRCPC - Release consistent Processor Consistent
`rcpc2`        | `rcpc`         | FEAT_LRCPC2 - RcPc with immediate offsets
`rdm`          |                | FEAT_RDM - Rounding Double Multiply accumulate
`sb`           |                | FEAT_SB - Speculation Barrier
`sha2`         | `neon`         | FEAT_SHA1 & FEAT_SHA256 - Advanced SIMD SHA instructions
`sha3`         | `sha2`         | FEAT_SHA512 & FEAT_SHA3 - Advanced SIMD SHA instructions
`sm4`          | `neon`         | FEAT_SM3 & FEAT_SM4 - Advanced SIMD SM3/4 instructions
`spe`          |                | FEAT_SPE - Statistical Profiling Extension
`ssbs`         |                | FEAT_SSBS - Speculative Store Bypass Safe
`sve`          | `fp16`         | FEAT_SVE - Scalable Vector Extension
`sve2`         | `sve`          | FEAT_SVE2 - Scalable Vector Extension 2
`sve2-aes`     | `sve2`, `aes`  | FEAT_SVE_AES - SVE AES instructions
`sve2-sm4`     | `sve2`, `sm4`  | FEAT_SVE_SM4 - SVE SM4 instructions
`sve2-sha3`    | `sve2`, `sha3` | FEAT_SVE_SHA3 - SVE SHA3 instructions
`sve2-bitperm` | `sve2`         | FEAT_SVE_BitPerm - SVE Bit Permute
`tme`          |                | FEAT_TME - Transactional Memory Extension
`vh`           |                | FEAT_VHE - Virtualization Host Extensions
{==+==}
特性  | 隐式启用  | 特性名称
---------------|--------------------|-------------------
`aes`          | `neon`         | FEAT_AES - 高级 <abbr title="Single Instruction Multiple Data">SIMD</abbr> AES 指令
`bf16`         |                | FEAT_BF16 - BFloat16 指令
`bti`          |                | FEAT_BTI - 分支目标识别
`crc`          |                | FEAT_CRC - CRC32 校验和指令
`dit`          |                | FEAT_DIT - 数据独立定时指令
`dotprod`      |                | FEAT_DotProd - 高级 SIMD Int8 点乘指令
`dpb`          |                | FEAT_DPB - 数据缓存清除到持久点
`dpb2`         |                | FEAT_DPB2 - 数据缓存清除到深度持久点
`f32mm`        | `sve`          | FEAT_F32MM - SVE 单精度 FP 矩阵乘法指令
`f64mm`        | `sve`          | FEAT_F64MM - SVE 双精度 FP 矩阵乘法指令
`fcma`         | `neon`         | FEAT_FCMA - 浮点复数支持
`fhm`          | `fp16`         | FEAT_FHM - 半精度 FP FMLAL 指令
`flagm`        |                | FEAT_FlagM - 条件标志位操作
`fp16`         | `neon`         | FEAT_FP16 - 半精度 FP 数据处理
`frintts`      |                | FEAT_FRINTTS - 浮点到整数帮助指令
`i8mm`         |                | FEAT_I8MM - Int8 矩阵乘法
`jsconv`       | `neon`         | FEAT_JSCVT - JavaScript 转换指令
`lse`          |                | FEAT_LSE - 大系统扩展
`lor`          |                | FEAT_LOR - 有限排序区域扩展
`mte`          |                | FEAT_MTE - 内存标记扩展
`neon`         |                | FEAT_FP 和 FEAT_AdvSIMD - 浮点数和高级 SIMD 扩展
`pan`          |                | FEAT_PAN - 特权访问不允许扩展
`paca`         |                | FEAT_PAuth - 指针认证（地址认证）
`pacg`         |                | FEAT_PAuth - 指针认证（通用认证）
`pmuv3`        |                | FEAT_PMUv3 - 性能监视器扩展（v3）
`rand`         |                | FEAT_RNG - 随机数生成器
`ras`          |                | FEAT_RAS - 可靠性、可用性和服务性扩展
`rcpc`         |                | FEAT_LRCPC - 一致性处理器ARMv8.2 可以支持多种架构，其中一些可用特性如下所示：
`rdm`          |                | FEAT_RDM - 双精度乘累加取整指令
`sb`           |                | FEAT_SB - 推测屏障指令
`sha2`         | `neon`         | FEAT_SHA1 & FEAT_SHA256 - 高级SIMD SHA指令
`sha3`         | `sha2`         | FEAT_SHA512 & FEAT_SHA3 - 高级SIMD SHA指令
`sm4`          | `neon`         | FEAT_SM3 & FEAT_SM4 - 高级SIMD SM3/4指令
`spe`          |                | FEAT_SPE - 统计分析扩展指令
`ssbs`         |                | FEAT_SSBS - 推测存储旁路安全指令
`sve`          | `fp16`         | FEAT_SVE - 可扩展向量扩展指令
`sve2`         | `sve`          | FEAT_SVE2 - 可扩展向量扩展2指令
`sve2-aes`     | `sve2`, `aes`  | FEAT_SVE_AES - SVE AES指令
`sve2-sm4`     | `sve2`, `sm4`  | FEAT_SVE_SM4 - SVE SM4指令
`sve2-sha3`    | `sve2`, `sha3` | FEAT_SVE_SHA3 - SVE SHA3指令
`sve2-bitperm` | `sve2`         | FEAT_SVE_BitPerm - SVE 位重排指令
`tme`          |                | FEAT_TME - 事务内存扩展指令
`vh`           |                | FEAT_VHE - 虚拟化主机扩展指令
{==+==}


{==+==}
#### `wasm32` or `wasm64`

`#[target_feature]` may be used with both safe and
[`unsafe` functions][unsafe function] on Wasm platforms. It is impossible to
cause undefined behavior via the `#[target_feature]` attribute because
attempting to use instructions unsupported by the Wasm engine will fail at load
time without the risk of being interpreted in a way different from what the
compiler expected.
{==+==}
#### `wasm32` 或 `wasm64`

 `#[target_feature]` 可以在 Wasm 平台上用于安全函数和 [`非安全函数`][unsafe function] 。
在 Wasm 引擎不支持的指令下，尝试使用 `#[target_feature]` 属性将在加载时失败，不会有与编译器预期不同的解释风险，因此不可能引起未定义行为。
{==+==}


{==+==}
Feature     | Description
------------|-------------------
`simd128`   | [WebAssembly simd proposal][simd128]

[simd128]: https://github.com/webassembly/simd
{==+==}
特性     | 描述
------------|-------------------
`simd128`   | [WebAssembly simd proposal][simd128]

[simd128]: https://github.com/webassembly/simd
{==+==}


{==+==}
### Additional information

See the [`target_feature` conditional compilation option] for selectively
enabling or disabling compilation of code based on compile-time settings. Note
that this option is not affected by the `target_feature` attribute, and is
only driven by the features enabled for the entire crate.

See the [`is_x86_feature_detected`] or [`is_aarch64_feature_detected`] macros
in the standard library for runtime feature detection on these platforms.

> Note: `rustc` has a default set of features enabled for each target and CPU.
> The CPU may be chosen with the [`-C target-cpu`] flag. Individual features
> may be enabled or disabled for an entire crate with the
> [`-C target-feature`] flag.
{==+==}
### 附加信息

[`target_feature` 条件编译选项][`target_feature` conditional compilation option] 可用于根据编译时设置选择性地启用或禁用代码的编译。
请注意，该选项不受 `target_feature` 属性的影响，仅受整个 crate 启用的特性的驱动。

标准库中的 [`is_x86_feature_detected`] 或 [`is_aarch64_feature_detected`] 宏可用于在这些平台上进行运行时特性检测。

> 注意: `rustc` 对于每个目标和 CPU 都有一组默认启用的特性。
> 可以使用 [`-C target-cpu`] 标志选择 CPU 。可以使用 [`-C target-feature`] 标志为整个 crate 启用或禁用单个特性。
{==+==}


{==+==}
## The `track_caller` attribute

The `track_caller` attribute may be applied to any function with [`"Rust"` ABI][rust-abi]
with the exception of the entry point `fn main`. When applied to functions and methods in
trait declarations, the attribute applies to all implementations. If the trait provides a
default implementation with the attribute, then the attribute also applies to override implementations.

When applied to a function in an `extern` block the attribute must also be applied to any linked
implementations, otherwise undefined behavior results. When applied to a function which is made
available to an `extern` block, the declaration in the `extern` block must also have the attribute,
otherwise undefined behavior results.
{==+==}
## `track_caller` 属性

 `track_caller` 属性可以应用于任何使用 [`"Rust"` ABI][rust-abi] 的函数，除了入口点 `fn main` 。
当应用于 trait 声明中的函数和方法时，该属性适用于所有实现。如果 trait 提供了带有该属性的默认实现，则该属性还适用于重写实现。

当应用于 `extern` 块中的函数时，该属性也必须应用于任何链接的实现，否则将导致未定义的行为。
当应用于提供给 `extern` 块的函数时，`extern` 块中的声明也必须具有该属性，否则将导致未定义的行为。
{==+==}


{==+==}
### Behavior

Applying the attribute to a function `f` allows code within `f` to get a hint of the [`Location`] of
the "topmost" tracked call that led to `f`'s invocation. At the point of observation, an
implementation behaves as if it walks up the stack from `f`'s frame to find the nearest frame of an
*unattributed* function `outer`, and it returns the [`Location`] of the tracked call in `outer`.
{==+==}
### 行为

将此属性应用于函数 `f` ，允许 `f` 中的代码获取一个提示，即导致 `f` 调用的 "顶级" 跟踪调用的 [`Location`] 。
在观察点，实现行为就好像它从 `f` 的帧向上遍历栈，查找 *未归属* 函数 `outer` 的最近帧，并返回 `outer` 中跟踪调用的 [`Location`]。
{==+==}


{==+==}
```rust
#[track_caller]
fn f() {
    println!("{}", std::panic::Location::caller());
}
```
{==+==}

{==+==}


{==+==}
> Note: `core` provides [`core::panic::Location::caller`] for observing caller locations. It wraps
> the [`core::intrinsics::caller_location`] intrinsic implemented by `rustc`.

> Note: because the resulting `Location` is a hint, an implementation may halt its walk up the stack
> early. See [Limitations](#limitations) for important caveats.
{==+==}
> 注意: `core` 提供 [`core::panic::Location::caller`] 用于获取调用地址。它包装了 `rustc` 实现的 [`core::intrinsics::caller_location`] 内置函数。

> 注意: 由于产生的 `Location` 只是一个提示，实现可能会在遍历堆栈时提前停止。请参阅 [限制](#limitations) 以获取重要的注意事项。
{==+==}


{==+==}
#### Examples

When `f` is called directly by `calls_f`, code in `f` observes its callsite within `calls_f`:
{==+==}
#### 示例

当 `f` 直接被 `calls_f` 调用时，`f` 中的代码会观察到它在 `calls_f` 中被调用位置:
{==+==}


{==+==}
```rust
# #[track_caller]
# fn f() {
#     println!("{}", std::panic::Location::caller());
# }
fn calls_f() {
    f(); // <-- f() prints this location
}
```
{==+==}

{==+==}


{==+==}
When `f` is called by another attributed function `g` which is in turn called by `calls_g`, code in
both `f` and `g` observes `g`'s callsite within `calls_g`:
{==+==}
当函数 `f` 被另一个带有 `track_caller` 属性的函数 `g` 直接调用，而 `g` 又被 `calls_g` 调用时， `f` 和 `g` 中的代码都可以观察到 `calls_g` 中调用 `g` 的位置:
{==+==}


{==+==}
```rust
# #[track_caller]
# fn f() {
#     println!("{}", std::panic::Location::caller());
# }
#[track_caller]
fn g() {
    println!("{}", std::panic::Location::caller());
    f();
}

fn calls_g() {
    g(); // <-- g() prints this location twice, once itself and once from f()
}
```
{==+==}

{==+==}


{==+==}
When `g` is called by another attributed function `h` which is in turn called by `calls_h`, all code
in `f`, `g`, and `h` observes `h`'s callsite within `calls_h`:
{==+==}
当函数 `g` 被另一个有 `track_caller` 属性的函数 `h` 直接或间接调用时 (例如 `h` 被 `calls_h` 调用) ，则 `f` 、 `g` 和 `h` 中的代码都会观察到 `calls_h` 中 `h` 的调用位置。
也就是说，调用链中最深的有 `track_caller` 属性的函数的调用位置会传递到所有下层函数:
{==+==}


{==+==}
```rust
# #[track_caller]
# fn f() {
#     println!("{}", std::panic::Location::caller());
# }
# #[track_caller]
# fn g() {
#     println!("{}", std::panic::Location::caller());
#     f();
# }
#[track_caller]
fn h() {
    println!("{}", std::panic::Location::caller());
    g();
}

fn calls_h() {
    h(); // <-- prints this location three times, once itself, once from g(), once from f()
}
```
{==+==}

{==+==}


{==+==}
And so on.
{==+==}
以次类推。
{==+==}


{==+==}
### Limitations

This information is a hint and implementations are not required to preserve it.

In particular, coercing a function with `#[track_caller]` to a function pointer creates a shim which
appears to observers to have been called at the attributed function's definition site, losing actual
caller information across virtual calls. A common example of this coercion is the creation of a
trait object whose methods are attributed.

> Note: The aforementioned shim for function pointers is necessary because `rustc` implements
> `track_caller` in a codegen context by appending an implicit parameter to the function ABI, but
> this would be unsound for an indirect call because the parameter is not a part of the function's
> type and a given function pointer type may or may not refer to a function with the attribute. The
> creation of a shim hides the implicit parameter from callers of the function pointer, preserving
> soundness.
{==+==}
### 限制

该信息是一个提示，实现不需要保留它。

特别地，将一个带有 `#[track_caller]` 的函数转换为函数指针会创建一个外壳函数，对于观察者来说，该外壳函数似乎是在函数定义的位置被调用的，从而在虚拟调用之间丢失了实际的调用者信息。
这种转换的常见例子是创建一个具有方法的 trait 对象，并将其进行标注。

> 注意: 函数指针的上述外壳函数是必要的，因为 `rustc` 在代码生成上实现 `track_caller` 通过将一个隐式参数附加到函数 ABI 上，但是这对于间接调用是非安全的，
> 因为该参数不是函数类型的一部分，给定的函数指针类型可能引用一个没有该属性的函数或具有该属性的函数。创建外壳函数隐藏了函数指针调用者的隐式参数，从而保持了安全性。
{==+==}


{==+==}
[_MetaListNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[`-C target-cpu`]: ../../rustc/codegen-options/index.html#target-cpu
[`-C target-feature`]: ../../rustc/codegen-options/index.html#target-feature
[`is_x86_feature_detected`]: ../../std/arch/macro.is_x86_feature_detected.html
[`is_aarch64_feature_detected`]: ../../std/arch/macro.is_aarch64_feature_detected.html
[`target_feature` conditional compilation option]: ../conditional-compilation.md#target_feature
[attribute]: ../attributes.md
[attributes]: ../attributes.md
[functions]: ../items/functions.md
[target architecture]: ../conditional-compilation.md#target_arch
[trait]: ../items/traits.md
[undefined behavior]: ../behavior-considered-undefined.md
[unsafe function]: ../unsafe-keyword.md
[rust-abi]: ../items/external-blocks.md#abi
[`core::intrinsics::caller_location`]: ../../core/intrinsics/fn.caller_location.html
[`core::panic::Location::caller`]: ../../core/panic/struct.Location.html#method.caller
[`Location`]: ../../core/panic/struct.Location.html
{==+==}

{==+==}


{==+==}
## The `instruction_set` attribute

The *`instruction_set` attribute* may be applied to a function to enable code generation for a specific
instruction set supported by the target architecture. It uses the [_MetaListPath_] syntax and a path
comprised of the architecture and instruction set to specify how to generate the code for
architectures where a single program may utilize multiple instruction sets.

The following values are available on targets for the `ARMv4` and `ARMv5te` architectures:
{==+==}
## `instruction_set` 属性

可以将 `instruction_set` 属性应用于函数，以启用针对目标体系结构支持的特定指令集的代码生成。
它使用 [_MetaListPath_] 语法和一个由架构和指令集组成的路径，指定如何为在单个程序中使用多个指令集的体系结构生成代码。

对于 `ARMv4` 和 `ARMv5te` 架构的目标，以下值可用:
{==+==}


{==+==}
* `arm::a32` - Uses ARM code.
* `arm::t32` - Uses Thumb code.
{==+==}
* `arm::a32` - 使用 ARM 编码.
* `arm::t32` - 使用 Thumb 编码.
{==+==}


{==+==}
<!-- ignore: arm-only -->
```rust,ignore
#[instruction_set(arm::a32)]
fn foo_arm_code() {}

#[instruction_set(arm::t32)]
fn bar_thumb_code() {}
```
{==+==}

{==+==}


{==+==}
[_MetaListPath_]: ../attributes.md#meta-item-attribute-syntax
{==+==}

{==+==}