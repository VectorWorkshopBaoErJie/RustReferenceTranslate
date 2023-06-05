{==+==}
# Function item types

When referred to, a function item, or the constructor of a tuple-like struct or
enum variant, yields a zero-sized value of its _function item type_. That type
explicitly identifies the function - its name, its type arguments, and its
early-bound lifetime arguments (but not its late-bound lifetime arguments,
which are only assigned when the function is called) - so the value does not
need to contain an actual function pointer, and no indirection is needed when
the function is called.

There is no syntax that directly refers to a function item type, but the
compiler will display the type as something like `fn(u32) -> i32 {fn_name}` in
error messages.

Because the function item type explicitly identifies the function, the item
types of different functions - different items, or the same item with different
generics - are distinct, and mixing them will create a type error:
{==+==}
# 函数条目类型

引用函数条目或类似元组结构或枚举变量的构造函数时，会生成其 _函数条目类型_ 的大小为零的值。
该类型明确标识函数 - 其名称、类型参数和其提前绑定的生命周期参数 (但不包括其延迟绑定的生命周期参数，这些参数仅在调用函数时分配) - 因此该值不需要包含实际的函数指针，并且调用函数时不需要进行间接寻址。

没有直接引用函数条目类型的语法，但编译器在错误消息中将显示类型为类似于 `fn(u32) -> i32 {fn_name}` 的类型。

因为函数条目类型明确标识函数，不同函数的条目类型 - 不同的条目，或者相同条目的不同泛型 - 是不同的，混合它们会产生类型错误:
{==+==}


{==+==}
```rust,compile_fail,E0308
fn foo<T>() { }
let x = &mut foo::<i32>;
*x = foo::<u32>; //~ ERROR mismatched types
```
{==+==}
```rust,compile_fail,E0308
fn foo<T>() { }
let x = &mut foo::<i32>;
*x = foo::<u32>; //~ ERROR 类型不匹配
```
{==+==}


{==+==}
However, there is a [coercion] from function items to [function pointers] with
the same signature, which is triggered not only when a function item is used
when a function pointer is directly expected, but also when different function
item types with the same signature meet in different arms of the same `if` or
`match`:
{==+==}
然而，从具有相同签名的函数条目到 [函数指针][function pointers] 存在 [强制转换][coercion] ，当期望直接使用函数指针时，会被触发。
当在同一 `if` 或 `match` 的不同分支中遇到具有相同签名的不同函数条目类型时，也会触发这种转换:
{==+==}


{==+==}
```rust
# let want_i32 = false;
# fn foo<T>() { }

// `foo_ptr_1` has function pointer type `fn()` here
let foo_ptr_1: fn() = foo::<i32>;

// ... and so does `foo_ptr_2` - this type-checks.
let foo_ptr_2 = if want_i32 {
    foo::<i32>
} else {
    foo::<u32>
};
```
{==+==}
```rust
# let want_i32 = false;
# fn foo<T>() { }

// 在这里，`foo_ptr_1` 具有函数指针类型 `fn()`
let foo_ptr_1: fn() = foo::<i32>;

// ... `foo_ptr_2` 也具有相同的函数指针类型 - 这样类型检查通过了
let foo_ptr_2 = if want_i32 {
    foo::<i32>
} else {
    foo::<u32>
};
```
{==+==}


{==+==}
All function items implement [`Fn`], [`FnMut`], [`FnOnce`], [`Copy`],
[`Clone`], [`Send`], and [`Sync`].
{==+==}
所有的函数条目都实现了 [`Fn`] , [`FnMut`] , [`FnOnce`] , [`Copy`] , [`Clone`] , [`Send`] 和 [`Sync`] 。
{==+==}


{==+==}
[`Clone`]: ../special-types-and-traits.md#clone
[`Copy`]: ../special-types-and-traits.md#copy
[`FnMut`]: ../../std/ops/trait.FnMut.html
[`FnOnce`]: ../../std/ops/trait.FnOnce.html
[`Fn`]: ../../std/ops/trait.Fn.html
[`Send`]: ../special-types-and-traits.md#send
[`Sync`]: ../special-types-and-traits.md#sync
[coercion]: ../type-coercions.md
[function pointers]: function-pointer.md
{==+==}

{==+==}