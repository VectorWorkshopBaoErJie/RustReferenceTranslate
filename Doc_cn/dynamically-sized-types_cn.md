{==+==}
# Dynamically Sized Types

Most types have a fixed size that is known at compile time and implement the
trait [`Sized`][sized]. A type with a size that is known only at run-time is
called a _dynamically sized type_ (_DST_) or, informally, an unsized type.
[Slices] and [trait objects] are two examples of <abbr title="dynamically sized
types">DSTs</abbr>. Such types can only be used in certain cases:

* [Pointer types] to <abbr title="dynamically sized types">DSTs</abbr> are
  sized but have twice the size of pointers to sized types
    * Pointers to slices also store the number of elements of the slice.
    * Pointers to trait objects also store a pointer to a vtable.
* <abbr title="dynamically sized types">DSTs</abbr> can be provided as
  type arguments to generic type parameters having the special `?Sized` bound.
  They can also be used for associated type definitions when the corresponding associated type declaration has a `?Sized` bound.
  By default, any type parameter or associated type has a `Sized` bound, unless it is relaxed using `?Sized`.
* Traits may be implemented for <abbr title="dynamically sized
  types">DSTs</abbr>.
  Unlike with generic type parameters, `Self: ?Sized` is the default in trait definitions.
* Structs may contain a <abbr title="dynamically sized type">DST</abbr> as the
  last field; this makes the struct itself a
  <abbr title="dynamically sized type">DST</abbr>.

> **Note**: [variables], function parameters, [const] items, and [static] items must be
`Sized`.
{==+==}
# 动态大小类型

大多数类型在编译时具有固定的大小并实现了 [`Sized`][sized] trait。大小仅在运行时已知的类型称为 _动态大小类型_ (_DST_) 或非大小类型 。
[切片][Slices] 和 [trait 对象][trait objects] 是两个 <abbr title="动态大小类型">DSTs</abbr> 的例子。这样的类型只能在某些情况下使用：

* 指向 <abbr title="动态大小类型">DSTs</abbr> 的指针类型是有大小的，但大小是指向大小类型的指针的两倍。
    * 指向切片的指针还会存储切片的元素数量。
    * 指向 trait 对象的指针还会存储指向虚表的指针。
* 可以将 <abbr title="动态大小类型">DSTs</abbr> 作为类型参数提供给具有特殊 `?Sized` 绑定的泛型类型参数。当相应的关联类型声明具有 `?Sized` 绑定时，它们还可用于关联类型定义。默认情况下，除非使用 `?Sized` 来放宽，否则任何类型参数或关联类型都具有 `Sized` 绑定。
* trait可以为 <abbr title="动态大小类型">DSTs</abbr> 实现。与泛型类型参数不同，在 trait 定义中，默认情况下 `Self: ?Sized` 。
* 结构体可能包含一个 <abbr title="动态大小类型">DST</abbr> 作为最后一个字段；这使得结构体本身成为了 <abbr title="动态大小类型">DST</abbr> 。

> **注意**: [变量][variables] 、函数参数、[const] 条目和 [static] 条目必须是 `Sized` 。
{==+==}


{==+==}
[sized]: special-types-and-traits.md#sized
[Slices]: types/slice.md
[trait objects]: types/trait-object.md
[Pointer types]: types/pointer.md
[variables]: variables.md
[const]: items/constant-items.md
[static]: items/static-items.md
{==+==}

{==+==}