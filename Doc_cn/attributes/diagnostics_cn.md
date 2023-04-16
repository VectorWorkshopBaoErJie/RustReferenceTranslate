{==+==}
# Diagnostic attributes

The following [attributes] are used for controlling or generating diagnostic
messages during compilation.
{==+==}
# 诊断属性

下面的 [属性][attributes] 用于在编译期间控制或生成诊断信息。
{==+==}


{==+==}
## Lint check attributes

A lint check names a potentially undesirable coding pattern, such as
unreachable code or omitted documentation. The lint attributes `allow`,
`warn`, `deny`, and `forbid` use the [_MetaListPaths_] syntax to specify a
list of lint names to change the lint level for the entity to which the
attribute applies.

For any lint check `C`:

* `allow(C)` overrides the check for `C` so that violations will go
   unreported,
* `warn(C)` warns about violations of `C` but continues compilation.
* `deny(C)` signals an error after encountering a violation of `C`,
* `forbid(C)` is the same as `deny(C)`, but also forbids changing the lint
   level afterwards,

> Note: The lint checks supported by `rustc` can be found via `rustc -W help`,
> along with their default settings and are documented in the [rustc book].
{==+==}
## Lint 检查属性

Lint检查指的是一些可能存在不良编码模式的代码，例如不可到达的代码或省略的文档。
lint属性 `allow` 、 `warn` 、 `deny` 和 `forbid` 使用 [_MetaListPaths_] 语法指定要更改为应用属性的实体的 lint 级别列表。

对于任何lint检查 `C` ：

* `allow(C)` 会覆盖 `C` 的检查，使违规不会被报告，
* `warn(C)` 警告有关 `C` 的违规，但继续编译。
* `deny(C)` 在遇到 `C` 的违规后发出错误信号，
* `forbid(C)` 与 `deny(C)` 相同，但还禁止以后更改 lint 级别。

> 注意: `rustc` 支持的 lint 检查可以通过 `rustc -W help` 找到，包括它们的默认设置，并在 [rustc book] 中有记录。
{==+==}


{==+==}
```rust
pub mod m1 {
    // Missing documentation is ignored here
    #[allow(missing_docs)]
    pub fn undocumented_one() -> i32 { 1 }

    // Missing documentation signals a warning here
    #[warn(missing_docs)]
    pub fn undocumented_too() -> i32 { 2 }

    // Missing documentation signals an error here
    #[deny(missing_docs)]
    pub fn undocumented_end() -> i32 { 3 }
}
```
{==+==}
```rust
pub mod m1 {
    // 忽略此处的缺失文档
    #[allow(missing_docs)]
    pub fn undocumented_one() -> i32 { 1 }

    // 此处缺失文档会发出警告
    #[warn(missing_docs)]
    pub fn undocumented_too() -> i32 { 2 }

    // 此处缺失文档会发出错误
    #[deny(missing_docs)]
    pub fn undocumented_end() -> i32 { 3 }
}
```
{==+==}


{==+==}
Lint attributes can override the level specified from a previous attribute, as
long as the level does not attempt to change a forbidden lint. Previous
attributes are those from a higher level in the syntax tree, or from a
previous attribute on the same entity as listed in left-to-right source order.

This example shows how one can use `allow` and `warn` to toggle a particular
check on and off:
{==+==}
Lint属性可以覆盖先前属性中指定的级别，只要级别不会试图更改禁止的lint。
先前的属性来自语法树中更高级别的属性，或者是源代码中左到右的先前属性。

以下示例展示了如何使用 `allow` 和 `warn` 来打开和关闭特定检查:
{==+==}


{==+==}
```rust
#[warn(missing_docs)]
pub mod m2{
    #[allow(missing_docs)]
    pub mod nested {
        // Missing documentation is ignored here
        pub fn undocumented_one() -> i32 { 1 }

        // Missing documentation signals a warning here,
        // despite the allow above.
        #[warn(missing_docs)]
        pub fn undocumented_two() -> i32 { 2 }
    }

    // Missing documentation signals a warning here
    pub fn undocumented_too() -> i32 { 3 }
}
```
{==+==}
```rust
#[warn(missing_docs)]
pub mod m2 {
    #[allow(missing_docs)]
    pub mod nested {
        // 忽略这里缺失的文档
        pub fn undocumented_one() -> i32 { 1 }

        // 这里缺失的文档会产生警告，
        // 即使之前有一个允许缺失文档的 #[allow(missing_docs)]
        #[warn(missing_docs)]
        pub fn undocumented_two() -> i32 { 2 }
    }

    // 这里缺失的文档会产生警告
    pub fn undocumented_too() -> i32 { 3 }
}
```
{==+==}


