{==+==}
Feature     | Implicitly Enables | Description
------------|--------------------|-------------------
`adx`       |          | [ADX] — Multi-Precision Add-Carry Instruction Extensions
`aes`       | `sse2`   | [AES] — Advanced Encryption Standard
`avx`       | `sse4.2` | [AVX] — Advanced Vector Extensions
`avx2`      | `avx`    | [AVX2] — Advanced Vector Extensions 2
`bmi1`      |          | [BMI1] — Bit Manipulation Instruction Sets
`bmi2`      |          | [BMI2] — Bit Manipulation Instruction Sets 2
`cmpxchg16b`|          | [`cmpxchg16b`] - Compares and exchange 16 bytes (128 bits) of data atomically
`fma`       | `avx`    | [FMA3] — Three-operand fused multiply-add
`fxsr`      |          | [`fxsave`] and [`fxrstor`] — Save and restore x87 FPU, MMX Technology, and SSE State
`lzcnt`     |          | [`lzcnt`] — Leading zeros count
`movbe`     |          | [`movbe`] - Move data after swapping bytes
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
特征 | 隐式启用 | 描述
--- | --- | ---
`adx` | | [ADX] — 多精度加法进位指令扩展
`aes` | `sse2` | [AES] — 高级加密标准
`avx` | `sse4.2` | [AVX] — 高级矢量扩展
`avx2` | `avx` | [AVX2] — 高级矢量扩展 2
`bmi1` | | [BMI1] — 位操作指令集
`bmi2` | | [BMI2] — 位操作指令集 2
`cmpxchg16b` | | [`cmpxchg16b`] — 原子比较并交换 16 字节 (128 位) 数据
`fma` | `avx` | [FMA3] — 三操作数融合乘加
`fxsr` | | [`fxsave`] 和 [`fxrstor`] — 保存和恢复 x87 FPU、MMX 技术和 SSE 状态
`lzcnt` | | [`lzcnt`] — 前导零计数
`movbe` | | [`movbe`] - 字节交换后移动数据
`pclmulqdq` | `sse2` | [`pclmulqdq`] — 打包无进位乘法 quadword
`popcnt` | | [`popcnt`] — 计算置 1 的位数
`rdrand` | | [`rdrand`] — 读取随机数
`rdseed` | | [`rdseed`] — 读取随机种子
`sha` | `sse2` | [SHA] — 安全哈希算法
`sse` | | [SSE] — 流式 SIMD 扩展
`sse2` | `sse` | [SSE2] — 流式 SIMD 扩展 2
`sse3` | `sse2` | [SSE3] — 流式 SIMD 扩展 3
`sse4.1` | `ssse3` | [SSE4.1] — 流式 SIMD 扩展 4.1
`sse4.2` | `sse4.1` | [SSE4.2] — 流式 SIMD 扩展 4.2
`ssse3` | `sse3` | [SSSE3] — 补充流式 SIMD 扩展 3
`xsave` | | [`xsave`] — 保存处理器扩展状态
`xsavec` | | [`xsavec`] — 压缩保存处理器扩展状态
`xsaveopt` | | [`xsaveopt`] — 优化保存处理器扩展状态
`xsaves` | | [`xsaves`] — 管理模式保存处理器扩展状态
{==+==}


{==+==}
[ADX]: https://en.wikipedia.org/wiki/Intel_ADX
[AES]: https://en.wikipedia.org/wiki/AES_instruction_set
[AVX]: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions
[AVX2]: https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#AVX2
[BMI1]: https://en.wikipedia.org/wiki/Bit_Manipulation_Instruction_Sets
[BMI2]: https://en.wikipedia.org/wiki/Bit_Manipulation_Instruction_Sets#BMI2
[`cmpxchg16b`]: https://www.felixcloutier.com/x86/cmpxchg8b:cmpxchg16b
[FMA3]: https://en.wikipedia.org/wiki/FMA_instruction_set
[`fxsave`]: https://www.felixcloutier.com/x86/fxsave
[`fxrstor`]: https://www.felixcloutier.com/x86/fxrstor
[`lzcnt`]: https://www.felixcloutier.com/x86/lzcnt
[`movbe`]: https://www.felixcloutier.com/x86/movbe
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