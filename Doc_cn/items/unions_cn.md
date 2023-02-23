{==+==}
# Unions
{==+==}
# 联合体
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Union_ :\
> &nbsp;&nbsp; `union` [IDENTIFIER]&nbsp;[_GenericParams_]<sup>?</sup> [_WhereClause_]<sup>?</sup>
>   `{`[_StructFields_] `}`
{==+==}

{==+==}


{==+==}
A union declaration uses the same syntax as a struct declaration, except with
`union` in place of `struct`.
{==+==}
联合体与结构体声明使用相同的语法，只是将 `struct` 替换为 `union`。
{==+==}


{==+==}
```rust
#[repr(C)]
union MyUnion {
    f1: u32,
    f2: f32,
}
```
{==+==}

{==+==}


{==+==}
The key property of unions is that all fields of a union share common storage.
As a result, writes to one field of a union can overwrite its other fields, and
size of a union is determined by the size of its largest field.
{==+==}
联合体的关键特性是，所有的字段共享相同的存储空间。
因此，对联合体的一个字段的写操作可能会覆盖其它字段的值，而联合的大小则由其最大字段的大小决定。
{==+==}


{==+==}
Union field types are restricted to the following subset of types:
- `Copy` types
- References (`&T` and `&mut T` for arbitrary `T`)
- `ManuallyDrop<T>` (for arbitrary `T`)
- Tuples and arrays containing only allowed union field types
{==+==}
联合体字段类型限制为以下类型的子集:
- `Copy` 类型
- 引用 (`&T` 和 `&mut T` 为任意的 `T`)
- `ManuallyDrop<T>` (为任意的 `T`)
- 元组和数组仅允许包含联合体字段类型
{==+==}


{==+==}
This restriction ensures, in particular, that union fields never need to be
dropped. Like for structs and enums, it is possible to `impl Drop` for a union
to manually define what happens when it gets dropped.
{==+==}
这个限制确保了联合体字段不需要被丢弃。
与结构体和枚举类似，也可以为联合体实现 `Drop` trait 来手动定义在释放时发生的行为。
{==+==}


{==+==}
## Initialization of a union
{==+==}
## 初始化联合体
{==+==}


{==+==}
A value of a union type can be created using the same syntax that is used for
struct types, except that it must specify exactly one field:
{==+==}
联合体类型的值可以使用与结构体类型相同的语法创建，只不过必须指定一个字段:
{==+==}


{==+==}
```rust
# union MyUnion { f1: u32, f2: f32 }
#
let u = MyUnion { f1: 1 };
```
{==+==}

{==+==}


{==+==}
The expression above creates a value of type `MyUnion` and initializes the
storage using field `f1`. The union can be accessed using the same syntax as
struct fields:
{==+==}
这个表达式创建了一个类型为 `MyUnion` 的值，并使用字段 `f1` 初始化了存储。
可以使用与结构体字段相同的语法来访问联合字段。
{==+==}


{==+==}
```rust
# union MyUnion { f1: u32, f2: f32 }
#
# let u = MyUnion { f1: 1 };
let f = unsafe { u.f1 };
```
{==+==}

{==+==}


{==+==}
## Reading and writing union fields
{==+==}
## 读取和写入联合体字段
{==+==}


{==+==}
Unions have no notion of an "active field". Instead, every union access just
interprets the storage at the type of the field used for the access. Reading a
union field reads the bits of the union at the field's type. Fields might have a
non-zero offset (except when [the C representation] is used); in that case the
bits starting at the offset of the fields are read. It is the programmer's
responsibility to make sure that the data is valid at the field's type. Failing
to do so results in [undefined behavior]. For example, reading the value `3`
through of a field of the [boolean type] is undefined behavior. Effectively,
writing to and then reading from a union with [the C representation] is
analogous to a [`transmute`] from the type used for writing to the type used for
reading.
{==+==}
联合体没有 "活动字段" 的概念。每次访问联合体时，只会解释用于访问的字段类型的存储。
读取联合体字段按位读取字段类型。
字段可能具有非零偏移量 (除非使用 [C表示法][the C representation]) ；在这种情况下，从字段偏移处开始读取位。程序员有责任确保数据在字段类型上是有效的。
未能这样做会导致 [未定义行为][undefined behavior] 。例如，通过 [布尔类型][boolean type] 字段读取值 `3` 是未定义行为。
实际上，使用 [C表示法][the C representation] 写入并然后读取联合体类似于从用于写入的类型到用于读取的类型的 [`transmute`]  "质变"。
{==+==}


{==+==}
Consequently, all reads of union fields have to be placed in `unsafe` blocks:
{==+==}
因此，所有对联合体字段的读取都必须放在 `unsafe` 块中:
{==+==}


{==+==}
```rust
# union MyUnion { f1: u32, f2: f32 }
# let u = MyUnion { f1: 1 };
#
unsafe {
    let f = u.f1;
}
```
{==+==}

{==+==}


{==+==}
Commonly, code using unions will provide safe wrappers around unsafe union
field accesses.
{==+==}
通常，使用联合体的代码会在不安全的联合体字段访问周围提供安全的包装器。
{==+==}


{==+==}
In contrast, writes to union fields are safe, since they just overwrite
arbitrary data, but cannot cause undefined behavior. (Note that union field
types can never have drop glue, so a union field write will never implicitly
drop anything.)
{==+==}
相比之下，写入联合体字段是安全的，因为它们只是覆盖任意数据，而不能导致未定义的行为。
(请注意，联合体字段类型永远不会具有粘联的丢弃 ，因此联合体字段写入永远不会隐式丢弃任何内容。)
{==+==}