{==+==}
This example shows how one can use `forbid` to disallow uses of `allow` for
that lint check:
{==+==}
这个例子展示了如何使用 `forbid` 禁止对某个 lint 检查使用 `allow` :
{==+==}


{==+==}
```rust,compile_fail
#[forbid(missing_docs)]
pub mod m3 {
    // Attempting to toggle warning signals an error here
    #[allow(missing_docs)]
    /// Returns 2.
    pub fn undocumented_too() -> i32 { 2 }
}
```
{==+==}
```rust,compile_fail
#[forbid(missing_docs)]
pub mod m3 {
    // 在这里尝试切换警告会导致错误
    #[allow(missing_docs)]
    /// Returns 2.
    pub fn undocumented_too() -> i32 { 2 }
}
```
{==+==}


{==+==}
> Note: `rustc` allows setting lint levels on the
> [command-line][rustc-lint-cli], and also supports [setting
> caps][rustc-lint-caps] on the lints that are reported.
{==+==}
> 注意：`rustc` 允许在 [命令行][rustc-lint-cli] 上设置 lint 级别，并支持在报告的 lints 上 [设置限制][rustc-lint-caps] 。
{==+==}


{==+==}
### Lint groups

Lints may be organized into named groups so that the level of related lints
can be adjusted together. Using a named group is equivalent to listing out the
lints within that group.
{==+==}
### Lint 组

Lints 可以被组织成具有名称的组，以便可以一起调整相关 lint 的级别。使用命名组相当于列出该组中的 lints。
{==+==}


{==+==}
```rust,compile_fail
// This allows all lints in the "unused" group.
#[allow(unused)]
// This overrides the "unused_must_use" lint from the "unused"
// group to deny.
#[deny(unused_must_use)]
fn example() {
    // This does not generate a warning because the "unused_variables"
    // lint is in the "unused" group.
    let x = 1;
    // This generates an error because the result is unused and
    // "unused_must_use" is marked as "deny".
    std::fs::remove_file("some_file"); // ERROR: unused `Result` that must be used
}
```
{==+==}
```rust,compile_fail
// 这允许所有 "unused" 组中的 lint。
#[allow(unused)]
// 这将 "unused" 组中的 "unused_must_use" lint 改为了 "deny"。
#[deny(unused_must_use)]
fn example() {
    // 这不会生成警告，因为 "unused_variables" lint 在 "unused" 组中。
    let x = 1;
    // 这会生成一个错误，因为结果未使用且 "unused_must_use" 被标记为 "deny"。
    std::fs::remove_file("some_file"); // ERROR: unused `Result` that must be used
}
```
{==+==}


{==+==}
There is a special group named "warnings" which includes all lints at the
"warn" level. The "warnings" group ignores attribute order and applies to all
lints that would otherwise warn within the entity.
{==+==}
有一个特殊的名为 "warnings" 的分组，其中包括所有 "warn" 级别的 lint。
"warnings" 分组忽略属性顺序，并适用于实体中所有会产生警告的 lint 。
{==+==}


{==+==}
```rust,compile_fail
# unsafe fn an_unsafe_fn() {}
// The order of these two attributes does not matter.
#[deny(warnings)]
// The unsafe_code lint is normally "allow" by default.
#[warn(unsafe_code)]
fn example_err() {
    // This is an error because the `unsafe_code` warning has
    // been lifted to "deny".
    unsafe { an_unsafe_fn() } // ERROR: usage of `unsafe` block
}
```
{==+==}
```rust,compile_fail
# unsafe fn an_unsafe_fn() {}
// 这两个属性的顺序无关紧要。
#[deny(warnings)]
// unsafe_code lint 通常默认为 "allow"。
#[warn(unsafe_code)]
fn example_err() {
    // 这是一个错误，因为 `unsafe_code` 警告已经被提升到了 "deny"。
    unsafe { an_unsafe_fn() } // ERROR: usage of `unsafe` block
}
```
{==+==}


