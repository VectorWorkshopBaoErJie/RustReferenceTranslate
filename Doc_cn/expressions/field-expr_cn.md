{==+==}
# Field access expressions
{==+==}
# 字段访问表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _FieldExpression_ :\
> &nbsp;&nbsp; [_Expression_] `.` [IDENTIFIER]
{==+==}
> **<sup>语法</sup>**\
> _字段表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `.` [标识符][IDENTIFIER]
{==+==}


{==+==}
A *field expression* is a [place expression] that evaluates to the location of a field of a [struct] or [union].
When the operand is [mutable], the field expression is also mutable.

The syntax for a field expression is an expression, called the *container operand*, then a `.`, and finally an [identifier].
Field expressions cannot be followed by a parenthetical comma-separated list of expressions, as that is instead parsed as a [method call expression].
That is, they cannot be the function operand of a [call expression].
{==+==}
*字段表达式* 是一个 [占位表达式][place expression]  ，它求值为 [结构体][struct] 或 [联合体][union] 的一个字段的位置。
当操作数是 [可变的][mutable] ，字段表达式也是可变的。
字段表达式的语法是一个表达式 (称为 *容器操作数* ) ，然后是一个 `.` ，最后是一个 [标识符][identifier] 。
字段表达式不能后跟一个括号内的逗号分隔的表达式列表，因为那将被解析为 [方法调用表达式][method call expression] 。
也就是说，它们不能作为 [调用表达式][call expression] 的函数操作数。
{==+==}


{==+==}
> **Note**: Wrap the field expression in a [parenthesized expression] to use it in a call expression.
>
> ```rust
> # struct HoldsCallable<F: Fn()> { callable: F }
> let holds_callable = HoldsCallable { callable: || () };
>
> // Invalid: Parsed as calling the method "callable"
> // holds_callable.callable();
>
> // Valid
> (holds_callable.callable)();
> ```
{==+==}
> **注意**: 在调用表达式中使用字段表达式时，要将其包装在 [括号表达式][parenthesized expression] 中。
> 
> ```rust
> # struct HoldsCallable<F: Fn()> { callable: F }
> let holds_callable = HoldsCallable { callable: || () };
>
> // 无效：被解析为调用 "callable" 方法
> // holds_callable.callable();
> 
> // 有效
> (holds_callable.callable)();
> ```
{==+==}


{==+==}
Examples:

<!-- ignore: needs lots of support code -->
```rust,ignore
mystruct.myfield;
foo().x;
(Struct {a: 10, b: 20}).a;
(mystruct.function_field)() // Call expression containing a field expression
```
{==+==}
示例:

<!-- ignore: needs lots of support code -->
```rust,ignore
mystruct.myfield;
foo().x;
(Struct {a: 10, b: 20}).a;
(mystruct.function_field)() // 包含字段表达式的调用表达式
```
{==+==}


{==+==}
## Automatic dereferencing

If the type of the container operand implements [`Deref`] or [`DerefMut`][`Deref`] depending on whether the operand is [mutable], it is *automatically dereferenced* as many times as necessary to make the field access possible.
This process is also called *autoderef* for short.
{==+==}
## 自动解引用

如果容器操作数的类型实现了 [`Deref`] 或 [`DerefMut`][`Deref`] (取决于操作数是否 [可变][mutable] )，
则会进行 *自动解引用* ，直到使字段访问成为可能为止。
{==+==}


{==+==}
## Borrowing

The fields of a struct or a reference to a struct are treated as separate entities when borrowing.
If the struct does not implement [`Drop`] and is stored in a local variable, this also applies to moving out of each of its fields.
This also does not apply if automatic dereferencing is done though user-defined types other than [`Box`].
{==+==}
## 借用

在借用过程中，结构体的字段或结构体的引用被视为独立的实体。
如果结构体没有实现 [`Drop`] 并且存储在局部变量中，则移动其每个字段时也适用于此规则。
但如果除了 [`Box`] 之外的用户定义类型进行自动解引用，则不适用此规则。
{==+==}


{==+==}
```rust
struct A { f1: String, f2: String, f3: String }
let mut x: A;
# x = A {
#     f1: "f1".to_string(),
#     f2: "f2".to_string(),
#     f3: "f3".to_string()
# };
let a: &mut String = &mut x.f1; // x.f1 borrowed mutably
let b: &String = &x.f2;         // x.f2 borrowed immutably
let c: &String = &x.f2;         // Can borrow again
let d: String = x.f3;           // Move out of x.f3
```
{==+==}
```rust
struct A { f1: String, f2: String, f3: String }
let mut x: A;
# x = A {
#     f1: "f1".to_string(),
#     f2: "f2".to_string(),
#     f3: "f3".to_string()
# };
let a: &mut String = &mut x.f1; // 可变地借用 x.f1
let b: &String = &x.f2;         // 不可变地借用 x.f2
let c: &String = &x.f2;         // 可以再次借用
let d: String = x.f3;           // 从 x.f3 中移动值
```
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[`Box`]: ../special-types-and-traits.md#boxt
[`Deref`]: ../special-types-and-traits.md#deref-and-derefmut
[`drop`]: ../special-types-and-traits.md#drop
[IDENTIFIER]: ../identifiers.md
[call expression]: call-expr.md
[method call expression]: method-call-expr.md
[mutable]: ../expressions.md#mutability
[parenthesized expression]: grouped-expr.md
[place expression]: ../expressions.md#place-expressions-and-value-expressions
[struct]: ../items/structs.md
[union]: ../items/unions.md
{==+==}

{==+==}