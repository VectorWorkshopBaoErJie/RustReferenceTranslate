{==+==}
# Type Layout

The layout of a type is its size, alignment, and the relative offsets of its
fields. For enums, how the discriminant is laid out and interpreted is also part
of type layout.

Type layout can be changed with each compilation. Instead of trying to document
exactly what is done, we only document what is guaranteed today.
{==+==}
# 类型布局

类型布局包括其类型的字节大小、字节对齐方式以及类型字段的相对偏移量。
对于枚举类型，还包括判别值的布局及其解释方式。
类型布局并不是始终不变的，每次编译后很有可能因为优化等原因而改变。
这里只记录目前可以确保的内容。
{==+==}


{==+==}
## Size and Alignment

All values have an alignment and size.

The *alignment* of a value specifies what addresses are valid to store the value
at. A value of alignment `n` must only be stored at an address that is a
multiple of n. For example, a value with an alignment of 2 must be stored at an
even address, while a value with an alignment of 1 can be stored at any address.
Alignment is measured in bytes, and must be at least 1, and always a power of 2.
The alignment of a value can be checked with the [`align_of_val`] function.

The *size* of a value is the offset in bytes between successive elements in an
array with that item type including alignment padding. The size of a value is
always a multiple of its alignment. Note that some types are zero-sized; 0 is
considered a multiple of any alignment (for example, on some platforms, the type
`[u16; 0]` has size 0 and alignment 2). The size of a value can be checked with
the [`size_of_val`] function.

Types where all values have the same size and alignment, and both are known at
compile time, implement the [`Sized`] trait and can be checked with the
[`size_of`] and [`align_of`] functions. Types that are not [`Sized`] are known
as [dynamically sized types]. Since all values of a `Sized` type share the same
size and alignment, we refer to those shared values as the size of the type and
the alignment of the type respectively.
{==+==}
## 大小和对齐

所有的值都有对齐和大小。

值的 *对齐* 决定存储这个值的地址结构，对齐值为 `n` 的值必须存储在地址是 `n` 的倍数的地址上。
例如，对齐值为 2 的值必须存储在偶数地址上，而对齐值为 1 的值可以存储在任意地址。
对齐值以字节为单位衡量，必须至少为 1 ，而且始终是 2 的幂次方。
可以使用 [`align_of_val`] 函数检查值的对齐。

值的 *大小* 是指数组中具有该条目类型的连续元素之间的字节偏移量，包括对齐填充。值的大小始终是其对齐值的倍数。
需注意，某些类型的大小为零； 0 被认为是任何对齐值的倍数 (例如，在某些平台上，类型 `[u16; 0]` 的大小为 0 ，对齐值为 2 )。
可以使用 [`size_of_val`] 函数检查值的大小。

通常类型都实现了 [`Sized`] trait ，每个具体值都具有相同大小和对齐方式，并且在编译时已知。
可以使用 [`size_of`] 和 [`align_of`] 函数进行检查。
非 [`Sized`] 的类型称为 [动态大小类型][dynamically sized types] 。
对于 `Sized` 类型的 '值' 的大小和对齐，也称为 '类型' 的大小和对齐。
{==+==}


{==+==}
## Primitive Data Layout

The size of most primitives is given in this table.
{==+==}
## 原始数据布局

这张表格给出了大多数原始类型的大小。
{==+==}


{==+==}
| Type              | `size_of::<Type>()`|
|--                 |--                  |
| `bool`            | 1                  |
| `u8` / `i8`       | 1                  |
| `u16` / `i16`     | 2                  |
| `u32` / `i32`     | 4                  |
| `u64` / `i64`     | 8                  |
| `u128` / `i128`   | 16                 |
| `f32`             | 4                  |
| `f64`             | 8                  |
| `char`            | 4                  |
{==+==}
| 类型              | `size_of::<Type>()`|
|--                 |--                  |
| `bool`            | 1                  |
| `u8` / `i8`       | 1                  |
| `u16` / `i16`     | 2                  |
| `u32` / `i32`     | 4                  |
| `u64` / `i64`     | 8                  |
| `u128` / `i128`   | 16                 |
| `f32`             | 4                  |
| `f64`             | 8                  |
| `char`            | 4                  |
{==+==}


{==+==}
`usize` and `isize` have a size big enough to contain every address on the
target platform. For example, on a 32 bit target, this is 4 bytes and on a 64
bit target, this is 8 bytes.

Most primitives are generally aligned to their size, although this is
platform-specific behavior. In particular, on x86 u64 and f64 are only
aligned to 32 bits.
{==+==}
`usize` 和 `isize` 类型大小大于等于目标平台上单个地址的大小。
例如，在 32 位目标平台上，其类型大小为 4 个字节，在 64 位目标平台上，类型大小为 8 个字节。

虽然系统平台可以去特定实现，但大多数基本数据类型的对齐方式通常是按照该类型的大小对齐。
特别是，在 x86 架构下， `u64` 和 `f64` 仅按照 32 位对齐。
{==+==}


{==+==}
## Pointers and References Layout