{==+==}
### Tool lint attributes

Tool lints allows using scoped lints, to `allow`, `warn`, `deny` or `forbid`
lints of certain tools.

Tool lints only get checked when the associated tool is active. If a lint
attribute, such as `allow`, references a nonexistent tool lint, the compiler
will not warn about the nonexistent lint until you use the tool.

Otherwise, they work just like regular lint attributes:
{==+==}
### 工具 lint 属性

工具 lint 允许使用作用域 lint ，以允许、警告、禁止某些工具的lint。

只有在相关工具处于活动状态时才会检查工 具lint 。
如果像 `allow` 这样的 lint 属性引用了不存在的工具 lint ，则编译器在使用该工具之前不会警告不存在的 lint 。

否则，它们的工作方式与常规 lint 属性相同：
{==+==}


{==+==}
```rust
// set the entire `pedantic` clippy lint group to warn
#![warn(clippy::pedantic)]
// silence warnings from the `filter_map` clippy lint
#![allow(clippy::filter_map)]

fn main() {
    // ...
}

// silence the `cmp_nan` clippy lint just for this function
#[allow(clippy::cmp_nan)]
fn foo() {
    // ...
}
```
{==+==}
```rust
// 将整个 pedantic clippy lint 组设置为警告
#![warn(clippy::pedantic)]
// 屏蔽 filter_map clippy lint 中的警告
#![allow(clippy::filter_map)]

fn main() {
// ...
}

// 仅针对该函数屏蔽 cmp_nan clippy lint 的警告
#[allow(clippy::cmp_nan)]
fn foo() {
// ...
}
```
{==+==}


{==+==}
> Note: `rustc` currently recognizes the tool lints for "[clippy]" and "[rustdoc]".
{==+==}
> 注意: 目前 `rustc` 只支持 "[clippy]" 和 "[rustdoc]" 的工具 lint 。
{==+==}


{==+==}
## The `deprecated` attribute

The *`deprecated` attribute* marks an item as deprecated. `rustc` will issue
warnings on usage of `#[deprecated]` items. `rustdoc` will show item
deprecation, including the `since` version and `note`, if available.

The `deprecated` attribute has several forms:

- `deprecated` — Issues a generic message.
- `deprecated = "message"` — Includes the given string in the deprecation
  message.
- [_MetaListNameValueStr_] syntax with two optional fields:
  - `since` — Specifies a version number when the item was deprecated. `rustc`
    does not currently interpret the string, but external tools like [Clippy]
    may check the validity of the value.
  - `note` — Specifies a string that should be included in the deprecation
    message. This is typically used to provide an explanation about the
    deprecation and preferred alternatives.

The `deprecated` attribute may be applied to any [item], [trait item], [enum
variant], [struct field], [external block item], or [macro definition]. It
cannot be applied to [trait implementation items]. When applied to an item
containing other items, such as a [module] or [implementation], all child
items inherit the deprecation attribute.
{==+==}
## `deprecated` 属性

*`deprecated` 属性* 将一个条目标记为已弃用。`rustc` 在使用被 `#[deprecated]` 标记的条目时会发出警告。
`rustdoc` 将显示条目的弃用信息，包括 `since` 版本和 `note` (如果有) 。

`deprecated` 属性有几种形式:

- `deprecated` — 发出一条通用信息。
- `deprecated = "message"` — 在弃用消息中包含给定的字符串。
- [_MetaListNameValueStr_] 语法有两个可选字段:
  - `since` — 指定条目被弃用的版本号。 `rustc` 目前不解释该字符串，但像 [Clippy] 这样的外部工具可能会检查值的有效性。
  - `note` — 指定应在弃用消息中包含的字符串。通常用于提供有关弃用和首选替代方法的解释。

