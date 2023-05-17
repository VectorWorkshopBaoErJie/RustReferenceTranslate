{==+==}
{{#include types-redirect.html}}
# Types
{==+==}
{{#include types-redirect.html}}
# 类型
{==+==}


{==+==}
Every variable, item, and value in a Rust program has a type. The _type_ of a
*value* defines the interpretation of the memory holding it and the operations
that may be performed on the value.

Built-in types are tightly integrated into the language, in nontrivial ways
that are not possible to emulate in user-defined types. User-defined types have
limited capabilities.

The list of types is:
{==+==}
在 Rust 程序中，每个变量、条目和值都有一个类型。
一个 *值* 的 _类型_ 定义了其存储在内存中的解释方式，以及该值可执行的相关操作。

内置类型与语言编译器紧密集成，实现方式与用户定义类型有所不同。
用户定义类型受到语法规则的限制，因而具有有限的功能。

Rust 类型包括：
{==+==}


{==+==}
* Primitive types:
    * [Boolean] — `bool`
    * [Numeric] — integer and float
    * [Textual] — `char` and `str`
    * [Never] — `!` — a type with no values
* Sequence types:
    * [Tuple]
    * [Array]
    * [Slice]
* User-defined types:
    * [Struct]
    * [Enum]
    * [Union]
* Function types:
    * [Functions]
    * [Closures]
* Pointer types:
    * [References]
    * [Raw pointers]
    * [Function pointers]
* Trait types:
    * [Trait objects]
    * [Impl trait]
{==+==}
* 原始类型:
    * [布尔类型][Boolean] — `bool`
    * [数值类型][Numeric] — 整数和浮点数
    * [文本类型][Textual] — char 和 str
    * [永不类型][Never] — `!` — 没有值的类型
* 序列类型:
    * [元组类型][Tuple]
    * [数组类型][Array]
    * [切片类型][Slice]
* 用户定义类型:
    * [结构体类型][Struct]
    * [枚举类型][Enum]
    * [联合体类型][Union]
* 函数类型：
    * [函数类型][Functions]
    * [闭包类型][Closures]
* 指针类型:
    * [引用类型][References]
    * [原始指针类型][Raw pointers]
    * [函数指针类型][Function pointers]
* Trait 类型:
    * [Trait 对象][Trait objects]
    * [Impl trait]
{==+==}


{==+==}
## Type expressions
{==+==}
## 类型表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Type_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _TypeNoBounds_\
> &nbsp;&nbsp; | [_ImplTraitType_]\
> &nbsp;&nbsp; | [_TraitObjectType_]
>
> _TypeNoBounds_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_ParenthesizedType_]\
> &nbsp;&nbsp; | [_ImplTraitTypeOneBound_]\
> &nbsp;&nbsp; | [_TraitObjectTypeOneBound_]\
> &nbsp;&nbsp; | [_TypePath_]\
> &nbsp;&nbsp; | [_TupleType_]\
> &nbsp;&nbsp; | [_NeverType_]\
> &nbsp;&nbsp; | [_RawPointerType_]\
> &nbsp;&nbsp; | [_ReferenceType_]\
> &nbsp;&nbsp; | [_ArrayType_]\
> &nbsp;&nbsp; | [_SliceType_]\
> &nbsp;&nbsp; | [_InferredType_]\
> &nbsp;&nbsp; | [_QualifiedPathInType_]\
> &nbsp;&nbsp; | [_BareFunctionType_]\
> &nbsp;&nbsp; | [_MacroInvocation_]
{==+==}
> **<sup>语法</sup>**\
> _类型_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _无约束类型组_\
> &nbsp;&nbsp; | [_ImplTrait类型_][_ImplTraitType_]\
> &nbsp;&nbsp; | [_Trait对象类型_][_TraitObjectType_]
>
> _无约束类型组_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_括号类型_][_ParenthesizedType_]\
> &nbsp;&nbsp; | [_ImplTrait类型单约束_][_ImplTraitTypeOneBound_]\
> &nbsp;&nbsp; | [_Trait对象类型单约束_][_TraitObjectTypeOneBound_]\
> &nbsp;&nbsp; | [_类型路径_][_TypePath_]\
> &nbsp;&nbsp; | [_元组类型_][_TupleType_]\
> &nbsp;&nbsp; | [_永不类型_][_NeverType_]\
> &nbsp;&nbsp; | [_原始指针类型_][_RawPointerType_]\
> &nbsp;&nbsp; | [_引用类型_][_ReferenceType_]\
> &nbsp;&nbsp; | [_数组类型_][_ArrayType_]\
> &nbsp;&nbsp; | [_切片类型_][_SliceType_]\
> &nbsp;&nbsp; | [_推断类型_][_InferredType_]\
> &nbsp;&nbsp; | [_限定路径类型_][_QualifiedPathInType_]\
> &nbsp;&nbsp; | [_裸函数类型_][_BareFunctionType_]\
> &nbsp;&nbsp; | [_宏调用_][_MacroInvocation_]
{==+==}


{==+==}
A _type expression_ as defined in the _Type_ grammar rule above is the syntax
for referring to a type. It may refer to:

* Sequence types ([tuple], [array], [slice]).
* [Type paths] which can reference:
    * Primitive types ([boolean], [numeric], [textual]).
    * Paths to an [item] ([struct], [enum], [union], [type alias], [trait]).
    * [`Self` path] where `Self` is the implementing type.
    * Generic [type parameters].
* Pointer types ([reference], [raw pointer], [function pointer]).
* The [inferred type] which asks the compiler to determine the type.
* [Parentheses] which are used for disambiguation.
* Trait types: [Trait objects] and [impl trait].
* The [never] type.
* [Macros] which expand to a type expression.
{==+==}
在上面的 _类型_ 语法规则中所定义的 _类型表达式_ 描述的类型语法，可对应包含以下类型：

* 序列类型 ( [元组][tuple] [数组][array] [切片][slice] ) 。
* [类型路径][Type paths] 可以引用：
    * 原始类型 ( [布尔][boolean] [数值][numeric] [文本][textual] ) 。
    * 对 [条目][item] 的引用 ( [结构体][struct] [枚举][enum] [联合体][union] [类型别名][type alias] [trait] ) 。
    * [Self 路径][`Self` path] ，其中 Self 是实现类型。
    * 泛型 [类型参数][type parameters] 。
* 指针类型 ( [引用][reference] [原始指针][raw pointer] [函数指针][function pointer] ) 。
* [推断类型][inferred type] ，请求编译器确定类型。
* 用于消除歧义的 [括号][Parentheses] 。
* Trait 类型: [Trait 对象][Trait objects] 和 [impl trait] 。
* [永不类型][never] 类型。
* [宏][Macros] ，可展开为类型表达式。

译注： rust 中相关语法和概念有很好的统一性，这里将类型归并到表达式概念下就是很好的体现。
这里要理解的一点是，之所以将类型归并到表达式，是强调了类型同样存在的求值机制，
即多个类型组合后的表达式会求值为一个更为复杂的类型结构，而通过这一类型去约束其他类型和值。
{==+==}


{==+==}
### Parenthesized types
{==+==}
### 括号类型
{==+==}


{==+==}
> _ParenthesizedType_ :\
> &nbsp;&nbsp; `(` [_Type_] `)`
{==+==}
> _括号类型_ :\
> &nbsp;&nbsp; `(` [_类型_][_Type_] `)`
{==+==}


{==+==}
In some situations the combination of types may be ambiguous. Use parentheses
around a type to avoid ambiguity. For example, the `+` operator for [type
boundaries] within a [reference type] is unclear where the
boundary applies, so the use of parentheses is required. Grammar rules that
require this disambiguation use the [_TypeNoBounds_] rule instead of
[_Type_].
{==+==}
有时，类型组合可能不明确，需要添加括号以区分。
例如， [引用类型][reference type] 中 [类型约束][type boundaries] 的 `+` 运算符对应的类型边界容易歧义，则需要使用括号。
这个消除歧义的语法使用 [_无约束类型组_][_TypeNoBounds_] 语法规则，而不是 [_类型_][_Type_] 。
{==+==}


{==+==}
```rust
# use std::any::Any;
type T<'a> = &'a (dyn Any + Send);
```
{==+==}

{==+==}


{==+==}
## Recursive types

Nominal types &mdash; [structs], [enumerations], and [unions] &mdash; may be
recursive. That is, each `enum` variant or `struct` or `union` field may
refer, directly or indirectly, to the enclosing `enum` or `struct` type
itself. Such recursion has restrictions:

* Recursive types must include a nominal type in the recursion (not mere [type
  aliases], or other structural types such as [arrays] or [tuples]). So `type
  Rec = &'static [Rec]` is not allowed.
* The size of a recursive type must be finite; in other words the recursive
  fields of the type must be [pointer types].

An example of a *recursive* type and its use:
{==+==}
## 递归类型

用户定义类型 - [结构体][structs] 、 [枚举][enumerations] 和 [联合体][unions] - 可以是递归的。
也就是说，每个 `enum` 变量或 `struct` 或 `union` 字段可以直接或间接地引用封闭的 `enum` 或 `struct` 类型本身。
这种递归有所限制:

* 递归类型必须包含在递归中的命名类型表达式中 (不是仅仅是 [类型别名][type aliases] ，其他结构类型，例如 [数组][arrays] 或 [元组][tuples] 也是) 。因此， `type Rec = &'static [Rec]` 是不允许的。

* 递归类型的大小必须是有限的；换言之，类型的递归字段必须是 [指针][pointer types] 。

以下是 *递归* 类型及其使用的一个例子:
{==+==}


{==+==}
```rust
enum List<T> {
    Nil,
    Cons(T, Box<List<T>>)
}

let a: List<i32> = List::Cons(7, Box::new(List::Cons(13, Box::new(List::Nil))));
```
{==+==}

{==+==}


{==+==}
[_ArrayType_]: types/array.md
[_BareFunctionType_]: types/function-pointer.md
[_ImplTraitTypeOneBound_]: types/impl-trait.md
[_ImplTraitType_]: types/impl-trait.md
[_InferredType_]: types/inferred.md
[_MacroInvocation_]: macros.md#macro-invocation
[_NeverType_]: types/never.md
[_ParenthesizedType_]: types.md#parenthesized-types
[_QualifiedPathInType_]: paths.md#qualified-paths
[_RawPointerType_]: types/pointer.md#raw-pointers-const-and-mut
[_ReferenceType_]: types/pointer.md#shared-references-
[_SliceType_]: types/slice.md
[_TraitObjectTypeOneBound_]: types/trait-object.md
[_TraitObjectType_]: types/trait-object.md
[_TupleType_]: types/tuple.md#tuple-types
[_TypeNoBounds_]: types.md#type-expressions
[_TypePath_]: paths.md#paths-in-types
[_Type_]: types.md#type-expressions

[Array]: types/array.md
[Boolean]: types/boolean.md
[Closures]: types/closure.md
[Enum]: types/enum.md
[Function pointers]: types/function-pointer.md
[Functions]: types/function-item.md
[Impl trait]: types/impl-trait.md
[Macros]: macros.md
[Numeric]: types/numeric.md
[Parentheses]: #parenthesized-types
[Raw pointers]: types/pointer.md#raw-pointers-const-and-mut
[References]: types/pointer.md#shared-references-
[Slice]: types/slice.md
[Struct]: types/struct.md
[Textual]: types/textual.md
[Trait objects]: types/trait-object.md
[Tuple]: types/tuple.md
[Type paths]: paths.md#paths-in-types
[Union]: types/union.md
[`Self` path]: paths.md#self-1
[arrays]: types/array.md
[enumerations]: types/enum.md
[function pointer]: types/function-pointer.md
[inferred type]: types/inferred.md
[item]: items.md
[never]: types/never.md
[pointer types]: types/pointer.md
[raw pointer]: types/pointer.md#raw-pointers-const-and-mut
[reference type]: types/pointer.md#shared-references-
[reference]: types/pointer.md#shared-references-
[structs]: types/struct.md
[trait]: types/trait-object.md
[tuples]: types/tuple.md
[type alias]: items/type-aliases.md
[type aliases]: items/type-aliases.md
[type boundaries]: trait-bounds.md
[type parameters]: types/parameters.md
[unions]: types/union.md
{==+==}

{==+==}