Pointers and references have the same layout. Mutability of the pointer or
reference does not change the layout.

Pointers to sized types have the same size and alignment as `usize`.

Pointers to unsized types are sized. The size and alignment is guaranteed to be
at least equal to the size and alignment of a pointer.

> Note: Though you should not rely on this, all pointers to
> <abbr title="Dynamically Sized Types">DSTs</abbr> are currently twice the
> size of the size of `usize` and have the same alignment.
{==+==}
## 指针和引用的布局

指针和引用具有相同的布局。指针或引用的可变性不会改变其布局。
指向有大小的类型的指针与 `usize` 具有相同的大小和对齐。
指向无大小的类型的指针是依然是有大小的。
其至少保证等于一个指针的大小和对齐。

> 注意: 虽然这个值不保证确定，但目前，所有指向动态大小类型的指针都是 `usize` 大小的两倍，并具有相同的对齐。
{==+==}


{==+==}
## Array Layout

An array of `[T; N]` has a size of `size_of::<T>() * N` and the same alignment
of `T`. Arrays are laid out so that the zero-based `nth` element of the array
is offset from the start of the array by `n * size_of::<T>()` bytes.
{==+==}
## 数组布局

一个 `[T; N]` 的数组大小为 `size_of::<T>() * N` ，并且具有与 `T` 相同的对齐方式。
数组的布局使得从零开始的第 `n` 个元素相对于数组的起始位置偏移了 `n * size_of::<T>()` 字节。
{==+==}


{==+==}
## Slice Layout

Slices have the same layout as the section of the array they slice.

> Note: This is about the raw `[T]` type, not pointers (`&[T]`, `Box<[T]>`,
> etc.) to slices.
{==+==}
## 切片布局

切片的布局与其所裁切数组的部分相同。

> 注意: 这指的是原始 `[T]` 类型，而不是指向切片的指针 (`&[T]` ， `Box<[T]>` 等) 。
{==+==}


{==+==}
## `str` Layout
String slices are a UTF-8 representation of characters that have the same layout as slices of type `[u8]`.
{==+==}
## `str` 的布局

字符串切片是字符的 UTF-8 表示，其布局与类型为 `[u8]` 的切片相同。
{==+==}


{==+==}
## Tuple Layout

Tuples are laid out according to the [default representation][Default].

The exception to this is the unit tuple (`()`), which is guaranteed as a
zero-sized type to have a size of 0 and an alignment of 1.
{==+==}
## 元组布局

元组的布局遵循 [默认表示][Default] 。

例外情况是空元组 (`()`) ，它确保的类型的大小为 0 ，对齐为 1 。
{==+==}


{==+==}
## Trait Object Layout

Trait objects have the same layout as the value the trait object is of.

> Note: This is about the raw trait object types, not pointers (`&dyn Trait`,
> `Box<dyn Trait>`, etc.) to trait objects.
{==+==}
## Trait 对象布局

Trait 对象的布局与其包含的值相同。

> 注意: 这里是指原始的 Trait 对象类型，而不是指指针 (如 `&dyn Trait` 、 `Box<dyn Trait>` 等) 指向 Trait 对象的类型。
{==+==}


{==+==}
## Closure Layout

Closures have no layout guarantees.
{==+==}
## 闭包布局

闭包没有布局的保证。
{==+==}


{==+==}
## Representations

All user-defined composite types (`struct`s, `enum`s, and `union`s) have a
*representation* that specifies what the layout is for the type. The possible
representations for a type are:

- [Default]
- [`C`]
- The [primitive representations]
- [`transparent`]

The representation of a type can be changed by applying the `repr` attribute
to it. The following example shows a struct with a `C` representation.
{==+==}
## 表示形式

所有用户定义的复合类型 ( `struct` 、 `enum` 和 `union` ) 都有对应的 *表示形式* ，用于指定类型的布局。
类型的可能表示形式包括:

- [默认][Default]
- [`C`]
- [原始表示形式][primitive representations]
- [`transparent`]

可以通过在类型上应用 `repr` 属性来更改类型的表示形式。
以下示例展示具有 `C` 表示形式的结构体。
{==+==}


{==+==}
```rust
#[repr(C)]
struct ThreeInts {
    first: i16,
    second: i8,
    third: i32
}
```
{==+==}

{==+==}


{==+==}
The alignment may be raised or lowered with the `align` and `packed` modifiers
respectively. They alter the representation specified in the attribute.
If no representation is specified, the default one is altered.
{==+==}
通过 `align` 和 `packed` 修饰符可以分别提升或降低对齐值。
修饰符会修改属性所指定的表示形式，如果未指定表示形式属性，则修改默认的表示形式。
{==+==}


{==+==}
```rust
// Default representation, alignment lowered to 2.
#[repr(packed(2))]
struct PackedStruct {
    first: i16,
    second: i8,
    third: i32
}

// C representation, alignment raised to 8
#[repr(C, align(8))]
struct AlignedStruct {
    first: i16,
    second: i8,
    third: i32
}
```
{==+==}
```rust
// 默认的表示，对齐方式被降低到 2。
#[repr(packed(2))]
struct PackedStruct {
    first: i16,
    second: i8,
    third: i32
}

// C 表示，对齐方式被提高到 8。
#[repr(C, align(8))]
struct AlignedStruct {
    first: i16,
    second: i8,
    third: i32
}
```
{==+==}


