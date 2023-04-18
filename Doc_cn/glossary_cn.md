{==+==}
# Glossary

### Abstract syntax tree

An ‘abstract syntax tree’, or ‘AST’, is an intermediate representation of
the structure of the program when the compiler is compiling it.

### Alignment

The alignment of a value specifies what addresses values are preferred to
start at. Always a power of two. References to a value must be aligned.
[More][alignment].

### Arity

Arity refers to the number of arguments a function or operator takes.
For some examples, `f(2, 3)` and `g(4, 6)` have arity 2, while `h(8, 2, 6)`
has arity 3. The `!` operator has arity 1.
{==+==}
# 术语表

### 抽象语法树

"抽象语法树" (AST) 是编译器在编译程序时的中间表示程序结构的一种方式。

### 对齐

值的对齐方式指定了值的首选地址。始终是 2 的幂次方。值的引用必须对齐。
[更多][alignment]。

### 函数的元数

元数是指函数或操作符接受的参数数量。例如， `f(2, 3)` 和 `g(4, 6)` 的元数为 2 ，而 `h(8, 2, 6)` 的元数为 3 。`!` 操作符的元数为 1 。 
{==+==}


{==+==}
### Array

An array, sometimes also called a fixed-size array or an inline array, is a value
describing a collection of elements, each selected by an index that can be computed
at run time by the program. It occupies a contiguous region of memory.

### Associated item

An associated item is an item that is associated with another item. Associated
items are defined in [implementations] and declared in [traits]. Only
functions, constants, and type aliases can be associated. Contrast to a [free
item].

### Blanket implementation

