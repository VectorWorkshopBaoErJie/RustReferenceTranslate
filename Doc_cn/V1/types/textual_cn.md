{==+==}
# Textual types
{==+==}
# 文本类型
{==+==}


{==+==}
The types `char` and `str` hold textual data.

A value of type `char` is a [Unicode scalar value] (i.e. a code point that is
not a surrogate), represented as a 32-bit unsigned word in the 0x0000 to 0xD7FF
or 0xE000 to 0x10FFFF range. It is immediate [Undefined Behavior] to create a
`char` that falls outside this range. A `[char]` is effectively a UCS-4 / UTF-32
string of length 1.

A value of type `str` is represented the same way as `[u8]`, it is a slice of
8-bit unsigned bytes. However, the Rust standard library makes extra assumptions
about `str`: methods working on `str` assume and ensure that the data in there
is valid UTF-8. Calling a `str` method with a non-UTF-8 buffer can cause
[Undefined Behavior] now or in the future.

Since `str` is a [dynamically sized type], it can only be instantiated through a
pointer type, such as `&str`.
{==+==}
类型 `char` 和 `str` 用于存储文本数据。

`char` 类型的值是 [Unicode 标量值][Unicode scalar value] (不是编码的替代品) ，为 32 位无符号整数，位于 0x0000 到 0xD7FF 或 0xE000 到 0x10FFFF 范围之间。
创建超出范围的 `char` 是 [未定义行为][Undefined Behavior] 。 `[char]` 是实际长度为 1 的 UCS-4 / UTF-32 字符串。

`str` 类型的值与 `[u8]` 相同，是 8 位无符号字节的切片。
但是，Rust 标准库对 `str` 做了一些额外的校验: `str` 上的方法将进行评估，以确保其中的数据是有效的 UTF-8 。
使用非 UTF-8 缓冲区调用 `str` 方法，可能导致立即触发或延后的 [未定义行为][Undefined Behavior] 。

由于 `str` 是 [动态大小类型][dynamically sized type] ，因此只能通过指针类型 (例如 `&str` ) 来实例化。
{==+==}


{==+==}
[Unicode scalar value]: http://www.unicode.org/glossary/#unicode_scalar_value
[Undefined Behavior]: ../behavior-considered-undefined.md
[dynamically sized type]: ../dynamically-sized-types.md
{==+==}

{==+==}