{==+==}
> Note: As a consequence of the representation being an attribute on the item,
> the representation does not depend on generic parameters. Any two types with
> the same name have the same representation. For example, `Foo<Bar>` and
> `Foo<Baz>` both have the same representation.

The representation of a type can change the padding between fields, but does
not change the layout of the fields themselves. For example, a struct with a
`C` representation that contains a struct `Inner` with the default
representation will not change the layout of `Inner`.
{==+==}
> 注意: 由于表示形式是条目的属性，代表了不依赖于泛型参数。
> 任何两个具有相同名称的类型都具有相同的表示形式。例如， `Foo<Bar>` 和 `Foo<Baz>` 的相同。

类型的表示形式可以改变字段之间的填充，但不会改变字段本身的布局。
例如，具有 `C` 表示形式的结构体中包含具有默认表示形式的结构体 `Inner` ，不会改变 `Inner` 的布局。
{==+==}


{==+==}
### The Default Representation

Nominal types without a `repr` attribute have the default representation.
Informally, this representation is also called the `rust` representation.

The only data layout guarantees made by this representation are those required
for soundness. They are:

 1. The fields are properly aligned.
 2. The fields do not overlap.
 3. The alignment of the type is at least the maximum alignment of its fields.

Formally, the first guarantee means that the offset of any field is divisible by
that field's alignment. The second guarantee means that the fields can be
ordered such that the offset plus the size of any field is less than or equal to
the offset of the next field in the ordering. The ordering does not have to be
the same as the order in which the fields are specified in the declaration of
the type.

Be aware that the second guarantee does not imply that the fields have distinct
addresses: zero-sized types may have the same address as other fields in the
same struct.

There are no other guarantees of data layout made by this representation.
{==+==}
### 默认表示形式

没有 `repr` 属性的具名类型采用默认表示形式。
该表示形式也被称为 `rust` 表示形式，但这个概念并不正式。
此表示形式做了数据布局的基本保证，以期望保持数据的正确性。
意味着:

1. 字段正确对齐。
2. 字段不重叠。
3. 类型的对齐至少是其字段的最大对齐。

严格地说，第一个保证意味着任何字段的偏移量都是该字段的对齐方式的倍数。
第二个保证不排除对字段进行排序，使得任何字段的偏移量加上其大小小于或等于排序中下一个字段的偏移量。排序时不保证与在类型声明中指定字段的顺序相同。

请注意，第二个保证并不保证字段具有不同的地址: 零大小类型可能与值结构中其他字段具有相同的地址。

此表示形式没有其他数据布局保证。
{==+==}


{==+==}
### The `C` Representation

The `C` representation is designed for dual purposes. One purpose is for
creating types that are interoperable with the C Language. The second purpose is
to create types that you can soundly perform operations on that rely on data
layout such as reinterpreting values as a different type.

Because of this dual purpose, it is possible to create types that are not useful
for interfacing with the C programming language.

This representation can be applied to structs, unions, and enums. The exception
is [zero-variant enums] for which the `C` representation is an error.
{==+==}
### `C` 表示形式

`C` 表示形式旨在实现两个目的。一个目的是创建可与 C 语言互操作的类型，另一个目的是创建可以对其执行依赖于数据布局的操作 (比如将值重新解释为不同类型) 的类型。因些，此表示形式不一定仅是 C 编程语言的接口。

此表示形式可以应用于结构体、联合体和枚举。
但，[空变体枚举][zero-variant enums] 不支持 `C` 表示形式。
{==+==}


{==+==}
#### `#[repr(C)]` Structs

The alignment of the struct is the alignment of the most-aligned field in it.

The size and offset of fields is determined by the following algorithm.

Start with a current offset of 0 bytes.

For each field in declaration order in the struct, first determine the size and
alignment of the field. If the current offset is not a multiple of the field's
alignment, then add padding bytes to the current offset until it is a multiple
of the field's alignment. The offset for the field is what the current offset
is now. Then increase the current offset by the size of the field.

Finally, the size of the struct is the current offset rounded up to the nearest
multiple of the struct's alignment.

Here is this algorithm described in pseudocode.
{==+==}
#### `#[repr(C)]` 结构体

结构体的对齐是其中对齐要求最高的字段的对齐。

字段的大小和偏移量由以下算法决定。

将当前偏移量初始化为 0 字节。

按照结构体中字段的声明顺序，确定字段的大小和对齐。
如果当前偏移量不是字段对齐的倍数，则添加填充字节，至到满足倍数。
字段的偏移量就是当前偏移量。
然后通过字段的大小增加当前偏移量。

最后，结构体的大小是将当前偏移量向上舍入到结构体对齐的最接近的倍数。

以下是这个算法的伪代码描述。
{==+==}


