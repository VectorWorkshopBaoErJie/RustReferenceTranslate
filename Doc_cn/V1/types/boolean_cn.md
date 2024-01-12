{==+==}
# Boolean type
{==+==}
# 布尔类型
{==+==}


{==+==}
```rust
let b: bool = true;
```
{==+==}

{==+==}


{==+==}
The *boolean type* or *bool* is a primitive data type that can take on one of
two values, called *true* and *false*.

Values of this type may be created using a [literal expression] using the
keywords `true` and `false` corresponding to the value of the same name.

This type is a part of the [language prelude] with the [name] `bool`.

An object with the boolean type has a [size and alignment] of 1 each. The
value false has the bit pattern `0x00` and the value true has the bit pattern
`0x01`. It is [undefined behavior] for an object with the boolean type to have
any other bit pattern.

The boolean type is the type of many operands in various [expressions]:
{==+==}
*布尔类型* 或 *bool* 是一种原始数据类型，可以取 *true* 和 *false* 两个值之一。

可以使用关键字 `true` 和 `false` 创建此类型的值，通过 [字面值表达式][literal expression] 生成。

此类型是 [语言预定义][language prelude] 的一部分，[名称][name] 为 `bool` 。

具有布尔类型的对象每个都具有为 1 的 [大小和对齐方式][size and alignment] 。
值 false 的位表示为 `0x00` ，值 true 的位表示为 `0x01` 。
布尔类型对象的其他任何位表示是 [未定义行为][undefined behavior] 。

布尔类型是各类 [表达式][expressions] 中许多操作数的类型：
{==+==}


{==+==}
* The condition operand in [if expressions] and [while expressions]
* The operands in [lazy boolean operator expressions][lazy]

> **Note**: The boolean type acts similarly to but is not an [enumerated type].
In practice, this mostly means that constructors are not associated to the type
(e.g. `bool::true`).

Like all primitives, the boolean type [implements][p-impl] the
[traits][p-traits] [`Clone`][p-clone], [`Copy`][p-copy], [`Sized`][p-sized],
[`Send`][p-send], and [`Sync`][p-sync].

> **Note**: See the [standard library docs][std] for library operations.
{==+==}
* [if 表达式][if expressions] 和 [while 表达式][while expressions] 中的条件操作数
* [惰性布尔运算符表达式][lazy] 中的操作数

> **注意**: 布尔类型类似于但不是 [可枚举类型][enumerated type] 。主要区别是构造函数未与类型关联 (比如 `bool::true` ) 。

与所有原始类型一样，布尔类型 [p-impl] 实现了 [traits][p-traits] [`Clone`][p-clone] ， [`Copy`][p-copy] ， [`Sized`][p-sized] ， [`Send`][p-send] 和 [`Sync`][p-sync] 。

> **注意**: 请参阅 [标准库文档][std] 以获取库操作。
{==+==}


{==+==}
## Operations on boolean values

<!-- This is washy wording --> When using certain operator expressions with a
boolean type for its operands, they evaluate using the rules of [boolean logic].
{==+==}
## 布尔值的操作

<!-- This is washy wording --> 当使用特定的运算符表达式作为布尔类型的操作数时，根据 [布尔逻辑][boolean logic] 规则进行计算。
{==+==}


{==+==}
### Logical not

| `b` | [`!b`][op-not] |
|- | - |
| `true` | `false` |
| `false` | `true` |
{==+==}
### 逻辑非

| `b` | [`!b`][op-not] |
|- | - |
| `true` | `false` |
| `false` | `true` |
{==+==}


{==+==}
### Logical or

| `a` | `b` | [<code>a &#124; b</code>][op-or] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |
{==+==}
### 逻辑或

| `a` | `b` | [<code>a &#124; b</code>][op-or] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |
{==+==}


{==+==}
### Logical and

| `a` | `b` | [`a & b`][op-and] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |
{==+==}
### 逻辑与

| `a` | `b` | [`a & b`][op-and] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |
{==+==}


{==+==}
### Logical xor

| `a` | `b` | [`a ^ b`][op-xor] |
|- | - | - |
| `true` | `true` | `false` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |
{==+==}
### 逻辑异或

| `a` | `b` | [`a ^ b`][op-xor] |
|- | - | - |
| `true` | `true` | `false` |
| `true` | `false` | `true` |
| `false` | `true` | `true` |
| `false` | `false` | `false` |
{==+==}


{==+==}
### Comparisons

| `a` | `b` | [`a == b`][op-compare] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `true` |

| `a` | `b` | [`a > b`][op-compare] |
|- | - | - |
| `true` | `true` | `false` |
| `true` | `false` | `true` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |

* `a != b` is the same as `!(a == b)`
* `a >= b` is the same as `a == b | a > b`
* `a < b` is the same as `!(a >= b)`
* `a <= b` is the same as `a == b | a < b`
{==+==}
### 比较

| `a` | `b` | [`a == b`][op-compare] |
|- | - | - |
| `true` | `true` | `true` |
| `true` | `false` | `false` |
| `false` | `true` | `false` |
| `false` | `false` | `true` |

| `a` | `b` | [`a > b`][op-compare] |
|- | - | - |
| `true` | `true` | `false` |
| `true` | `false` | `true` |
| `false` | `true` | `false` |
| `false` | `false` | `false` |

* `a != b` 相同于 `!(a == b)`
* `a >= b` 相同于 `a == b | a > b`
* `a < b` 相同于 `!(a >= b)`
* `a <= b` 相同于 `a == b | a < b`
{==+==}


{==+==}
[boolean logic]: https://en.wikipedia.org/wiki/Boolean_algebra
[enumerated type]: enum.md
[expressions]: ../expressions.md
[if expressions]: ../expressions/if-expr.md#if-expressions
[language prelude]: ../names/preludes.md#language-prelude
[lazy]: ../expressions/operator-expr.md#lazy-boolean-operators
[literal expression]: ../expressions/literal-expr.md
[name]: ../names.md
[op-and]: ../expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[op-compare]: ../expressions/operator-expr.md#comparison-operators
[op-not]: ../expressions/operator-expr.md#negation-operators
[op-or]: ../expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[op-xor]: ../expressions/operator-expr.md#arithmetic-and-logical-binary-operators
[p-clone]: ../special-types-and-traits.md#clone
[p-copy]: ../special-types-and-traits.md#copy
[p-impl]: ../items/implementations.md
[p-send]: ../special-types-and-traits.md#send
[p-sized]: ../special-types-and-traits.md#sized
[p-sync]: ../special-types-and-traits.md#sync
[p-traits]: ../items/traits.md
[size and alignment]: ../type-layout.md#size-and-alignment
[std]: ../../std/primitive.bool.html
[undefined behavior]: ../behavior-considered-undefined.md
[while expressions]: ../expressions/loop-expr.md#predicate-loops
{==+==}

{==+==}