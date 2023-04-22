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

{==+==}


{==+==}
Slice patterns are irrefutable when matching an array as long as each element is irrefutable.
When matching a slice, it is irrefutable only in the form with a single `..` [rest pattern](#rest-patterns) or [identifier pattern](#identifier-patterns) with the `..` rest pattern as a subpattern.

Within a slice, a range pattern without both lower and upper bound must be enclosed in parentheses, as in `(a..)`, to clarify it is intended to match against a single slice element.
A range pattern with both lower and upper bound, like `a..=b`, is not required to be enclosed in parentheses.
{==+==}

{==+==}