{==+==}
<!-- ignore: pseudocode -->
```rust,ignore
/// Returns the amount of padding needed after `offset` to ensure that the
/// following address will be aligned to `alignment`.
fn padding_needed_for(offset: usize, alignment: usize) -> usize {
    let misalignment = offset % alignment;
    if misalignment > 0 {
        // round up to next multiple of `alignment`
        alignment - misalignment
    } else {
        // already a multiple of `alignment`
        0
    }
}

struct.alignment = struct.fields().map(|field| field.alignment).max();

let current_offset = 0;

for field in struct.fields_in_declaration_order() {
    // Increase the current offset so that it's a multiple of the alignment
    // of this field. For the first field, this will always be zero.
    // The skipped bytes are called padding bytes.
    current_offset += padding_needed_for(current_offset, field.alignment);

    struct[field].offset = current_offset;

    current_offset += field.size;
}

struct.size = current_offset + padding_needed_for(current_offset, struct.alignment);
```
{==+==}
<!-- ignore: pseudocode -->
```rust,ignore
/// 返回在偏移 `offset` 之后需要的填充量，以确保以下地址将对齐到 `alignment`。
fn padding_needed_for(offset: usize, alignment: usize) -> usize {
    let misalignment = offset % alignment;
    if misalignment > 0 {
        // 向上舍入到下一个 `alignment` 的倍数
        alignment - misalignment
    } else {
        // 已经是 `alignment` 的倍数
        0
    }
}

// 计算结构体中最大字段的对齐方式，作为结构体自身的对齐方式
struct.alignment = struct.fields().map(|field| field.alignment).max();

let current_offset = 0;

for field in struct.fields_in_declaration_order() {
    // 增加当前偏移量，使其成为此字段的对齐方式的倍数。对于第一个字段，这将始终为零。
    // 被跳过的字节称为填充字节。
    current_offset += padding_needed_for(current_offset, field.alignment);

    struct[field].offset = current_offset;

    current_offset += field.size;
}

// 结构体的大小为当前偏移量加上在此处添加的填充量，以确保结构体整体对齐到 `alignment` 的倍数
struct.size = current_offset + padding_needed_for(current_offset, struct.alignment);
```
{==+==}


{==+==}
<div class="warning">

Warning: This pseudocode uses a naive algorithm that ignores overflow issues for
the sake of clarity. To perform memory layout computations in actual code, use
[`Layout`].

</div>
{==+==}
<div class="warning">

警告：此伪代码使用一种简单算法，为了清晰起见忽略了溢出问题。对于实际代码中执行内存布局计算，请使用 [`Layout`] 。

</div>
{==+==}


{==+==}
> Note: This algorithm can produce zero-sized structs. In C, an empty struct
> declaration like `struct Foo { }` is illegal. However, both gcc and clang
> support options to enable such structs, and assign them size zero. C++, in
> contrast, gives empty structs a size of 1, unless they are inherited from or
> they are fields that have the `[[no_unique_address]]` attribute, in which
> case they do not increase the overall size of the struct.
{==+==}
> 注意: 此算法可能会产生大小为零的结构体。在 C 中，像 `struct Foo { }` 这样的空结构体声明是不合法的。
> 但是，gcc 和 clang 都支持启用这样的结构体，并将其大小分配为零。
> 而 C++ 给空结构体大小分配为 1 ，如果是从其他结构体继承，或者是带有 `[[no_unique_address]]` 属性的字段时，不会增加结构体的整体大小。
{==+==}


{==+==}
#### `#[repr(C)]` Unions

A union declared with `#[repr(C)]` will have the same size and alignment as an
equivalent C union declaration in the C language for the target platform.
The union will have a size of the maximum size of all of its fields rounded to
its alignment, and an alignment of the maximum alignment of all of its fields.
These maximums may come from different fields.
{==+==}
#### `#[repr(C)]` 联合体

使用 `#[repr(C)]` 声明的联合体将与目标平台上等效的 C 语言联合体声明具有相同的大小和对齐。
该联合体的大小将是其所有字段的最大大小舍入为其对齐，其对齐将是其所有字段的最大对齐。这些最大值可以来自不同的字段。
{==+==}


{==+==}
```rust
#[repr(C)]
union Union {
    f1: u16,
    f2: [u8; 4],
}

assert_eq!(std::mem::size_of::<Union>(), 4);  // From f2
assert_eq!(std::mem::align_of::<Union>(), 2); // From f1

#[repr(C)]
union SizeRoundedUp {
   a: u32,
   b: [u16; 3],
}

assert_eq!(std::mem::size_of::<SizeRoundedUp>(), 8);  // Size of 6 from b,
                                                      // rounded up to 8 from
                                                      // alignment of a.
assert_eq!(std::mem::align_of::<SizeRoundedUp>(), 4); // From a
```
{==+==}
```rust
#[repr(C)]
union Union {
    f1: u16,
    f2: [u8; 4],
}

assert_eq!(std::mem::size_of::<Union>(), 4);  // 来自 f2
assert_eq!(std::mem::align_of::<Union>(), 2); // 来自 f1

#[repr(C)]
union SizeRoundedUp {
   a: u32,
   b: [u16; 3],
}

assert_eq!(std::mem::size_of::<SizeRoundedUp>(), 8);  // 大小为 6，来自 b，舍入为 8，来自 a 的对齐
assert_eq!(std::mem::align_of::<SizeRoundedUp>(), 4); // 来自 a
```
{==+==}


