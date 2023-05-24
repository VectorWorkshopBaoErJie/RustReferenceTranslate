{==+==}
# Implementations
{==+==}
# 实现
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Implementation_ :\
> &nbsp;&nbsp; _InherentImpl_ | _TraitImpl_
>
> _InherentImpl_ :\
> &nbsp;&nbsp; `impl` [_GenericParams_]<sup>?</sup>&nbsp;[_Type_]&nbsp;[_WhereClause_]<sup>?</sup> `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; [_AssociatedItem_]<sup>\*</sup>\
> &nbsp;&nbsp; `}`
>
> _TraitImpl_ :\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> `impl` [_GenericParams_]<sup>?</sup> `!`<sup>?</sup>
>              [_TypePath_] `for` [_Type_]\
> &nbsp;&nbsp; [_WhereClause_]<sup>?</sup>\
> &nbsp;&nbsp; `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; [_AssociatedItem_]<sup>\*</sup>\
> &nbsp;&nbsp; `}`
{==+==}
> **<sup>语法</sup>**\
> _实现_ :\
> &nbsp;&nbsp; _内部实现_ | _Trait实现_
>
> _内部实现_ :\
> &nbsp;&nbsp; `impl` [_泛型参数组_][_GenericParams_]<sup>?</sup>&nbsp;[_类型_][_Type_]&nbsp;[_Where从句_][_WhereClause_]<sup>?</sup> `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; [_关联条目_][_AssociatedItem_]<sup>\*</sup>\
> &nbsp;&nbsp; `}`
>
> _Trait实现_ :\
> &nbsp;&nbsp; `unsafe`<sup>?</sup> `impl` [_泛型参数组_][_GenericParams_]<sup>?</sup> `!`<sup>?</sup>
>              [_类型路径_][_TypePath_] `for` [_类型_][_Type_]\
> &nbsp;&nbsp; [_Where从句_][_WhereClause_]<sup>?</sup>\
> &nbsp;&nbsp; `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; [_内部属性_][_InnerAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; [_关联条目_][_AssociatedItem_]<sup>\*</sup>\
> &nbsp;&nbsp; `}`
{==+==}


{==+==}
An _implementation_ is an item that associates items with an _implementing type_.
Implementations are defined with the keyword `impl` and contain functions
that belong to an instance of the type that is being implemented or to the
type statically.
{==+==}
 _实现_ 条目将 _实现类型_ 与其中条目相关联。
实现使用关键字 `impl` 定义，其包含被实现类型实例或静态类型的函数。
{==+==}


{==+==}
There are two types of implementations:
{==+==}
实现有两种类型:
{==+==}


{==+==}
- inherent implementations
- [trait] implementations
{==+==}
- 内部实现
- [trait] 实现
{==+==}


{==+==}
## Inherent Implementations
{==+==}
## 内部实现
{==+==}


{==+==}
An inherent implementation is defined as the sequence of the `impl` keyword,
generic type declarations, a path to a nominal type, a where clause, and a
bracketed set of associable items.
{==+==}
内部实现被定义为以下几个部分的集合: `impl` 关键字、泛型类型声明、指向具名类型的路径、 where 从句，以及一组用括号包围的可关联条目。
{==+==}


{==+==}
The nominal type is called the _implementing type_ and the associable items are
the _associated items_ to the implementing type.
{==+==}
该具名类型被称为 _实现类型_ ，所包含的条目是该实现类型的 _关联条目_ 。
{==+==}


{==+==}
Inherent implementations associate the contained items to the
implementing type.  Inherent implementations can contain [associated
functions] (including [methods]) and [associated constants]. They cannot
contain associated type aliases.
{==+==}
内部实现将包含的条目与实现类型关联。内部实现可以包含关联函数 (包括 [方法][methods] ) 和 [关联常量][associated constants] 。它们不能包含关联类型别名。
{==+==}


{==+==}
The [path] to an associated item is any path to the implementing type,
followed by the associated item's identifier as the final path
component.
{==+==}
到关联条目的 [路径][path] 是到实现类型路径，而后跟随关联条目的标识符。
{==+==}


{==+==}
A type can also have multiple inherent implementations. An implementing type
must be defined within the same crate as the original type definition.
{==+==}
一个类型也可以有多个内部实现。实现类型必须与原始类型定义在相同的 crate 中。
{==+==}


{==+==}
``` rust
pub mod color {
    pub struct Color(pub u8, pub u8, pub u8);

    impl Color {
        pub const WHITE: Color = Color(255, 255, 255);
    }
}

mod values {
    use super::color::Color;
    impl Color {
        pub fn red() -> Color {
            Color(255, 0, 0)
        }
    }
}
{==+==}

{==+==}


{==+==}
pub use self::color::Color;
fn main() {
    // Actual path to the implementing type and impl in the same module.
    color::Color::WHITE;

    // Impl blocks in different modules are still accessed through a path to the type.
    color::Color::red();

    // Re-exported paths to the implementing type also work.
    Color::red();

    // Does not work, because use in `values` is not pub.
    // values::Color::red();
}
```
{==+==}
pub use self::color::Color;
fn main() {
    // 实现类型的真实路径和 impl 在同一模块。
    color::Color::WHITE;

    // 不同模块中的 Impl 块仍然是通过类型的路径来访问。
    color::Color::red();

    // 也可以将路径重新导出到实现类型。
    Color::red();

    // 不起作用，因为在 `values` 中没有使用 pub 。
    // values::Color::red();
}
```
{==+==}



{==+==}
## Trait Implementations
{==+==}
## Trait 实现
{==+==}


{==+==}
A _trait implementation_ is defined like an inherent implementation except that
the optional generic type declarations are followed by a [trait], followed
by the keyword `for`, followed by a path to a nominal type.
{==+==}
 _trait实现_ 和内部实现类似，只是可选的泛型类型声明后面是 [trait] ，再跟着 `for` 关键字，最后是指向具名类型的路径。
{==+==}


{==+==}
<!-- To understand this, you have to back-reference to the previous section. :( -->
{==+==}

{==+==}


{==+==}
The trait is known as the _implemented trait_. The implementing type
implements the implemented trait.
{==+==}
该 trait 被称为 _实现 trait_ 。实现类型实现其实现 trait 。
{==+==}


{==+==}
A trait implementation must define all non-default associated items declared
by the implemented trait, may redefine default associated items defined by the
implemented trait, and cannot define any other items.
{==+==}
 trait 所有非默认关联条目必须实现，可以重新定义其定义的默认关联条目，但无法定义任何其他条目。
{==+==}


{==+==}
The path to the associated items is `<` followed by a path to the implementing
type followed by `as` followed by a path to the trait followed by `>` as a path
component followed by the associated item's path component.
{==+==}
与实现类型相关联的关联条目的路径是 `<`，后跟到实现类型的路径，然后是 `as`，后跟到 trait 的路径，之后 `>` 作为路径组件，然后是关联条目的路径组件。
{==+==}


{==+==}
[Unsafe traits] require the trait implementation to begin with the `unsafe`
keyword.
{==+==}
[Unsafe traits] 指的是需要在 trait 实现中使用 `unsafe` 关键字的 trait 。
{==+==}


{==+==}
```rust
# #[derive(Copy, Clone)]
# struct Point {x: f64, y: f64};
# type Surface = i32;
# struct BoundingBox {x: f64, y: f64, width: f64, height: f64};
# trait Shape { fn draw(&self, s: Surface); fn bounding_box(&self) -> BoundingBox; }
# fn do_draw_circle(s: Surface, c: Circle) { }
struct Circle {
    radius: f64,
    center: Point,
}

impl Copy for Circle {}

impl Clone for Circle {
    fn clone(&self) -> Circle { *self }
}

impl Shape for Circle {
    fn draw(&self, s: Surface) { do_draw_circle(s, *self); }
    fn bounding_box(&self) -> BoundingBox {
        let r = self.radius;
        BoundingBox {
            x: self.center.x - r,
            y: self.center.y - r,
            width: 2.0 * r,
            height: 2.0 * r,
        }
    }
}
```
{==+==}

{==+==}


{==+==}
### Trait Implementation Coherence
{==+==}
### Trait 实现一致性
{==+==}


{==+==}
A trait implementation is considered incoherent if either the orphan rules check fails
or there are overlapping implementation instances.
{==+==}
如果孤儿规则检查失败，存在重复的实现实例，则认为 Trait 实现不一致。
{==+==}


{==+==}
Two trait implementations overlap when there is a non-empty intersection of the
traits the implementation is for, the implementations can be instantiated with
the same type. <!-- This is probably wrong? Source: No two implementations can
be instantiable with the same set of types for the input type parameters. -->
{==+==}
两个 trait 实现重叠当且仅当它们的 trait 集合存在非空交集，并且这两个 trait 实现都可以实例化为同一类型。
<!-- This is probably wrong? Source: No two implementations can be instantiable with the same set of types for the input type parameters. -->
{==+==}


{==+==}
#### Orphan rules
{==+==}
#### 唯一性规则
{==+==}


{==+==}
Given `impl<P1..=Pn> Trait<T1..=Tn> for T0`, an `impl` is valid only if at
least one of the following is true:
{==+==}
规定 `impl<P1..=Pn> Trait<T1..=Tn> for T0` 只有当以下至少一项为真时， `impl` 才是有效:
{==+==}


{==+==}
- `Trait` is a [local trait]
- All of
  - At least one of the types `T0..=Tn` must be a [local type]. Let `Ti` be the
    first such type.
  - No [uncovered type] parameters `P1..=Pn` may appear in `T0..Ti` (excluding
    `Ti`)
{==+==}
- `Trait` 是 [局部trait][local trait]
- 全部为
  - `T0..=Tn` 中至少有一个类型必须是 [局部类型][local type] 。让 `Ti` 成为第一个这样的类型。
  - 在 `T0..Ti` (不包括 `Ti` ) 中不得出现 [未覆盖类型][uncovered type] 参数 `P1..=Pn` 。
{==+==}


{==+==}
Only the appearance of *uncovered* type parameters is restricted.
Note that for the purposes of coherence, [fundamental types] are
special. The `T` in `Box<T>` is not considered covered, and `Box<LocalType>`
is considered local.
{==+==}
仅出现 *未覆盖* 类型参数是受限制的。需要注意的是，在一致性的目的下， [基本类型][fundamental types] 是特殊的。
 `Box<T>` 中的 `T` 不被视为被覆盖，而 `Box<LocalType>` 则被视为是局部类型。
{==+==}


{==+==}
## Generic Implementations
{==+==}
## 泛型实现
{==+==}


{==+==}
An implementation can take [generic parameters], which can be used in the rest
of the implementation. Implementation parameters are written directly after the
`impl` keyword.
{==+==}
一个实现可以带有 [泛型参数][generic parameters] ，这些参数可以在实现的其余部分中使用。实现参数直接写在 `impl` 关键字之后。
{==+==}


{==+==}
```rust
# trait Seq<T> { fn dummy(&self, _: T) { } }
impl<T> Seq<T> for Vec<T> {
    /* ... */
}
impl Seq<bool> for u32 {
    /* Treat the integer as a sequence of bits */
}
```
{==+==}
```rust
# trait Seq<T> { fn dummy(&self, _: T) { } }
impl<T> Seq<T> for Vec<T> {
    /* ... */
}
impl Seq<bool> for u32 {
    /* 将整数视为比特序列 */
}
```
{==+==}


{==+==}
Generic parameters *constrain* an implementation if the parameter appears at
least once in one of:
{==+==}
如果泛型参数至少在一种类型的关联条目中出现，则该泛型参数 *约束* 了实现:
{==+==}


{==+==}
* The implemented trait, if it has one
* The implementing type
* As an [associated type] in the [bounds] of a type that contains another
  parameter that constrains the implementation
{==+==}
* 已实现的 trait ，如果有其一
* 实现类型
* 作为一个 [关联类型][associated type] 的 [约束][bounds] ，该类型包含约束实现的另一个参数
{==+==}


{==+==}
Type and const parameters must always constrain the implementation. Lifetimes
must constrain the implementation if the lifetime is used in an associated type.
{==+==}
类型和常量参数必须始终约束实现。如果在关联类型中使用生命周期，则生命周期必须约束实现。
{==+==}


{==+==}
Examples of constraining situations:
{==+==}
约束示例:
{==+==}


{==+==}
```rust
# trait Trait{}
# trait GenericTrait<T> {}
# trait HasAssocType { type Ty; }
# struct Struct;
# struct GenericStruct<T>(T);
# struct ConstGenericStruct<const N: usize>([(); N]);
{==+==}

{==+==}


{==+==}
// T constrains by being an argument to GenericTrait.
impl<T> GenericTrait<T> for i32 { /* ... */ }

// T constrains by being an arguement to GenericStruct
impl<T> Trait for GenericStruct<T> { /* ... */ }
{==+==}
// T 通过作为 GenericTrait 的一个参数来约束。
impl<T> GenericTrait<T> for i32 { /* ... */ }

// T 通过作为 GenericTrait 的一个参数来约束。
impl<T> Trait for GenericStruct<T> { /* ... */ }
{==+==}


{==+==}
// Likewise, N constrains by being an argument to ConstGenericStruct
impl<const N: usize> Trait for ConstGenericStruct<N> { /* ... */ }

// T constrains by being in an associated type in a bound for type `U` which is
// itself a generic parameter constraining the trait.
impl<T, U> GenericTrait<U> for u32 where U: HasAssocType<Ty = T> { /* ... */ }

// Like previous, except the type is `(U, isize)`. `U` appears inside the type
// that includes `T`, and is not the type itself.
impl<T, U> GenericStruct<U> where (U, isize): HasAssocType<Ty = T> { /* ... */ }
```
{==+==}
// 同样地， N 通过作为 ConstGenericStruct 的一个参数来约束
impl<const N: usize> Trait for ConstGenericStruct<N> { /* ... */ }

// T 通过在类型 `U` 的约束中的关联类型来约束，该类型本身是约束 trait 的一个泛型参数。
impl<T, U> GenericTrait<U> for u32 where U: HasAssocType<Ty = T> { /* ... */ }

// 和前面一样，除了类型是 `(U, isize)` 。 `U` 出现在包括 `T` 的类型里面，而不是类型本身。
impl<T, U> GenericStruct<U> where (U, isize): HasAssocType<Ty = T> { /* ... */ }
```
{==+==}


{==+==}
Examples of non-constraining situations:
{==+==}
非约束性的示例:
{==+==}


{==+==}
```rust,compile_fail
// The rest of these are errors, since they have type or const parameters that
// do not constrain.

// T does not constrain since it does not appear at all.
impl<T> Struct { /* ... */ }

// N does not constrain for the same reason.
impl<const N: usize> Struct { /* ... */ }

// Usage of T inside the implementation does not constrain the impl.
impl<T> Struct {
    fn uses_t(t: &T) { /* ... */ }
}

// T is used as an associated type in the bounds for U, but U does not constrain.
impl<T, U> Struct where U: HasAssocType<Ty = T> { /* ... */ }

// T is used in the bounds, but not as an associated type, so it does not constrain.
impl<T, U> GenericTrait<U> for u32 where U: GenericTrait<T> {}
```
{==+==}
```rust,compile_fail
// 其余的都是错误，因为它们的类型或常量参数不受约束。

// T没有约束，因为它根本就没有出现。
impl<T> Struct { /* ... */ }

// 由于同样的原因，N并没有受到约束。
impl<const N: usize> Struct { /* ... */ }

// 在实现中使用 T 并不会约束 impl。
impl<T> Struct {
    fn uses_t(t: &T) { /* ... */ }
}

// 在 U 的绑定中， T 用作关联类型，但 U 没有约束。
impl<T, U> Struct where U: HasAssocType<Ty = T> { /* ... */ }

// T 在绑定中使用，但不是作为一个关联类型，所以它不受约束。
impl<T, U> GenericTrait<U> for u32 where U: GenericTrait<T> {}
```
{==+==}


{==+==}
Example of an allowed unconstraining lifetime parameter:
{==+==}
允许不受约束的生命周期参数的示例:
{==+==}


{==+==}
```rust
# struct Struct;
impl<'a> Struct {}
```

Example of a disallowed unconstraining lifetime parameter:

```rust,compile_fail
# struct Struct;
# trait HasAssocType { type Ty; }
impl<'a> HasAssocType for Struct {
    type Ty = &'a Struct;
}
```
{==+==}
```rust
# struct Struct;
impl<'a> Struct {}
```

不允许非约束性生命周期参数的示例:

```rust,compile_fail
# struct Struct;
# trait HasAssocType { type Ty; }
impl<'a> HasAssocType for Struct {
    type Ty = &'a Struct;
}
```
{==+==}


{==+==}
## Attributes on Implementations
{==+==}
## 实现的属性
{==+==}


{==+==}
Implementations may contain outer [attributes] before the `impl` keyword and
inner [attributes] inside the brackets that contain the associated items. Inner
attributes must come before any associated items. The attributes that have
meaning here are [`cfg`], [`deprecated`], [`doc`], and [the lint check
attributes].
{==+==}
实现可以在 `impl` 关键字之前包含外围 [属性][attributes] ，在包含关联条目的括号内部包含内部 [属性][attributes] 。
内部属性必须在任何关联条目之前。在这里有意义的属性是 [`cfg`] ， [`deprecated`] ， [`doc`] 和 [代码分析][the lint check attributes] 。
{==+==}


{==+==}
[_AssociatedItem_]: associated-items.md
[_GenericParams_]: generics.md
[_InnerAttribute_]: ../attributes.md
[_TypePath_]: ../paths.md#paths-in-types
[_Type_]: ../types.md#type-expressions
[_WhereClause_]: generics.md#where-clauses
[trait]: traits.md
[associated constants]: associated-items.md#associated-constants
[associated functions]: associated-items.md#associated-functions-and-methods
[associated type]: associated-items.md#associated-types
[attributes]: ../attributes.md
[bounds]: ../trait-bounds.md
[`cfg`]: ../conditional-compilation.md
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[generic parameters]: generics.md
[methods]: associated-items.md#methods
[path]: ../paths.md
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[Unsafe traits]: traits.md#unsafe-traits
[local trait]: ../glossary.md#local-trait
[local type]: ../glossary.md#local-type
[fundamental types]: ../glossary.md#fundamental-type-constructors
[uncovered type]: ../glossary.md#uncovered-type
{==+==}

{==+==}