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
# 子类型化和协变

子类型化是隐式的，会在类型检查或推断阶段发生。
子类型化只限于两种情况：
类型和具有更高阶生命周期的类型之间的协变。
如果抹去类型的生命周期，则唯一的子类型是类型相等。

译注：子类型的这个概念与面向对象的特性相同，如果类型 A 是类型 B 的子类型，那么意味着 A 类型的值可以被 B 类型变量接收。
其主要的应用场景将值在不同类型间进行传递时，如果安全，不必手动转换，并且这种转换仅限于对类型生命周期的范围的调整，在这里被称为 '协变' 。

思考以下示例: 字符串字面量 "hi" 始终具有 `'static` 生命周期，仍然可以将 `s` 分配给 `t` :
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
由于 `'static` 生命周期超过了生命周期参数 `'a` ，因此 `&'static str` 是 `&'a str` 的子类型。

[高阶][Higher-ranked] 函数指针和 [trait 对象][trait objects] 有另一种子类型关系。
它们是由高阶生命周期取代所给定的类型的子类型。
一些例子：
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
// 这里将 'a 取代为 'static
let subtype: &(for<'a> fn(&'a i32) -> &'a i32) = &((|x| x) as fn(&_) -> &_);
let supertype: &(fn(&'static i32) -> &'static i32) = subtype;

// 这同样适用于 trait 对象
let subtype: &(dyn for<'a> Fn(&'a i32) -> &'a i32) = &|x| x;
let supertype: &(dyn Fn(&'static i32) -> &'static i32) = subtype;

// 还可以将一个高阶生命周期取代为另一个
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
## 协变

协变是泛型类型与其参数相关的一个特性。
泛型类型在参数上的 *协变* 性，其表示了参数的子类型化如何影响类型的子类型化的行为。

* 如果 `T` 是 `U` 的子类型，若 `F<T>` 是 *协变的* 的，意味着 `F<T>` 是 `F<U>` 的子类型 ( 子类型关系 "传递" )。
* 如果 `T` 是 `U` 的子类型，若 `F<T>` 是 *逆变的* 的，意味着 `F<U>` 是 `F<T>` 的子类型。
* 否则，`F<T>` 是 *不变的* 的，不会推导任何子类型关系。

类型的协变性可以按照以下方式自动确定：
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
| 类型                          | 在 `'a` 上协变     | 在 `T` 上协变     |
|-------------------------------|----------------|----------------|
| `&'a T`                       | 协变的           | 协变的           |
| `&'a mut T`                   | 协变的           | 不变的           |
| `*const T`                    |                  | 协变的           |
| `*mut T`                      |                  | 不变的           |
| `[T]` 和 `[T; n]`             |                  | 协变的           |
| `fn() -> T`                   |                  | 协变的           |
| `fn(T) -> ()`                 |                  | 逆变的           |
| `std::cell::UnsafeCell<T>`    |                  | 不变的           |
| `std::marker::PhantomData<T>` |                  | 协变的           |
| `dyn Trait<T> + 'a`           | 协变的           | 不变的           |
{==+==}


{==+==}
The variance of other `struct`, `enum`, and `union` types is decided by
looking at the variance of the types of their fields. If the parameter is used
in positions with different variances then the parameter is invariant. For
example the following struct is covariant in `'a` and `T` and invariant in `'b`, `'c`,
and `U`.
{==+==}
其他 `struct` 、 `enum` 和 `union` 类型的协变性是通过查看它们字段类型的协变性来决定的。
如果在不同协变位置使用了参数，则该参数是不变的。
例如，下面的结构体在 `'a` 和 `T` 上是协变的，在 `'b`、`'c` 和 `U` 上是不变的。
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
    x: &'a U,               // 这使得 `Variance` 在 'a 上是协变的，也会
                            // 使得在 U 上是协变的，但 U 在后面使用了
    y: *const T,            // 在 T 上是协变的
    z: UnsafeCell<&'b f64>, // 在 'b 上是不变的
    w: *mut U,              // 在 U 上是不变的，使整个结构体不变

    f: fn(&'c ()) -> &'c () // 同时是逆变和协变的，在结构体中使 'c 不变
}
```
{==+==}


{==+==}
When used outside of an `struct`, `enum`, or `union`, the variance for parameters is checked at each location separately.
{==+==}
当在 `struct` 、 `enum` 或 `union` 之外使用时，参数的协变性在每个位置上分别进行检查。
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
    // `'long` 在元组中同时用于协变位置和不变位置。
    x: (&'long u32, UnsafeCell<&'long u32>),
) {
    // 因为这些位置上的协变性是分别计算的，
    // 所以我们可以在协变位置上自由缩小 'long。
    let _: (&'short u32, UnsafeCell<&'long u32>) = x;
}

fn takes_fn_ptr<'short, 'middle: 'short>(
    // `'middle` 在一个协变位置和一个逆变位置上同时使用。
    f: fn(&'middle ()) -> &'middle (),
) {
    // 因为这些位置上的协变性是分别计算的，
    // 所以我们可以在协变位置上自由缩小 'middle，
    // 在逆变位置上扩展它。
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