{==+==}
#### `#[repr(C)]` Field-less Enums

For [field-less enums], the `C` representation has the size and alignment of
the default `enum` size and alignment for the target platform's C ABI.

> Note: The enum representation in C is implementation defined, so this is
> really a "best guess". In particular, this may be incorrect when the C code
> of interest is compiled with certain flags.
{==+==}
#### `#[repr(C)]` 无字段的枚举

对于 [无字段的枚举][field-less enums] ，其 `C` 表示形式具有目标平台 C ABI 的默认 `enum` 大小和对齐。

> 注意：C 中的枚举表示形式是实现定义的，因此这只是一个 "最佳猜测" 。特别是，当 C 代码使用某些标志编译时，可能会不正确。
{==+==}


{==+==}
<div class="warning">

Warning: There are crucial differences between an `enum` in the C language and
Rust's [field-less enums] with this representation. An `enum` in C is
mostly a `typedef` plus some named constants; in other words, an object of an
`enum` type can hold any integer value. For example, this is often used for
bitflags in `C`. In contrast, Rust’s [field-less enums] can only legally hold
the discriminant values, everything else is [undefined behavior]. Therefore,
using a field-less enum in FFI to model a C `enum` is often wrong.

</div>
{==+==}
<div class="warning">

警告：C 语言中的 `enum` 和 Rust 的 [无字段的枚举][field-less enums] 在这种表示形式下存在关键差异。
在 C 中，`enum` 主要是 `typedef` 与一些命名常量组合，其 `enum` 类型对象可以容纳任意整数值。
比如，在 `C` 中，通常用于标志位。
相比之下，Rust 的 [无字段的枚举][field-less enums] 只能合法地容纳判别值，其他状况都是 [未定义行为][undefined behavior] 。
因此，在 FFI 中使用无字段的枚举来模拟 C `enum` 通常是错误的。

</div>
{==+==}


{==+==}
#### `#[repr(C)]` Enums With Fields

The representation of a `repr(C)` enum with fields is a `repr(C)` struct with
two fields, also called a "tagged union" in C:

- a `repr(C)` version of the enum with all fields removed ("the tag")
- a `repr(C)` union of `repr(C)` structs for the fields of each variant that had
  them ("the payload")

> Note: Due to the representation of `repr(C)` structs and unions, if a variant
> has a single field there is no difference between putting that field directly
> in the union or wrapping it in a struct; any system which wishes to manipulate
> such an `enum`'s representation may therefore use whichever form is more
> convenient or consistent for them.
{==+==}
#### `#[repr(C)]` 枚举类型的字段

使用 `#[repr(C)]` 声明的带有字段的枚举类型的表示形式是带有两个字段的 `repr(C)` 结构体，也称为 C 语言 "标签化联合体" ，即：

- 去掉所有字段的 `repr(C)` 版本的枚举类型 ("标签")
-  `repr(C)` 联合体，其中包含每个有字段的变体的 `repr(C)` 结构体 ("有效载荷")

> 注意: 对于 `repr(C)` 结构体和联合体的表示形式，如果变体只有一个字段，在联合体中直接放置该字段或将其包装在结构体中就没有区别；
> 因此，如果希望操作这种 `enum` 则其表示形式可以使用对其来说更方便或更一致的形式。
{==+==}


{==+==}
```rust
// This Enum has the same representation as ...
#[repr(C)]
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ... this struct.
#[repr(C)]
struct MyEnumRepr {
    tag: MyEnumDiscriminant,
    payload: MyEnumFields,
}

// This is the discriminant enum.
#[repr(C)]
enum MyEnumDiscriminant { A, B, C, D }

// This is the variant union.
#[repr(C)]
union MyEnumFields {
    A: MyAFields,
    B: MyBFields,
    C: MyCFields,
    D: MyDFields,
}

#[repr(C)]
#[derive(Copy, Clone)]
struct MyAFields(u32);

#[repr(C)]
#[derive(Copy, Clone)]
struct MyBFields(f32, u64);

#[repr(C)]
#[derive(Copy, Clone)]
struct MyCFields { x: u32, y: u8 }

// This struct could be omitted (it is a zero-sized type), and it must be in
// C/C++ headers.
#[repr(C)]
#[derive(Copy, Clone)]
struct MyDFields;
```
{==+==}
```rust
// 这个枚举与下面的结构体具有相同的表示形式
#[repr(C)]
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ... 这个结构体
#[repr(C)]
struct MyEnumRepr {
    tag: MyEnumDiscriminant, // 枚举的判别值
    payload: MyEnumFields, // 枚举的数据部分
}

// 这是枚举的判别值枚举
#[repr(C)]
enum MyEnumDiscriminant { A, B, C, D }

// 这是变体的联合体
#[repr(C)]
union MyEnumFields {
    A: MyAFields,
    B: MyBFields,
    C: MyCFields,
    D: MyDFields,
}

#[repr(C)]
#[derive(Copy, Clone)]
struct MyAFields(u32);

#[repr(C)]
#[derive(Copy, Clone)]
struct MyBFields(f32, u64);

#[repr(C)]
#[derive(Copy, Clone)]
struct MyCFields { x: u32, y: u8 }

// 这个结构体可以省略 (它是零大小的类型) ，并且它必须在 C/C++ 头文件中。
#[repr(C)]
#[derive(Copy, Clone)]
struct MyDFields;
```
{==+==}


