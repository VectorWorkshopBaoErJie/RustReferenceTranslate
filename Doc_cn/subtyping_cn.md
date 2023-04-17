{==+==}
# Subtyping and Variance

Subtyping is implicit and can occur at any stage in type checking or
inference. Subtyping is restricted to two cases:
variance with respect to lifetimes and between types with higher ranked
lifetimes. If we were to erase lifetimes from types, then the only subtyping
would be due to type equality.

Consider the following example: string literals always have `'static`
lifetime. Nevertheless, we can assign `s` to `t`:
{==+==}
# 子类型和协变性

子类型是隐式的，并且可以在类型检查或推断的任意阶段产生。
子类型仅限于两种情况:
与生命周期相关的协变性和具有更高层生命周期的类型之间的协变性。
如果我们从类型中抹去生命周期，那么唯一的子类型就是由于类型相等而产生的。

考虑以下示例: 字符串字面值始终具有 `'static` 生命周期。尽管如此，我们可以将 `s` 分配给 `t` :
{==+==}


{==+==}
```rust
fn bar<'a>() {
    let s: &'static str = "hi";
    let t: &'a str = s;
}
```
{==+==}

{==+==}


{==+==}
Since `'static` outlives the lifetime parameter `'a`, `&'static str` is a
subtype of `&'a str`.

[Higher-ranked]&#32;[function pointers] and [trait objects] have another
subtype relation. They are subtypes of types that are given by substitutions of
the higher-ranked lifetimes. Some examples:
{==+==}
由于 `'static` 寿命超过了生命周期参数 `'a`，因此 `&'static str` 是 `&'a str` 的子类型。

[高阶][Higher-ranked] 函数指针和 [trait 对象][trait objects] 具有另一种子类型关系。
它们是由更高层生命周期的替换所给出的类型的子类型。以下是一些例子:
{==+==}


{==+==}
```rust
// Here 'a is substituted for 'static
let subtype: &(for<'a> fn(&'a i32) -> &'a i32) = &((|x| x) as fn(&_) -> &_);
let supertype: &(fn(&'static i32) -> &'static i32) = subtype;

// This works similarly for trait objects
let subtype: &(dyn for<'a> Fn(&'a i32) -> &'a i32) = &|x| x;
let supertype: &(dyn Fn(&'static i32) -> &'static i32) = subtype;

// We can also substitute one higher-ranked lifetime for another
let subtype: &(for<'a, 'b> fn(&'a i32, &'b i32))= &((|x, y| {}) as fn(&_, &_));
let supertype: &for<'c> fn(&'c i32, &'c i32) = subtype;
```
{==+==}
```rust
// 这里将 'a 替换为 'static
let subtype: &(for<'a> fn(&'a i32) -> &'a i32) = &((|x| x) as fn(&_) -> &_);
let supertype: &(fn(&'static i32) -> &'static i32) = subtype;

// 这同样适用于 trait 对象
let subtype: &(dyn for<'a> Fn(&'a i32) -> &'a i32) = &|x| x;
let supertype: &(dyn Fn(&'static i32) -> &'static i32) = subtype;

// 我们还可以将一个高层生命周期替换为另一个
let subtype: &(for<'a, 'b> fn(&'a i32, &'b i32))= &((|x, y| {}) as fn(&_, &_));
let supertype: &for<'c> fn(&'c i32, &'c i32) = subtype;
```
{==+==}


{==+==}
## Variance

Variance is a property that generic types have with respect to their arguments.
A generic type's *variance* in a parameter is how the subtyping of the
parameter affects the subtyping of the type.

* `F<T>` is *covariant* over `T` if `T` being a subtype of `U` implies that
  `F<T>` is a subtype of `F<U>` (subtyping "passes through")
* `F<T>` is *contravariant* over `T` if `T` being a subtype of `U` implies that
  `F<U>` is a subtype of `F<T>`
* `F<T>` is *invariant* over `T` otherwise (no subtyping relation can be
  derived)

Variance of types is automatically determined as follows
{==+==}
## 协变性

协变性是泛型类型相对于其参数具有的属性。泛型类型在参数上的 *协变性* 是参数的子类型化如何影响类型的子类型化。

* 如果 `T` 是 `U` 的子类型，那么 `F<T>` 是 `F<U>` 的子类型，`F<T>` 对 `T` 是 *协变的* (covariant) ( "通过" 子类型化)
* 如果 `T` 是 `U` 的子类型，那么 `F<U>` 是 `F<T>` 的子类型，`F<T>` 对 `T` 是 *逆变的* (contravariant)
* 否则，`F<T>` 对 `T` 是 *不变的* (invariant) (不能推导出子类型关系)

类型的协变性是按以下方式自动确定的:
{==+==}


{==+==}
| Type                          | Variance in `'a`  | Variance in `T`   |
|-------------------------------|-------------------|-------------------|
| `&'a T`                       | covariant         | covariant         |
| `&'a mut T`                   | covariant         | invariant         |
| `*const T`                    |                   | covariant         |
| `*mut T`                      |                   | invariant         |
| `[T]` and `[T; n]`            |                   | covariant         |
| `fn() -> T`                   |                   | covariant         |
| `fn(T) -> ()`                 |                   | contravariant     |
| `std::cell::UnsafeCell<T>`    |                   | invariant         |
| `std::marker::PhantomData<T>` |                   | covariant         |
| `dyn Trait<T> + 'a`           | covariant         | invariant         |
{==+==}


