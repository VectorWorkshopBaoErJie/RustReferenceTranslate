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
联合体没有 "活动字段" 的概念。相反，每次联合体访问只是将存储解释为用于访问的字段类型。
读取联合体字段会读取字段类型的联合体的位。字段可能具有非零偏移量 (除非使用了 [C表示法][the C representation])；在这种情况下，从字段偏移处开始的位将被读取。
程序员有责任确保数据在字段类型上是有效的。未能这样做会导致 [未定义的行为][undefined behavior] 。
例如，从 [布尔类型][boolean type] 的字段读取值 `3` 是未定义行为。
实际上，使用 [C表示法][the C representation] 写入然后读取联合体类似于从用于写入的类型到用于读取的类型的 [`形变`][`transmute`] 。
{==+==}