{==+==}
> Note: `union`s with non-`Copy` fields are unstable, see [55149].

### Primitive representations

The *primitive representations* are the representations with the same names as
the primitive integer types. That is: `u8`, `u16`, `u32`, `u64`, `u128`,
`usize`, `i8`, `i16`, `i32`, `i64`, `i128`, and `isize`.

Primitive representations can only be applied to enumerations and have
different behavior whether the enum has fields or no fields. It is an error
for [zero-variant enums] to have a primitive representation. Combining
two primitive representations together is an error.
{==+==}
> 注意: 带有非 `Copy` 字段的 `union` 是未稳定的，请参见 [55149]。

### 原始表示形式

*原始表示形式* 是与原始整数类型具有相同名称的表示。
即：`u8`、`u16`、`u32`、`u64`、`u128`、`usize`、`i8`、`i16`、`i32`、`i64`、`i128`和`isize`。

原始表示形式仅适用于枚举类型，并根据枚举是否有字段具有不同的行为。
对于 [零变体枚举][zero-variant enums] 来说，使用原始表示形式是错误的。
将两个原始表示形式结合在一起也是错误的。
{==+==}


{==+==}
#### Primitive Representation of Field-less Enums

For [field-less enums], primitive representations set the size and alignment to
be the same as the primitive type of the same name. For example, a field-less
enum with a `u8` representation can only have discriminants between 0 and 255
inclusive.
{==+==}
#### 无字段枚举的原始表示形式

对于 [无字段枚举][field-less enums] ，原始表示形式将大小和对齐设置为与同名的原始类型相同。
例如，具有 `u8` 表示的无字段枚举只能具有 0 到 255 之间 (包括 0 和 255 ) 的判别值。
{==+==}


{==+==}
#### Primitive Representation of Enums With Fields

The representation of a primitive representation enum is a `repr(C)` union of
`repr(C)` structs for each variant with a field. The first field of each struct
in the union is the primitive representation version of the enum with all fields
removed ("the tag") and the remaining fields are the fields of that variant.

> Note: This representation is unchanged if the tag is given its own member in
> the union, should that make manipulation more clear for you (although to
> follow the C++ standard the tag member should be wrapped in a `struct`).
{==+==}
#### 具有字段的枚举的基本表示形式

具有字段的基本表示形式枚举的表示形式是 `repr(C)` 联合体，其中包含每个变量的 `repr(C)` 结构体。
联合体中每个结构体的第一个字段是去除所有字段的枚举的基本表示形式版本 ("标签") ，剩余字段是该变量的字段。

> 注意：如果标签在联合体中有自己的成员，则该表示形式不会改变，这样可以更清晰地进行操作 (尽管为了遵循 C++ 标准，标签成员应该包装在一个 `struct` 中)。
{==+==}


{==+==}
```rust
// This enum has the same representation as ...
#[repr(u8)]
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ... this union.
#[repr(C)]
union MyEnumRepr {
    A: MyVariantA,
    B: MyVariantB,
    C: MyVariantC,
    D: MyVariantD,
}

// This is the discriminant enum.
#[repr(u8)]
#[derive(Copy, Clone)]
enum MyEnumDiscriminant { A, B, C, D }

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantA(MyEnumDiscriminant, u32);

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantB(MyEnumDiscriminant, f32, u64);

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantC { tag: MyEnumDiscriminant, x: u32, y: u8 }

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantD(MyEnumDiscriminant);
```
{==+==}
```rust
// 这个枚举和...具有相同的表示形式
#[repr(u8)]
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ... 这个联合体。
#[repr(C)]
union MyEnumRepr {
    A: MyVariantA,
    B: MyVariantB,
    C: MyVariantC,
    D: MyVariantD,
}

// 这是判别值枚举。
#[repr(u8)]
#[derive(Copy, Clone)]
enum MyEnumDiscriminant { A, B, C, D }

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantA(MyEnumDiscriminant, u32);

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantB(MyEnumDiscriminant, f32, u64);

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantC { tag: MyEnumDiscriminant, x: u32, y: u8 }

#[repr(C)]
#[derive(Clone, Copy)]
struct MyVariantD(MyEnumDiscriminant);
```
{==+==}


{==+==}
> Note: `union`s with non-`Copy` fields are unstable, see [55149].
{==+==}
> 注意: 具有非 `Copy` 字段的联合体是未稳定的，请参见 [55149] 。
{==+==}


{==+==}
#### Combining primitive representations of enums with fields and `#[repr(C)]`

