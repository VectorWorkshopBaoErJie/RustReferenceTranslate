{==+==}
# Enumerations
{==+==}
# 枚举
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Enumeration_ :\
> &nbsp;&nbsp; `enum`
>    [IDENTIFIER]&nbsp;
>    [_GenericParams_]<sup>?</sup>
>    [_WhereClause_]<sup>?</sup>
>    `{` _EnumItems_<sup>?</sup> `}`
>
> _EnumItems_ :\
> &nbsp;&nbsp; _EnumItem_ ( `,` _EnumItem_ )<sup>\*</sup> `,`<sup>?</sup>
>
> _EnumItem_ :\
> &nbsp;&nbsp; _OuterAttribute_<sup>\*</sup> [_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [IDENTIFIER]&nbsp;( _EnumItemTuple_ | _EnumItemStruct_ )<sup>?</sup>
>                                _EnumItemDiscriminant_<sup>?</sup>
>
> _EnumItemTuple_ :\
> &nbsp;&nbsp; `(` [_TupleFields_]<sup>?</sup> `)`
>
> _EnumItemStruct_ :\
> &nbsp;&nbsp; `{` [_StructFields_]<sup>?</sup> `}`
>
> _EnumItemDiscriminant_ :\
> &nbsp;&nbsp; `=` [_Expression_]
{==+==}
> **<sup>语法</sup>**\
> _枚举_ :\
> &nbsp;&nbsp; `enum`
>    [标识符][IDENTIFIER]&nbsp;
>    [_泛型参数组_][_GenericParams_]<sup>?</sup>
>    [_Where子句_][_WhereClause_]<sup>?</sup>
>    `{` _枚举条目组_<sup>?</sup> `}`
>
> _枚举条目组_ :\
> &nbsp;&nbsp; _枚举条目_ ( `,` _枚举条目_ )<sup>\*</sup> `,`<sup>?</sup>
>
> _枚举条目_ :\
> &nbsp;&nbsp; _外部属性_<sup>\*</sup> [_可见性_][_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; [标识符][IDENTIFIER]&nbsp;( _枚举条目元组_ | _枚举条目结构体_ )<sup>?</sup>
>                                _枚举条目判别式_<sup>?</sup>
>
> _枚举条目元组_ :\
> &nbsp;&nbsp; `(` [_元组字段组_][_TupleFields_]<sup>?</sup> `)`
>
> _枚举条目结构体_ :\
> &nbsp;&nbsp; `{` [_结构体字段组_]<sup>?</sup> `}`
>
> _枚举条目判别式_ :\
> &nbsp;&nbsp; `=` [_表达式_][_Expression_]
{==+==}


{==+==}
An *enumeration*, also referred to as an *enum*, is a simultaneous definition of a
nominal [enumerated type] as well as a set of *constructors*, that can be used
to create or pattern-match values of the corresponding enumerated type.
{==+==}
一个 [枚举类型][enumerated type] ， 简称枚举 *enum* ，是一种同时定义了枚举类型和一组构造器 *constructors* 的命名类型。这些构造器可以用来创建或者匹配相应枚举类型的值。
{==+==}


{==+==}
Enumerations are declared with the keyword `enum`.
{==+==}
枚举类型使用关键字 `enum` 进行声明。
{==+==}


{==+==}
An example of an `enum` item and its use:
{==+==}
下面是一个 `enum` 的使用示例:
{==+==}


{==+==}
```rust
enum Animal {
    Dog,
    Cat,
}

let mut a: Animal = Animal::Dog;
a = Animal::Cat;
```
{==+==}

{==+==}


{==+==}
Enum constructors can have either named or unnamed fields:
{==+==}
枚举构造器可以拥有命名的字段或者没有命名的字段:
{==+==}


{==+==}
```rust
enum Animal {
    Dog(String, f64),
    Cat { name: String, weight: f64 },
}

let mut a: Animal = Animal::Dog("Cocoa".to_string(), 37.2);
a = Animal::Cat { name: "Spotty".to_string(), weight: 2.7 };
```
{==+==}

{==+==}


{==+==}
In this example, `Cat` is a _struct-like enum variant_, whereas `Dog` is simply
called an enum variant.
{==+==}
在这个例子中， `Cat` 是一个 _类结构体枚举变体_ ，而 `Dog` 则仅称为枚举变体。
{==+==}


{==+==}
An enum where no constructors contain fields are called a
*<span id="field-less-enum">field-less enum</span>*. For example, this is a fieldless enum:
{==+==}
没有构造器包含字段的枚举称为 *<span id="field-less-enum">field-less enum</span>* "无字段枚举" 。例如，以下是一个无字段枚举:
{==+==}


{==+==}
```rust
enum Fieldless {
    Tuple(),
    Struct{},
    Unit,
}
```
{==+==}

{==+==}


{==+==}
If a field-less enum only contains unit variants, the enum is called an
*<span id="unit-only-enum">unit-only enum</span>*. For example:
{==+==}
如果一个不包含字段的枚举只包含单元枚举变体，则该枚举称为 *<span id="unit-only-enum">unit-only enum</span>* "单元枚举"。例如：
{==+==}


{==+==}
```rust
enum Enum {
    Foo = 3,
    Bar = 2,
    Baz = 1,
}
```
{==+==}

{==+==}


{==+==}
<span id="custom-discriminant-values-for-fieldless-enumerations"></span>
## Discriminants
{==+==}
<span id="custom-discriminant-values-for-fieldless-enumerations"></span>
## 鉴别值
{==+==}


{==+==}
Each enum instance has a _discriminant_: an integer logically associated to it
that is used to determine which variant it holds.
{==+==}
每个枚举实例都有一个 _鉴别值_ ：一个逻辑上与之关联的整数，用来确定它持有的变体。
{==+==}


{==+==}
Under the [default representation], the discriminant is interpreted as
an `isize` value. However, the compiler is allowed to use a smaller type (or
another means of distinguishing variants) in its actual memory layout.
{==+==}
在 [默认表示][default representation] 下，鉴别值解释为 `isize` 值。
然而，编译器允许在实际内存布局中使用较小的类型 (或其他区分变体的方式) 。
{==+==}


{==+==}
### Assigning discriminant values
{==+==}
### 分配鉴别值
{==+==}


{==+==}
#### Explicit discriminants
{==+==}
#### 显式鉴别值
{==+==}


{==+==}
In two circumstances, the discriminant of a variant may be explicitly set by
following the variant name with `=` and a [constant expression]:
{==+==}
在两种情况下，可以通过在变体名称后跟 `=` 和 [常量表达式][constant expression] 来明确设置变体的判别式 :
{==+==}


{==+==}
1. if the enumeration is "[unit-only]".
{==+==}
1. 如果该枚举是 "[unit-only]" 单元枚举 。
{==+==}


{==+==}
2. if a [primitive representation] is used. For example:
{==+==}
2. 如果使用 [原始表示] [primitive representation] 。例如：
{==+==}


{==+==}
   ```rust
   #[repr(u8)]
   enum Enum {
       Unit = 3,
       Tuple(u16),
       Struct {
           a: u8,
           b: u16,
       } = 1,
   }
   ```
{==+==}

{==+==}


{==+==}
#### Implicit discriminants
{==+==}
#### 隐式鉴别值
{==+==}


{==+==}
If a discriminant for a variant is not specified, then it is set to one higher
than the discriminant of the previous variant in the declaration. If the
discriminant of the first variant in the declaration is unspecified, then
it is set to zero.
{==+==}
如果枚举变体的鉴别值没有指定，则它被设置为在声明中前一个变体的鉴别值加1。如果第一个变体的鉴别值未指定，则设置为零。
{==+==}


{==+==}
```rust
enum Foo {
    Bar,            // 0
    Baz = 123,      // 123
    Quux,           // 124
}
{==+==}
```rust
enum Foo {
    Bar,            // 0        第一个未指定，则为0
    Baz = 123,      // 123
    Quux,           // 124      未指定，相比前一个加1
}
{==+==}


{==+==}
let baz_discriminant = Foo::Baz as u32;
assert_eq!(baz_discriminant, 123);
```
{==+==}

{==+==}


{==+==}
#### Restrictions
{==+==}
#### 限制条件
{==+==}


{==+==}
It is an error when two variants share the same discriminant.
{==+==}
当两个变体鉴别值相同时，是一个错误。
{==+==}


{==+==}
```rust,compile_fail
enum SharedDiscriminantError {
    SharedA = 1,
    SharedB = 1
}

enum SharedDiscriminantError2 {
    Zero,       // 0
    One,        // 1
    OneToo = 1  // 1 (collision with previous!)
}
```
{==+==}

{==+==}


{==+==}
It is also an error to have an unspecified discriminant where the previous
discriminant is the maximum value for the size of the discriminant.
{==+==}
如果先前枚举的鉴别值达到了其类型能够表示的最大值，那么下一个没有指定鉴别值的项将导致错误。
{==+==}


{==+==}
```rust,compile_fail
#[repr(u8)]
enum OverflowingDiscriminantError {
    Max = 255,
    MaxPlusOne // Would be 256, but that overflows the enum.
}

#[repr(u8)]
enum OverflowingDiscriminantError2 {
    MaxMinusOne = 254, // 254
    Max,               // 255
    MaxPlusOne         // Would be 256, but that overflows the enum.
}
```
{==+==}
```rust,compile_fail
#[repr(u8)]
enum OverflowingDiscriminantError {
    Max = 255,
    MaxPlusOne // 将是256，但那会使枚举溢出。
}

#[repr(u8)]
enum OverflowingDiscriminantError2 {
    MaxMinusOne = 254, // 254
    Max,               // 255
    MaxPlusOne         // 将是256，但那会使枚举溢出。
}
```
{==+==}


{==+==}
### Accessing discriminant
{==+==}
### 访问鉴别值
{==+==}


{==+==}
#### Via `mem::discriminant`
{==+==}
#### 通过 `mem::discriminant`
{==+==}


{==+==}
[`mem::discriminant`] returns an opaque reference to the discriminant of
an enum value which can be compared. This cannot be used to get the value
of the discriminant.
{==+==}
[`mem::discriminant`] 返回一个不透明的引用，指向枚举值的鉴别值，可以进行比较。但不能用于获取鉴别值。
{==+==}


{==+==}
#### Casting
{==+==}
#### 转换
{==+==}


{==+==}
If an enumeration is [unit-only] (with no tuple and struct variants), then its
discriminant can be directly accessed with a [numeric cast]; e.g.:
{==+==}
如果一个枚举类型是 [unit-only]  (没有元组和结构的变体) ，那么它的鉴别值可以通过 [数字强制转换][numeric cast] 直接访问；比如:
{==+==}


{==+==}
```rust
enum Enum {
    Foo,
    Bar,
    Baz,
}

assert_eq!(0, Enum::Foo as isize);
assert_eq!(1, Enum::Bar as isize);
assert_eq!(2, Enum::Baz as isize);
```
{==+==}

{==+==}


{==+==}
[Field-less enums] can be casted if they do not have explicit discriminants, or where only unit variants are explicit.
{==+==}
[无成员的枚举][Field-less enums] 可以被强制类型转换，如果它们没有显式的鉴别值，或者只有单元变体是显式的。
{==+==}


{==+==}
```rust
enum Fieldless {
    Tuple(),
    Struct{},
    Unit,
}

assert_eq!(0, Fieldless::Tuple() as isize);
assert_eq!(1, Fieldless::Struct{} as isize);
assert_eq!(2, Fieldless::Unit as isize);

#[repr(u8)]
enum FieldlessWithDiscrimants {
    First = 10,
    Tuple(),
    Second = 20,
    Struct{},
    Unit,
}

assert_eq!(10, FieldlessWithDiscrimants::First as u8);
assert_eq!(11, FieldlessWithDiscrimants::Tuple() as u8);
assert_eq!(20, FieldlessWithDiscrimants::Second as u8);
assert_eq!(21, FieldlessWithDiscrimants::Struct{} as u8);
assert_eq!(22, FieldlessWithDiscrimants::Unit as u8);
```
{==+==}

{==+==}


{==+==}
#### Pointer casting
{==+==}
#### 指针转换
{==+==}


{==+==}
If the enumeration specifies a [primitive representation], then the
discriminant may be reliably accessed via unsafe pointer casting:
{==+==}
如果枚举指定了 [原始表示][primitive representation] ，那么可以通过不安全的指针转换来可靠地访问鉴别值:
{==+==}


{==+==}
```rust
#[repr(u8)]
enum Enum {
    Unit,
    Tuple(bool),
    Struct{a: bool},
}

impl Enum {
    fn discriminant(&self) -> u8 {
        unsafe { *(self as *const Self as *const u8) }
    }
}

let unit_like = Enum::Unit;
let tuple_like = Enum::Tuple(true);
let struct_like = Enum::Struct{a: false};

assert_eq!(0, unit_like.discriminant());
assert_eq!(1, tuple_like.discriminant());
assert_eq!(2, struct_like.discriminant());
```
{==+==}

{==+==}


{==+==}
## Zero-variant enums
{==+==}
## 零变体枚举
{==+==}


{==+==}
Enums with zero variants are known as *zero-variant enums*. As they have
no valid values, they cannot be instantiated.
{==+==}
没有变体的枚举被称为 *零变体枚举* 。由于它们没有有效值，因此不能实例化。
{==+==}


{==+==}
```rust
enum ZeroVariants {}
```
{==+==}

{==+==}


{==+==}
Zero-variant enums are equivalent to the [never type], but they cannot be
coerced into other types.
{==+==}
零变体枚举被认为是 [永不类型][never type] 的等效形式，但是它们不能被强制转换为其他类型。
{==+==}


{==+==}
```rust,compile_fail
# enum ZeroVariants {}
let x: ZeroVariants = panic!();
let y: u32 = x; // mismatched type error
```
{==+==}
```rust,compile_fail
# enum ZeroVariants {}
let x: ZeroVariants = panic!();
let y: u32 = x; // 类型不匹配的错误
```
{==+==}


{==+==}
## Variant visibility
{==+==}
## 变体可见性
{==+==}


{==+==}
Enum variants syntactically allow a [_Visibility_] annotation, but this is
rejected when the enum is validated. This allows items to be parsed with a
unified syntax across different contexts where they are used.
{==+==}
枚举变体在语法上允许使用 [_可见性_][_Visibility_] 注释，但在验证枚举时，会被拒绝。
这使得可以在使用它们的不同上下文中使用统一的语法来解析条目。
{==+==}


{==+==}
```rust
macro_rules! mac_variant {
    ($vis:vis $name:ident) => {
        enum $name {
            $vis Unit,

            $vis Tuple(u8, u16),

            $vis Struct { f: u8 },
        }
    }
}

// Empty `vis` is allowed.
mac_variant! { E }

// This is allowed, since it is removed before being validated.
#[cfg(FALSE)]
enum E {
    pub U,
    pub(crate) T(u8),
    pub(super) T { f: String }
}
```
{==+==}
```rust
macro_rules! mac_variant {
    ($vis:vis $name:ident) => {
        enum $name {
            $vis Unit,

            $vis Tuple(u8, u16),

            $vis Struct { f: u8 },
        }
    }
}

// 空的 `vis` 是允许的。
mac_variant! { E }

// 这是允许的，因为它是在验证之前已删除。
#[cfg(FALSE)]
enum E {
    pub U,
    pub(crate) T(u8),
    pub(super) T { f: String }
}
```
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[_GenericParams_]: generics.md
[_WhereClause_]: generics.md#where-clauses
[_Expression_]: ../expressions.md
[_TupleFields_]: structs.md
[_StructFields_]: structs.md
[_Visibility_]: ../visibility-and-privacy.md
[enumerated type]: ../types/enum.md
[`mem::discriminant`]: ../../std/mem/fn.discriminant.html
[never type]: ../types/never.md
[unit-only]: #unit-only-enum
[numeric cast]: ../expressions/operator-expr.md#semantics
[constant expression]: ../const_eval.md#constant-expressions
[default representation]: ../type-layout.md#the-default-representation
[primitive representation]: ../type-layout.md#primitive-representations
[`C` representation]: ../type-layout.md#the-c-representation
[Field-less enums]: #field-less-enum
{==+==}

{==+==}