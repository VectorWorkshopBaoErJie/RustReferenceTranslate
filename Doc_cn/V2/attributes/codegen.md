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