{==+==}
## Pattern matching on unions
{==+==}
## 联合体模式匹配
{==+==}


{==+==}
Another way to access union fields is to use pattern matching. Pattern matching
on union fields uses the same syntax as struct patterns, except that the pattern
must specify exactly one field. Since pattern matching is like reading the union
with a particular field, it has to be placed in `unsafe` blocks as well.
{==+==}
另一种访问联合体字段的方式是使用模式匹配。
对于联合体字段的模式匹配与结构体模式匹配使用相同的语法，不同的是模式必须指定一个且仅一个字段。
由于模式匹配类似于使用特定字段读取联合体，因此它也必须放在 `unsafe` 块中。
{==+==}


{==+==}
```rust
# union MyUnion { f1: u32, f2: f32 }
#
fn f(u: MyUnion) {
    unsafe {
        match u {
            MyUnion { f1: 10 } => { println!("ten"); }
            MyUnion { f2 } => { println!("{}", f2); }
        }
    }
}
```
{==+==}

{==+==}


{==+==}
Pattern matching may match a union as a field of a larger structure. In
particular, when using a Rust union to implement a C tagged union via FFI, this
allows matching on the tag and the corresponding field simultaneously:
{==+==}
模式匹配可能将联合体作为更大结构体的字段进行匹配。
特别是在使用 Rust 联合体通过 FFI 实现 C 标记联合时，这允许同时匹配标记和相应字段:
{==+==}


{==+==}
```rust
#[repr(u32)]
enum Tag { I, F }

#[repr(C)]
union U {
    i: i32,
    f: f32,
}

#[repr(C)]
struct Value {
    tag: Tag,
    u: U,
}

fn is_zero(v: Value) -> bool {
    unsafe {
        match v {
            Value { tag: Tag::I, u: U { i: 0 } } => true,
            Value { tag: Tag::F, u: U { f: num } } if num == 0.0 => true,
            _ => false,
        }
    }
}
```
{==+==}

{==+==}


{==+==}
## References to union fields
{==+==}
## 联合体字段的引用
{==+==}


{==+==}
Since union fields share common storage, gaining write access to one field of a
union can give write access to all its remaining fields. Borrow checking rules
have to be adjusted to account for this fact. As a result, if one field of a
union is borrowed, all its remaining fields are borrowed as well for the same
lifetime.
{==+==}
由于联合体字段共享存储空间，因此获得对联合体的一个字段的写访问权限可以给予对其所有其余字段的写访问权限。
借用检查规则必须调整以考虑这一点。
因此，如果联合体的一个字段被借用，则其所有其余字段在相同的生命周期内也被借用。
{==+==}


{==+==}
```rust,compile_fail
# union MyUnion { f1: u32, f2: f32 }
// ERROR: cannot borrow `u` (via `u.f2`) as mutable more than once at a time
fn test() {
    let mut u = MyUnion { f1: 1 };
    unsafe {
        let b1 = &mut u.f1;
//                    ---- first mutable borrow occurs here (via `u.f1`)
        let b2 = &mut u.f2;
//                    ^^^^ second mutable borrow occurs here (via `u.f2`)
        *b1 = 5;
    }
//  - first borrow ends here
    assert_eq!(unsafe { u.f1 }, 5);
}
```
{==+==}
```rust,compile_fail
# union MyUnion { f1: u32, f2: f32 }
// ERROR: 不能将 `u` (通过 `u.f2` ) 借为可变的，同一时间不能超过一次。
fn test() {
    let mut u = MyUnion { f1: 1 };
    unsafe {
        let b1 = &mut u.f1;
//                    ---- 第一个可变借用发生在这里 (通过 `u.f1`)
        let b2 = &mut u.f2;
//                    ^^^^ 第二个可变借用发生在这里 (通过 `u.f2`)
        *b1 = 5;
    }
//  -  第一个借用这里结束
    assert_eq!(unsafe { u.f1 }, 5);
}
```
{==+==}


{==+==}
As you could see, in many aspects (except for layouts, safety, and ownership)
unions behave exactly like structs, largely as a consequence of inheriting
their syntactic shape from structs. This is also true for many unmentioned
aspects of Rust language (such as privacy, name resolution, type inference,
generics, trait implementations, inherent implementations, coherence, pattern
checking, etc etc etc).
{==+==}
你所看到的是，在很多方面 (除了布局、安全性和所有权) 上，联合体和结构体表现得非常相似，这是由于从结构体继承了语法形式。
这在 Rust 语言的许多未提及方面也同样适用 (例如私有性、名称解析、类型推断、泛型、trait实现、内部实现、一致性、模式检查等等) 。
{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[_GenericParams_]: generics.md
[_WhereClause_]: generics.md#where-clauses
[_StructFields_]: structs.md
[`transmute`]: ../../std/mem/fn.transmute.html
[`Copy`]: ../../std/marker/trait.Copy.html
[boolean type]: ../types/boolean.md
[ManuallyDrop]: ../../std/mem/struct.ManuallyDrop.html
[the C representation]: ../type-layout.md#reprc-unions
[undefined behavior]: ../behavior-considered-undefined.html
{==+==}

{==+==}