Any implementation where a type appears [uncovered](#uncovered-type). `impl<T> Foo
for T`, `impl<T> Bar<T> for T`, `impl<T> Bar<Vec<T>> for T`, and `impl<T> Bar<T>
for Vec<T>` are considered blanket impls. However, `impl<T> Bar<Vec<T>> for
Vec<T>` is not a blanket impl, as all instances of `T` which appear in this `impl`
are covered by `Vec`.
{==+==}
### 数组

数组有时也称为固定大小数组或内联数组，是一个值，描述了一个由元素组成的集合，每个元素都可以由程序在运行时计算的索引所选择。它占用了一段连续的内存区域。

### 关联条目

关联条目是与另一个条目相关联的条目。关联条目在 [实现][implementations] 中定义并在 [traits] 中声明。
只有函数、常量和类型别名可以是关联的。与 [自由条目][free item] 相对。

### 泛型实现

任何一个类型出现 [uncovered](#uncovered-type) 的实现都是泛型实现。
 `impl<T> Foo for T` ， `impl<T> Bar<T> for T` ， `impl<T> Bar<Vec<T>> for T` 和 `impl<T> Bar<T> for Vec<T>` 都被视为泛型实现。
但是， `impl<T> Bar<Vec<T>> for Vec<T>` 不是泛型实现，因为此 `impl` 中出现的所有 `T` 的实例都被 `Vec` 覆盖了。
{==+==}


{==+==}
### Bound

Bounds are constraints on a type or trait. For example, if a bound
is placed on the argument a function takes, types passed to that function
must abide by that constraint.

### Combinator

Combinators are higher-order functions that apply only functions and
earlier defined combinators to provide a result from its arguments.
They can be used to manage control flow in a modular fashion.

### Crate

A crate is the unit of compilation and linking. There are different [types of
crates], such as libraries or executables. Crates may link and refer to other
library crates, called external crates. A crate has a self-contained tree of
[modules], starting from an unnamed root module called the crate root. [Items]
may be made visible to other crates by marking them as public in the crate
root, including through [paths] of public modules.
[More][crate].
{==+==}
### 约束

约束是对类型或特征的限制。例如，如果在函数的参数上放置了约束，则传递给该函数的类型必须遵守该约束。

### 组合子

组合子是只应用函数和先前定义的组合子以从其参数提供结果的高阶函数。它们可用于以模块化的方式管理控制流。

### Crate

Crate (箱体) 是编译和链接的基本单位。它有不同的类型，例如库或可执行文件。 Crate 可以链接和引用其他库 Crate ，称为外部 Crate 。 Crate 具有一个自包含的模块树，从一个未命名的根模块开始，称为 Crate 根。通过在 Crate 根中将条目标记为公共的，包括公共模块的路径，可以使这些条目对其他 Crate 可见。请参考 [Rust文档][crate] 以了解更多信息。
{==+==}


{==+==}
### Dispatch

Dispatch is the mechanism to determine which specific version of code is actually
run when it involves polymorphism. Two major forms of dispatch are static dispatch and
dynamic dispatch. While Rust favors static dispatch, it also supports dynamic dispatch
through a mechanism called ‘trait objects’.

### Dynamically sized type

A dynamically sized type (DST) is a type without a statically known size or alignment.

### Entity

An [*entity*] is a language construct that can be referred to in some way
within the source program, usually via a [path][paths]. Entities include
[types], [items], [generic parameters], [variable bindings], [loop labels],
[lifetimes], [fields], [attributes], and [lints].
{==+==}
### 调度

调度是在涉及多态性时确定实际运行的特定代码版本的机制。静态调度和动态调度是两种主要的调度形式。虽然 Rust 更偏爱静态调度，但它也通过称为 "特征对象" 的机制支持动态调度。

### 动态大小类型

动态大小类型 (DST) 是一种没有静态已知大小或对齐方式的类型。

### 实体

[*实体*][*entity*] 是一种语言结构，在源程序中可以通过某种方式进行引用，通常是通过 [路径][paths] 。实体包括 [类型][types] 、[条目][items] 、[泛型参数][generic parameters] 、 [变量绑定][variable bindings] 、 [循环标签][loop labels] 、 [生命周期][lifetimes] 、 [字段][fields]、 [属性][attributes] 和 [lints] 。
 
{==+==}


{==+==}
### Expression

An expression is a combination of values, constants, variables, operators
and functions that evaluate to a single value, with or without side-effects.

For example, `2 + (3 * 4)` is an expression that returns the value 14.

### Free item

An [item] that is not a member of an [implementation], such as a *free
function* or a *free const*. Contrast to an [associated item].

### Fundamental traits

A fundamental trait is one where adding an impl of it for an existing type is a breaking change.
The `Fn` traits and `Sized` are fundamental.
{==+==}
### 表达式

表达式是值、常量、变量、运算符和函数的组合，可以求值为单个值，带有或不带有副作用。

例如，`2 + (3 * 4)` 是一个返回值为 14 的表达式。

### 自由条目

不属于 [实现][implementation] 的 [条目][item] ，例如 *自由函数* 或 *自由常量* 。与 [关联条目][associated item] 相对。

### 基本 trait

基本 trait 是指为现有类型添加一个 impl 会导致代码不兼容的 trait 。 `Fn` trait 和 `Sized` trait 是基本 trait 。
{==+==}


{==+==}
### Fundamental type constructors

A fundamental type constructor is a type where implementing a [blanket implementation](#blanket-implementation) over it
is a breaking change. `&`, `&mut`, `Box`, and `Pin`  are fundamental.

Any time a type `T` is considered [local](#local-type), `&T`, `&mut T`, `Box<T>`, and `Pin<T>`
are also considered local. Fundamental type constructors cannot [cover](#uncovered-type) other types.
Any time the term "covered type" is used,
the `T` in `&T`, `&mut T`, `Box<T>`, and `Pin<T>` is not considered covered.

### Inhabited

A type is inhabited if it has constructors and therefore can be instantiated. An inhabited type is
not "empty" in the sense that there can be values of the type. Opposite of
[Uninhabited](#uninhabited).

### Inherent implementation

An [implementation] that applies to a nominal type, not to a trait-type pair.
[More][inherent implementation].
{==+==}
### 基本类型构造器

基本类型构造器是指实现 [泛型实现](#blanket-implementation) 会破坏其结构的类型。 `&` 、 `&mut` 、 `Box` 和 `Pin` 是基本类型构造器。

任何时候，只要类型 `T` 被视为 [局部类型](#local-type)， `&T` 、 `&mut T` 、 `Box<T>` 和 `Pin<T>` 也会被视为局部类型。基本类型构造器不能[覆盖](#uncovered-type)其他类型。每当使用术语 "覆盖类型" 时， `&T` 、 `&mut T` 、 `Box<T>` 和 `Pin<T>` 中的 `T` 不被视为覆盖的类型。

### 有实例

如果一个类型具有构造函数并且因此可以被实例化，则称其为 "有实例" 。有实例的类型不是 "空的" ，因为它可以有值。与 [无实例](#uninhabited) 相对。

### 内在实现

适用于命名类型而不是 trait-类型对的 [实现][implementation] 。 [更多][inherent implementation] 。
{==+==}


{==+==}
### Inherent method

A [method] defined in an [inherent implementation], not in a trait
implementation.

### Initialized

A variable is initialized if it has been assigned a value and hasn't since been
moved from. All other memory locations are assumed to be uninitialized. Only
unsafe Rust can create a memory location without initializing it.

### Local trait

A `trait` which was defined in the current crate. A trait definition is local
or not independent of applied type arguments. Given `trait Foo<T, U>`,
`Foo` is always local, regardless of the types substituted for `T` and `U`.
{==+==}
### 内在方法

在内在实现中定义的方法，而不是在 trait 实现中定义的方法。

### 初始化

如果变量被赋值且未被移动，那么它就被初始化了。所有其他的内存位置都被认为是未初始化的。只有使用不安全 Rust 才能创建未初始化的内存位置。

### 本地 trait

在当前 crate 中定义的 `trait` 。一个 trait 定义是否本地与应用的类型参数无关。例如对于 `trait Foo<T, U>` ，不管 `T` 和 `U` 被替换为哪些类型， `Foo` 都是本地的。
{==+==}


{==+==}
### Local type

A `struct`, `enum`, or `union` which was defined in the current crate.
This is not affected by applied type arguments. `struct Foo` is considered local, but
`Vec<Foo>` is not. `LocalType<ForeignType>` is local. Type aliases do not
affect locality.

### Module

A module is a container for zero or more [items]. Modules are organized in a
tree, starting from an unnamed module at the root called the crate root or the
root module. [Paths] may be used to refer to items from other modules, which
may be restricted by [visibility rules].
[More][modules]

### Name

A [*name*] is an [identifier] or [lifetime or loop label] that refers to an
[entity](#entity). A *name binding* is when an entity declaration introduces
an identifier or label associated with that entity. [Paths],
identifiers, and labels are used to refer to an entity.
{==+==}
### 局部类型

在当前 crate 中定义的 `struct` 、 `enum` 或 `union` 类型。应用的类型参数不会影响此定义。例如， `struct Foo` 被认为是局部类型，但 `Vec<Foo>` 不是。`LocalType<ForeignType>` 也是局部类型。类型别名不影响类型的局部性。

### 模块

模块是容纳零个或多个 [条目][items] 的容器。模块被组织成一棵树，从一个未命名的根模块开始，称为 crate root 或 root module。可以使用 [路径][Paths] 引用其他模块中的条目，这些引用可能受 [可见性规则][visibility rules] 的限制。
[更多信息][modules]

### 名称

[*名称*][*name*] 是指引用一个 [实体](#entity) 的 [标识符][identifier] 、 [生命周期或循环标签][lifetime or loop label] 。当一个实体声明引入与该实体相关联的标识符或标签时，称之为 *名称绑定* 。可以使用 [路径][Paths] 、标识符和标签来引用实体。
{==+==}


{==+==}
### Name resolution

[*Name resolution*] is the compile-time process of tying [paths],
[identifiers], and [labels] to [entity](#entity) declarations.

### Namespace

A *namespace* is a logical grouping of declared [names](#name) based on the
kind of [entity](#entity) the name refers to. Namespaces allow the occurrence
of a name in one namespace to not conflict with the same name in another
namespace.

Within a namespace, names are organized in a hierarchy, where each level of
the hierarchy has its own collection of named entities.

### Nominal types

Types that can be referred to by a path directly. Specifically [enums],
[structs], [unions], and [trait objects].
{==+==}
### 名称解析

[*名称解析*][*Name resolution*] 是将 [路径][paths] 、 [标识符][identifiers] 和 [标签][labels] 与 [实体](#entity) 声明绑定的编译时过程。

### 命名空间

*命名空间* 是基于名称所引用的 [实体](#entity) 的种类而声明的名称的逻辑分组。
命名空间允许一个命名空间中的名称与另一个命名空间中相同的名称不发生冲突。

在命名空间内，名称按层级组织，每个层级都有自己的命名实体集合。

### 名义类型

可直接通过路径引用的类型。具体而言，包括 [枚举][enums] 、 [结构体][structs] 、 [联合体][unions] 和 [trait 对象][trait objects] 。
{==+==}


{==+==}
### Object safe traits

[Traits] that can be used as [trait objects]. Only traits that follow specific
[rules][object safety] are object safe.

### Path

A [*path*] is a sequence of one or more path segments used to refer to an
[entity](#entity) in the current scope or other levels of a
[namespace](#namespace) hierarchy.

### Prelude

Prelude, or The Rust Prelude, is a small collection of items - mostly traits - that are
imported into every module of every crate. The traits in the prelude are pervasive.
{==+==}
### 对象安全trait

可以用作 [trait objects] 的 [Traits] 。只有符合特定 [规则][object safety] 的 Trait 才是对象安全。

### 路径

[*Path*] 是一个由一个或多个路径段组成的序列，用于引用当前作用域或其他层级的 [命名空间](#namespace) 中的 [实体](#entity) 。

### 预导入

预导入模块，或称为 Rust 预导入库，是一个小集合的项目，主要是 Trait ，被导入到每个 crate 的每个模块中。预导入库中的 Trait 是普遍适用的。
{==+==}


{==+==}
### Scope

A [*scope*] is the region of source text where a named [entity](#entity) may
be referenced with that name.

### Scrutinee

A scrutinee is the expression that is matched on in `match` expressions and
similar pattern matching constructs. For example, in `match x { A => 1, B => 2 }`,
the expression `x` is the scrutinee.

### Size

The size of a value has two definitions.

The first is that it is how much memory must be allocated to store that value.

The second is that it is the offset in bytes between successive elements in an
array with that item type.

It is a multiple of the alignment, including zero. The size can change
depending on compiler version (as new optimizations are made) and target
platform (similar to how `usize` varies per-platform).

[More][alignment].
{==+==}
### 作用域

作用域 [*scope*] 是源代码文本中的一个区域，在此区域内的命名实体 [entity](#entity) 可以使用其名称引用。

### 被匹配对象

被匹配对象 [*Scrutinee*] 是在 `match` 表达式和类似的模式匹配结构中被匹配的表达式。例如，在 `match x { A => 1, B => 2 }` 中，表达式 `x` 是被匹配对象。

### Size

一个值的 [*Size*] 有两个定义:

第一个定义是该值所需的内存量。

第二个定义是具有该项类型的数组中，相邻元素之间的偏移量 (以字节为单位) 。

大小是对齐的倍数，包括零。大小可以根据编译器版本 (随着新的优化) 和目标平台而变化 (类似于`usize`因平台而异) 。

请参考 [Rust文档][alignment] 以了解更多信息。
{==+==}


{==+==}
### Slice

A slice is dynamically-sized view into a contiguous sequence, written as `[T]`.

It is often seen in its borrowed forms, either mutable or shared. The shared
slice type is `&[T]`, while the mutable slice type is `&mut [T]`, where `T` represents
the element type.

### Statement

A statement is the smallest standalone element of a programming language
that commands a computer to perform an action.

### String literal

A string literal is a string stored directly in the final binary, and so will be
valid for the `'static` duration.

Its type is `'static` duration borrowed string slice, `&'static str`.
{==+==}
### 切片

切片 [*Slice*] 是对连续序列的动态大小视图，写作 `[T]` 。

它经常以其借用形式出现，可以是可变的或共享的。共享切片类型是 `&[T]` ，而可变切片类型是 `&mut [T]` ，其中 `T` 表示元素类型。

### 语句

语句 [*Statement*] 语句是编程语言中最小的独立元素，它命令计算机执行一个操作。

### 字符串字面值

字符串字面值 [*String literal*] 是直接存储在最终二进制文件中的字符串，因此将对 `'static` 持续时间有效。

它的类型是 `'static` 持续时间借用字符串切片， `&'static str` 。
{==+==}


{==+==}
### String slice

A string slice is the most primitive string type in Rust, written as `str`. It is
often seen in its borrowed forms, either mutable or shared. The shared
string slice type is `&str`, while the mutable string slice type is `&mut str`.

Strings slices are always valid UTF-8.

### Trait

A trait is a language item that is used for describing the functionalities a type must provide.
It allows a type to make certain promises about its behavior.

Generic functions and generic structs can use traits to constrain, or bound, the types they accept.

### Turbofish

Paths with generic parameters in expressions must prefix the opening brackets with a `::`.
Combined with the angular brackets for generics, this looks like a fish `::<>`.
As such, this syntax is colloquially referred to as turbofish syntax.
{==+==}
### 字符串切片

字符串切片 [*String slice*] 是 Rust 中最原始的字符串类型，写作`str`。它经常以其借用形式出现，可以是可变的或共享的。共享字符串切片类型是`&str`，而可变字符串切片类型是`&mut str`。

字符串切片始终有效的UTF-8。

### Trait

[*Trait*]是一种语言条目，用于描述类型必须提供的功能。它允许类型对其行为做出某些承诺。

通用函数和通用结构可以使用特征来约束或限制它们接受的类型。

### Turbofish

在表达式中具有通用参数的路径必须在开括号前加上 `::` 。与通用角括号结合使用，看起来像鱼形符号 `::<>` 。因此，这种语法俗称为 turbofish 语法。
{==+==}


{==+==}
Examples:

```rust
let ok_num = Ok::<_, ()>(5);
let vec = [1, 2, 3].iter().map(|n| n * 2).collect::<Vec<_>>();
```

This `::` prefix is required to disambiguate generic paths with multiple comparisons in a comma-separate list.
See [the bastion of the turbofish][turbofish test] for an example where not having the prefix would be ambiguous.
{==+==}
例如:

```rust
let ok_num = Ok::<_, ()>(5);
let vec = [1, 2, 3].iter().map(|n| n * 2).collect::<Vec<_>>();
```

这个 `::` 前缀是为了消除在逗号分隔列表中有多个类型参数的泛型路径的歧义。
请参见 [turbofish test][turbofish test] ，其中有一个在没有前缀时产生歧义的例子。
{==+==}


{==+==}
### Uncovered type

A type which does not appear as an argument to another type. For example,
`T` is uncovered, but the `T` in `Vec<T>` is covered. This is only relevant for
type arguments.

### Undefined behavior

Compile-time or run-time behavior that is not specified. This may result in,
but is not limited to: process termination or corruption; improper, incorrect,
or unintended computation; or platform-specific results.
[More][undefined-behavior].

### Uninhabited

A type is uninhabited if it has no constructors and therefore can never be instantiated. An
uninhabited type is "empty" in the sense that there are no values of the type. The canonical
example of an uninhabited type is the [never type] `!`, or an enum with no variants
`enum Never { }`. Opposite of [Inhabited](#inhabited).
{==+==}
### 未涵盖的类型

一个未出现为另一个类型的参数的类型。例如， `T` 是未涵盖的类型，而 `Vec<T>` 中的 `T` 是涵盖的。这仅与类型参数有关。

### 未定义行为

未指定的编译时或运行时行为。这可能导致但不限于：进程终止或破坏；不适当、不正确或意外的计算；或平台特定的结果。[更多信息][undefined-behavior]。

### 无法驻留的

如果一个类型没有构造函数，因此永远无法被实例化，则该类型是无法驻留的。一个无法驻留的类型在某种意义上是 "空的" ，因为该类型没有值。无法居住类型的典型示例是 [永不类型][never type] `!` ，或者没有变体的枚举 `enum Never { }` 。与 [Inhabited](#inhabited) 相对。
{==+==}


{==+==}
[alignment]: type-layout.md#size-and-alignment
[associated item]: #associated-item
[attributes]: attributes.md
[*entity*]: names.md
[crate]: crates-and-source-files.md
[enums]: items/enumerations.md
[fields]: expressions/field-expr.md
[free item]: #free-item
[generic parameters]: items/generics.md
[identifier]: identifiers.md
[identifiers]: identifiers.md
[implementation]: items/implementations.md
[implementations]: items/implementations.md
[inherent implementation]: items/implementations.md#inherent-implementations
[item]: items.md
[items]: items.md
[labels]: tokens.md#lifetimes-and-loop-labels
[lifetime or loop label]: tokens.md#lifetimes-and-loop-labels
[lifetimes]: tokens.md#lifetimes-and-loop-labels
[lints]: attributes/diagnostics.md#lint-check-attributes
[loop labels]: tokens.md#lifetimes-and-loop-labels
[method]: items/associated-items.md#methods
[modules]: items/modules.md
[*Name resolution*]: names/name-resolution.md
[*name*]: names.md
[*namespace*]: names/namespaces.md
[never type]: types/never.md
[object safety]: items/traits.md#object-safety
[*path*]: paths.md
[Paths]: paths.md
[*scope*]: names/scopes.md
[structs]: items/structs.md
[trait objects]: types/trait-object.md
[traits]: items/traits.md
[turbofish test]: https://github.com/rust-lang/rust/blob/1.58.0/src/test/ui/parser/bastion-of-the-turbofish.rs
[types of crates]: linkage.md
[types]: types.md
[undefined-behavior]: behavior-considered-undefined.md
[unions]: items/unions.md
[variable bindings]: patterns.md
[visibility rules]: visibility-and-privacy.md
{==+==}

{==+==}