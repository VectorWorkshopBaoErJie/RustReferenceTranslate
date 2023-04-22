{==+==}
# Numeric types

## Integer types
{==+==}
# 数字类型

## 整数类型
{==+==}


{==+==}
The unsigned integer types consist of:
{==+==}
无符号整数类型包括:
{==+==}


{==+==}
Type   | Minimum | Maximum
-------|---------|-------------------
`u8`   | 0       | 2<sup>8</sup>-1
`u16`  | 0       | 2<sup>16</sup>-1
`u32`  | 0       | 2<sup>32</sup>-1
`u64`  | 0       | 2<sup>64</sup>-1
`u128` | 0       | 2<sup>128</sup>-1
{==+==}
类型   | 最小值 | 最大值
-------|---------|-------------------
`u8`   | 0       | 2<sup>8</sup>-1
`u16`  | 0       | 2<sup>16</sup>-1
`u32`  | 0       | 2<sup>32</sup>-1
`u64`  | 0       | 2<sup>64</sup>-1
`u128` | 0       | 2<sup>128</sup>-1
{==+==}


{==+==}
The signed two's complement integer types consist of:
{==+==}
有符号的二进制补码整数类型包括:
{==+==}


{==+==}
Type   | Minimum            | Maximum
-------|--------------------|-------------------
`i8`   | -(2<sup>7</sup>)   | 2<sup>7</sup>-1
`i16`  | -(2<sup>15</sup>)  | 2<sup>15</sup>-1
`i32`  | -(2<sup>31</sup>)  | 2<sup>31</sup>-1
`i64`  | -(2<sup>63</sup>)  | 2<sup>63</sup>-1
`i128` | -(2<sup>127</sup>) | 2<sup>127</sup>-1
{==+==}
类型   | 最小值            | 最大值
-------|--------------------|-------------------
`i8`   | -(2<sup>7</sup>)   | 2<sup>7</sup>-1
`i16`  | -(2<sup>15</sup>)  | 2<sup>15</sup>-1
`i32`  | -(2<sup>31</sup>)  | 2<sup>31</sup>-1
`i64`  | -(2<sup>63</sup>)  | 2<sup>63</sup>-1
`i128` | -(2<sup>127</sup>) | 2<sup>127</sup>-1
{==+==}


{==+==}
## Floating-point types

The IEEE 754-2008 "binary32" and "binary64" floating-point types are `f32` and
`f64`, respectively.
{==+==}
## 浮点数类型

IEEE 754-2008 标准中的 "binary32" 和 "binary64" 浮点数类型分别为 `f32` 和 `f64` 。
{==+==}


{==+==}
## Machine-dependent integer types

The `usize` type is an unsigned integer type with the same number of bits as the
platform's pointer type. It can represent every memory address in the process.

The `isize` type is a signed integer type with the same number of bits as the
platform's pointer type. The theoretical upper bound on object and array size
is the maximum `isize` value. This ensures that `isize` can be used to calculate
differences between pointers into an object or array and can address every byte
within an object along with one byte past the end.

`usize` and `isize` are at least 16-bits wide.
{==+==}
## 机器相关的整数类型

`usize` 类型是一种无符号整数类型，其位数与平台的指针类型相同。它可以表示进程中的每个内存地址。

`isize` 类型是一种带符号整数类型，其位数与平台的指针类型相同。对象和数组大小的理论上限是最大的 `isize` 值。
这确保了 `isize` 可用于计算指向对象或数组中的指针之间的差异，并且可以寻址对象内的每个字节以及超出末尾一个字节。

`usize` 和 `isize` 至少有 16 位宽度。
{==+==}


{==+==}
> **Note**: Many pieces of Rust code may assume that pointers, `usize`, and
> `isize` are either 32-bit or 64-bit. As a consequence, 16-bit
> pointer support is limited and may require explicit care and acknowledgment
> from a library to support.
{==+==}
> 注意：许多 Rust 代码可能假设指针、 `usize` 和 `isize` 是 32 位或 64 位。
> 因此，对于 16 位指针的支持是有限的，可能需要库显式关注并确认其支持。
{==+==}