| 类型                          | `'a` 的变化     | `T` 的变化     |
|-------------------------------|----------------|----------------|
| `&'a T`                       | 协变           | 协变           |
| `&'a mut T`                   | 协变           | 不变           |
| `*const T`                    |                | 协变           |
| `*mut T`                      |                | 不变           |
| `[T]` 和 `[T; n]`             |                | 协变           |
| `fn() -> T`                   |                | 协变           |
| `fn(T) -> ()`                 |                | 逆变           |
| `std::cell::UnsafeCell<T>`    |                | 不变           |
| `std::marker::PhantomData<T>` |                | 协变           |
| `dyn Trait<T> + 'a`           | 协变           | 不变           |
{==+==}


{==+==}
The variance of other `struct`, `enum`, and `union` types is decided by
looking at the variance of the types of their fields. If the parameter is used
in positions with different variances then the parameter is invariant. For
example the following struct is covariant in `'a` and `T` and invariant in `'b`, `'c`,
and `U`.
{==+==}
其他的 `struct` ， `enum` 和 `union` 类型的协变性由它们字段的类型的协变性来决定。
如果参数在不同协变性的位置被使用，则参数是不变的。例如，以下的 `struct` 在 `'a` 和 `T` 上是协变的，在 `'b` ， `'c` 和 `U` 上是不变的。
{==+==}


{==+==}
```rust
use std::cell::UnsafeCell;
struct Variance<'a, 'b, 'c, T, U: 'a> {
    x: &'a U,               // This makes `Variance` covariant in 'a, and would
                            // make it covariant in U, but U is used later
    y: *const T,            // Covariant in T
    z: UnsafeCell<&'b f64>, // Invariant in 'b
    w: *mut U,              // Invariant in U, makes the whole struct invariant

    f: fn(&'c ()) -> &'c () // Both co- and contravariant, makes 'c invariant
                            // in the struct.
}
```
{==+==}
```rust
use std::cell::UnsafeCell;
struct Variance<'a, 'b, 'c, T, U: 'a> {
    x: &'a U,               // 这使得 'a 上的 `Variance` 逆变，也使 U 上的 `Variance` 逆变，但是 U 在后面被使用了，所以它的 `Variance` 是不确定的。
    y: *const T,            // T 上的 `Variance` 是协变的
    z: UnsafeCell<&'b f64>, // 'b 上的 `Variance` 是不变的
    w: *mut U,              // U 上的 `Variance` 是不变的，使整个结构体是不变的。

    f: fn(&'c ()) -> &'c () // 在协变和逆变方向上都是的，使得 'c 在结构体中是不变的。
}
```
{==+==}


{==+==}
When used outside of an `struct`, `enum`, or `union`, the variance for parameters is checked at each location separately.
{==+==}
当在 `struct`  `enum` 或 `union` 之外使用参数时，每个位置的参数的协变性是单独检查的。
{==+==}


{==+==}
```rust
# use std::cell::UnsafeCell;
fn generic_tuple<'short, 'long: 'short>(
    // 'long is used inside of a tuple in both a co- and invariant position.
    x: (&'long u32, UnsafeCell<&'long u32>),
) {
    // As the variance at these positions is computed separately,
    // we can freely shrink 'long in the covariant position.
    let _: (&'short u32, UnsafeCell<&'long u32>) = x;
}

fn takes_fn_ptr<'short, 'middle: 'short>(
    // 'middle is used in both a co- and contravariant position.
    f: fn(&'middle ()) -> &'middle (),
) {
    // As the variance at these positions is computed separately,
    // we can freely shrink 'middle in the covariant position
    // and extend it in the contravariant position.
    let _: fn(&'static ()) -> &'short () = f;
}
```
{==+==}
```rust
# use std::cell::UnsafeCell;
fn generic_tuple<'short, 'long: 'short>(
    // `'long` 在元组中同时用于协变和不变位置。
    x: (&'long u32, UnsafeCell<&'long u32>),
) {
    // 因为这些位置上的 variance 是单独计算的，
    // 所以我们可以在协变位置上自由缩小 'long 生命周期。
    let _: (&'short u32, UnsafeCell<&'long u32>) = x;
}

fn takes_fn_ptr<'short, 'middle: 'short>(
    // 'middle 生命周期在协变和逆变位置上都被使用。
    f: fn(&'middle ()) -> &'middle (),
) {
    // 因为这些位置上的 variance 是单独计算的，
    // 所以我们可以在协变位置上自由缩小 'middle 生命周期，
    // 在逆变位置上自由扩展 'middle 生命周期。
    let _: fn(&'static ()) -> &'short () = f;
}
```
{==+==}


{==+==}
[function pointers]: types/function-pointer.md
[Higher-ranked]: ../nomicon/hrtb.html
[trait objects]: types/trait-object.md
{==+==}

{==+==}