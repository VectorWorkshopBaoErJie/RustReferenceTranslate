{==+==}
# Constant items
{==+==}
# 常量条目
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ConstantItem_ :\
> &nbsp;&nbsp; `const` ( [IDENTIFIER] | `_` ) `:` [_Type_] ( `=` [_Expression_] )<sup>?</sup> `;`
{==+==}

{==+==}


{==+==}
A *constant item* is an optionally named _[constant value]_ which is not associated
with a specific memory location in the program. Constants are essentially inlined
wherever they are used, meaning that they are copied directly into the relevant
context when used. This includes usage of constants from external crates, and
non-[`Copy`] types. References to the same constant are not necessarily
guaranteed to refer to the same memory address.
{==+==}
*常量条目* 是可选命称的 _[constant value]_ "常量值" ，与程序中的特定内存地址没有关联。
在任何地方使用常量，始终是内联的，在使用时直接克隆到上下文。包括使用外部 crate 的常量，以及非 [`Copy`] 类型。
对同一常量的引用不能保证指向同一内存地址。
{==+==}


{==+==}
Constants must be explicitly typed. The type must have a `'static` lifetime: any
references in the initializer must have `'static` lifetimes.
{==+==}
常量必须是明确的类型。该类型必须有 `'static` 生命周期，初始化器中的任何引用必须有 `'static` 生命周期。
{==+==}


{==+==}
Constants may refer to the address of other constants, in which case the
address will have elided lifetimes where applicable, otherwise – in most cases
– defaulting to the `static` lifetime. (See [static lifetime
elision].) The compiler is, however, still at liberty to translate the constant
many times, so the address referred to may not be stable.
{==+==}
常量可以引用其他常量的地址，在这种情况下，该地址在适用的情况下会有省略的生命周期，否则，在大多数情况下，会默认为 `'static` 生命周期。
(参见[static lifetime elision])。然而，编译器能自由地多次解释常量，所指向的地址可能不稳定。
{==+==}


{==+==}
```rust
const BIT1: u32 = 1 << 0;
const BIT2: u32 = 1 << 1;

const BITS: [u32; 2] = [BIT1, BIT2];
const STRING: &'static str = "bitstring";

struct BitsNStrings<'a> {
    mybits: [u32; 2],
    mystring: &'a str,
}

const BITS_N_STRINGS: BitsNStrings<'static> = BitsNStrings {
    mybits: BITS,
    mystring: STRING,
};
```
{==+==}

{==+==}


{==+==}
The constant expression may only be omitted in a [trait definition].
{==+==}
常量表达式只能在 [trait 定义][trait definition] 中省略。
{==+==}


{==+==}
## Constants with Destructors
{==+==}
## 常量析构函数
{==+==}


{==+==}
Constants can contain destructors. Destructors are run when the value goes out
of scope.
{==+==}
常量可以包含析构器。当值超出作用域时，将运行析构函数。
{==+==}


{==+==}
```rust
struct TypeWithDestructor(i32);

impl Drop for TypeWithDestructor {
    fn drop(&mut self) {
        println!("Dropped. Held {}.", self.0);
    }
}

const ZERO_WITH_DESTRUCTOR: TypeWithDestructor = TypeWithDestructor(0);

fn create_and_drop_zero_with_destructor() {
    let x = ZERO_WITH_DESTRUCTOR;
    // x gets dropped at end of function, calling drop.
    // prints "Dropped. Held 0.".
}
```
{==+==}
```rust
struct TypeWithDestructor(i32);

impl Drop for TypeWithDestructor {
    fn drop(&mut self) {
        println!("Dropped. Held {}.", self.0);
    }
}

const ZERO_WITH_DESTRUCTOR: TypeWithDestructor = TypeWithDestructor(0);

fn create_and_drop_zero_with_destructor() {
    let x = ZERO_WITH_DESTRUCTOR;
    // x 在函数结束时丢弃，调用drop。
    // prints "Dropped. Held 0.".
}
```
{==+==}


{==+==}
## Unnamed constant
{==+==}
## 未命名常量
{==+==}


{==+==}
Unlike an [associated constant], a [free] constant may be unnamed by using
an underscore instead of the name. For example:
{==+==}
与 [关联常量][associated constant] 不同，[自由][free] 常量可以通过下划线而不被命名。例如:
{==+==}


{==+==}
```rust
const _: () =  { struct _SameNameTwice; };

// OK although it is the same name as above:
const _: () =  { struct _SameNameTwice; };
```
{==+==}
```rust
const _: () =  { struct _SameNameTwice; };

// OK 虽然和上面的同名:
const _: () =  { struct _SameNameTwice; };
```
{==+==}


{==+==}
As with [underscore imports], macros may safely emit the same unnamed constant in
the same scope more than once. For example, the following should not produce an error:
{==+==}
与 [下划线导入][underscore imports] 一样，宏可以安全地在同一个作用域中多次发送同一个未命名的常量。例如，下面的内容不会产生错误:
{==+==}


{==+==}
```rust
macro_rules! m {
    ($item: item) => { $item $item }
}

m!(const _: () = (););
// This expands to:
// const _: () = ();
// const _: () = ();
```
{==+==}

{==+==}


{==+==}
[associated constant]: ../items/associated-items.md#associated-constants
[constant value]: ../const_eval.md#constant-expressions
[free]: ../glossary.md#free-item
[static lifetime elision]: ../lifetime-elision.md#static-lifetime-elision
[trait definition]: traits.md
[IDENTIFIER]: ../identifiers.md
[underscore imports]: use-declarations.md#underscore-imports
[_Type_]: ../types.md#type-expressions
[_Expression_]: ../expressions.md
[`Copy`]: ../special-types-and-traits.md#copy
{==+==}

{==+==}