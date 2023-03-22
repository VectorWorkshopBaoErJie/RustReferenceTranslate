{==+==}
# Namespaces

A *namespace* is a logical grouping of declared [names]. Names are segregated
into separate namespaces based on the kind of entity the name refers to.
Namespaces allow the occurrence of a name in one namespace to not conflict
with the same name in another namespace.

Within a namespace, names are organized in a hierarchy, where each level of
the hierarchy has its own collection of named entities.

There are several different namespaces that each contain different kinds of
entities. The usage of a name will look for the declaration of that name in
different namespaces, based on the context, as described in the [name
resolution] chapter.

The following is a list of namespaces, with their corresponding entities:
{==+==}
# 命名空间

*命名空间* 是声明的 [名称][names] 的逻辑分组。根据名称所引用的实体类型，名称被分割为不同的命名空间。
命名空间使得一个名称在一个命名空间中的出现不会与另一个命名空间中相同的名称发生冲突。

在一个命名空间中，名称被组织成层次结构，每个层级都有自己的命名实体集合。

有几个不同的命名空间，每个命名空间都包含不同类型的实体。名称的使用将根据上下文在不同的命名空间中查找该名称的声明，如 [名称解析][name resolution] 章节所述。

以下是命名空间列表，以及它们对应的实体:
{==+==}


{==+==}
* Type Namespace
    * [Module declarations]
    * [External crate declarations]
    * [External crate prelude] items
    * [Struct], [union], [enum], enum variant declarations
    * [Trait item declarations]
    * [Type aliases]
    * [Associated type declarations]
    * Built-in types: [boolean], [numeric], and [textual]
    * [Generic type parameters]
    * [`Self` type]
    * [Tool attribute modules]
* Value Namespace
    * [Function declarations]
    * [Constant item declarations]
    * [Static item declarations]
    * [Struct constructors]
    * [Enum variant constructors]
    * [`Self` constructors]
    * [Generic const parameters]
    * [Associated const declarations]
    * [Associated function declarations]
    * Local bindings — [`let`], [`if let`], [`while let`], [`for`], [`match`]
      arms, [function parameters], [closure parameters]
    * Captured [closure] variables
* Macro Namespace
    * [`macro_rules` declarations]
    * [Built-in attributes]
    * [Tool attributes]
    * [Function-like procedural macros]
    * [Derive macros]
    * [Derive macro helpers]
    * [Attribute macros]
* Lifetime Namespace
    * [Generic lifetime parameters]
* Label Namespace
    * [Loop labels]
{==+==}
* 类型命名空间
    * [模块声明][Module declarations]
    * [外部 crate 声明][External crate declarations]
    * [外部 crate 预定义][External crate prelude] 条目
    * 结构体、联合体、枚举类型、枚举类型变量声明
    * [Trait条目声明][Trait item declarations]
    * [类型别名][Type aliases]
    * [关联类型声明][Associated type declarations]
    * 内置类型： [布尔][boolean] 、 [数字][numeric] 、 [文本][textual] 
    * [泛型类型参数][Generic type parameters]
    * [`Self` 类型][`Self` type]
    * [工具属性模块][Tool attribute modules]
* 值命名空间
    * [函数声明][Function declarations]
    * [常量声明][Constant item declarations]
    * [静态声明][Static item declarations]
    * [结构体构造函数][Struct constructors]
    * [枚举类型变量构造函数][Enum variant constructors]
    * [`Self` 构造函数][`Self` constructors]
    * [泛型常量参数][Generic const parameters]
    * [关联常量声明][Associated const declarations]
    * [关联函数声明][Associated function declarations]
    * 本地绑定 —  [`let`][`let`] 、 [`if let`] 、 [`while let`] 、 [`for`] 、 [`match`] 条件、 [函数参数][function parameters] 、 [闭包参数][closure parameters] 
    * 被 [闭包][closure] 捕获的变量
