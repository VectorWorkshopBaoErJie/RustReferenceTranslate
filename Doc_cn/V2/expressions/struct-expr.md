{==+==}
Struct expressions with curly braces can't be used directly in a [loop] or [if] expression's head, or in the [scrutinee] of an [if let] or [match] expression.
However, struct expressions can be used in these situations if they are within another expression, for example inside [parentheses].

The field names can be decimal integer values to specify indices for constructing tuple structs.
This can be used with base structs to fill out the remaining indices not specified:
{==+==}

{==+==}
