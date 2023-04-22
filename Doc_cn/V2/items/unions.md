{==+==}
Unions have no notion of an "active field". Instead, every union access just
interprets the storage as the type of the field used for the access. Reading a
union field reads the bits of the union at the field's type. Fields might have a
non-zero offset (except when [the C representation] is used); in that case the
bits starting at the offset of the fields are read. It is the programmer's
responsibility to make sure that the data is valid at the field's type. Failing
to do so results in [undefined behavior]. For example, reading the value `3`
from a field of the [boolean type] is undefined behavior. Effectively,
writing to and then reading from a union with [the C representation] is
analogous to a [`transmute`] from the type used for writing to the type used for
reading.
{==+==}

{==+==}