`deprecated` 属性可应用于任何 [item] 、 [trait item] 、 [enum variant] 、 [struct field] 、 [external block item] 或 [macro definition]。
它不能应用于 [trait implementation items]。当应用于包含其他条目的条目 (例如 [module] 或 [implementation] )时，所有子条目都继承弃用属性。
{==+==}


{==+==}
<!-- NOTE: It is only rejected for trait impl items
(AnnotationKind::Prohibited). In all other locations, it is silently ignored.
Tuple struct fields are ignored.
-->
{==+==}

{==+==}


{==+==}
Here is an example:
{==+==}
以下一个例子:
{==+==}


{==+==}
```rust
#[deprecated(since = "5.2.0", note = "foo was rarely used. Users should instead use bar")]
pub fn foo() {}

pub fn bar() {}
```
{==+==}

{==+==}


{==+==}
The [RFC][1270-deprecation.md] contains motivations and more details.

[1270-deprecation.md]: https://github.com/rust-lang/rfcs/blob/master/text/1270-deprecation.md
{==+==}
[RFC][1270-deprecation.md] 包含了动机和更多细节。

[1270-deprecation.md]: https://github.com/rust-lang/rfcs/blob/master/text/1270-deprecation.md
{==+==}


{==+==}
## The `must_use` attribute

The *`must_use` attribute* is used to issue a diagnostic warning when a value
is not "used". It can be applied to user-defined composite types
([`struct`s][struct], [`enum`s][enum], and [`union`s][union]), [functions],
and [traits].

The `must_use` attribute may include a message by using the
[_MetaNameValueStr_] syntax such as `#[must_use = "example message"]`. The
message will be given alongside the warning.

When used on user-defined composite types, if the [expression] of an
[expression statement] has that type, then the `unused_must_use` lint is
violated.
{==+==}
## `must_use` 属性

*`must_use` 属性* 用于在值未被 "使用" 时发出诊断警告。它可以应用于用户定义的复合类型 ( [`struct`] 、 [`enum`] 和 [`union`] ) 、 [`functions`] 和 [`traits`] 。

`must_use` 属性可以通过使用 [_元名称值字符串_][_MetaNameValueStr_] 语法 (例如 `#[must_use = "example message"]` ) 包含一条消息。该消息将与警告一起给出。

当在用户定义的复合类型上使用时，如果 [expression] 的 [expression statement] 具有该类型，则违反了 `unused_must_use` lint。
{==+==}


{==+==}
```rust
#[must_use]
struct MustUse {
    // some fields
}

# impl MustUse {
#   fn new() -> MustUse { MustUse {} }
# }
#
// Violates the `unused_must_use` lint.
MustUse::new();
```
{==+==}
```rust
#[must_use]
struct MustUse {
    // 一些字段
}

# impl MustUse {
#   fn new() -> MustUse { MustUse {} }
# }
#
// 违反了 `unused_must_use` lint.
MustUse::new();
```
{==+==}


{==+==}
When used on a function, if the [expression] of an [expression statement] is a
[call expression] to that function, then the `unused_must_use` lint is
violated.
{==+==}
当应用于函数时，如果 [表达式][expression] 是对该函数的 [调用表达式][call expression]，那么将违反 `unused_must_use` 警告。
{==+==}


{==+==}
```rust
#[must_use]
fn five() -> i32 { 5i32 }

// Violates the unused_must_use lint.
five();
```
{==+==}
```rust
#[must_use]
fn five() -> i32 { 5i32 }

// 违反了 `unused_must_use` lint.
five();
```
{==+==}


{==+==}
When used on a [trait declaration], a [call expression] of an [expression
statement] to a function that returns an [impl trait] or a [dyn trait] of that trait violates
the `unused_must_use` lint.
{==+==}
当在 [trait 声明][trait declaration] 上使用时，对该 trait 的 [impl trait] 或 [dyn trait] 返回的函数的 [call expression] 的 [expression statement] 违反了 `unused_must_use` lint。
{==+==}


{==+==}
```rust
#[must_use]
trait Critical {}
impl Critical for i32 {}

fn get_critical() -> impl Critical {
    4i32
}

// Violates the `unused_must_use` lint.
get_critical();
```
{==+==}
```rust
#[must_use]
trait Critical {}
impl Critical for i32 {}

fn get_critical() -> impl Critical {
    4i32
}

// 违反了 `unused_must_use` lint.
get_critical();
```
{==+==}


