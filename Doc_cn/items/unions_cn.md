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
联合体与结构体声明语法相同，只是用 `union` 代替了 `struct` 。
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
联合体的关键属性是所有字段都共享共用的存储空间。
对联合体一个字段的写入可以覆盖其他字段，其联合体的大小是由最大字段的大小决定的。
{==+==}


{==+==}
Union field types are restricted to the following subset of types:
- `Copy` types
- References (`&T` and `&mut T` for arbitrary `T`)
- `ManuallyDrop<T>` (for arbitrary `T`)
- Tuples and arrays containing only allowed union field types
{==+==}
联合字段类型限制在以下类型的子集:
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
这一限制确保，在特定情况下，联合体的字段决不需要丢弃。
与结构体和枚举相同，允许为联合体添加 `impl Drop` ，手动定义丢弃时的行为。
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
联合体类型值的创建可使用与结构体类型相同的语法，但必须明确指定一个字段:
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
上面的表达式创建了一个 `MyUnion` 类型的值，并使用字段 `f1`初始化了存储。
可以使用与结构体字段相同的语法来访问联合体。
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
联合体没有 "活动字段 "的概念。每个联合体的访问仅解释为当前访问的字段类型的存储。
读取一个联合体字段时，按字段类型读取联合体位。
字段可能有一个非零的偏移量 (除了在使用 [C表示法][the C representation] 时)；在这种情况，从字段偏移量开始的位读取。
程序员有责任确保字段类型上的数据是有效的，否则，会导致 [undefined behavior] "未定义的行为"。
比如，通过一个 [布尔类型][boolean type] 的字段读取值 `3` 是未定义的行为。
实际上，用 [C表示法][the C representation] 向联合体写入然后从联合体中读出，将是从写入类型到读取类型的 [`transmute`] "质变"。
{==+==}


{==+==}
Consequently, all reads of union fields have to be placed in `unsafe` blocks:
{==+==}
因此，所有对联合字段的读取都必须放在 `unsafe` 块中:
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
通常，使用联合体的代码将为不安全的联合体字段访问提供安全包装。
{==+==}


{==+==}
In contrast, writes to union fields are safe, since they just overwrite
arbitrary data, but cannot cause undefined behavior. (Note that union field
types can never have drop glue, so a union field write will never implicitly
drop anything.)
{==+==}
相比之下，对联合字段的写入是安全的，因为仅覆盖了数据，但不能引起未定义的行为。
(请注意，联合体字段类型永远不能有粘联的丢弃，所以联合字段的写入将不会有隐式丢弃。)
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
另一种访问联合体字段的方法是使用模式匹配。
联合体字段的模式匹配与结构体的模式语法相同，只是模式必须明确指定字段。
由于模式匹配按特定字段读取联合体，必须放在 `unsafe` 块中。
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
模式匹配可以将联合体作为更大结构的一个字段来匹配。
特别是，当通过 FFI 实现 C 标记的 Rust 联合体，允许同时对标记和相应的字段进行匹配:
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
## 联盟字段的引用
{==+==}


{==+==}
Since union fields share common storage, gaining write access to one field of a
union can give write access to all its remaining fields. Borrow checking rules
have to be adjusted to account for this fact. As a result, if one field of a
union is borrowed, all its remaining fields are borrowed as well for the same
lifetime.
{==+==}
由于联合体字段共享存储空间，获得联合体某个字段的写入权限可以获得其余字段的写入权限。
借用检查规则检测这一情况，如果联合体的某个字段被借用，其余字段也会被借用，而且是在同一生命周期内。  
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
正如你所看到的，在许多方面 (除了布局、安全性和所有权)，联合体的行为与结构体完全一样，主要是继承了结构体的语法。
对于Rust语言中许多未被提及的方面也是如此 (比如私有性、名称解析、类型推理、泛型、trait实现、内部实现、一致性、模式检查等等)。
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