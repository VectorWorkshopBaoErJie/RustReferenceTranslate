{==+==}
# Operator expressions
{==+==}
# 运算符表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _OperatorExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_BorrowExpression_]\
> &nbsp;&nbsp; | [_DereferenceExpression_]\
> &nbsp;&nbsp; | [_ErrorPropagationExpression_]\
> &nbsp;&nbsp; | [_NegationExpression_]\
> &nbsp;&nbsp; | [_ArithmeticOrLogicalExpression_]\
> &nbsp;&nbsp; | [_ComparisonExpression_]\
> &nbsp;&nbsp; | [_LazyBooleanExpression_]\
> &nbsp;&nbsp; | [_TypeCastExpression_]\
> &nbsp;&nbsp; | [_AssignmentExpression_]\
> &nbsp;&nbsp; | [_CompoundAssignmentExpression_]
{==+==}
> **<sup>语法</sup>**\
> _运算符表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_借用表达式_][_BorrowExpression_]\
> &nbsp;&nbsp; | [_解引用表达式_][_DereferenceExpression_]\
> &nbsp;&nbsp; | [_错误传导表达式_][_ErrorPropagationExpression_]\
> &nbsp;&nbsp; | [_取反表达式_][_NegationExpression_]\
> &nbsp;&nbsp; | [_算术或逻辑表达式_][_ArithmeticOrLogicalExpression_]\
> &nbsp;&nbsp; | [_比较表达式_][_ComparisonExpression_]\
> &nbsp;&nbsp; | [_惰性布尔表达式_][_LazyBooleanExpression_]\
> &nbsp;&nbsp; | [_类型转换表达式_][_TypeCastExpression_]\
> &nbsp;&nbsp; | [_赋值表达式_][_AssignmentExpression_]\
> &nbsp;&nbsp; | [_复合赋值表达式_][_CompoundAssignmentExpression_]
{==+==}


{==+==}
Operators are defined for built in types by the Rust language.
Many of the following operators can also be overloaded using traits in `std::ops` or `std::cmp`.
{==+==}
在 Rust 语言中，内置类型的运算符已经被定义好了。
许多下面提到的运算符也可以通过 `std::ops` 或 `std::cmp` 中的 trait 进行重载。
{==+==}


{==+==}
## Overflow

Integer operators will panic when they overflow when compiled in debug mode.
The `-C debug-assertions` and `-C overflow-checks` compiler flags can be used to control this more directly.
The following things are considered to be overflow:

* When `+`, `*` or binary `-` create a value greater than the maximum value, or less than the minimum value that can be stored.
* Applying unary `-` to the most negative value of any signed integer type, unless the operand is a [literal expression] (or a literal expression standing alone inside one or more [grouped expressions][grouped expression]).
* Using `/` or `%`, where the left-hand argument is the smallest integer of a signed integer type and the right-hand argument is `-1`.
  These checks occur even when `-C overflow-checks` is disabled, for legacy reasons.
* Using `<<` or `>>` where the right-hand argument is greater than or equal to the number of bits in the type of the left-hand argument, or is negative.
{==+==}
## 溢出

在调试模式下编译的整数运算符会在溢出时触发 panic。
可以使用 `-C debug-assertions` 和 `-C overflow-checks` 编译器标志来更直接地控制这个行为。下列情况被视为溢出：

* 当 `+`、`*` 或二元 `-` 创建的值大于可以存储的最大值或小于最小值。
* 对任何有符号整数类型的最小值应用一元 `-` ，除非操作数是 [字面值表达式][literal expression]
  (或在一个或多个 [分组表达式][grouped expression] 中单独使用的字面值表达式) 。
* 在左操作数是有符号整数类型的最小整数且右操作数是 `-1` 时，使用 `/` 或 `%` 。 出于兼容性原因，即使禁用了 `-C overflow-checks` ，这些检查也会发生。
* 在右操作数大于或等于左操作数类型的位数或为负时，使用 `<<` 或 `>>` 。
{==+==}


{==+==}
> **Note**: The exception for literal expressions behind unary `-` means that forms such as `-128_i8` or `let j: i8 = -(128)` never cause a panic and have the expected value of -128.
>
> In these cases, the literal expression already has the most negative value for its type (for example, `128_i8` has the value -128) because integer literals are truncated to their type per the description in [Integer literal expressions][literal expression].
>
> Negation of these most negative values leaves the value unchanged due to two's complement overflow conventions.
>
> In `rustc`, these most negative expressions are also ignored by the `overflowing_literals` lint check.
{==+==}
> **注意**: 对于一元 `-` 后面的字面值表达式，这意味着类似 `-128_i8` 或 `let j: i8 = -(128)` 的形式永远不会导致 panic，并且其期望值为 -128 。
>
> 在这些情况下，字面值表达式已经具有其类型的最小值 (例如， `128_i8` 的值为 -128) ，因为整数字面值按照 [整数字面值表达式][literal expression] 中的描述被截断为其类型。
>
> 这些最小值的否定不会改变值，因为采用了二进制补码的溢出约定。
>
> 在 `rustc` 中，这些最小值表达式也被 `overflowing_literals` 代码分析检查忽略。
{==+==}


{==+==}
## Borrow operators
{==+==}
## 借用运算符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _BorrowExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; (`&`|`&&`) [_Expression_]\
> &nbsp;&nbsp; | (`&`|`&&`) `mut` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _借用表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; (`&`|`&&`) [_表达式_][_Expression_]\
> &nbsp;&nbsp; | (`&`|`&&`) `mut` [_表达式_][_Expression_]
{==+==}


{==+==}
The `&` (shared borrow) and `&mut` (mutable borrow) operators are unary prefix operators.
When applied to a [place expression], this expressions produces a reference (pointer) to the location that the value refers to.
The memory location is also placed into a borrowed state for the duration of the reference.
For a shared borrow (`&`), this implies that the place may not be mutated, but it may be read or shared again.
For a mutable borrow (`&mut`), the place may not be accessed in any way until the borrow expires.
`&mut` evaluates its operand in a mutable place expression context.
If the `&` or `&mut` operators are applied to a [value expression], then a [temporary value] is created.

These operators cannot be overloaded.
{==+==}
`&` (共享借用) 和 `&mut` (可变借用) 运算符是一元前缀运算符。
当应用于一个 [占位表达式][place expression] 时，该表达式产生一个指向该值引用的位置的引用 (指针) 。
在引用的持续时间内，该内存位置也被置于借用状态。对于共享借用 ( `&` ) ，这意味着该位置可能不能被修改，但可以被读取或再次共享。
对于可变借用 (`&mut`) ，在借用过期之前，该位置可能不能以任何方式访问。
`&mut` 在可变位置表达式上下文中评估其操作数。如果 `&` 或 `&mut` 运算符应用于一个 [值表达式][value expression] ，则将创建一个 [临时值][temporary value]。