* 宏命名空间
    * [`macro_rules` 声明][`macro_rules` declarations]
    * [内置属性][Built-in attributes]
    * [工具属性][Tool attributes]
    * [类函数过程宏][Function-like procedural macros]
    * [派生宏][Derive macros]
    * [派生宏帮助工具][Derive macro helpers]
    * [属性宏][Attribute macros]
* 生命周期命名空间
    * [泛型生命周期参数][Generic lifetime parameters]
* 标签命名空间
    * [循环标签][Loop labels]
{==+==}


{==+==}
An example of how overlapping names in different namespaces can be used unambiguously:

```rust
// Foo introduces a type in the type namespace and a constructor in the value
// namespace.
struct Foo(u32);

// The `Foo` macro is declared in the macro namespace.
macro_rules! Foo {
    () => {};
}

// `Foo` in the `f` parameter type refers to `Foo` in the type namespace.
// `'Foo` introduces a new lifetime in the lifetime namespace.
fn example<'Foo>(f: Foo) {
    // `Foo` refers to the `Foo` constructor in the value namespace.
    let ctor = Foo;
    // `Foo` refers to the `Foo` macro in the macro namespace.
    Foo!{}
    // `'Foo` introduces a label in the label namespace.
    'Foo: loop {
        // `'Foo` refers to the `'Foo` lifetime parameter, and `Foo`
        // refers to the type namespace.
        let x: &'Foo Foo;
        // `'Foo` refers to the label.
        break 'Foo;
    }
}
```
{==+==}
不同命名空间中重复的名称如何可以无歧义地使用的示例：

```rust
// Foo 在类型命名空间中引入了一个类型和在值命名空间中引入了一个构造函数。
struct Foo(u32);

// `Foo` 宏在宏命名空间中声明。
macro_rules! Foo {
    () => {};
}