{==+==}
When used on a function in a trait declaration, then the behavior also applies
when the call expression is a function from an implementation of the trait.
{==+==}
当在 trait 声明中的函数上使用时，当调用表达式是 trait 实现的函数时，该行为也适用。
{==+==}


{==+==}
```rust
trait Trait {
    #[must_use]
    fn use_me(&self) -> i32;
}

impl Trait for i32 {
    fn use_me(&self) -> i32 { 0i32 }
}

// Violates the `unused_must_use` lint.
5i32.use_me();
```
{==+==}
```rust
trait Trait {
    #[must_use]
    fn use_me(&self) -> i32;
}

impl Trait for i32 {
    fn use_me(&self) -> i32 { 0i32 }
}

// 违反了 `unused_must_use` lint.
5i32.use_me();
```
{==+==}


{==+==}
When used on a function in a trait implementation, the attribute does nothing.

> Note: Trivial no-op expressions containing the value will not violate the
> lint. Examples include wrapping the value in a type that does not implement
> [`Drop`] and then not using that type and being the final expression of a
> [block expression] that is not used.
>
> ```rust
> #[must_use]
> fn five() -> i32 { 5i32 }
>
> // None of these violate the unused_must_use lint.
> (five(),);
> Some(five());
> { five() };
> if true { five() } else { 0i32 };
> match true {
>     _ => five()
> };
> ```

> Note: It is idiomatic to use a [let statement] with a pattern of `_`
> when a must-used value is purposely discarded.
>
> ```rust
> #[must_use]
> fn five() -> i32 { 5i32 }
>
> // Does not violate the unused_must_use lint.
> let _ = five();
> ```
{==+==}
当用于 trait 实现中的函数时，该属性无效。

> 注意：包含该值的无关紧要的无操作表达式不会违反 lint 。
> 例如，将该值包装在未实现 [`Drop`] 的类型中，然后不使用该类型，或者是块表达式的最终表达式，而不使用该表达式。

> ```rust
> #[must_use]
> fn five() -> i32 { 5i32 }
>
> // 这些都不违反 unused_must_use lint 。
> (five(),);
> Some(five());
> { five() };
> if true { five() } else { 0i32 };
> match true {
>     _ => five()
> };
> ```

> 注意：当有一个有意舍弃的必须使用的值时，使用带有 `_` 模式的 let 语句是惯用的写法。
> 
> ```rust
> #[must_use]
> fn five() -> i32 { 5i32 }
>
> // 不违反 unused_must_use lint 。
> let _ = five();
> ```
{==+==}


{==+==}
[Clippy]: https://github.com/rust-lang/rust-clippy
[_MetaListNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[_MetaListPaths_]: ../attributes.md#meta-item-attribute-syntax
[_MetaNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[`Drop`]: ../special-types-and-traits.md#drop
[attributes]: ../attributes.md
[block expression]: ../expressions/block-expr.md
[call expression]: ../expressions/call-expr.md
[dyn trait]: ../types/trait-object.md
[enum variant]: ../items/enumerations.md
[enum]: ../items/enumerations.md
[expression statement]: ../statements.md#expression-statements
[expression]: ../expressions.md
[external block item]: ../items/external-blocks.md
[functions]: ../items/functions.md
[impl trait]: ../types/impl-trait.md
[implementation]: ../items/implementations.md
[item]: ../items.md
[let statement]: ../statements.md#let-statements
[macro definition]: ../macros-by-example.md
[module]: ../items/modules.md
[rustc book]: ../../rustc/lints/index.html
[rustc-lint-caps]: ../../rustc/lints/levels.html#capping-lints
[rustc-lint-cli]: ../../rustc/lints/levels.html#via-compiler-flag
[rustdoc]: ../../rustdoc/lints.html
[struct field]: ../items/structs.md
[struct]: ../items/structs.md
[trait declaration]: ../items/traits.md
[trait implementation items]: ../items/implementations.md#trait-implementations
[trait item]: ../items/traits.md
[traits]: ../items/traits.md
[union]: ../items/unions.md
{==+==}

{==+==}