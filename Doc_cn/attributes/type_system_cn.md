{==+==}
# Type system attributes

The following [attributes] are used for changing how a type can be used.

## The `non_exhaustive` attribute

The *`non_exhaustive` attribute* indicates that a type or variant may have
more fields or variants added in the future. It can be applied to
[`struct`s][struct], [`enum`s][enum], and `enum` variants.

The `non_exhaustive` attribute uses the [_MetaWord_] syntax and thus does not
take any inputs.

Within the defining crate, `non_exhaustive` has no effect.
{==+==}
# 类型系统属性

以下属性用于更改类型的使用方式。

## `non_exhaustive` 属性

*`non_exhaustive` 属性* 表示类型或变体将来可能会添加更多的字段或变体。
它可以应用于 [`struct`] 、 [`enum`] 和 `enum` 变体。

`non_exhaustive` 属性使用 [_元字_][_MetaWord_] 语法，因此不需要输入任何内容。

在定义的 crate 中，`non_exhaustive` 没有任何作用。
{==+==}


{==+==}
```rust
#[non_exhaustive]
pub struct Config {
    pub window_width: u16,
    pub window_height: u16,
}

#[non_exhaustive]
pub enum Error {
    Message(String),
    Other,
}

pub enum Message {
    #[non_exhaustive] Send { from: u32, to: u32, contents: String },
    #[non_exhaustive] Reaction(u32),
    #[non_exhaustive] Quit,
}

// Non-exhaustive structs can be constructed as normal within the defining crate.
let config = Config { window_width: 640, window_height: 480 };

// Non-exhaustive structs can be matched on exhaustively within the defining crate.
if let Config { window_width, window_height } = config {
    // ...
}

let error = Error::Other;
let message = Message::Reaction(3);

// Non-exhaustive enums can be matched on exhaustively within the defining crate.
match error {
    Error::Message(ref s) => { },
    Error::Other => { },
}

match message {
    // Non-exhaustive variants can be matched on exhaustively within the defining crate.
    Message::Send { from, to, contents } => { },
    Message::Reaction(id) => { },
    Message::Quit => { },
}
```
{==+==}
```rust
#[non_exhaustive]
pub struct Config {
    pub window_width: u16,
    pub window_height: u16,
}

#[non_exhaustive]
pub enum Error {
    Message(String),
    Other,
}

pub enum Message {
    #[non_exhaustive] Send { from: u32, to: u32, contents: String },
    #[non_exhaustive] Reaction(u32),
    #[non_exhaustive] Quit,
}

// 非穷尽结构体可以在定义的 crate 中像普通结构体一样构造。
let config = Config { window_width: 640, window_height: 480 };

// 非穷尽结构体可以在定义的 crate 中进行完全匹配。
if let Config { window_width, window_height } = config {
    // ...
}

let error = Error::Other;
let message = Message::Reaction(3);

// 非穷尽枚举可以在定义的 crate 中进行完全匹配。
match error {
    Error::Message(ref s) => { },
    Error::Other => { },
}

match message {
    // 非穷尽变体可以在定义的 crate 中进行完全匹配。
    Message::Send { from, to, contents } => { },
    Message::Reaction(id) => { },
    Message::Quit => { },
}
```
{==+==}


{==+==}
Outside of the defining crate, types annotated with `non_exhaustive` have limitations that
preserve backwards compatibility when new fields or variants are added.

Non-exhaustive types cannot be constructed outside of the defining crate:

- Non-exhaustive variants ([`struct`][struct] or [`enum` variant][enum]) cannot be constructed
  with a [_StructExpression_] \(including with [functional update syntax]).
- [`enum`][enum] instances can be constructed.
{==+==}
在定义之外，使用 `non_exhaustive` 注解的类型有限制，以保持向后兼容性，当添加新字段或变量时。

不能在定义之外构造非穷尽类型:

- 不能使用 [_结构体表达式_][_StructExpression_] (包括 [函数更新语法][functional update syntax]) 构造非穷尽变体 ( [`struct`][struct] 或 [`enum` variant][enum] ) 。
- 可以构造 [`enum`] 实例。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
// `Config`, `Error`, and `Message` are types defined in an upstream crate that have been
// annotated as `#[non_exhaustive]`.
use upstream::{Config, Error, Message};

// Cannot construct an instance of `Config`, if new fields were added in
// a new version of `upstream` then this would fail to compile, so it is
// disallowed.
let config = Config { window_width: 640, window_height: 480 };

// Can construct an instance of `Error`, new variants being introduced would
// not result in this failing to compile.
let error = Error::Message("foo".to_string());

// Cannot construct an instance of `Message::Send` or `Message::Reaction`,
// if new fields were added in a new version of `upstream` then this would
// fail to compile, so it is disallowed.
let message = Message::Send { from: 0, to: 1, contents: "foo".to_string(), };
let message = Message::Reaction(0);

// Cannot construct an instance of `Message::Quit`, if this were converted to
// a tuple-variant `upstream` then this would fail to compile.
let message = Message::Quit;
```
{==+==}
<!-- ignore: requires external crates -->
```rust,ignore
// `Config`，`Error` 和 `Message` 是在上游 crate 中定义并使用了 `#[non_exhaustive]` 标记的类型。
use upstream::{Config, Error, Message};