// `Foo` 在 `f` 参数类型中引用了类型命名空间中的 `Foo` ， `'Foo` 引入了生命周期命名空间中的新生命周期。
fn example<'Foo>(f: Foo) {
    // `Foo` 引用了值命名空间中的 `Foo` 构造函数。
    let ctor = Foo;
    // `Foo` 引用了宏命名空间中的 `Foo` 宏。
    Foo!{}
    // `'Foo` 引入了标签命名空间中的一个标签。
    'Foo: loop {
        // `'Foo` 引用了 `'Foo` 生命周期参数，`Foo` 引用了类型命名空间。
        let x: &'Foo Foo;
        // `'Foo` 引用了该标签。
        break 'Foo;
    }
}
```
{==+==}


{==+==}
## Named entities without a namespace

The following entities have explicit names, but the names are not a part of
any specific namespace.

### Fields

Even though struct, enum, and union fields are named, the named fields do not
live in an explicit namespace. They can only be accessed via a [field
expression], which only inspects the field names of the specific type being
accessed.

### Use declarations

A [use declaration] has named aliases that it imports into scope, but the
`use` item itself does not belong to a specific namespace. Instead, it can
introduce aliases into multiple namespaces, depending on the item kind being
imported.

<!-- TODO: describe how `use` works on the use-declarations page, and link to it here. -->
{==+==}
## 没有命名空间的命名实体

以下实体具有显式名称，但名称不是任何特定命名空间的一部分。

### 字段

尽管结构体、枚举和联合体字段具有名称，但命名字段不属于显式命名空间。它们只能通过 [字段表达式][field expression] 访问，后者仅检查正在访问的特定类型的字段名称。

### Use 声明

[use 声明][use declaration] 具有命名的别名，它们被导入到作用域中，但 `use` 条目本身不属于特定的命名空间。相反，它可以根据导入的条目种类将别名引入多个命名空间中。

<!-- TODO: 描述 `use` 如何在 use 声明页上工作，并在此处链接到该页面。 -->
{==+==}


{==+==}
## Sub-namespaces

The macro namespace is split into two sub-namespaces: one for [bang-style macros] and one for [attributes].
When an attribute is resolved, any bang-style macros in scope will be ignored.
And conversely resolving a bang-style macro will ignore attribute macros in scope.
This prevents one style from shadowing another.

For example, the [`cfg` attribute] and the [`cfg` macro] are two different entities with the same name in the macro namespace, but they can still be used in their respective context.

It is still an error for a [`use` import] to shadow another macro, regardless of their sub-namespaces.
{==+==}
## 子命名空间

宏命名空间被分成两个子命名空间：一个是用于 [感叹号风格宏][bang-style macros] 的子命名空间，另一个是用于 [属性][attributes] 的子命名空间。
当解析属性时，会忽略任何作用域内的感叹号风格宏。
相反，解析感叹号风格宏将忽略作用域内的属性宏。
这防止了一种风格的宏隐藏另一种风格的宏。

例如，[`cfg` 属性][`cfg` attribute] 和 [`cfg` 宏][`cfg` macro] 是宏命名空间中具有相同名称的两个不同实体，但它们仍然可以在各自的上下文中使用。

无论子命名空间如何，[`use` 导入][`use` import] 隐藏另一个宏仍然是一个错误。
{==+==}


{==+==}
[`cfg` attribute]: ../conditional-compilation.md#the-cfg-attribute
[`cfg` macro]: ../conditional-compilation.md#the-cfg-macro
[`for`]: ../expressions/loop-expr.md#iterator-loops
[`if let`]: ../expressions/if-expr.md#if-let-expressions
[`let`]: ../statements.md#let-statements
[`macro_rules` declarations]: ../macros-by-example.md
[`match`]: ../expressions/match-expr.md
[`Self` constructors]: ../paths.md#self-1
[`Self` type]: ../paths.md#self-1
[`use` import]: ../items/use-declarations.md
[`while let`]: ../expressions/loop-expr.md#predicate-pattern-loops
[Associated const declarations]: ../items/associated-items.md#associated-constants
[Associated function declarations]: ../items/associated-items.md#associated-functions-and-methods
[Associated type declarations]: ../items/associated-items.md#associated-types
[Attribute macros]: ../procedural-macros.md#attribute-macros
[attributes]: ../attributes.md
[bang-style macros]: ../macros.md
[boolean]: ../types/boolean.md
[Built-in attributes]: ../attributes.md#built-in-attributes-index
[closure parameters]: ../expressions/closure-expr.md
[closure]: ../expressions/closure-expr.md
[Constant item declarations]: ../items/constant-items.md
[Derive macro helpers]: ../procedural-macros.md#derive-macro-helper-attributes
[Derive macros]: ../procedural-macros.md#derive-macros
[entity]: ../glossary.md#entity
[Enum variant constructors]: ../items/enumerations.md
[enum]: ../items/enumerations.md
[External crate declarations]: ../items/extern-crates.md
[External crate prelude]: preludes.md#extern-prelude
[field expression]: ../expressions/field-expr.md
[Function declarations]: ../items/functions.md
[function parameters]: ../items/functions.md#function-parameters
[Function-like procedural macros]: ../procedural-macros.md#function-like-procedural-macros
[Generic const parameters]: ../items/generics.md#const-generics
[Generic lifetime parameters]: ../items/generics.md
[Generic type parameters]: ../items/generics.md
[Loop labels]: ../expressions/loop-expr.md#loop-labels
[Module declarations]: ../items/modules.md
[name resolution]: name-resolution.md
[names]: ../names.md
[numeric]: ../types/numeric.md
[Static item declarations]: ../items/static-items.md
[Struct constructors]: ../items/structs.md
[Struct]: ../items/structs.md
[textual]: ../types/textual.md
[Tool attribute modules]: ../attributes.md#tool-attributes
[Tool attributes]: ../attributes.md#tool-attributes
[Trait item declarations]: ../items/traits.md
[Type aliases]: ../items/type-aliases.md
[union]: ../items/unions.md
[use declaration]: ../items/use-declarations.md
{==+==}

{==+==}