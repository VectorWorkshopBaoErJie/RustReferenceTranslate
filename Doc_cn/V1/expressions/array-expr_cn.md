{==+==}
# Array and array index expressions

## Array expressions
{==+==}
# 数组和数组索引表达式

## 数组表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ArrayExpression_ :\
> &nbsp;&nbsp; `[` _ArrayElements_<sup>?</sup> `]`
>
> _ArrayElements_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Expression_] ( `,` [_Expression_] )<sup>\*</sup> `,`<sup>?</sup>\
> &nbsp;&nbsp; | [_Expression_] `;` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _数组表达式_ :\
> &nbsp;&nbsp; `[` _数组元素组_<sup>?</sup> `]`
>
> _数组元素组_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式_][_Expression_] ( `,` [_表达式_][_Expression_] )<sup>\*</sup> `,`<sup>?</sup>\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `;` [_表达式_][_Expression_]
{==+==}


{==+==}
*Array expressions* construct [arrays][array].
Array expressions come in two forms.

The first form lists out every value in the array.
The syntax for this form is a comma-separated list of expressions of uniform type enclosed in square brackets.
This produces an array containing each of these values in the order they are written.

The syntax for the second form is two expressions separated by a semicolon (`;`) enclosed in square brackets.
The expression before the `;` is called the *repeat operand*.
The expression after the `;` is called the *length operand*.
It must have type `usize` and be a [constant expression], such as a [literal] or a [constant item].
An array expression of this form creates an array with the length of the value of the length operand with each element being a copy of the repeat operand.
That is, `[a; b]` creates an array containing `b` copies of the value of `a`.
If the length operand has a value greater than 1 then this requires that the type of the repeat operand is [`Copy`] or that it must be a [path] to a constant item.

When the repeat operand is a constant item, it is evaluated the length operand's value times.
If that value is `0`, then the constant item is not evaluated at all.
For expressions that are not a constant item, it is evaluated exactly once, and then the result is copied the length operand's value times.
{==+==}
*数组表达式* 构造 [数组][array] 。
数组表达式有两种形式。

第一种形式列出数组中的每个值。
这种形式的语法是将类型相同的表达式列表用逗号隔开并用方括号括起来。
这会产生一个包含按照书写顺序排列的这些值的数组。

第二种形式的语法是两个表达式之间用分号 (`;`) 隔开并用方括号括起来。
分号前的表达式称为 *重复操作数* 。
分号后的表达式称为 *长度操作数* 。
它必须是类型为 usize 的 [常量表达式][constant expression] ，例如  [字面值][literal] 或 [常量条目][constant item] 。
这种形式的数组表达式会创建一个长度为长度操作数的值的数组，每个元素都是重复操作数的副本。
也就是说，[a; b] 创建一个包含 b 个 a 值的副本的数组。
如果长度操作数的值大于 1 ，则要求重复操作数的类型是 [`Copy`] ，或者必须是指向常量条目的 [路径][path] 。

当重复操作数是常量条目时，它会被计算长度操作数的值的次数。
如果该值为 0 ，则不会对常量条目进行计算。
对于不是常量条目的表达式，则进行一次计算，然后将结果以长度操作数的次数复制。
{==+==}


{==+==}
```rust
[1, 2, 3, 4];
["a", "b", "c", "d"];
[0; 128];              // array with 128 zeros
[0u8, 0u8, 0u8, 0u8,];
[[1, 0, 0], [0, 1, 0], [0, 0, 1]]; // 2D array
const EMPTY: Vec<i32> = Vec::new();
[EMPTY; 2];
```
{==+==}
```rust
[1, 2, 3, 4];
["a", "b", "c", "d"];
[0; 128];              // 数组为 128 个 0 
[0u8, 0u8, 0u8, 0u8,];
[[1, 0, 0], [0, 1, 0], [0, 0, 1]]; // 二维数组
const EMPTY: Vec<i32> = Vec::new();
[EMPTY; 2];
```
{==+==}


{==+==}
## Array and slice indexing expressions
{==+==}
## 数组和切片索引表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _IndexExpression_ :\
> &nbsp;&nbsp; [_Expression_] `[` [_Expression_] `]`
{==+==}
> **<sup>语法</sup>**\
> _索引表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `[` [_表达式_][_Expression_] `]`
{==+==}


{==+==}
[Array] and [slice]-typed values can be indexed by writing a square-bracket-enclosed expression of type `usize` (the index) after them.
When the array is mutable, the resulting [memory location] can be assigned to.

For other types an index expression `a[b]` is equivalent to `*std::ops::Index::index(&a, b)`, or `*std::ops::IndexMut::index_mut(&mut a, b)` in a mutable place expression context.
Just as with methods, Rust will also insert dereference operations on `a` repeatedly to find an implementation.

Indices are zero-based for arrays and slices.
Array access is a [constant expression], so bounds can be checked at compile-time with a constant index value.
Otherwise a check will be performed at run-time that will put the thread in a _panicked state_ if it fails.
{==+==}
[数组][Array] 和 [切片][slice] 类型的值可以通过在它们后面写一个类型为 `usize` 的方括号括起来的表达式进行索引。
当数组是可变的，其结果的 [内存位置][memory location] 可以被赋值。

对于其他类型，索引表达式 `a[b]` 等价于 `*std::ops::Index::index(&a, b)` ，或者，在可变占位表达式的上下文中等价于 `*std::ops::IndexMut::index_mut(&mut a, b)` 。就像方法一样，会对 `a` 解引用以找到实现。

数组和切片的索引是从零开始。
数组访问是一个 [常量表达式][constant expression] ，因此如果索引值是常量，编译时可以进行边界检查。
否则，运行时会进行检查，如果检查失败，则线程将置于 _恐慌状态_。
{==+==}


{==+==}
```rust,should_panic
// lint is deny by default.
#![warn(unconditional_panic)]

([1, 2, 3, 4])[2];        // Evaluates to 3

let b = [[1, 0, 0], [0, 1, 0], [0, 0, 1]];
b[1][2];                  // multidimensional array indexing

let x = (["a", "b"])[10]; // warning: index out of bounds

let n = 10;
let y = (["a", "b"])[n];  // panics

let arr = ["a", "b"];
arr[10];                  // warning: index out of bounds
```
{==+==}
```rust,should_panic
// 默认情况下 lint 为 deny。
#![warn(unconditional_panic)]

([1, 2, 3, 4])[2];        // 计算结果为 3

let b = [[1, 0, 0], [0, 1, 0], [0, 0, 1]];
b[1][2];                  // 多维数组索引

let x = (["a", "b"])[10]; // 警告：索引越界

let n = 10;
let y = (["a", "b"])[n];  // 引发 panic

let arr = ["a", "b"];
arr[10];                  // 警告：索引越界
```
{==+==}


{==+==}
The array index expression can be implemented for types other than arrays and slices by implementing the [Index] and [IndexMut] traits.
{==+==}
数组索引表达式可以通过实现 [Index] 和 [IndexMut] trait 来实现其他类型的索引。
{==+==}


{==+==}
[`Copy`]: ../special-types-and-traits.md#copy
[IndexMut]: ../../std/ops/trait.IndexMut.html
[Index]: ../../std/ops/trait.Index.html
[_Expression_]: ../expressions.md
[array]: ../types/array.md
[constant expression]: ../const_eval.md#constant-expressions
[constant item]: ../items/constant-items.md
[literal]: ../tokens.md#literals
[memory location]: ../expressions.md#place-expressions-and-value-expressions
[path]: path-expr.md
[slice]: ../types/slice.md
{==+==}

{==+==}