// 无法构造 `Config` 的实例，如果在新版本中添加了新的字段，则会导致编译失败，因此不允许。
let config = Config { window_width: 640, window_height: 480 };

// 可以构造 `Error` 的实例，如果新的变量被引入，也不会导致编译失败。
let error = Error::Message("foo".to_string());

// 无法构造 `Message::Send` 或 `Message::Reaction` 的实例，如果在新版本中添加了新的字段，则会导致编译失败，因此不允许。
let message = Message::Send { from: 0, to: 1, contents: "foo".to_string(), };
let message = Message::Reaction(0);

// 无法构造 `Message::Quit` 的实例，如果将其转换为元组变量 `upstream`，则会导致编译失败。
let message = Message::Quit;
```
{==+==}


{==+==}
There are limitations when matching on non-exhaustive types outside of the defining crate:

- When pattern matching on a non-exhaustive variant ([`struct`][struct] or [`enum` variant][enum]),
  a [_StructPattern_] must be used which must include a `..`. Tuple variant constructor visibility
  is lowered to `min($vis, pub(crate))`.
- When pattern matching on a non-exhaustive [`enum`][enum], matching on a variant does not
  contribute towards the exhaustiveness of the arms.
{==+==}
在定义类型的 crate 之外，在匹配非穷尽类型时存在以下限制:

- 当匹配非穷尽变体 ([`struct`][struct] 或 [`enum` variant][enum]) 时，必须使用带有 `..` 的 [_结构体模式_][_StructPattern_] ，元组变体构造函数的可见性降为 `min($vis, pub(crate))` 。
- 当匹配非穷尽 [`enum`][enum] 时，匹配一个变体不会导致分支的穷尽性。
{==+==}


{==+==}
<!-- ignore: requires external crates -->
```rust, ignore
// `Config`, `Error`, and `Message` are types defined in an upstream crate that have been
// annotated as `#[non_exhaustive]`.
use upstream::{Config, Error, Message};

// Cannot match on a non-exhaustive enum without including a wildcard arm.
match error {
  Error::Message(ref s) => { },
  Error::Other => { },
  // would compile with: `_ => {},`
}

// Cannot match on a non-exhaustive struct without a wildcard.
if let Ok(Config { window_width, window_height }) = config {
    // would compile with: `..`
}

match message {
  // Cannot match on a non-exhaustive struct enum variant without including a wildcard.
  Message::Send { from, to, contents } => { },
  // Cannot match on a non-exhaustive tuple or unit enum variant.
  Message::Reaction(type) => { },
  Message::Quit => { },
}
```
{==+==}
<!-- ignore: requires external crates -->
```rust, ignore
// `Config` ， `Error` 和 `Message` 是在上游 crate 中定义的类型，已经被注释为 `#[non_exhaustive]`。
use upstream::{Config, Error, Message};

// 无法在非枚举穷尽(enum non-exhaustive)上进行匹配而不包括通配符分支。
match error {
Error::Message(ref s) => {},
Error::Other => {},
// 可以编译: `_ => {}，`
}

// 无法在非穷尽的结构体上进行匹配，除非使用通配符。
if let Ok(Config { window_width, window_height }) = config {
    // 可以编译: `..`
}

match message {
  // 无法在非穷尽的结构体上进行匹配，除非使用通配符。
  Message::Send { from, to, contents } => { },
  // 无法匹配非穷尽的元组或单元枚举变体。
  Message::Reaction(type) => { },
  Message::Quit => { },
}
```
{==+==}


{==+==}
It's also not allowed to cast non-exhaustive types from foreign crates.
```rust, ignore
use othercrate::NonExhaustiveEnum;

// Cannot cast a non-exhaustive enum outside of its defining crate.
let _ = NonExhaustiveEnum::default() as u8;
```
{==+==}
在外部的 crate 中，也不允许将非穷尽类型进行强制类型转换。
```rust, ignore
use othercrate::NonExhaustiveEnum;

// 无法在其定义 crate 之外将非穷尽枚举转换为其他类型。
let _ = NonExhaustiveEnum::default() as u8;
```
{==+==}


{==+==}
Non-exhaustive types are always considered inhabited in downstream crates.
{==+==}
非穷尽类型在下游的 crate 中总是被视为是有 inhabitant(指代值或实例) 。
{==+==}


{==+==}
[_MetaWord_]: ../attributes.md#meta-item-attribute-syntax
[_StructExpression_]: ../expressions/struct-expr.md
[_StructPattern_]: ../patterns.md#struct-patterns
[_TupleStructPattern_]: ../patterns.md#tuple-struct-patterns
[`if let`]: ../expressions/if-expr.md#if-let-expressions
[`match`]: ../expressions/match-expr.md
[attributes]: ../attributes.md
[enum]: ../items/enumerations.md
[functional update syntax]: ../expressions/struct-expr.md#functional-update-syntax
[struct]: ../items/structs.md
{==+==}

{==+==}