这些运算符不能被重载。
{==+==}


{==+==}
```rust
{
    // a temporary with value 7 is created that lasts for this scope.
    let shared_reference = &7;
}
let mut array = [-2, 3, 9];
{
    // Mutably borrows `array` for this scope.
    // `array` may only be used through `mutable_reference`.
    let mutable_reference = &mut array;
}
```
{==+==}
```rust
{
    // 创建一个值为 7 的临时值，它只在当前作用域有效。
    let shared_reference = &7;
}
let mut array = [-2, 3, 9];
{
    // 对 `array` 进行可变借用，这个作用域内 `array` 只能通过 `mutable_reference` 使用。
    let mutable_reference = &mut array;
}
```
{==+==}


{==+==}
Even though `&&` is a single token ([the lazy 'and' operator](#lazy-boolean-operators)), when used in the context of borrow expressions it works as two borrows:
{==+==}
即使 `&&` 是单个标记 ([惰性 'and' 运算符](#lazy-boolean-operators)) ，但在借用表达式的上下文中使用时，它的作用相当于两个借用:
{==+==}


{==+==}
```rust
// same meanings:
let a = &&  10;
let a = & & 10;

// same meanings:
let a = &&&&  mut 10;
let a = && && mut 10;
let a = & & & & mut 10;
```
{==+==}
```rust
// 同样的含义:
let a = &&  10;
let a = & & 10;

// 同样的含义:
let a = &&&&  mut 10;
let a = && && mut 10;
let a = & & & & mut 10;
```
{==+==}


{==+==}
### Raw address-of operators

Related to the borrow operators are the *raw address-of operators*, which do not have first-class syntax, but are exposed via the macros [`ptr::addr_of!(expr)`][addr_of] and [`ptr::addr_of_mut!(expr)`][addr_of_mut].
The expression `expr` is evaluated in place expression context.
`ptr::addr_of!(expr)` then creates a const raw pointer of type `*const T` to the given place, and `ptr::addr_of_mut!(expr)` creates a mutable raw pointer of type `*mut T`.

The raw address-of operators must be used instead of a borrow operator whenever the place expression could evaluate to a place that is not properly aligned or does not store a valid value as determined by its type, or whenever creating a reference would introduce incorrect aliasing assumptions.
In those situations, using a borrow operator would cause [undefined behavior] by creating an invalid reference, but a raw pointer may still be constructed using an address-of operator.

The following is an example of creating a raw pointer to an unaligned place through a `packed` struct:
{==+==}
### 原始地址运算符

与借用运算符相关的是 *原始地址运算符* ，它们没有一级语法，但通过宏 [`ptr::addr_of!(expr)`][addr_of] 和 [`ptr::addr_of_mut!(expr)`][addr_of_mut] 暴露出来。
表达式 `expr` 在占位表达式上下文中进行评估。
`ptr::addr_of!(expr)` 然后创建一个类型为 `*const T` 的常量原始指针指向给定的位置，而 `ptr::addr_of_mut!(expr)` 则创建一个类型为 `*mut T` 的可变原始指针。

必须使用原始地址运算符而不是借用运算符，每当占位表达式可以评估为未正确对齐或不存储有效值 (根据其类型确定) ，或者每当创建引用会引入不正确的别名假设时。
在这些情况下，使用借用运算符将通过创建无效引用导致 [未定义行为][undefined behavior] ，但仍可以使用地址运算符构造原始指针。

以下是通过 `packed` 结构创建指向未对齐位置的原始指针的示例：
{==+==}


{==+==}
```rust
use std::ptr;

#[repr(packed)]
struct Packed {
    f1: u8,
    f2: u16,
}

let packed = Packed { f1: 1, f2: 2 };
// `&packed.f2` would create an unaligned reference, and thus be Undefined Behavior!
let raw_f2 = ptr::addr_of!(packed.f2);
assert_eq!(unsafe { raw_f2.read_unaligned() }, 2);
```
{==+==}
```rust
use std::ptr;

// 定义一个 packed 结构体
#[repr(packed)]
struct Packed {
    f1: u8,
    f2: u16,
}

// 创建一个 packed 实例
let packed = Packed { f1: 1, f2: 2 };
// `&packed.f2` 会创建一个不对齐的引用，因此是未定义行为！
// 使用 `ptr::addr_of!` 创建一个指向 `packed.f2` 的不可变原始指针
let raw_f2 = ptr::addr_of!(packed.f2);
// 读取 `raw_f2` 指向的值，并断言其值等于2
assert_eq!(unsafe { raw_f2.read_unaligned() }, 2);
```
{==+==}


{==+==}
The following is an example of creating a raw pointer to a place that does not contain a valid value:
{==+==}
以下是创建指向不包含有效值的位置的原始指针的示例:
{==+==}


{==+==}
```rust
use std::{ptr, mem::MaybeUninit};

struct Demo {
    field: bool,
}

let mut uninit = MaybeUninit::<Demo>::uninit();
// `&uninit.as_mut().field` would create a reference to an uninitialized `bool`,
// and thus be Undefined Behavior!
let f1_ptr = unsafe { ptr::addr_of_mut!((*uninit.as_mut_ptr()).field) };
unsafe { f1_ptr.write(true); }
let init = unsafe { uninit.assume_init() };
```
{==+==}
```rust
use std::{ptr, mem::MaybeUninit};

struct Demo {
    field: bool,
}

let mut uninit = MaybeUninit::<Demo>::uninit();
// &uninit.as_mut().field 将创建对未初始化的  `bool` 的引用，因此为未定义行为!
let f1_ptr = unsafe { ptr::addr_of_mut!((*uninit.as_mut_ptr()).field) };
// 将 'true' 写入先前未初始化的字段。
unsafe { f1_ptr.write(true); }
// 因为 'uninit' 已在上面初始化，所以是安全的。
let init = unsafe { uninit.assume_init() };
```
{==+==}


{==+==}
## The dereference operator
{==+==}
## 解引用操作符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _DereferenceExpression_ :\
> &nbsp;&nbsp; `*` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _解引用表达式_ :\
> &nbsp;&nbsp; `*` [_表达式_][_Expression_]
{==+==}


{==+==}
The `*` (dereference) operator is also a unary prefix operator.
When applied to a [pointer](../types/pointer.md) it denotes the pointed-to location.
If the expression is of type `&mut T` or `*mut T`, and is either a local variable, a (nested) field of a local variable or is a mutable [place expression], then the resulting memory location can be assigned to.
Dereferencing a raw pointer requires `unsafe`.

On non-pointer types `*x` is equivalent to `*std::ops::Deref::deref(&x)` in an [immutable place expression context](../expressions.md#mutability) and `*std::ops::DerefMut::deref_mut(&mut x)` in a mutable place expression context.
{==+==}
解引用运算符 `*` 也是一个一元前缀运算符。
当应用于 [指针](../types/pointer.md) 时，表示指向的地址。
如果表达式的类型为 `&mut T` 或 `*mut T`，并且是一个局部变量、局部变量的 (嵌套) 字段或可变的 [占位表达式][place expression] ，则可以将结果的内存位置分配给其它变量。
对一个裸指针进行解引用需要使用 `unsafe` 。

对于非指针类型， `*x` 在不可变的 [占位表达式上下文](../expressions.md#mutability) 中等同于 `*std::ops::Deref::deref(&x)` ，在可变的占位表达式上下文中等同于 `*std::ops::DerefMut::deref_mut(&mut x)`。
{==+==}


{==+==}
```rust
let x = &7;
assert_eq!(*x, 7);
let y = &mut 9;
*y = 11;
assert_eq!(*y, 11);
```
{==+==}

{==+==}


{==+==}
## The question mark operator
{==+==}
## 问号操作符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ErrorPropagationExpression_ :\
> &nbsp;&nbsp; [_Expression_] `?`
{==+==}
> **<sup>语法</sup>**\
> _错误传导表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `?`
{==+==}


{==+==}
The question mark operator (`?`) unwraps valid values or returns erroneous values, propagating them to the calling function.
It is a unary postfix operator that can only be applied to the types `Result<T, E>` and `Option<T>`.

When applied to values of the `Result<T, E>` type, it propagates errors.
If the value is `Err(e)`, then it will return `Err(From::from(e))` from the enclosing function or closure.
If applied to `Ok(x)`, then it will unwrap the value to evaluate to `x`.
{==+==}
问号运算符 (`?`) 可以展开有效的值或返回错误值，并将它们传播到调用函数中。
它是一个一元后缀运算符，只能应用于 `Result<T, E>` 和 `Option<T>` 类型。

当应用于 `Result<T, E>` 类型的值时，它会传播错误。
如果该值是 `Err(e)` ，则它将从包含的函数或闭包返回 `Err(From::from(e))` 。如果应用于 `Ok(x)` ，则它将展开该值以求值为 `x` 。
{==+==}


{==+==}
```rust
# use std::num::ParseIntError;
fn try_to_parse() -> Result<i32, ParseIntError> {
    let x: i32 = "123".parse()?; // x = 123
    let y: i32 = "24a".parse()?; // returns an Err() immediately
    Ok(x + y)                    // Doesn't run.
}

let res = try_to_parse();
println!("{:?}", res);
# assert!(res.is_err())
```
{==+==}
```rust
# use std::num::ParseIntError;
fn try_to_parse() -> Result<i32, ParseIntError> {
    let x: i32 = "123".parse()?; // x = 123
    let y: i32 = "24a".parse()?; // 立即返回一个 Err()
    Ok(x + y)                    // 不会被执行
}

let res = try_to_parse();
println!("{:?}", res);
# assert!(res.is_err())
```
{==+==}


{==+==}
When applied to values of the `Option<T>` type, it propagates `None`s.
If the value is `None`, then it will return `None`.
If applied to `Some(x)`, then it will unwrap the value to evaluate to `x`.
{==+==}
当应用于类型为 `Option<T>` 的值时，它会传播 `None` 。如果值为 `None` ，则它将返回 `None` 。如果应用于 `Some(x)` ，则它将展开该值以求值为 `x` 。
{==+==}


{==+==}
```rust
fn try_option_some() -> Option<u8> {
    let val = Some(1)?;
    Some(val)
}
assert_eq!(try_option_some(), Some(1));

fn try_option_none() -> Option<u8> {
    let val = None?;
    Some(val)
}
assert_eq!(try_option_none(), None);
```
{==+==}

{==+==}


{==+==}
`?` cannot be overloaded.
{==+==}
`?` 不能被重载。
{==+==}


{==+==}
## Negation operators
{==+==}
## 取反运算符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _NegationExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `-` [_Expression_]\
> &nbsp;&nbsp; | `!` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _取反表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `-` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | `!` [_表达式_][_Expression_]
{==+==}


{==+==}
These are the last two unary operators.
This table summarizes the behavior of them on primitive types and which traits are used to overload these operators for other types.
Remember that signed integers are always represented using two's complement.
The operands of all of these operators are evaluated in [value expression context][value expression] so are moved or copied.
{==+==}
这是最后两个一元操作符。
这张表总结了它们在原始类型上的行为以及用于重载其他类型的这些操作符的 trait 。
请记住，有符号整数总是使用二进制补码表示。所有这些操作符的操作数都在 [值表达式上下文][value expression] 中评估，因此它们会被移动或复制。
{==+==}


{==+==}
| Symbol | Integer     | `bool`        | Floating Point | Overloading Trait  |
|--------|-------------|-------------- |----------------|--------------------|
| `-`    | Negation*   |               | Negation       | `std::ops::Neg`    |
| `!`    | Bitwise NOT | [Logical NOT] |                | `std::ops::Not`    |
{==+==}
| 符号   | 整数        | 布尔                   | 浮点数        | 重载 Trait   |
|--------|-------------|--------------|---------------|---------------------|
| `-`    | 取反*       |                          | 取反           | `std::ops::Neg`     |
| `!`    | 按位取反    | [逻辑取反][Logical NOT]  |               | `std::ops::Not`     |
{==+==}


{==+==}
\* Only for signed integer types.

Here are some example of these operators
{==+==}
\* 仅适用于有符号整数类型。

下面是这些运算符的一些示例
{==+==}


{==+==}
```rust
let x = 6;
assert_eq!(-x, -6);
assert_eq!(!x, -7);
assert_eq!(true, !false);
```
{==+==}

{==+==}


{==+==}
## Arithmetic and Logical Binary Operators
{==+==}
## 算术和逻辑二元操作符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ArithmeticOrLogicalExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Expression_] `+` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `-` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `*` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `/` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `%` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `&` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `|` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `^` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `<<` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `>>` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _算术或逻辑表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式_][_Expression_] `+` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `-` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `*` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `/` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `%` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `&` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `|` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `^` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `<<` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `>>` [_表达式_][_Expression_]
{==+==}


{==+==}
Binary operators expressions are all written with infix notation.
This table summarizes the behavior of arithmetic and logical binary operators on primitive types and which traits are used to overload these operators for other types.
Remember that signed integers are always represented using two's complement.
The operands of all of these operators are evaluated in [value expression context][value expression] so are moved or copied.
{==+==}
二元运算符表达式均以中缀表示。此表总结了原始类型上算术和逻辑二元运算符的行为，以及用于重载其他类型的这些运算符的特性。
请记住，有符号整数始终使用二进制补码表示。所有这些运算符的操作数都在 [值表达式上下文][value expression] 中计算，因此会被移动或复制。
{==+==}


{==+==}
| Symbol | Integer                 | `bool`        | Floating Point | Overloading Trait  | Overloading Compound Assignment Trait |
|--------|-------------------------|---------------|----------------|--------------------| ------------------------------------- |
| `+`    | Addition                |               | Addition       | `std::ops::Add`    | `std::ops::AddAssign`                 |
| `-`    | Subtraction             |               | Subtraction    | `std::ops::Sub`    | `std::ops::SubAssign`                 |
| `*`    | Multiplication          |               | Multiplication | `std::ops::Mul`    | `std::ops::MulAssign`                 |
| `/`    | Division*               |               | Division       | `std::ops::Div`    | `std::ops::DivAssign`                 |
| `%`    | Remainder**             |               | Remainder      | `std::ops::Rem`    | `std::ops::RemAssign`                 |
| `&`    | Bitwise AND             | [Logical AND] |                | `std::ops::BitAnd` | `std::ops::BitAndAssign`              |
| <code>&#124;</code> | Bitwise OR | [Logical OR]  |                | `std::ops::BitOr`  | `std::ops::BitOrAssign`               |
| `^`    | Bitwise XOR             | [Logical XOR] |                | `std::ops::BitXor` | `std::ops::BitXorAssign`              |
| `<<`   | Left Shift              |               |                | `std::ops::Shl`    | `std::ops::ShlAssign`                 |
| `>>`   | Right Shift***          |               |                | `std::ops::Shr`    |  `std::ops::ShrAssign`                |
{==+==}
| 符号   | 整数                   | 布尔       | 浮点数        |   重载 Trait  | 重载复合赋值 Trait |
|--------|-------------------------|---------------|----------------|--------------------| --------------------------------------|
| `+`    | 加法                    |               | 加法           | `std::ops::Add`    | `std::ops::AddAssign`                  |
| `-`    | 减法                    |               | 减法           | `std::ops::Sub`    | `std::ops::SubAssign`                  |
| `*`    | 乘法                    |               | 乘法           | `std::ops::Mul`    | `std::ops::MulAssign`                  |
| `/`    | 除法*                   |               | 除法           | `std::ops::Div`    | `std::ops::DivAssign`                  |
| `%`    | 取余**                  |               | 取余           | `std::ops::Rem`    | `std::ops::RemAssign`                  |
| `&`    | 按位与                  | [逻辑与][Logical AND]      |                | `std::ops::BitAnd` | `std::ops::BitAndAssign`               |
| <code>&#124;</code> | 按位或  | [逻辑或][Logical OR]      |                | `std::ops::BitOr`  | `std::ops::BitOrAssign`                |
| `^`    | 按位异或                | [逻辑异或][Logical XOR]    |                | `std::ops::BitXor` | `std::ops::BitXorAssign`               |
| `<<`   | 左移                    |               |                | `std::ops::Shl`    | `std::ops::ShlAssign`                  |
| `>>`   | 右移***                 |               |                | `std::ops::Shr`    |  `std::ops::ShrAssign`                 |
{==+==}


{==+==}
\* Integer division rounds towards zero.

\*\* Rust uses a remainder defined with [truncating division](https://en.wikipedia.org/wiki/Modulo_operation#Variants_of_the_definition). Given `remainder = dividend % divisor`, the remainder will have the same sign as the dividend.

\*\*\* Arithmetic right shift on signed integer types, logical right shift on
unsigned integer types.

Here are examples of these operators being used.
{==+==}
\* 整数除法向零取整。
\*\* Rust使用的余数采用 [截断除法](https://en.wikipedia.org/wiki/Modulo_operation#Variants_of_the_definition) 定义。
  给定 `remainder = dividend % divisor` ，余数将与被除数具有相同的符号。
\*\*\* 在有符号整数类型上进行算术右移，无符号整数类型上进行逻辑右移。

以下是使用这些运算符的示例：
{==+==}


{==+==}
```rust
assert_eq!(3 + 6, 9);
assert_eq!(5.5 - 1.25, 4.25);
assert_eq!(-5 * 14, -70);
assert_eq!(14 / 3, 4);
assert_eq!(100 % 7, 2);
assert_eq!(0b1010 & 0b1100, 0b1000);
assert_eq!(0b1010 | 0b1100, 0b1110);
assert_eq!(0b1010 ^ 0b1100, 0b110);
assert_eq!(13 << 3, 104);
assert_eq!(-10 >> 2, -3);
```
{==+==}

{==+==}


{==+==}
## Comparison Operators
{==+==}
## 比较运算符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ComparisonExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Expression_] `==` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `!=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `>` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `<` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `>=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `<=` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _比较表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式_][_Expression_] `==` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_]`!=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `>` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `<` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `>=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `<=` [_表达式_][_Expression_]
{==+==}


{==+==}
Comparison operators are also defined both for primitive types and many types in the standard library.
Parentheses are required when chaining comparison operators. For example, the expression `a == b == c` is invalid and may be written as `(a == b) == c`.

Unlike arithmetic and logical operators, the traits for overloading these operators are used more generally to show how a type may be compared and will likely be assumed to define actual comparisons by functions that use these traits as bounds.
Many functions and macros in the standard library can then use that assumption (although not to ensure safety).
Unlike the arithmetic and logical operators above, these operators implicitly take shared borrows of their operands, evaluating them in [place expression context][place expression]:
{==+==}
比较运算符也被定义在基本类型和标准库的许多类型上。当链接比较运算符时，需要使用括号。
例如，表达式 `a == b == c` 是无效的，可以写成 `(a == b) == c` 。

与算术和逻辑运算符不同，用于重载这些运算符的 trait 通常更普遍地用于表明类型如何进行比较，并且可能会被假定为函数定义实际比较的条件。
标准库中的许多函数和宏可以利用这个假设 (虽然不能确保安全性) 。
与上面的算术和逻辑运算符不同，这些运算符隐式地获取其操作数的共享借用，以 [占位表达式上下文][place expression] 中的方式进行评估：
{==+==}


{==+==}
```rust
# let a = 1;
# let b = 1;
a == b;
// is equivalent to
::std::cmp::PartialEq::eq(&a, &b);
```
{==+==}
```rust
# let a = 1;
# let b = 1;
a == b;
// 等价于
::std::cmp::PartialEq::eq(&a, &b);
```
{==+==}


{==+==}
This means that the operands don't have to be moved out of.
{==+==}
这意味着操作数不必移动出去。
{==+==}


{==+==}
| Symbol | Meaning                  | Overloading method         |
|--------|--------------------------|----------------------------|
| `==`   | Equal                    | `std::cmp::PartialEq::eq`  |
| `!=`   | Not equal                | `std::cmp::PartialEq::ne`  |
| `>`    | Greater than             | `std::cmp::PartialOrd::gt` |
| `<`    | Less than                | `std::cmp::PartialOrd::lt` |
| `>=`   | Greater than or equal to | `std::cmp::PartialOrd::ge` |
| `<=`   | Less than or equal to    | `std::cmp::PartialOrd::le` |
{==+==}
| 符号   | 意义                 |  重载方法                  |
|--------|----------------------|----------------------------|
| `==`   | 等于                 | `std::cmp::PartialEq::eq`  |
| `!=`   | 不等于               | `std::cmp::PartialEq::ne`  |
| `>`    | 大于                 | `std::cmp::PartialOrd::gt` |
| `<`    | 小于                 | `std::cmp::PartialOrd::lt` |
| `>=`   | 大于等于             | `std::cmp::PartialOrd::ge` |
| `<=`   | 小于等于             | `std::cmp::PartialOrd::le` |
{==+==}


{==+==}
Here are examples of the comparison operators being used.
{==+==}
这里是使用比较运算符的示例：
{==+==}


{==+==}
```rust
assert!(123 == 123);
assert!(23 != -12);
assert!(12.5 > 12.2);
assert!([1, 2, 3] < [1, 3, 4]);
assert!('A' <= 'B');
assert!("World" >= "Hello");
```
{==+==}

{==+==}


{==+==}
## Lazy boolean operators
{==+==}
## 惰性布尔运算符
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LazyBooleanExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Expression_] `||` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `&&` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _惰性布尔表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式_][_Expression_] `||` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `&&` [_表达式_][_Expression_]
{==+==}


{==+==}
The operators `||` and `&&` may be applied to operands of boolean type.
The `||` operator denotes logical 'or', and the `&&` operator denotes logical 'and'.
They differ from `|` and `&` in that the right-hand operand is only evaluated when the left-hand operand does not already determine the result of the expression.
That is, `||` only evaluates its right-hand operand when the left-hand operand evaluates to `false`, and `&&` only when it evaluates to `true`.
{==+==}
运算符 `||` 和 `&&` 可以用于布尔类型的操作数。 `||` 运算符表示逻辑或， `&&` 运算符表示逻辑与。
它们与 `|` 和 `&` 的区别在于，只有当左操作数不能确定表达式结果时才会对右操作数进行求值。
也就是说，只有当左操作数求值结果为 `false` 时 `||` 才会对右操作数进行求值，只有当左操作数求值结果为 `true` 时 `&&` 才会对右操作数进行求值。
{==+==}


{==+==}
```rust
let x = false || true; // true
let y = false && panic!(); // false, doesn't evaluate `panic!()`
```
{==+==}
```rust
let x = false || true; // true
let y = false && panic!(); // false 不会对右侧的操作数进行求值，因此 `panic!()` 表达式不会被执行。
```
{==+==}


{==+==}
## Type cast expressions
{==+==}
## 类型转换表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TypeCastExpression_ :\
> &nbsp;&nbsp; [_Expression_] `as` [_TypeNoBounds_]
{==+==}
> **<sup>语法</sup>**\
> _类型转换表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_]  `as` [_无约束类型_][_TypeNoBounds_]
{==+==}


{==+==}
A type cast expression is denoted with the binary operator `as`.

Executing an `as` expression casts the value on the left-hand side to the type on the right-hand side.

An example of an `as` expression:
{==+==}
类型转换表达式使用二元运算符 `as` 表示。

执行一个 `as` 表达式会将左侧的值转换为右侧的类型。

以下是一个 `as` 表达式的例子:
{==+==}


{==+==}
```rust
# fn sum(values: &[f64]) -> f64 { 0.0 }
# fn len(values: &[f64]) -> i32 { 0 }
fn average(values: &[f64]) -> f64 {
    let sum: f64 = sum(values);
    let size: f64 = len(values) as f64;
    sum / size
}
```
{==+==}

{==+==}


{==+==}
`as` can be used to explicitly perform [coercions](../type-coercions.md), as well as the following additional casts.
Any cast that does not fit either a coercion rule or an entry in the table is a compiler error.
Here `*T` means either `*const T` or `*mut T`. `m` stands for optional `mut` in
reference types and `mut` or `const` in pointer types.
{==+==}
`as` 可以用于显式执行 [强转](../type-coercions.md) ，以及以下附加转换。
任何不符合强转规则或表中条目的转换都是编译器错误。
这里的 `*T` 表示 `*const T` 或 `*mut T`。
在引用类型中， `m` 表示可选的 `mut` ，在指针类型中表示 `mut` 或 `const` 。
{==+==}


{==+==}
| Type of `e`           | `U`                   | Cast performed by `e as U`       |
|-----------------------|-----------------------|----------------------------------|
| Integer or Float type | Integer or Float type | Numeric cast                     |
| Enumeration           | Integer type          | Enum cast                        |
| `bool` or `char`      | Integer type          | Primitive to integer cast        |
| `u8`                  | `char`                | `u8` to `char` cast              |
| `*T`                  | `*V` where `V: Sized` \* | Pointer to pointer cast       |
| `*T` where `T: Sized` | Integer type          | Pointer to address cast          |
| Integer type          | `*V` where `V: Sized` | Address to pointer cast          |
| `&m₁ T`               | `*m₂ T` \*\*          | Reference to pointer cast        |
| `&m₁ [T; n]`          | `*m₂ T` \*\*          | Array to pointer cast            |
| [Function item]       | [Function pointer]    | Function item to function pointer cast |
| [Function item]       | `*V` where `V: Sized` | Function item to pointer cast    |
| [Function item]       | Integer               | Function item to address cast    |
| [Function pointer]    | `*V` where `V: Sized` | Function pointer to pointer cast |
| [Function pointer]    | Integer               | Function pointer to address cast |
| Closure \*\*\*        | Function pointer      | Closure to function pointer cast |
{==+==}
| `e` 的类型                | `U`                   | `e as U` 执行的类型转换             |
|---------------------------|-----------------------|--------------------------------------|
| 整型或浮点型               | 整型或浮点型               | 数值类型转换                        |
| 枚举类型                  | 整型类型                | 枚举类型转换                        |
| `bool` 或 `char` 类型      | 整型类型                | 基本类型到整型类型的转换            |
| `u8` 类型                  | `char` 类型             | `u8` 类型到 `char` 类型的转换       |
| `*T` 类型                  | `*V` where `V: Sized` \* | 指针类型到指针类型的转换         |
| `*T` where `T: Sized`   | 整型类型                | 指针类型到地址类型的转换            |
| 整型类型                  | `*V` where `V: Sized`    | 地址类型到指针类型的转换            |
| `&m₁ T` 类型               | `*m₂ T` \*\*           | 引用类型到指针类型的转换            |
| `&m₁ [T; n]` 类型          | `*m₂ T` \*\*           | 数组类型到指针类型的转换            |
| [函数条目][Function item] 类型              | [函数指针][Function pointer] 类型        | 函数条目到函数指针类型的转换         |
| [函数条目][Function item] 类型              | `*V` where `V: Sized`   | 函数条目到指针类型的转换             |
| [函数条目][Function item] 类型              | 整型类型                | 函数条目到地址类型的转换             |
| [函数指针][Function pointer] 类型            | `*V` where `V: Sized`   | 函数指针到指针类型的转换           |
| [函数指针][Function pointer] 类型            | 整型类型                | 函数指针到地址类型的转换           |
| 闭包类型 \*\*\*            | 函数指针类型           | 闭包类型到函数指针类型的转换       |
{==+==}


{==+==}
\* or `T` and `V` are compatible unsized types, e.g., both slices, both the same trait object.

\*\* only when `m₁` is `mut` or `m₂` is `const`. Casting `mut` reference to
`const` pointer is allowed.

\*\*\* only for closures that do not capture (close over) any local variables
{==+==}
\* 当 `T` 和 `V` 都是不定大小类型但类型相同时，例如都是切片类型，它们之间是兼容的。
\*\* 当且仅当 `m₁` 为 `mut` 或 `m₂` 为 `const` 时。允许将 `mut` 引用转换为 `const` 指针。
\*\*\* 仅适用于没有捕获 (关闭) 任何局部变量的闭包。
{==+==}


{==+==}
### Semantics

#### Numeric cast
{==+==}
### 语义

#### 数值转换
{==+==}


{==+==}
* Casting between two integers of the same size (e.g. i32 -> u32) is a no-op
  (Rust uses 2's complement for negative values of fixed integers)
* Casting from a larger integer to a smaller integer (e.g. u32 -> u8) will
  truncate
* Casting from a smaller integer to a larger integer (e.g. u8 -> u32) will
    * zero-extend if the source is unsigned
    * sign-extend if the source is signed
* Casting from a float to an integer will round the float towards zero
    * `NaN` will return `0`
    * Values larger than the maximum integer value, including `INFINITY`, will saturate to the maximum value of the integer type.
    * Values smaller than the minimum integer value, including `NEG_INFINITY`, will saturate to the minimum value of the integer type.
* Casting from an integer to float will produce the closest possible float \*
    * if necessary, rounding is according to `roundTiesToEven` mode \*\*\*
    * on overflow, infinity (of the same sign as the input) is produced
    * note: with the current set of numeric types, overflow can only happen
      on `u128 as f32` for values greater or equal to `f32::MAX + (0.5 ULP)`
* Casting from an f32 to an f64 is perfect and lossless
* Casting from an f64 to an f32 will produce the closest possible f32 \*\*
    * if necessary, rounding is according to `roundTiesToEven` mode \*\*\*
    * on overflow, infinity (of the same sign as the input) is produced
{==+==}
* 两个大小相同的整数之间的转换 (例如 i32 -> u32) 是无操作的 (Rust 对于固定整数的负值使用 2 的补码) 
* 从较大的整数转换为较小的整数 (例如 u32 -> u8) 会截断
* 从较小的整数转换为较大的整数 (例如 u8 -> u32) 将：
    * 如果源是无符号的，则用零填充
    * 如果源是有符号的，则使用符号扩展
* 将浮点数转换为整数会将浮点数向零舍入 
    * `NaN` 将返回 `0` 
    * 大于最大整数值的值，包括 `INFINITY` ，将饱和到整数类型的最大值。
    * 小于最小整数值的值，包括 `NEG_INFINITY` ，将饱和到整数类型的最小值。
* 将整数转换为浮点数将产生可能最接近的浮点数 \*
    * 如有必要，舍入按照 `roundTiesToEven` 模式进行  \*\*\* 
    * 溢出时，将生成无限大 (与输入相同的符号) 
    * 注意：对于当前的数字类型集，只有在 `u128 as f32` 的值大于或等于 `f32::MAX + (0.5 ULP)` 时才会发生溢出
* 从 f32 转换为 f64 是完美的和无损的
* 从 f64 转换为 f32 将产生可能最接近的 f32 \*\*
    * 如有必要，舍入按照 `roundTiesToEven` 模式进行 \*\*\* 
    * 溢出时，将生成无限大 (与输入相同的符号) 
{==+==}


{==+==}
\* if integer-to-float casts with this rounding mode and overflow behavior are
not supported natively by the hardware, these casts will likely be slower than
expected.

\*\* if f64-to-f32 casts with this rounding mode and overflow behavior are not
supported natively by the hardware, these casts will likely be slower than
expected.

\*\*\* as defined in IEEE 754-2008 &sect;4.3.1: pick the nearest floating point
number, preferring the one with an even least significant digit if exactly
halfway between two floating point numbers.
{==+==}
\* 如果硬件不支持此舍入模式和溢出行为的整数到浮点数的转换，则此类转换可能比预期的慢。

\*\* 如果硬件不支持此舍入模式和溢出行为的 f64 到 f32 的转换，则此类转换可能比预期的慢。

\*\*\* 正如 IEEE 754-2008 &sect;4.3.1: 中所定义的那样：选择最接近的浮点数，如果恰好介于两个浮点数之间，则优先选择带有偶数最低位数字的数字。
{==+==}


{==+==}
#### Enum cast

Casts an enum to its discriminant, then uses a numeric cast if needed.
Casting is limited to the following kinds of enumerations:

* [Unit-only enums]
* [Field-less enums] without [explicit discriminants], or where only unit-variants have explicit discriminants
{==+==}
#### 枚举转换

将枚举转换为其判别值，然后根据需要使用数值转换。转换仅限于以下种类的枚举:

* [仅单元元素的枚举][Unit-only enums]
* [没有字段的枚举][Field-less enums] 且没有 [显式判别值][explicit discriminants] ，或者只有单元元素有显式判别值
{==+==}


{==+==}
#### Primitive to integer cast

* `false` casts to `0`, `true` casts to `1`
* `char` casts to the value of the code point, then uses a numeric cast if needed.
{==+==}
#### 原始类型到整数的转换

* `false` 转换为 `0` ， `true` 转换为 `1`
* `char` 转换为其码点值，然后如果需要则使用数字转换。
{==+==}


{==+==}
#### `u8` to `char` cast

Casts to the `char` with the corresponding code point.
{==+==}
#### `u8` 到 `char` 的类型转换

将 `u8` 类型的值转换为相应的 Unicode 编码对应的字符类型 `char` 。
{==+==}


{==+==}
#### Pointer to address cast

Casting from a raw pointer to an integer produces the machine address of the referenced memory.
If the integer type is smaller than the pointer type, the address may be truncated; using `usize` avoids this.
{==+==}
#### 指针到地址转换

将一个裸指针转换为整数类型会产生指向内存的机器地址。
如果整数类型比指针类型小，地址可能会被截断；使用 `usize` 可以避免这种情况。
{==+==}


{==+==}
#### Address to pointer cast

Casting from an integer to a raw pointer interprets the integer as a memory address and produces a pointer referencing that memory.
{==+==}
#### 地址到指针的强制类型转换

将整数强制类型转换为原始指针会将该整数解释为内存地址，并产生一个指向该内存的指针。
{==+==}


{==+==}
Warning:
This interacts with the Rust memory model, which is still under development.
A pointer obtained from this cast may suffer additional restrictions even if it is bitwise equal to a valid pointer.
Dereferencing such a pointer may be [undefined behavior] if aliasing rules are not followed.
{==+==}
警告:
这会与 Rust 内存模型产生交互，而该模型仍在开发中。
即使一个通过此类型转换获得的指针在比特位上等同于一个有效指针，它也可能受到额外的限制
。如果未遵循别名规则，则解引用此类指针可能会导致 [未定义行为][undefined behavior] 。
{==+==}


{==+==}
A trivial example of sound address arithmetic:
{==+==}
一些简单正确的地址解算的示例:
{==+==}


{==+==}
```rust
let mut values: [i32; 2] = [1, 2];
let p1: *mut i32 = values.as_mut_ptr();
let first_address = p1 as usize;
let second_address = first_address + 4; // 4 == size_of::<i32>()
let p2 = second_address as *mut i32;
unsafe {
    *p2 += 1;
}
assert_eq!(values[1], 3);
```
{==+==}

{==+==}


{==+==}
## Assignment expressions
{==+==}
## 赋值表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _AssignmentExpression_ :\
> &nbsp;&nbsp; [_Expression_] `=` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _赋值表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `=` [_表达式_][_Expression_]
{==+==}


{==+==}
An *assignment expression* moves a value into a specified place.

An assignment expression consists of a [mutable] [assignee expression], the *assignee operand*, followed by an equals sign (`=`) and a [value expression], the *assigned value operand*.
In its most basic form, an assignee expression is a [place expression], and we discuss this case first.
The more general case of destructuring assignment is discussed below, but this case always decomposes into sequential assignments to place expressions, which may be considered the more fundamental case.
{==+==}
一个 *赋值表达式* 将一个值移动到一个指定的位置。

一个赋值表达式由一个 [可变的][assignee expression] ，即 *赋值运算符* ，后面跟一个等于号 (`=`) 和一个 [值表达式][value expression] ，即 *被赋的值操作数* 组成。
在最基本的形式中，一个赋值运算符是一个 [占位表达式][place expression] ，我们首先讨论这种情况。
下面讨论更一般的解构赋值情况，但是这种情况总是分解为对占位表达式的顺序赋值，这可能被视为更基本的情况。
{==+==}


{==+==}
### Basic assignments

Evaluating assignment expressions begins by evaluating its operands.
The assigned value operand is evaluated first, followed by the assignee expression.
For destructuring assignment, subexpressions of the assignee expression are evaluated left-to-right.

> **Note**: This is different than other expressions in that the right operand is evaluated before the left one.

It then has the effect of first [dropping] the value at the assigned place, unless the place is an uninitialized local variable or an uninitialized field of a local variable.
Next it either [copies or moves] the assigned value to the assigned place.

An assignment expression always produces [the unit value][unit].
{==+==}
### 基本赋值语句

对赋值表达式求值，需要先对操作数进行求值。先对被赋值操作数进行求值，然后是赋值操作数表达式。对于解构赋值，可赋值表达式的子表达式从左到右进行求值。

> **注意**：与其他表达式不同的是，右操作数在左操作数之前进行求值。该表达式首先有一个效果：删除被赋值位置的值，除非被赋值的地方是未初始化的本地变量或本地变量的未初始化字段。然后将赋值的值复制或移动到被赋值的地方。

赋值表达式总是生成 [单元值][unit] 。
{==+==}


{==+==}
Example:

```rust
let mut x = 0;
let y = 0;
x = y;
```
{==+==}

{==+==}


{==+==}
### Destructuring assignments

Destructuring assignment is a counterpart to destructuring pattern matches for variable declaration, permitting assignment to complex values, such as tuples or structs.
For instance, we may swap two mutable variables:
{==+==}
### 解构赋值

解构赋值是变量声明解构模式匹配的一种对应物，允许对复杂的值进行赋值，例如元组或结构体。
例如，我们可以交换两个可变变量:
{==+==}


{==+==}
```rust
let (mut a, mut b) = (0, 1);
// Swap `a` and `b` using destructuring assignment.
(b, a) = (a, b);
```
{==+==}
```rust
let (mut a, mut b) = (0, 1);
// 使用解构赋值交换 `a` 和 `b`。
(b, a) = (a, b);
```
{==+==}


{==+==}
In contrast to destructuring declarations using `let`, patterns may not appear on the left-hand side of an assignment due to syntactic ambiguities.
Instead, a group of expressions that correspond to patterns are designated to be [assignee expressions][assignee expression], and permitted on the left-hand side of an assignment.
Assignee expressions are then desugared to pattern matches followed by sequential assignment.
The desugared patterns must be irrefutable: in particular, this means that only slice patterns whose length is known at compile-time, and the trivial slice `[..]`, are permitted for destructuring assignment.

The desugaring method is straightforward, and is illustrated best by example.
{==+==}
与使用 `let` 进行解构声明不同，由于语法歧义，模式不能出现在赋值语句的左侧。
相反，一组对应模式的表达式被指定为 [可赋值表达式][assignee expression] ，并允许出现在赋值语句的左侧。
然后，将赋值表达式展开为模式匹配，然后进行顺序赋值。
展开后的模式必须是不可拒绝的：特别地，这意味着仅允许具有在编译时已知长度的切片模式以及简单的切片 `[..]` 用于解构赋值。

展开的方法很简单，最好通过示例来说明。
{==+==}


{==+==}
```rust
# struct Struct { x: u32, y: u32 }
# let (mut a, mut b) = (0, 0);
(a, b) = (3, 4);

[a, b] = [3, 4];

Struct { x: a, y: b } = Struct { x: 3, y: 4};

// desugars to:

{
    let (_a, _b) = (3, 4);
    a = _a;
    b = _b;
}

{
    let [_a, _b] = [3, 4];
    a = _a;
    b = _b;
}

{
    let Struct { x: _a, y: _b } = Struct { x: 3, y: 4};
    a = _a;
    b = _b;
}
```
{==+==}

{==+==}


{==+==}
Identifiers are not forbidden from being used multiple times in a single assignee expression.

[Underscore expressions][_UnderscoreExpression_] and empty [range expressions][_RangeExpression_] may be used to ignore certain values, without binding them.

Note that default binding modes do not apply for the desugared expression.
{==+==}
在单个可赋值表达式中，标识符不禁止被多次使用。

[下划线表达式][_UnderscoreExpression_] 和空 [区间表达式][_RangeExpression_] 可用于忽略某些值，而不将它们绑定到变量上。

注意，对于展开后的表达式，不适用默认绑定模式。
{==+==}


{==+==}
## Compound assignment expressions
{==+==}
## 复合赋值表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _CompoundAssignmentExpression_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Expression_] `+=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `-=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `*=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `/=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `%=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `&=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `|=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `^=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `<<=` [_Expression_]\
> &nbsp;&nbsp; | [_Expression_] `>>=` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _复合赋值表达式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_表达式_][_Expression_] `+=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `-=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `*=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `/=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `%=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `&=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `|=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `^=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `<<=` [_表达式_][_Expression_]\
> &nbsp;&nbsp; | [_表达式_][_Expression_] `>>=` [_表达式_][_Expression_]
{==+==}


{==+==}
*Compound assignment expressions* combine arithmetic and logical binary operators with assignment expressions.
{==+==}
*复合赋值表达式* 将算术和逻辑二元运算符与赋值表达式结合在一起。
{==+==}


{==+==}
For example:

```rust
let mut x = 5;
x += 1;
assert!(x == 6);
```
{==+==}
例如:

```rust
let mut x = 5;
x += 1;
assert!(x == 6);
```
{==+==}


{==+==}
The syntax of compound assignment is a [mutable] [place expression], the *assigned operand*, then one of the operators followed by an `=` as a single token (no whitespace), and then a [value expression], the *modifying operand*.

Unlike other place operands, the assigned place operand must be a place expression.
Attempting to use a value expression is a compiler error rather than promoting it to a temporary.

Evaluation of compound assignment expressions depends on the types of the operators.

If both types are primitives, then the modifying operand will be evaluated first followed by the assigned operand.
It will then set the value of the assigned operand's place to the value of performing the operation of the operator with the values of the assigned operand and modifying operand.

> **Note**: This is different than other expressions in that the right operand is evaluated before the left one.

Otherwise, this expression is syntactic sugar for calling the function of the overloading compound assignment trait of the operator (see the table earlier in this chapter).
A mutable borrow of the assigned operand is automatically taken.

For example, the following expression statements in `example` are equivalent:
{==+==}
复合赋值语法是一个 [可变的][mutable] [占位表达式][place expression]，紧接着是 *被赋值的操作数* ，然后是其中一个运算符，后面跟着一个 `=` 符号 (无空格) ，最后是一个 [值表达式][value expression] ，即 *修改操作数* 。

与其他占位操作数不同，被赋值的占位操作数必须是一个占位表达式。尝试使用值表达式会导致编译错误，而不是将其升级为临时值。

复合赋值表达式的求值取决于操作符的类型。

如果两个类型都是原始类型，则将先计算修改操作数，然后是被赋值的操作数。
然后它将使用执行该运算符操作的被赋值操作数和修改操作数的值来设置被赋值操作数的占位的值。

> **注意**: 这与其他表达式不同，因为右操作数在左操作数之前被评估。

否则，这个表达式是语法糖，用于调用运算符的重载复合赋值特性的函数 (参见本章前面的表格) 。
被赋值的操作数会自动被可变地借用。

例如，在 `example` 中，以下表达式语句是等价的：
{==+==}


{==+==}
```rust
# struct Addable;
# use std::ops::AddAssign;

impl AddAssign<Addable> for Addable {
    /* */
# fn add_assign(&mut self, other: Addable) {}
}

fn example() {
# let (mut a1, a2) = (Addable, Addable);
  a1 += a2;

# let (mut a1, a2) = (Addable, Addable);
  AddAssign::add_assign(&mut a1, a2);
}
```
{==+==}

{==+==}


{==+==}
Like assignment expressions, compound assignment expressions always produce [the unit value][unit].
{==+==}
与赋值表达式一样，复合赋值表达式始终生成 [单元值][unit] 。
{==+==}


{==+==}
Warning: The evaluation order of operands swaps depending on the types of the operands:
with primitive types the right-hand side will get evaluated first, while with non-primitive types the left-hand side will get evaluated first.
Try not to write code that depends on the evaluation order of operands in compound assignment expressions.
See [this test] for an example of using this dependency.
{==+==}
警告：操作数的求值顺序根据操作数的类型而交换：对于原始类型，右操作数将首先得到评估，而对于非原始类型，左操作数将首先得到评估。
尽量不要编写依赖于复合赋值表达式中操作数的求值顺序的代码。参见 [这个测试][this test] ，了解使用此依赖性的示例。
{==+==}


{==+==}
[copies or moves]: ../expressions.md#moved-and-copied-types
[dropping]: ../destructors.md
[explicit discriminants]: ../items/enumerations.md#explicit-discriminants
[field-less enums]: ../items/enumerations.md#field-less-enum
[grouped expression]: grouped-expr.md
[literal expression]: literal-expr.md#integer-literal-expressions
[logical and]: ../types/boolean.md#logical-and
[logical not]: ../types/boolean.md#logical-not
[logical or]: ../types/boolean.md#logical-or
[logical xor]: ../types/boolean.md#logical-xor
[mutable]: ../expressions.md#mutability
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[assignee expression]: ../expressions.md#place-expressions-and-value-expressions
[undefined behavior]: ../behavior-considered-undefined.md
[unit]: ../types/tuple.md
[Unit-only enums]: ../items/enumerations.md#unit-only-enum
[value expression]: ../expressions.md#place-expressions-and-value-expressions
[temporary value]: ../expressions.md#temporaries
[this test]: https://github.com/rust-lang/rust/blob/1.58.0/src/test/ui/expr/compound-assignment/eval-order.rs
[float-float]: https://github.com/rust-lang/rust/issues/15536
[Function pointer]: ../types/function-pointer.md
[Function item]: ../types/function-item.md
[undefined behavior]: ../behavior-considered-undefined.md
[addr_of]: ../../std/ptr/macro.addr_of.html
[addr_of_mut]: ../../std/ptr/macro.addr_of_mut.html

[_BorrowExpression_]: #borrow-operators
[_DereferenceExpression_]: #the-dereference-operator
[_ErrorPropagationExpression_]: #the-question-mark-operator
[_NegationExpression_]: #negation-operators
[_ArithmeticOrLogicalExpression_]: #arithmetic-and-logical-binary-operators
[_ComparisonExpression_]: #comparison-operators
[_LazyBooleanExpression_]: #lazy-boolean-operators
[_TypeCastExpression_]: #type-cast-expressions
[_AssignmentExpression_]: #assignment-expressions
[_CompoundAssignmentExpression_]: #compound-assignment-expressions

[_Expression_]: ../expressions.md
[_TypeNoBounds_]: ../types.md#type-expressions
[_RangeExpression_]: ./range-expr.md
[_UnderscoreExpression_]: ./underscore-expr.md
{==+==}

{==+==}