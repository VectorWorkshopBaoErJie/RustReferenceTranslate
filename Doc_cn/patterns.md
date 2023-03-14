{==+==}
# Patterns
{==+==}
# 模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Pattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `|`<sup>?</sup> _PatternNoTopAlt_  ( `|` _PatternNoTopAlt_ )<sup>\*</sup>
>
> _PatternNoTopAlt_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _PatternWithoutRange_\
> &nbsp;&nbsp; | [_RangePattern_]
>
> _PatternWithoutRange_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_LiteralPattern_]\
> &nbsp;&nbsp; | [_IdentifierPattern_]\
> &nbsp;&nbsp; | [_WildcardPattern_]\
> &nbsp;&nbsp; | [_RestPattern_]\
> &nbsp;&nbsp; | [_ReferencePattern_]\
> &nbsp;&nbsp; | [_StructPattern_]\
> &nbsp;&nbsp; | [_TupleStructPattern_]\
> &nbsp;&nbsp; | [_TuplePattern_]\
> &nbsp;&nbsp; | [_GroupedPattern_]\
> &nbsp;&nbsp; | [_SlicePattern_]\
> &nbsp;&nbsp; | [_PathPattern_]\
> &nbsp;&nbsp; | [_MacroInvocation_]
{==+==}

{==+==}


{==+==}
Patterns are used to match values against structures and to, optionally, bind variables to values inside these structures.
They are also used in variable declarations and parameters for functions and closures.

The pattern in the following example does four things:

* Tests if `person` has the `car` field filled with something.
* Tests if the person's `age` field is between 13 and 19, and binds its value to the `person_age` variable.
* Binds a reference to the `name` field to the variable `person_name`.
* Ignores the rest of the fields of `person`.
  The remaining fields can have any value and are not bound to any variables.
{==+==}
模式用于将值与结构进行匹配，并在这些结构内将变量绑定到值（可选）。
它们还用于函数和闭包的变量声明和参数。以下示例中的模式执行四个操作：

* 检查 `person` 是否有填充了某些内容的 `car` 字段。
* 检查 `person` 的 `age` 字段是否在 13 到 19 之间，并将其值绑定到 `person_age` 变量。
* 将 `name` 字段的引用绑定到变量 `person_name` 。
* 忽略 `person` 的其余字段。剩余字段可以具有任何值，并且不会绑定到任何变量。
{==+==}


{==+==}
```rust
# struct Car;
# struct Computer;
# struct Person {
#     name: String,
#     car: Option<Car>,
#     computer: Option<Computer>,
#     age: u8,
# }
# let person = Person {
#     name: String::from("John"),
#     car: Some(Car),
#     computer: None,
#     age: 15,
# };
if let
    Person {
        car: Some(_),
        age: person_age @ 13..=19,
        name: ref person_name,
        ..
    } = person
{
    println!("{} has a car and is {} years old.", person_name, person_age);
}
```
{==+==}

{==+==}


{==+==}
Patterns are used in:

