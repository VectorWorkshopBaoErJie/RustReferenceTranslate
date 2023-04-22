{==+==}
> **<sup>Syntax</sup>**\
> _RangePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _RangeInclusivePattern_\
> &nbsp;&nbsp; | _RangeFromPattern_\
> &nbsp;&nbsp; | _RangeToInclusivePattern_\
> &nbsp;&nbsp; | _ObsoleteRangePattern_
>
> _RangeInclusivePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _RangePatternBound_ `..=` _RangePatternBound_
>
> _RangeFromPattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _RangePatternBound_ `..`
>
> _RangeToInclusivePattern_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `..=` _RangePatternBound_
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
> **<sup>语法</sup>**\
> _区间模式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _区间内部模式_\
> &nbsp;&nbsp; | _区间From模式_\
> &nbsp;&nbsp; | _区间To内部模式_\
> &nbsp;&nbsp; | _废弃区间模式_
>
> _区间内部模式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _区间模式约束_ `..=` _区间模式约束_
>
> _区间From模式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _区间模式约束_ `..`
>
> _区间To内部模式_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; `..=` _区间模式约束_
>
> _废弃区间模式_ :\
> &nbsp;&nbsp; _区间模式约束_ `...` _区间模式约束_
>
> _区间模式约束_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; [字符字面值][CHAR_LITERAL]\
> &nbsp;&nbsp; | [字节字面值][BYTE_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [整数字面值][INTEGER_LITERAL]\
> &nbsp;&nbsp; | `-`<sup>?</sup> [浮点数字面值][FLOAT_LITERAL]\
> &nbsp;&nbsp; | [_路径表达式_][_PathExpression_]
{==+==}


{==+==}
*Range patterns* match scalar values within the range defined by their bounds.
They comprise a *sigil* (one of `..`, `..=`, or `...`) and a bound on one or both sides.
A bound on the left of the sigil is a *lower bound*.
A bound on the right is an *upper bound*.

A range pattern with both a lower and upper bound will match all values between and including both of its bounds.
It is written as its lower bound, followed by `..=`, followed by its upper bound.
The type of the range pattern is the type unification of its upper and lower bounds.

For example, a pattern `'m'..='p'` will match only the values `'m'`, `'n'`, `'o'`, and `'p'`.

The lower bound cannot be greater than the upper bound.
That is, in `a..=b`, a &le; b must be the case.
For example, it is an error to have a range pattern `10..=0`.

A range pattern with only a lower bound will match any value greater than or equal to the lower bound.
It is written as its lower bound followed by `..`, and has the same type as its lower bound.
For example, `1..` will match 1, 9, or 9001, or 9007199254740991 (if it is of an appropriate size), but not 0, and not negative numbers for signed integers.

A range pattern with only an upper bound matches any value less than or equal to the upper bound.
It is written as `..=` followed by its upper bound, and has the same type as its upper bound.
For example, `..=10` will match 10, 1, 0, and for signed integer types, all negative values.

Range patterns with only one bound cannot be used as the top-level pattern for subpatterns in [slice patterns](#slice-patterns).
{==+==}
*区间模式* 匹配由其边界定义的区间内的标量值。它们由一个 *符号* ( `..` 、 `..=` 或 `...` 中的一个) 和一个或两个边界组成。在符号左边的边界是 *下界* ，在右边的是 *上界* 。

具有下界和上界的区间模式将匹配其两个边界之间及其包括的所有值。它由其下界、后跟 `..=` ，后跟其上界组成。区间模式的类型与其上界和下界的类型统一。

例如，模式 `'m'..='p'` 将仅匹配值 `'m'`  `'n'`  `'o'` 和 `'p'`。

下界不能大于上界。也就是说，在 `a..=b` 中，必须满足 $a &le; b$。例如，拥有区间模式 `10..=0` 是错误的。

只有下界的区间模式将匹配大于或等于下界的任何值。它由其下界后跟 `..` 组成，与其下界具有相同的类型。例如， `1..` 将匹配 1、9、9001 或 9007199254740991 (如果它是适当大小的) ，但不匹配 0 和有符号整数的负数。

只有上界的区间模式将匹配小于或等于上界的任何值。它由 `..=` 后跟其上界组成，与其上界具有相同的类型。例如， `..=10` 将匹配 10、1、0 和有符号整数类型的所有负值。

只有一个边界的区间模式不能用作 [切片模式](#slice-patterns) 的子模式的顶级模式。
{==+==}


{==+==}
Range patterns for fix-width integer and `char` types are irrefutable when they span the entire set of possible values of a type.
For example, `0u8..=255u8` is irrefutable.
The range of values for an integer type is the closed range from its minimum to maximum value.
The range of values for a `char` type are precisely those ranges containing all Unicode Scalar Values: `'\u{0000}'..='\u{D7FF}'` and `'\u{E000}'..='\u{10FFFF}'`.

Floating point range patterns are deprecated and may be removed in a future Rust release.
See [issue #41620](https://github.com/rust-lang/rust/issues/41620) for more information.

> **Edition Differences**: Before the 2021 edition, range patterns with both a lower and upper bound may also be written using `...` in place of `..=`, with the same meaning.

> **Note**: Although range patterns use the same syntax as [range expressions], there are no exclusive range patterns.
> That is, neither `x .. y` nor `.. x` are valid range patterns.
{==+==}
固定宽度整数和 `char` 类型的区间模式在它们 span 类型的所有可能值时是不可反驳的。
例如， `0u8..=255u8` 是不可反驳的。
整数类型的值区间是从其最小值到最大值的闭区间。
`char` 类型的值区间恰好包含所有 Unicode 标量值的区间: `'\U{0000}'..='\U{D7FF}'` 和 `'\U{E000}'..='\U{10FFFF}'` 。

浮点数区间模式已被弃用，可能会在未来的Rust版本中删除。
有关更多信息，请参见 [issue #41620](https://github.com/rust-lang/rust/issues/41620) 。

> **版本差异**: 在 2021 版之前，带有下限和上限的区间模式也可以使用 `...` 代替 `..=` 进行编写，具有相同的含义。

> **注意**: 尽管区间模式使用与 [区间表达式][range expressions] 相同的语法，但不存在独占区间模式。
> 也就是说， `x .. y` 和 `.. x` 都不是有效的区间模式。
{==+==}


{==+==}
Slice patterns are irrefutable when matching an array as long as each element is irrefutable.
When matching a slice, it is irrefutable only in the form with a single `..` [rest pattern](#rest-patterns) or [identifier pattern](#identifier-patterns) with the `..` rest pattern as a subpattern.

Within a slice, a range pattern without both lower and upper bound must be enclosed in parentheses, as in `(a..)`, to clarify it is intended to match against a single slice element.
A range pattern with both lower and upper bound, like `a..=b`, is not required to be enclosed in parentheses.
{==+==}
切片模式在匹配数组时是不可反驳的，只要每个元素都是不可反驳的。
在匹配切片时，只有在形式为单个 `..` 的 [剩余模式](#rest-patterns) 或 [标识符模式](#identifier-patterns) 时才是不可反驳的，其中 `..` 剩余模式作为子模式。

在切片内，没有同时具有下限和上限的区间模式必须用括号括起来，例如 `(a..)`，以明确其意图是匹配单个切片元素。
同时具有下限和上限的区间模式，例如 `a..=b` ，不需要用括号括起来。
{==+==}