For enums with fields, it is also possible to combine `repr(C)` and a
primitive representation (e.g., `repr(C, u8)`). This modifies the [`repr(C)`] by
changing the representation of the discriminant enum to the chosen primitive
instead. So, if you chose the `u8` representation, then the discriminant enum
would have a size and alignment of 1 byte.

The discriminant enum from the example [earlier][`repr(C)`] then becomes:
{==+==}
#### 将带字段的枚举的原始表示形式与 `#[repr(C)]` 结合使用

对于带字段的枚举，还可以将 `repr(C)` 和原始表示形式 (例如 `repr(C, u8)`) 结合使用。这会修改 [`repr(C)`] ，将判别值枚举的表示形式更改为所选择的原始表示形式。
因此，如果你选择了 `u8` 表示形式，则判别值枚举的大小和对齐将为 1 字节。来自 [之前][`repr(C)`] 示例的判别值枚举如下：
{==+==}


{==+==}
```rust
#[repr(C, u8)] // `u8` was added
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ...

#[repr(u8)] // So `u8` is used here instead of `C`
enum MyEnumDiscriminant { A, B, C, D }

// ...
```
{==+==}
```rust
#[repr(C, u8)] // 添加了 `u8`
enum MyEnum {
    A(u32),
    B(f32, u64),
    C { x: u32, y: u8 },
    D,
 }

// ...

#[repr(u8)] // 所以这里使用了 `u8` 而不是 `C`
enum MyEnumDiscriminant { A, B, C, D }

// ...
```
{==+==}


{==+==}
For example, with a `repr(C, u8)` enum it is not possible to have 257 unique
discriminants ("tags") whereas the same enum with only a `repr(C)` attribute
will compile without any problems.

Using a primitive representation in addition to `repr(C)` can change the size of
an enum from the `repr(C)` form:
{==+==}
例如，对于一个 `repr(C, u8)` 枚举，不可能有 257 个唯一的判别值 ("标签") ，而只有一个 `repr(C)` 属性的相同枚举将在没有任何问题的情况下编译。

在 `repr(C)` 之外使用原始表示形式可能会改变枚举的大小:
{==+==}


{==+==}
```rust
#[repr(C)]
enum EnumC {
    Variant0(u8),
    Variant1,
}

#[repr(C, u8)]
enum Enum8 {
    Variant0(u8),
    Variant1,
}

#[repr(C, u16)]
enum Enum16 {
    Variant0(u8),
    Variant1,
}

// The size of the C representation is platform dependant
assert_eq!(std::mem::size_of::<EnumC>(), 8);
// One byte for the discriminant and one byte for the value in Enum8::Variant0
assert_eq!(std::mem::size_of::<Enum8>(), 2);
// Two bytes for the discriminant and one byte for the value in Enum16::Variant0
// plus one byte of padding.
assert_eq!(std::mem::size_of::<Enum16>(), 4);
```
{==+==}
```rust
#[repr(C)]
enum EnumC {
    Variant0(u8),
    Variant1,
}

#[repr(C, u8)]
enum Enum8 {
    Variant0(u8),
    Variant1,
}

#[repr(C, u16)]
enum Enum16 {
    Variant0(u8),
    Variant1,
}

// C 表示形式的大小取决于平台
assert_eq!(std::mem::size_of::<EnumC>(), 8);
// Enum8::Variant0 中判别值和值各占 1 个字节
assert_eq!(std::mem::size_of::<Enum8>(), 2);
// Enum16::Variant0 中判别值和值各占 1 个字节，再加上 1 个字节的填充
assert_eq!(std::mem::size_of::<Enum16>(), 4);
```
{==+==}


{==+==}
[`repr(C)`]: #reprc-enums-with-fields
{==+==}

{==+==}


{==+==}
### The alignment modifiers

The `align` and `packed` modifiers can be used to respectively raise or lower
the alignment of `struct`s and `union`s. `packed` may also alter the padding
between fields (although it will not alter the padding inside of any field).

The alignment is specified as an integer parameter in the form of
`#[repr(align(x))]` or `#[repr(packed(x))]`. The alignment value must be a
power of two from 1 up to 2<sup>29</sup>. For `packed`, if no value is given,
as in `#[repr(packed)]`, then the value is 1.

For `align`, if the specified alignment is less than the alignment of the type
without the `align` modifier, then the alignment is unaffected.

For `packed`, if the specified alignment is greater than the type's alignment
without the `packed` modifier, then the alignment and layout is unaffected.
The alignments of each field, for the purpose of positioning fields, is the
smaller of the specified alignment and the alignment of the field's type.
Inter-field padding is guaranteed to be the minimum required in order to
satisfy each field's (possibly altered) alignment (although note that, on its
own, `packed` does not provide any guarantee about field ordering). An
important consequence of these rules is that a type with `#[repr(packed(1))]`
(or `#[repr(packed)]`) will have no inter-field padding.

The `align` and `packed` modifiers cannot be applied on the same type and a
`packed` type cannot transitively contain another `align`ed type. `align` and
`packed` may only be applied to the [default] and [`C`] representations.