* [`let` declarations](statements.md#let-statements)
* [Function](items/functions.md) and [closure](expressions/closure-expr.md) parameters
* [`match` expressions](expressions/match-expr.md)
* [`if let` expressions](expressions/if-expr.md)
* [`while let` expressions](expressions/loop-expr.md#predicate-pattern-loops)
* [`for` expressions](expressions/loop-expr.md#iterator-loops)
{==+==}
模式用于:

* [`let` declarations](statements.md#let-statements)
* [Function](items/functions.md) and [closure](expressions/closure-expr.md) parameters
* [`match` expressions](expressions/match-expr.md)
* [`if let` expressions](expressions/if-expr.md)
* [`while let` expressions](expressions/loop-expr.md#predicate-pattern-loops)
* [`for` expressions](expressions/loop-expr.md#iterator-loops)
{==+==}


{==+==}
## Destructuring

Patterns can be used to *destructure* [structs], [enums], and [tuples].
Destructuring breaks up a value into its component pieces.
The syntax used is almost the same as when creating such values.
In a pattern whose [scrutinee] expression has a `struct`, `enum` or `tuple` type, a placeholder (`_`) stands in for a *single* data field, whereas a wildcard `..`  stands in for *all* the remaining fields of a particular variant.
When destructuring a data structure with named (but not numbered) fields, it is allowed to write `fieldname` as a shorthand for `fieldname: fieldname`.
{==+==}
## 解构

解构模式可以用于解构 [structs] 、 [enums] 和 [tuples] 。解构将一个值分解成其组成部分。
使用的语法与创建这些值时几乎相同。在一个模式中，其 [scrutinee] 表达式具有 `struct`、`enum` 或 `tuple` 类型，占位符 (`_`) 代表 *单个* 数据字段，而通配符 `..` 代表 *特定变体的所有* 其余字段。
当解构具有命名字段 (但未编号) 的数据结构时，允许将 `fieldname` 写成 `fieldname: fieldname` 的简写形式。
在 Rust 中，解构是一种方便的工具，可用于从结构化数据类型中提取所需数据。
{==+==}


{==+==}
```rust
# enum Message {
#     Quit,
#     WriteString(String),
#     Move { x: i32, y: i32 },
#     ChangeColor(u8, u8, u8),
# }
# let message = Message::Quit;
match message {
    Message::Quit => println!("Quit"),
    Message::WriteString(write) => println!("{}", &write),
    Message::Move{ x, y: 0 } => println!("move {} horizontally", x),
    Message::Move{ .. } => println!("other move"),
    Message::ChangeColor { 0: red, 1: green, 2: _ } => {
        println!("color change, red: {}, green: {}", red, green);
    }
};
```
{==+==}

{==+==}


{==+==}
## Refutability

A pattern is said to be *refutable* when it has the possibility of not being matched by the value it is being matched against.
*Irrefutable* patterns, on the other hand, always match the value they are being matched against.
Examples:
{==+==}
## 可反驳性

当模式可能无法匹配其所匹配的值时，该模式被称为 *可反驳的* 。相反， *不可反驳* 模式总是与其所匹配的值匹配。例如：
{==+==}


{==+==}
```rust
let (x, y) = (1, 2);               // "(x, y)" is an irrefutable pattern

if let (a, 3) = (1, 2) {           // "(a, 3)" is refutable, and will not match
    panic!("Shouldn't reach here");
} else if let (a, 4) = (3, 4) {    // "(a, 4)" is refutable, and will match
    println!("Matched ({}, 4)", a);
}
```
{==+==}
```rust
let (x, y) = (1, 2);               // "(x, y)" 是一个不可反驳的模式

if let (a, 3) = (1, 2) {           // "(a, 3)" 是可反驳的，将不匹配
    panic!("不应该到达这里");
} else if let (a, 4) = (3, 4) {    // "(a, 4)" 是可反驳的，并将匹配
    println!("匹配到 ({}, 4)", a);
}
```
{==+==}


{==+==}
## Literal patterns
{==+==}
## 字面值模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _LiteralPattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `true` | `false`\
> &nbsp;&nbsp; | [CHAR_LITERAL]\
> &nbsp;&nbsp; | [BYTE_LITERAL]\
> &nbsp;&nbsp; | [STRING_LITERAL]\
> &nbsp;&nbsp; | [RAW_STRING_LITERAL]\
> &nbsp;&nbsp; | [BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | [RAW_BYTE_STRING_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [INTEGER_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [FLOAT_LITERAL]
{==+==}

{==+==}


{==+==}
[CHAR_LITERAL]: tokens.md#character-literals
[BYTE_LITERAL]: tokens.md#byte-literals
[STRING_LITERAL]: tokens.md#string-literals
[RAW_STRING_LITERAL]: tokens.md#raw-string-literals
[BYTE_STRING_LITERAL]: tokens.md#byte-string-literals
[RAW_BYTE_STRING_LITERAL]: tokens.md#raw-byte-string-literals
[INTEGER_LITERAL]: tokens.md#integer-literals
[FLOAT_LITERAL]: tokens.md#floating-point-literals
{==+==}

{==+==}


{==+==}
_Literal patterns_ match exactly the same value as what is created by the literal.
Since negative numbers are not [literals], literal patterns also accept an optional minus sign before the literal, which acts like the negation operator.

<div class="warning">

Floating-point literals are currently accepted, but due to the complexity of comparing them, they are going to be forbidden on literal patterns in a future version of Rust (see [issue #41620](https://github.com/rust-lang/rust/issues/41620)).

</div>
{==+==}
_字面值模式_ 正好匹配与字面值创建的值相同的值。
由于负数不是字面值，字面值模式也接受字面值前面的可选负号，其作用类似于否定运算符。

<div class="warning">

目前接受浮点型字面值，但由于比较复杂，未来的 Rust 版本中将禁止在字面值模式中使用它们 (参见[问题 #41620](https://github.com/rust-lang/rust/issues/41620))。

</div>
{==+==}


{==+==}
Literal patterns are always refutable.

Examples:

```rust
for i in -2..5 {
    match i {
        -1 => println!("It's minus one"),
        1 => println!("It's a one"),
        2|4 => println!("It's either a two or a four"),
        _ => println!("Matched none of the arms"),
    }
}
```
{==+==}
字面值模式总是可反驳的。

例如:

```rust
for i in -2..5 {
    match i {
        -1 => println!("It's minus one"),
        1 => println!("It's a one"),
        2|4 => println!("It's either a two or a four"),
        _ => println!("Matched none of the arms"),
    }
}
```
{==+==}


{==+==}
## Identifier patterns
{==+==}
## 标识符模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _IdentifierPattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `ref`<sup>?</sup> `mut`<sup>?</sup> [IDENTIFIER] (`@` [_PatternNoTopAlt_] ) <sup>?</sup>
{==+==}

{==+==}


{==+==}
Identifier patterns bind the value they match to a variable.
The identifier must be unique within the pattern.
The variable will shadow any variables of the same name in scope.
The scope of the new binding depends on the context of where the pattern is used (such as a `let` binding or a `match` arm).

Patterns that consist of only an identifier, possibly with a `mut`, match any value and bind it to that identifier.
This is the most commonly used pattern in variable declarations and parameters for functions and closures.
{==+==}
标识符模式将其匹配的值绑定到变量上。
标识符在模式中必须是唯一的。
该变量将隐藏作用域中具有相同名称的任何变量。
新绑定的作用域取决于模式用于何处的上下文 (例如 `let` 绑定或 `match` 分支) 。

仅由标识符组成的模式 (可能带有 `mut` ) 会匹配任何值并将其绑定到该标识符上。这是变量声明和函数及闭包参数中最常用的模式。
{==+==}


{==+==}
```rust
let mut variable = 10;
fn sum(x: i32, y: i32) -> i32 {
#    x + y
# }
```
{==+==}

{==+==}


{==+==}
To bind the matched value of a pattern to a variable, use the syntax `variable @ subpattern`.
For example, the following binds the value 2 to `e` (not the entire range: the range here is a range subpattern).
{==+==}
要将模式的匹配值绑定到变量上，请使用语法 `variable @ subpattern` 。
例如，以下代码将值 2 绑定到 `e` (而不是整个范围：这里的范围是一个范围子模式) 。
{==+==}


{==+==}
```rust
let x = 2;

match x {
    e @ 1 ..= 5 => println!("got a range element {}", e),
    _ => println!("anything"),
}
```
{==+==}

{==+==}


{==+==}
By default, identifier patterns bind a variable to a copy of or move from the matched value depending on whether the matched value implements [`Copy`].
This can be changed to bind to a reference by using the `ref` keyword, or to a mutable reference using `ref mut`. For example:
{==+==}
默认情况下，标识符模式将根据匹配的值是否实现了 [`Copy`] ，绑定一个变量到匹配值的复制或移动。
可以使用 `ref` 关键字将其更改为绑定到一个引用，或使用 `ref mut` 更改为可变引用。例如:
{==+==}


{==+==}
```rust
# let a = Some(10);
match a {
    None => (),
    Some(value) => (),
}

match a {
    None => (),
    Some(ref value) => (),
}
```
{==+==}

{==+==}


{==+==}
In the first match expression, the value is copied (or moved).
In the second match, a reference to the same memory location is bound to the variable value.
This syntax is needed because in destructuring subpatterns the `&` operator can't be applied to the value's fields.
For example, the following is not valid:
{==+==}
在第一个 match 表达式中，该值被复制 (或移动) 。在第二个 match 中，对同一内存位置的引用被绑定到变量值。
这种语法是必要的，因为在解构子模式中， `&` 操作符不能应用于值的字段。
例如，以下是无效的:
{==+==}


{==+==}
```rust,compile_fail
# struct Person {
#    name: String,
#    age: u8,
# }
# let value = Person { name: String::from("John"), age: 23 };
if let Person { name: &person_name, age: 18..=150 } = value { }
```
{==+==}

{==+==}


{==+==}
To make it valid, write the following:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let value = Person { name: String::from("John"), age: 23 };
if let Person {name: ref person_name, age: 18..=150 } = value { }
```
{==+==}
要使其有效，请写如下代码:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let value = Person { name: String::from("John"), age: 23 };
if let Person {name: ref person_name, age: 18..=150 } = value { }
```
{==+==}


{==+==}
Thus, `ref` is not something that is being matched against.
Its objective is exclusively to make the matched binding a reference, instead of potentially copying or moving what was matched.

[Path patterns](#path-patterns) take precedence over identifier patterns.
It is an error if `ref` or `ref mut` is specified and the identifier shadows a constant.

Identifier patterns are irrefutable if the `@` subpattern is irrefutable or the subpattern is not specified.
{==+==}
因此， `ref` 不是被匹配的内容。
它的目的仅仅是将匹配的绑定作为引用而不是复制或移动匹配的内容。

[路径模式](#path-patterns) 优先于标识符模式。如果指定了 `ref` 或 `ref mut` ，并且标识符遮蔽了一个常量，则会出现错误。

如果子模式是不可反驳的或未指定子模式，则标识符模式是不可反驳的。
{==+==}


{==+==}
### Binding modes

To service better ergonomics, patterns operate in different *binding modes* in order to make it easier to bind references to values.
When a reference value is matched by a non-reference pattern, it will be automatically treated as a `ref` or `ref mut` binding.
Example:
{==+==}
### 绑定模式

为了提供更好的人机交互，模式运行在不同的 *绑定模式* 中，以便更容易地将引用绑定到值。
当引用值被非引用模式匹配时，它会自动被视为 `ref` 或 `ref mut` 绑定。
例如：
{==+==}


{==+==}
```rust
let x: &Option<i32> = &Some(3);
if let Some(y) = x {
    // y was converted to `ref y` and its type is &i32
}
```
{==+==}
```rust
let x: &Option<i32> = &Some(3);    // 创建一个类型为 &Option<i32> 的引用 x，指向 Some(3)
if let Some(y) = x {               // 对 x 进行模式匹配
    // y 被转换成了 `ref y`，其类型为 &i32
}
```
{==+==}


{==+==}
*Non-reference patterns* include all patterns except bindings, [wildcard patterns](#wildcard-pattern) (`_`), [`const` patterns](#path-patterns) of reference types, and [reference patterns](#reference-patterns).

If a binding pattern does not explicitly have `ref`, `ref mut`, or `mut`, then it uses the *default binding mode* to determine how the variable is bound.
The default binding mode starts in "move" mode which uses move semantics.
When matching a pattern, the compiler starts from the outside of the pattern and works inwards.
Each time a reference is matched using a non-reference pattern, it will automatically dereference the value and update the default binding mode.
References will set the default binding mode to `ref`.
Mutable references will set the mode to `ref mut` unless the mode is already `ref` in which case it remains `ref`.
If the automatically dereferenced value is still a reference, it is dereferenced and this process repeats.

Move bindings and reference bindings can be mixed together in the same pattern.
Doing so will result in partial move of the object bound to and the object cannot be used afterwards.
This applies only if the type cannot be copied.

In the example below, `name` is moved out of `person`.
Trying to use `person` as a whole or `person.name` would result in an error because of *partial move*.
{==+==}
*非引用模式* 包括除绑定、 [通配符模式](#wildcard-pattern) (`_`)、引用类型的 [`const`模式](#path-patterns) 和 [引用模式](#reference-patterns) 之外的所有模式。

如果一个绑定模式没有明确指定 `ref` 、 `ref mut` 或 `mut` ，则会使用 *默认绑定模式* 来确定变量如何被绑定。
默认绑定模式开始于 "移动" 模式，使用移动语义。
匹配模式时，编译器从外向内开始。
每次使用非引用模式匹配引用时，它将自动解引用该值并更新默认绑定模式。
引用将默认绑定模式设置为 `ref` 。
可变引用将模式设置为 `ref mut` ，除非模式已经是 `ref` ，否则它将保持为 `ref` 。
如果自动解引用的值仍然是引用，则解引用该值并重复此过程。

移动绑定和引用绑定可以混合在同一个模式中。
这样做将导致对象的部分移动绑定，并且之后无法再使用该对象。
如果类型不能复制，则适用此规则。

在下面的示例中， `name` 从 `person` 中移动出来。试图将 `person` 作为整体或 `person.name` 使用会导致错误，因为存在 *部分移动* 。
{==+==}


{==+==}
Example:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let person = Person{ name: String::from("John"), age: 23 };
// `name` is moved from person and `age` referenced
let Person { name, ref age } = person;
```
{==+==}
例如:

```rust
# struct Person {
#    name: String,
#    age: u8,
# }
# let person = Person{ name: String::from("John"), age: 23 };
// `name` 从 `person` 中被移动了，而 `age` 则被引用。
let Person { name, ref age } = person;
```
{==+==}


{==+==}
## Wildcard pattern
{==+==}
## 通配符模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _WildcardPattern_ :\
> &nbsp;&nbsp; `_`
{==+==}

{==+==}


{==+==}
The _wildcard pattern_ (an underscore symbol) matches any value.
It is used to ignore values when they don't matter.
Inside other patterns it matches a single data field (as opposed to the `..` which matches the remaining fields).
Unlike identifier patterns, it does not copy, move or borrow the value it matches.
{==+==}
_通配符模式_ (下划线符号) 匹配任何值。它用于在不重要时忽略值。
在其他模式内部，它匹配单个数据字段 (与 `..` 匹配剩余字段不同)。
与标识符模式不同，它不会复制、移动或借用它所匹配的值。
{==+==}


{==+==}
Examples:

```rust
# let x = 20;
let (a, _) = (10, x);   // the x is always matched by _
# assert_eq!(a, 10);

// ignore a function/closure param
let real_part = |a: f64, _: f64| { a };

// ignore a field from a struct
# struct RGBA {
#    r: f32,
#    g: f32,
#    b: f32,
#    a: f32,
# }
# let color = RGBA{r: 0.4, g: 0.1, b: 0.9, a: 0.5};
let RGBA{r: red, g: green, b: blue, a: _} = color;
# assert_eq!(color.r, red);
# assert_eq!(color.g, green);
# assert_eq!(color.b, blue);

// accept any Some, with any value
# let x = Some(10);
if let Some(_) = x {}
```
{==+==}
例如:

```rust
# let x = 20;
let (a, _) = (10, x);   // x总是被 _ 匹配
# assert_eq!(a, 10);

// 忽略函数/闭包的参数
let real_part = |a: f64, _: f64| { a };

// 忽略函数/闭包的参数
# struct RGBA {
#    r: f32,
#    g: f32,
#    b: f32,
#    a: f32,
# }
# let color = RGBA{r: 0.4, g: 0.1, b: 0.9, a: 0.5};
let RGBA{r: red, g: green, b: blue, a: _} = color;
# assert_eq!(color.r, red);
# assert_eq!(color.g, green);
# assert_eq!(color.b, blue);

// 接受任何 Some，与任何值
# let x = Some(10);
if let Some(_) = x {}
```
{==+==}


{==+==}
The wildcard pattern is always irrefutable.
{==+==}
通配符模式始终是不可反驳的 (irrefutable) 。
{==+==}


{==+==}
## Rest patterns
{==+==}
## 剩余模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _RestPattern_ :\
> &nbsp;&nbsp; `..`
{==+==}

{==+==}


{==+==}
The _rest pattern_ (the `..` token) acts as a variable-length pattern which matches zero or more elements that haven't been matched already before and after.
It may only be used in [tuple](#tuple-patterns), [tuple struct](#tuple-struct-patterns), and [slice](#slice-patterns) patterns, and may only appear once as one of the elements in those patterns.
It is also allowed in an [identifier pattern](#identifier-patterns) for [slice patterns](#slice-patterns) only.

The rest pattern is always irrefutable.
{==+==}
_rest pattern_ (剩余模式) (`..` 符号) 作为一个可变长度的模式，用于匹配在之前和之后尚未被匹配到的零个或多个元素。
它只能在 [元组模式](#tuple-patterns) 、 [元组结构体模式](#tuple-struct-patterns) 和 [切片模式](#slice-patterns) 中使用，并且只能出现一次作为这些模式中的一个元素。
在 [切片模式](#slice-patterns) 中，它也允许出现在 [标识符模式](#identifier-patterns) 中。

剩余模式始终是不可反驳的。
{==+==}


{==+==}
Examples:

```rust
# let words = vec!["a", "b", "c"];
# let slice = &words[..];
match slice {
    [] => println!("slice is empty"),
    [one] => println!("single element {}", one),
    [head, tail @ ..] => println!("head={} tail={:?}", head, tail),
}

match slice {
    // Ignore everything but the last element, which must be "!".
    [.., "!"] => println!("!!!"),

    // `start` is a slice of everything except the last element, which must be "z".
    [start @ .., "z"] => println!("starts with: {:?}", start),

    // `end` is a slice of everything but the first element, which must be "a".
    ["a", end @ ..] => println!("ends with: {:?}", end),

    // 'whole' is the entire slice and `last` is the final element
    whole @ [.., last] => println!("the last element of {:?} is {}", whole, last),

    rest => println!("{:?}", rest),
}

if let [.., penultimate, _] = slice {
    println!("next to last is {}", penultimate);
}

# let tuple = (1, 2, 3, 4, 5);
// Rest patterns may also be used in tuple and tuple struct patterns.
match tuple {
    (1, .., y, z) => println!("y={} z={}", y, z),
    (.., 5) => println!("tail must be 5"),
    (..) => println!("matches everything else"),
}
```
{==+==}
例如:

```rust
# let words = vec!["a", "b", "c"];
# let slice = &words[..];
match slice {
    [] => println!("slice is empty"),
    [one] => println!("single element {}", one),
    [head, tail @ ..] => println!("head={} tail={:?}", head, tail), // 匹配第一个元素和其余元素

}

match slice {
    [.., "!"] => println!("!!!"), // 忽略除了最后一个元素之外的所有元素，最后一个元素必须是 "!" 
    [start @ .., "z"] => println!("starts with: {:?}", start), // `start` 是除了最后一个元素之外的所有元素，最后一个元素必须是 "z" 
    ["a", end @ ..] => println!("ends with: {:?}", end), // `end` 是除了第一个元素之外的所有元素，第一个元素必须是"a"
    whole @ [.., last] => println!("the last element of {:?} is {}", whole, last), // 'whole' 是整个切片， `last` 是最后一个元素
    rest => println!("{:?}", rest),
}

if let [.., penultimate, _] = slice {
    println!("next to last is {}", penultimate); // 获取倒数第二个元素
}

# let tuple = (1, 2, 3, 4, 5);
// 剩余模式也可以用于元组和元组结构体模式。
match tuple {
    (1, .., y, z) => println!("y={} z={}", y, z), // 匹配第一个元素和其余元素，但是只保留最后两个元素
    (.., 5) => println!("tail must be 5"), // 必须以 5 结尾
    (..) => println!("matches everything else"), // 匹配所有其他情况
}
```
{==+==}


{==+==}
## Range patterns
{==+==}
## 范围模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _RangePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _InclusiveRangePattern_\
> &nbsp;&nbsp; | _HalfOpenRangePattern_\
> &nbsp;&nbsp; | _ObsoleteRangePattern_
>
> _InclusiveRangePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _RangePatternBound_ `..=` _RangePatternBound_
>
> _HalfOpenRangePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _RangePatternBound_ `..`
> &nbsp;&nbsp; | `..=` _RangePatternBound_
>
> _ObsoleteRangePattern_ :\
> &nbsp;&nbsp; _RangePatternBound_ `...` _RangePatternBound_
>
> _RangePatternBound_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [CHAR_LITERAL]\
> &nbsp;&nbsp; | [BYTE_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [INTEGER_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [FLOAT_LITERAL]\
> &nbsp;&nbsp; | [_PathExpression_]
{==+==}

{==+==}


{==+==}
*Range patterns* match scalar values within the range defined by their bounds.
A bound on the left of its sigils is a *lower bound*.
A bound on the right is an *upper bound*.
A range pattern may be closed or half-open.

A range pattern is *closed* if it has both a lower and an upper bound.
The only closed ranged pattern is the inclusive range pattern.

*Inclusive range patterns* match all the values between and including both of its bounds.
It is written as its lower bounds, followed by `..=`, followed by its upper bounds.
The type of it is the type unification of its upper and lower bounds.

For example, a pattern `'m'..='p'` will match only the values `'m'`, `'n'`, `'o'`, and `'p'`.
{==+==}
*范围模式* 匹配在它的边界定义的标量值的范围。
在符号左侧的边界是一个 *下界* 。
右侧的边界是一个 *上界* 。
范围模式可以是闭合或半开的。

如果范围模式有下界和上界，那么它是 *闭合的* 范围模式。
唯一的闭合范围模式是包含范围模式。

*包含范围模式* 匹配在它的边界之间和包括边界两侧的所有值。
它由其下界、跟着 `..=` 符号、以及它的上界组成。
它的类型是它的上界和下界的类型统一。

例如，模式 `'m'..='p'` 将只匹配值 `'m'`、`'n'`、`'o'` 和 `'p'`。
{==+==}


{==+==}
The lower bound cannot be greater than the upper bound.
That is, in `a..=b`, a &le; b must be the case.
For example, it is an error to have a range pattern `10..=0`.

Range patterns are *half-open* if they have only an upper or lower bound.
They have the same type as their upper or lower bound.

A half open range with only a lower bound is written as its lower bound followed by `..`.
These range patterns will match on any value greater than or equal to the lower bound.
For example, `1..` will match 1, 9, or 9001, or 9007199254740991 (if it is of an appropriate size), but not 0, and not negative numbers for signed integers.
The bounds can be literals or paths that point to constant values.

A half open range with only an upper bound is written as `..=` followed by its upper bound.
These range patterns will match on any value less than or equal to the upper bound.
For example, `..=10` will match 10, 1, 0, and for signed integer types, all negative values.

Half-open range patterns cannot be used as the top-level pattern for subpatterns in [slice patterns](#slice-patterns).
{==+==}
下边界不能大于上边界，即在 `a..=b` 中，必须满足 a &le; b 。
例如，有一个范围模式 `10..=0` 是错误的。

当范围模式仅具有上限或下限时，它们是 *半开放的* 。
它们的类型与其上限或下限相同。

仅具有下限的半开放范围写为其下限后跟 `..` 。
这些范围模式将匹配大于或等于下限的任何值。
例如， `1..` 将匹配 1 、 9 或 9001 ，或 9007199254740991 (如果它是适当大小的) ，但不匹配 0，对于有符号整数不匹配负数。

边界可以是字面值或指向常量值的路径。仅具有上限的半开放范围写为 `..=` 后跟其上限。
这些范围模式将匹配小于或等于上限的任何值。
例如，`..=10` 将匹配 10、1、0，对于有符号整数类型还会匹配所有负值。

半开放的范围模式不能用作 [切片模式](#slice-patterns) 中子模式的顶层模式。
{==+==}


{==+==}
The bounds is written as one of:

* A character, byte, integer, or float literal.
* A `-` followed by an integer or float literal.
* A [path]

If the bounds is written as a path, after macro resolution, the path must resolve to a constant item of the type `char`, an integer type, or a float type.

The type and value of the bounds is dependent upon how it is written out.
If the bounds is a [path], the pattern has the type and value of the [constant] the path resolves to.
If it is a literal, it has the type and value of the corresponding [literal expression].
If is a literal preceded by a `-`, it has the same type as the corresponding [literal expression] and the value of [negating] the value of the corresponding literal expression.
{==+==}
边界值可以写作以下其中之一:

* 字符、字节、整数或浮点数字面值。
* `-` 后跟一个整数或浮点数字面值。
* [路径][path]。

如果边界值是作为路径写的，在宏展开后，该路径必须解析为类型为 `char` 、整数类型或浮点类型的常量条目。

边界的类型和值取决于它的写法。如果边界是一个路径，则模式具有路径解析为的常量的类型和值。
如果是字面值，则它具有相应的字面表达式的类型和值。
如果是一个以 `-` 开头的字面值，则它具有与相应字面值表达式相同的类型，以及相应字面值表达式的值的 [取反][negating] 的值。
{==+==}


{==+==}
Examples:

```rust
# let c = 'f';
let valid_variable = match c {
    'a'..='z' => true,
    'A'..='Z' => true,
    'α'..='ω' => true,
    _ => false,
};

# let ph = 10;
println!("{}", match ph {
    0..=6 => "acid",
    7 => "neutral",
    8..=14 => "base",
    _ => unreachable!(),
});

# let uint: u32 = 5;
match uint {
    0 => "zero!",
    1.. => "positive number!",
};

// using paths to constants:
# const TROPOSPHERE_MIN : u8 = 6;
# const TROPOSPHERE_MAX : u8 = 20;
#
# const STRATOSPHERE_MIN : u8 = TROPOSPHERE_MAX + 1;
# const STRATOSPHERE_MAX : u8 = 50;
#
# const MESOSPHERE_MIN : u8 = STRATOSPHERE_MAX + 1;
# const MESOSPHERE_MAX : u8 = 85;
#
# let altitude = 70;
#
println!("{}", match altitude {
    TROPOSPHERE_MIN..=TROPOSPHERE_MAX => "troposphere",
    STRATOSPHERE_MIN..=STRATOSPHERE_MAX => "stratosphere",
    MESOSPHERE_MIN..=MESOSPHERE_MAX => "mesosphere",
    _ => "outer space, maybe",
});

# pub mod binary {
#     pub const MEGA : u64 = 1024*1024;
#     pub const GIGA : u64 = 1024*1024*1024;
# }
# let n_items = 20_832_425;
# let bytes_per_item = 12;
if let size @ binary::MEGA..=binary::GIGA = n_items * bytes_per_item {
    println!("It fits and occupies {} bytes", size);
}

# trait MaxValue {
#     const MAX: u64;
# }
# impl MaxValue for u8 {
#     const MAX: u64 = (1 << 8) - 1;
# }
# impl MaxValue for u16 {
#     const MAX: u64 = (1 << 16) - 1;
# }
# impl MaxValue for u32 {
#     const MAX: u64 = (1 << 32) - 1;
# }
// using qualified paths:
println!("{}", match 0xfacade {
    0 ..= <u8 as MaxValue>::MAX => "fits in a u8",
    0 ..= <u16 as MaxValue>::MAX => "fits in a u16",
    0 ..= <u32 as MaxValue>::MAX => "fits in a u32",
    _ => "too big",
});
```
{==+==}

{==+==}


{==+==}
Range patterns for fix-width integer and `char` types are irrefutable when they span the entire set of possible values of a type.
For example, `0u8..=255u8` is irrefutable.
The range of values for an integer type is the closed range from its minimum to maximum value.
The range of values for a `char` type are precisely those ranges containing all Unicode Scalar Values: `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{10FFFF}'`.

Floating point range patterns are deprecated and may be removed in a future Rust release.
See [issue #41620](https://github.com/rust-lang/rust/issues/41620) for more information.

> **Edition Differences**: Before the 2021 edition, closed range patterns may also be written using `...` as an alternative to `..=`, with the same meaning.

> **Note**: Although range patterns use the same syntax as [range expressions], there are no exclusive range patterns.
> That is, neither `x .. y` nor `.. x` are valid range patterns.
{==+==}
固定宽度整数类型和 `char` 类型的范围模式在它们跨越类型的所有可能值时是不可反驳的。
例如，`0u8..=255u8` 是不可反驳的。
整数类型的值范围是从最小值到最大值的闭合范围。
`char` 类型的值范围恰好包含所有 Unicode 标量值的范围：`'\U{0000}'..='\U{D7FF}'` 和 `'\U{E000}'..='\U{10FFFF}'`。

浮点数范围模式已被弃用，并可能在未来的 Rust 版本中被移除。有关更多信息，请参见 [问题 #41620](https://github.com/rust-lang/rust/issues/41620)。

> **Edition Differences**: 在 2021 版本之前，闭合范围模式也可以使用 `...` 作为 `..=` 的替代，具有相同的含义。

> **注意**：虽然范围模式使用与 [范围表达式](https://doc.rust-lang.org/reference/expressions/range-expr.html) 相同的语法，但不存在独占范围模式。也就是说，`x .. y` 和 `.. x` 都不是有效的范围模式。
{==+==}


{==+==}
## Reference patterns
{==+==}
## 引用模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _ReferencePattern_ :\
> &nbsp;&nbsp; (`&`|`&&`) `mut`<sup>?</sup> [_PatternWithoutRange_]
{==+==}

{==+==}


{==+==}
Reference patterns dereference the pointers that are being matched and, thus, borrow them.

For example, these two matches on `x: &i32` are equivalent:
{==+==}
引用模式会对正在匹配的指针进行解引用，因此会将它们借用。

例如，以下两个对 `x: &i32` 的匹配是等效的:
{==+==}


{==+==}
```rust
let int_reference = &3;

let a = match *int_reference { 0 => "zero", _ => "some" };
let b = match int_reference { &0 => "zero", _ => "some" };

assert_eq!(a, b);
```
{==+==}

{==+==}


{==+==}
The grammar production for reference patterns has to match the token `&&` to match a reference to a reference because it is a token by itself, not two `&` tokens.

Adding the `mut` keyword dereferences a mutable reference. The mutability must match the mutability of the reference.

Reference patterns are always irrefutable.
{==+==}
引用模式的语法规则必须匹配标记 `&&` 来匹配引用到引用，因为它本身是一个标记，不是两个 `&` 标记。

添加 `mut` 关键字会对可变引用进行解引用。
可变性必须与引用的可变性相匹配。

引用模式始终是不可反驳的。
{==+==}


{==+==}
## Struct patterns
{==+==}
## 结构模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _StructPattern_ :\
> &nbsp;&nbsp; [_PathInExpression_] `{`\
> &nbsp;&nbsp; &nbsp;&nbsp; _StructPatternElements_ <sup>?</sup>\
> &nbsp;&nbsp; `}`
>
> _StructPatternElements_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _StructPatternFields_ (`,` | `,` _StructPatternEtCetera_)<sup>?</sup>\
> &nbsp;&nbsp; | _StructPatternEtCetera_
>
> _StructPatternFields_ :\
> &nbsp;&nbsp; _StructPatternField_ (`,` _StructPatternField_) <sup>\*</sup>
>
> _StructPatternField_ :\
> &nbsp;&nbsp; [_OuterAttribute_] <sup>\*</sup>\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp; [TUPLE_INDEX] `:` [_Pattern_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [IDENTIFIER] `:` [_Pattern_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | `ref`<sup>?</sup> `mut`<sup>?</sup> [IDENTIFIER]\
> &nbsp;&nbsp; )
>
> _StructPatternEtCetera_ :\
> &nbsp;&nbsp; [_OuterAttribute_] <sup>\*</sup>\
> &nbsp;&nbsp; `..`
{==+==}

{==+==}


{==+==}
[_OuterAttribute_]: attributes.md
[TUPLE_INDEX]: tokens.md#tuple-index
{==+==}

{==+==}


{==+==}
Struct patterns match struct values that match all criteria defined by its subpatterns.
They are also used to [destructure](#destructuring) a struct.

On a struct pattern, the fields are referenced by name, index (in the case of tuple structs) or ignored by use of `..`:
{==+==}
结构体模式匹配满足其子模式定义的所有条件的结构体值。
它们还用于 [解构](#destructuring) 结构体。

在结构体模式中，可以通过名称、索引 (对于元组结构体) 引用字段，或通过使用 `..` 忽略字段：
{==+==}


{==+==}
```rust
# struct Point {
#     x: u32,
#     y: u32,
# }
# let s = Point {x: 1, y: 1};
#
match s {
    Point {x: 10, y: 20} => (),
    Point {y: 10, x: 20} => (),    // order doesn't matter
    Point {x: 10, ..} => (),
    Point {..} => (),
}

# struct PointTuple (
#     u32,
#     u32,
# );
# let t = PointTuple(1, 2);
#
match t {
    PointTuple {0: 10, 1: 20} => (),
    PointTuple {1: 10, 0: 20} => (),   // order doesn't matter
    PointTuple {0: 10, ..} => (),
    PointTuple {..} => (),
}
```
{==+==}

{==+==}


{==+==}
If `..` is not used, it is required to match all fields:
{==+==}
如果没有使用 `..` ，则需要匹配所有字段:
{==+==}


{==+==}
```rust
# struct Struct {
#    a: i32,
#    b: char,
#    c: bool,
# }
# let mut struct_value = Struct{a: 10, b: 'X', c: false};
#
match struct_value {
    Struct{a: 10, b: 'X', c: false} => (),
    Struct{a: 10, b: 'X', ref c} => (),
    Struct{a: 10, b: 'X', ref mut c} => (),
    Struct{a: 10, b: 'X', c: _} => (),
    Struct{a: _, b: _, c: _} => (),
}
```
{==+==}

{==+==}


{==+==}
The `ref` and/or `mut` _IDENTIFIER_ syntax matches any value and binds it to a variable with the same name as the given field.
{==+==}
`ref` 和/或 `mut` _IDENTIFIER_ 语法匹配任何值，并将其绑定到与给定字段同名的变量。
{==+==}


{==+==}
```rust
# struct Struct {
#    a: i32,
#    b: char,
#    c: bool,
# }
# let struct_value = Struct{a: 10, b: 'X', c: false};
#
let Struct{a: x, b: y, c: z} = struct_value;          // destructure all fields
```
{==+==}

{==+==}


{==+==}
A struct pattern is refutable when one of its subpatterns is refutable.
{==+==}
当结构体模式的任何子模式都是可反驳的时，结构体模式就是可反驳的。
{==+==}


{==+==}
## Tuple struct patterns
{==+==}
## 元组结构模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TupleStructPattern_ :\
> &nbsp;&nbsp; [_PathInExpression_] `(` _TupleStructItems_<sup>?</sup> `)`
>
> _TupleStructItems_ :\
> &nbsp;&nbsp; [_Pattern_]&nbsp;( `,` [_Pattern_] )<sup>\*</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
Tuple struct patterns match tuple struct and enum values that match all criteria defined by its subpatterns.
They are also used to [destructure](#destructuring) a tuple struct or enum value.

A tuple struct pattern is refutable when one of its subpatterns is refutable.
{==+==}
元组结构体模式匹配元组结构体和枚举值，匹配所有符合其子模式定义的条件。它们还可用于 [解构](#destructuring) 元组结构体或枚举值。

当子模式中有不可反驳模式时，元组结构体模式就是可反驳的。
{==+==}


{==+==}
## Tuple patterns
{==+==}
## 元组模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _TuplePattern_ :\
> &nbsp;&nbsp; `(` _TuplePatternItems_<sup>?</sup> `)`
>
> _TuplePatternItems_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_Pattern_] `,`\
> &nbsp;&nbsp; | [_RestPattern_]\
> &nbsp;&nbsp; | [_Pattern_]&nbsp;(`,` [_Pattern_])<sup>+</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
Tuple patterns match tuple values that match all criteria defined by its subpatterns.
They are also used to [destructure](#destructuring) a tuple.

The form `(..)` with a single [_RestPattern_] is a special form that does not require a comma, and matches a tuple of any size.

The tuple pattern is refutable when one of its subpatterns is refutable.

An example of using tuple patterns:
{==+==}
元组模式匹配满足其子模式定义的所有条件的元组值。它们也用于 [解构](#destructuring) 元组。
形式为 `(..)` 且具有单个 [_RestPattern_] 的是一种特殊形式，它不需要逗号，可以匹配任意大小的元组。

当其子模式中有一个可反驳时，元组模式是可反驳的。

以下是使用元组模式的示例：
{==+==}


{==+==}
```rust
let pair = (10, "ten");
let (a, b) = pair;

assert_eq!(a, 10);
assert_eq!(b, "ten");
```
{==+==}

{==+==}


{==+==}
## Grouped patterns
{==+==}
## 分组模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _GroupedPattern_ :\
> &nbsp;&nbsp; `(` [_Pattern_] `)`
{==+==}

{==+==}


{==+==}
Enclosing a pattern in parentheses can be used to explicitly control the precedence of compound patterns.
For example, a reference pattern next to a range pattern such as `&0..=5` is ambiguous and is not allowed, but can be expressed with parentheses.
{==+==}
将一个模式括在括号中可以用于显式地控制复合模式的优先级。
例如，一个引用模式紧贴着一个范围模式，如 `&0..=5` 是有歧义的并且不被允许，但可以用括号来表示。
{==+==}


{==+==}
```rust
let int_reference = &3;
match int_reference {
    &(0..=5) => (),
    _ => (),
}
```
{==+==}

{==+==}


{==+==}
## Slice patterns
{==+==}
## 切片模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _SlicePattern_ :\
> &nbsp;&nbsp; `[` _SlicePatternItems_<sup>?</sup> `]`
>
> _SlicePatternItems_ :\
> &nbsp;&nbsp; [_Pattern_] \(`,` [_Pattern_])<sup>\*</sup> `,`<sup>?</sup>
{==+==}

{==+==}


{==+==}
Slice patterns can match both arrays of fixed size and slices of dynamic size.
{==+==}
切片模式可以匹配固定大小的数组和动态大小的切片。
{==+==}


{==+==}
```rust
// Fixed size
let arr = [1, 2, 3];
match arr {
    [1, _, _] => "starts with one",
    [a, b, c] => "starts with something else",
};
```
{==+==}

{==+==}


{==+==}
```rust
// Dynamic size
let v = vec![1, 2, 3];
match v[..] {
    [a, b] => { /* this arm will not apply because the length doesn't match */ }
    [a, b, c] => { /* this arm will apply */ }
    _ => { /* this wildcard is required, since the length is not known statically */ }
};
```
{==+==}
```rust
// 动态大小
let v = vec![1, 2, 3];
match v[..] {
    [a, b] => { /* 这个分支不会匹配，因为长度不匹配 */ }
    [a, b, c] => { /* 这个分支会匹配 */ }
    _ => { /* 这个通配符是必须的，因为长度不能在编译时确定 */ }
};
```
{==+==}


{==+==}
Slice patterns are irrefutable when matching an array as long as each element is irrefutable.
When matching a slice, it is irrefutable only in the form with a single `..` [rest pattern](#rest-patterns) or [identifier pattern](#identifier-patterns) with the `..` rest pattern as a subpattern.

Within a slice, a half-open range pattern like `a..` must be enclosed in parentheses, as in `(a..)`, to clarify it is intended to match a single value.
{==+==}
切片模式在匹配数组时是不可反驳的，只要每个元素都是不可反驳的即可。
在匹配切片时，只有在使用单个 `..` [剩余模式](#rest-patterns) 或使用 `..` 剩余模式作为子模式的 [标识符模式](#identifier-patterns) 时才是不可反驳的。

在一个切片内，半开区间模式如 `a..` 必须用括号括起来，例如 `(a..)`，以明确其意图是匹配单个值。
{==+==}


{==+==}
## Path patterns
{==+==}
## 路径模式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _PathPattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [_PathExpression_]
{==+==}

{==+==}


{==+==}
_Path patterns_ are patterns that refer either to constant values or
to structs or enum variants that have no fields.

Unqualified path patterns can refer to:

* enum variants
* structs
* constants
* associated constants

Qualified path patterns can only refer to associated constants.

Constants cannot be a union type.
Struct and enum constants must have `#[derive(PartialEq, Eq)]` (not merely implemented).

Path patterns are irrefutable when they refer to structs or an enum variant when the enum has only one variant or a constant whose type is irrefutable.
They are refutable when they refer to refutable constants or enum variants for enums with multiple variants.
{==+==}
_Path patterns_ 是指引用常量值或没有字段的结构体或枚举变体的模式。未经修饰的路径模式可以引用：

* 枚举变体
* 结构体
* 常量
* 关联常量
  
修饰的路径模式只能引用关联常量。常量不能是联合类型。结构体和枚举常量必须有`#[derive(PartialEq, Eq)]`（而不是仅实现）。当路径模式引用结构体或枚举变体并且枚举只有一个变体或类型为不可反驳类型的常量时，路径模式是不可反驳的。当路径模式引用可反驳的常量或具有多个变体的枚举变体时，它们是可反驳的。
{==+==}


{==+==}
## Or-patterns

_Or-patterns_ are patterns that match on one of two or more sub-patterns (for example `A | B | C`).
They can nest arbitrarily.
Syntactically, or-patterns are allowed in any of the places where other patterns are allowed (represented by the _Pattern_ production), with the exceptions of `let`-bindings and function and closure arguments (represented by the _PatternNoTopAlt_ production).
{==+==}

{==+==}


{==+==}
### Static semantics

1. Given a pattern `p | q` at some depth for some arbitrary patterns `p` and `q`, the pattern is considered ill-formed if:

   + the type inferred for `p` does not unify with the type inferred for `q`, or
   + the same set of bindings are not introduced in `p` and `q`, or
   + the type of any two bindings with the same name in `p` and `q` do not unify with respect to types or binding modes.

   Unification of types is in all instances aforementioned exact and implicit [type coercions] do not apply.

2. When type checking an expression `match e_s { a_1 => e_1, ... a_n => e_n }`,
   for each match arm `a_i` which contains a pattern of form `p_i | q_i`,
   the pattern `p_i | q_i` is considered ill formed if,
   at the depth `d` where it exists the fragment of `e_s` at depth `d`,
   the type of the expression fragment does not unify with `p_i | q_i`.

3. With respect to exhaustiveness checking, a pattern `p | q` is considered to cover `p` as well as `q`.
   For some constructor `c(x, ..)` the distributive law applies such that `c(p | q, ..rest)` covers the same set of value as `c(p, ..rest) | c(q, ..rest)` does.
   This can be applied recursively until there are no more nested patterns of form `p | q` other than those that exist at the top level.

   Note that by *"constructor"* we do not refer to tuple struct patterns, but rather we refer to a pattern for any product type.
   This includes enum variants, tuple structs, structs with named fields, arrays, tuples, and slices.
{==+==}

{==+==}


{==+==}
### Dynamic semantics

1. The dynamic semantics of pattern matching a scrutinee expression `e_s` against a pattern `c(p | q, ..rest)` at depth `d` where `c` is some constructor,
   `p` and `q` are arbitrary patterns,
   and `rest` is optionally any remaining potential factors in `c`,
   is defined as being the same as that of `c(p, ..rest) | c(q, ..rest)`.
{==+==}

{==+==}


{==+==}
### Precedence with other undelimited patterns

As shown elsewhere in this chapter, there are several types of patterns that are syntactically undelimited, including identifier patterns, reference patterns, and or-patterns.
Or-patterns always have the lowest-precedence.
This allows us to reserve syntactic space for a possible future type ascription feature and also to reduce ambiguity.
For example, `x @ A(..) | B(..)` will result in an error that `x` is not bound in all patterns.
`&A(x) | B(x)` will result in a type mismatch between `x` in the different subpatterns.
{==+==}

{==+==}


{==+==}
[_GroupedPattern_]: #grouped-patterns
[_IdentifierPattern_]: #identifier-patterns
[_LiteralPattern_]: #literal-patterns
[_MacroInvocation_]: macros.md#macro-invocation
[_ObsoleteRangePattern_]: #range-patterns
[_PathInExpression_]: paths.md#paths-in-expressions
[_PathExpression_]: expressions/path-expr.md
[_PathPattern_]: #path-patterns
[_Pattern_]: #patterns
[_PatternNoTopAlt_]: #patterns
[_PatternWithoutRange_]: #patterns
[_QualifiedPathInExpression_]: paths.md#qualified-paths
[_RangePattern_]: #range-patterns
[_ReferencePattern_]: #reference-patterns
[_RestPattern_]: #rest-patterns
[_SlicePattern_]: #slice-patterns
[_StructPattern_]: #struct-patterns
[_TuplePattern_]: #tuple-patterns
[_TupleStructPattern_]: #tuple-struct-patterns
[_WildcardPattern_]: #wildcard-pattern

[`Copy`]: special-types-and-traits.md#copy
[IDENTIFIER]: identifiers.md
[constant]: items/constant-items.md
[enums]: items/enumerations.md
[literals]: expressions/literal-expr.md
[literal expression]: expressions/literal-expr.md
[negating]: expressions/operator-expr.md#negation-operators
[path]: expressions/path-expr.md
[range expressions]: expressions/range-expr.md
[structs]: items/structs.md
[tuples]: types/tuple.md
[scrutinee]: glossary.md#scrutinee
[type coercions]: type-coercions.md
{==+==}

{==+==}