The `align` modifier can also be applied on an `enum`.
When it is, the effect on the `enum`'s alignment is the same as if the `enum`
was wrapped in a newtype `struct` with the same `align` modifier.
{==+==}
### 对齐修饰符

`align` 和 `packed` 修饰符可以用于分别提高或降低 `struct` 和 `union` 的对齐。
 `packed` 也可以改变字段之间的填充 (但不会改变任何字段内部的填充) 。

 对齐是以整数形式的参数指定的，形式为 `#[repr(align(x))]` 或 `#[repr(packed(x))]` 。
 对齐值必须是从 1 到 2<sup>29</sup> 的 2 的幂。对于 `packed`，如果没有给出值，例如 `#[repr(packed)]`，那么该值为 1。
 对于 `align` ，如果指定的对齐小于没有 `align` 修饰符的类型的对齐，则对齐不受影响。

 对于 `packed`，如果指定的对齐大于没有 `packed` 修饰符的类型的对齐，则对齐和布局不受影响。
 对于定位字段的目的，每个字段的对齐是指定的对齐和字段类型的对齐中较小的一个。
 确保字段间的填充是最小的，以满足每个字段的 (可能已更改的) 对齐 (需注意，单独使用 `packed` 不能提供有关字段顺序的任何保证) 。
 这些规则的一个重要后果是，具有 `#[repr(packed(1))]` (或 `#[repr(packed)]`) 的类型将没有字段间的填充。

 `align` 和 `packed` 修饰符不能应用于同一类型，`packed` 类型不能以传递形式包含另一个 `align` 化的类型。
 `align` 和 `packed` 只能应用于 [默认] 和 [`C`] 表示形式。
 
 `align` 修饰符也可以应用于 `enum`。 
 当这样做时，`enum` 的对齐的效果与如果将 `enum` 包装在具有相同 `align` 修饰符的新类型 `struct` 中相同。
{==+==}


{==+==}
<div class="warning">

***Warning:*** Dereferencing an unaligned pointer is [undefined behavior] and
it is possible to [safely create unaligned pointers to `packed` fields][27060].
Like all ways to create undefined behavior in safe Rust, this is a bug.

</div>
{==+==}
<div class="warning">

***警告:*** 对未对齐的指针进行解引用是 [未定义行为][undefined behavior] ，可以 [安全地创建指向 `packed` 字段的未对齐指针][27060]。
这与 Rust 所有安全代码中所创建的未定义行为一样，是一个错误。

</div>
{==+==}


{==+==}
### The `transparent` Representation

The `transparent` representation can only be used on a [`struct`][structs]
or an [`enum`][enumerations] with a single variant that has:

- a single field with non-zero size, and
- any number of fields with size 0 and alignment 1 (e.g. [`PhantomData<T>`]).

Structs and enums with this representation have the same layout and ABI
as the single non-zero sized field.

This is different than the `C` representation because
a struct with the `C` representation will always have the ABI of a `C` `struct`
while, for example, a struct with the `transparent` representation with a
primitive field will have the ABI of the primitive field.

Because this representation delegates type layout to another type, it cannot be
used with any other representation.
{==+==}
### `transparent` 表示形式

`transparent` 表示形式只能用于具有以下特点的 `struct` 或 `enum` 中的单个变体:

- 具有非零大小的单个字段，且
- 具有大小为 0 且对齐为 1 的任意数量的字段 (例如 [`PhantomData<T>`]) 。

具有此表示形式的结构体和枚举具有与单个非零大小字段相同的布局和 ABI 。

这与 `C` 表示形式不同，因为具有 `C` 表示形式的结构体将始终具有 `C` `struct` 的 ABI，
而比如具有原始字段的 `transparent` 表示形式的结构体将具有原始字段的 ABI 。

由于此表示形式将类型布局委托给另一种类型，因此无法与任何其他表示形式一起使用。
{==+==}


{==+==}
[`align_of_val`]: ../std/mem/fn.align_of_val.html
[`size_of_val`]: ../std/mem/fn.size_of_val.html
[`align_of`]: ../std/mem/fn.align_of.html
[`size_of`]: ../std/mem/fn.size_of.html
[`Sized`]: ../std/marker/trait.Sized.html
[`Copy`]: ../std/marker/trait.Copy.html
[dynamically sized types]: dynamically-sized-types.md
[field-less enums]: items/enumerations.md#field-less-enum
[enumerations]: items/enumerations.md
[zero-variant enums]: items/enumerations.md#zero-variant-enums
[undefined behavior]: behavior-considered-undefined.md
[27060]: https://github.com/rust-lang/rust/issues/27060
[55149]: https://github.com/rust-lang/rust/issues/55149
[`PhantomData<T>`]: special-types-and-traits.md#phantomdatat
[Default]: #the-default-representation
[`C`]: #the-c-representation
[primitive representations]: #primitive-representations
[structs]: items/structs.md
[`transparent`]: #the-transparent-representation
[`Layout`]: ../std/alloc/struct.Layout.html
{==+==}

{==+==}