{==+==}
# Type coercions

**Type coercions** are implicit operations that change the type of a value.
They happen automatically at specific locations and are highly restricted in
what types actually coerce.

Any conversions allowed by coercion can also be explicitly performed by the
[type cast operator], `as`.

Coercions are originally defined in [RFC 401] and expanded upon in [RFC 1558].
{==+==}
# 类型强转

**类型强转** 是隐式操作，可以更改值的类型。它们在特定位置自动发生，并且高度限制实际强转的类型。
由类型强转允许的任何转换也可以通过 [类型转换运算符][type cast operator] `as` 显式执行。
强制转换最初在 [RFC 401] 中定义，并在 [RFC 1558] 中进行了扩展。
{==+==}


{==+==}
## Coercion sites

A coercion can only occur at certain coercion sites in a program; these are
typically places where the desired type is explicit or can be derived by
propagation from explicit types (without type inference). Possible coercion
sites are:

* `let` statements where an explicit type is given.

   For example, `&mut 42` is coerced to have type `&i8` in the following:

   ```rust
   let _: &i8 = &mut 42;
   ```

* `static` and `const` item declarations (similar to `let` statements).

* Arguments for function calls

  The value being coerced is the actual parameter, and it is coerced to
  the type of the formal parameter.

  For example, `&mut 42` is coerced to have type `&i8` in the following:
{==+==}
## 强转位置

强转只能发生在程序的某些位置；通常这些位置上，期望的类型是显式或可以从显式类型(不需要类型推断)中衍生。可能的强制转换位置包括：

* `let` 语句中给出显式类型的位置。
   例如，在以下代码中， `&mut 42` 被强转为类型 `&i8` :

   ```rust
   let _: &i8 = &mut 42;
   ```

* `static` 和 `const` 条目的声明 (其类似于 `let` 语句) 。

* 函数调用的参数
  被强制转换的值是实参，将其强转为形参的类型。

  例如，在以下代码中，`&mut 42` 被强转为类型 `&i8` :
{==+==}


{==+==}
  ```rust
  fn bar(_: &i8) { }

  fn main() {
      bar(&mut 42);
  }
  ```
{==+==}

{==+==}


{==+==}
  For method calls, the receiver (`self` parameter) can only take advantage
  of [unsized coercions](#unsized-coercions).

* Instantiations of struct, union, or enum variant fields

  For example, `&mut 42` is coerced to have type `&i8` in the following:
{==+==}
  在方法调用中，接收器 (`self` 参数) 只能利用 [非定长强转](#unsized-coercions) 。

* 结构体、联合体或枚举变体字段的实例化

  例如，在以下示例中， `&mut 42` 被强转为类型 `&i8` ：
{==+==}


{==+==}
  ```rust
  struct Foo<'a> { x: &'a i8 }

  fn main() {
      Foo { x: &mut 42 };
  }
  ```
{==+==}

{==+==}


{==+==}
* Function results&mdash;either the final line of a block if it is not
  semicolon-terminated or any expression in a `return` statement

  For example, `x` is coerced to have type `&dyn Display` in the following:
{==+==}
* 函数结果，可以是块的最后一行，如果它不以分号结尾，或者是 `return` 语句中的任何表达式。

  例如，在以下示例中， `x` 被强转为类型 `&dyn Display` ：
{==+==}


{==+==}
  ```rust
  use std::fmt::Display;
  fn foo(x: &u32) -> &dyn Display {
      x
  }
  ```
{==+==}

{==+==}


{==+==}
If the expression in one of these coercion sites is a coercion-propagating
expression, then the relevant sub-expressions in that expression are also
coercion sites. Propagation recurses from these new coercion sites.
Propagating expressions and their relevant sub-expressions are:

* Array literals, where the array has type `[U; n]`. Each sub-expression in
the array literal is a coercion site for coercion to type `U`.

* Array literals with repeating syntax, where the array has type `[U; n]`. The
repeated sub-expression is a coercion site for coercion to type `U`.

* Tuples, where a tuple is a coercion site to type `(U_0, U_1, ..., U_n)`.
Each sub-expression is a coercion site to the respective type, e.g. the
zeroth sub-expression is a coercion site to type `U_0`.

* Parenthesized sub-expressions (`(e)`): if the expression has type `U`, then
the sub-expression is a coercion site to `U`.

* Blocks: if a block has type `U`, then the last expression in the block (if
it is not semicolon-terminated) is a coercion site to `U`. This includes
blocks which are part of control flow statements, such as `if`/`else`, if
the block has a known type.
{==+==}
如果在这些类型转换位置之一的表达式是一个类型转换传导表达式，那么该表达式中的相关子表达式也是类型转换位置。
传导从这些新的类型转换位置递归进行。传导表达式及其相关子表达式包括:

* 数组字面值，数组类型为 `[U; n]` 。数组字面值中的每个子表达式都是类型强转到类型 `U` 的强转位置。

* 带有重复语法的数组字面值，数组类型为 `[U; n]` 。重复的子表达式是类型强转到类型 `U` 的强转位置。

* 元组，其中元组是类型强转到类型 `(U_0, U_1, ..., U_n)` 的强转位置。每个子表达式都是相应类型的类型转换位置，例如，第零个子表达式是类型强转到类型 `U_0` 的强转位置。

* 括号中的子表达式 (`(e)`) : 如果表达式的类型是 `U` ，那么子表达式就是类型强转到 `U` 的强转位置。

* 块: 如果块的类型是 `U` ，则块中的最后一个表达式 (如果它没有以分号结尾) 是类型强转到 `U` 的强转位置。
这包括控制流语句中的块，例如 `if`/`else` ，如果块具有已知类型的话。
{==+==}


{==+==}
## Coercion types

Coercion is allowed between the following types:

* `T` to `U` if `T` is a [subtype] of `U` (*reflexive case*)

* `T_1` to `T_3` where `T_1` coerces to `T_2` and `T_2` coerces to `T_3`
(*transitive case*)

    Note that this is not fully supported yet.

* `&mut T` to `&T`

* `*mut T` to `*const T`

* `&T` to `*const T`

* `&mut T` to `*mut T`

* `&T` or `&mut T` to `&U` if `T` implements `Deref<Target = U>`. For example:
{==+==}
## 强制类型转换

以下类型之间允许进行强制类型转换:

* 如果 `T` 是 `U` 的 [亚类型][subtype] ，则 `T` 可以强转为 `U` (*自反性的情况*)

* 当 `T_1` 转换为 `T_2` ， `T_2` 转换为 `T_3` 时，`T_1` 可以转换为 `T_3` (*可传递的情况*)

    注意，这种情况目前尚未完全支持。

* `&mut T` 可以强转为 `&T`

* `*mut T` 可以强转为 `*const T`

* `&T` 可以强转为 `*const T`

* `&mut T` 可以强转为 `*mut T`

* 如果 `T` 实现了 `Deref<Target = U>` ，则 `&T` 或 `&mut T` 可以强转为 `&U` 。例如:
{==+==}


{==+==}
  ```rust
  use std::ops::Deref;

  struct CharContainer {
      value: char,
  }

  impl Deref for CharContainer {
      type Target = char;

      fn deref<'a>(&'a self) -> &'a char {
          &self.value
      }
  }

  fn foo(arg: &char) {}

  fn main() {
      let x = &mut CharContainer { value: 'y' };
      foo(x); //&mut CharContainer is coerced to &char.
  }
  ```
{==+==}
  ```rust
  use std::ops::Deref;

  struct CharContainer {
      value: char,
  }

  impl Deref for CharContainer {
      type Target = char;

      fn deref<'a>(&'a self) -> &'a char {
          &self.value
      }
  }

  fn foo(arg: &char) {}

  fn main() {
      let x = &mut CharContainer { value: 'y' };
      foo(x); //&mut `CharContainer` 被强转为 &char 。
  }
  ```
{==+==}


{==+==}
* `&mut T` to `&mut U` if `T` implements `DerefMut<Target = U>`.

* TyCtor(`T`) to TyCtor(`U`), where TyCtor(`T`) is one of
    - `&T`
    - `&mut T`
    - `*const T`
    - `*mut T`
    - `Box<T>`

    and where `U` can be obtained from `T` by [unsized coercion](#unsized-coercions).

    <!--In the future, coerce_inner will be recursively extended to tuples and
    structs. In addition, coercions from subtraits to supertraits will be
    added. See [RFC 401] for more details.-->

* Function item types to `fn` pointers

* Non capturing closures to `fn` pointers

* `!` to any `T`
{==+==}
* 如果 `T` 实现了 `DerefMut<Target = U>`，则 `&mut T` 可以强转为 `&mut U`。

* TyCtor(`T`) 可以强转为 TyCtor(`U`) ，其中 TyCtor(`T`) 是以下之一：
    - `&T`
    - `&mut T`
    - `*const T`
    - `*mut T`
    - `Box<T>`

    而且 `U` 可以通过 [不定大小强转](#unsized-coercions) 从 `T` 中获得。

    <!-- 在未来，coerce_inner 将递归地扩展到元组和结构体。
    此外，将添加从子 trait 到超 trait 的强转。
    有关更多详细信息，请参见 [RFC 401]。-->
    
* 函数条目类型到 `fn` 指针

* 非捕获闭包到 `fn` 指针

* `!` 到任何 `T` 
{==+==}


{==+==}
### Unsized Coercions

The following coercions are called `unsized coercions`, since they
relate to converting sized types to unsized types, and are permitted in a few
cases where other coercions are not, as described above. They can still happen
anywhere else a coercion can occur.

Two traits, [`Unsize`] and [`CoerceUnsized`], are used
to assist in this process and expose it for library use. The following
coercions are built-ins and, if `T` can be coerced to `U` with one of them, then
an implementation of `Unsize<U>` for `T` will be provided:

* `[T; n]` to `[T]`.

* `T` to `dyn U`, when `T` implements `U + Sized`, and `U` is [object safe].

* `Foo<..., T, ...>` to `Foo<..., U, ...>`, when:
    * `Foo` is a struct.
    * `T` implements `Unsize<U>`.
    * The last field of `Foo` has a type involving `T`.
    * If that field has type `Bar<T>`, then `Bar<T>` implements `Unsized<Bar<U>>`.
    * T is not part of the type of any other fields.

Additionally, a type `Foo<T>` can implement `CoerceUnsized<Foo<U>>` when `T`
implements `Unsize<U>` or `CoerceUnsized<Foo<U>>`. This allows it to provide a
unsized coercion to `Foo<U>`.

> Note: While the definition of the unsized coercions and their implementation
> has been stabilized, the traits themselves are not yet stable and therefore
> can't be used directly in stable Rust.
{==+==}
### 不定大小强转

下列类型强转被称为 `不定大小强转` ，因为它们涉及将有大小限制的类型转换为无大小限制的类型，并且在一些其他强转不允许的情况下被允许，如上所述。
它们仍然可以发生在强转可以发生的任何地方。两个 trait [`Unsize`] 和 [`CoerceUnsized`] 用于协助此过程并将其公开供库使用。
下列强转是内置的，如果 `T` 可以通过其中之一转换为 `U` ，则将为 `T` 提供一个实现 `Unsize<U>` 的实现：

* `[T; n]` 转换为 `[T]` 。

* 当 `T` 实现 `U + Sized` 时， `T` 转换为 `dyn U` ，而 `U` 是 [对象安全][object safe] 的。

* 当满足以下条件时， `Foo<..., T, ...>` 转换为 `Foo<..., U, ...>` ：
    * `Foo` 是一个结构体。
    * `T` 实现了 `Unsize<U>` 。
    * `Foo` 的最后一个字段的类型涉及到了 `T` 。
    * 如果该字段的类型为 `Bar<T>` ，则 `Bar<T>` 实现了 `Unsized<Bar<U>>` 。
    * `T` 不是任何其他字段类型的一部分。

此外，当 `T` 实现了 `Unsize<U>` 或 `CoerceUnsized<Foo<U>>` 时，类型 `Foo<T>` 可以实现 `CoerceUnsized<Foo<U>>`。这使它可以提供到 `Foo<U>` 的不定大小的强转。

> 注意：尽管已经稳定化了不定大小的强转的定义及其实现，但这些特性本身尚未稳定，因此不能在稳定的 Rust 中直接使用它们。
{==+==}


{==+==}
## Least upper bound coercions

In some contexts, the compiler must coerce together multiple types to try and
find the most general type. This is called a "Least Upper Bound" coercion.
LUB coercion is used and only used in the following situations:

+ To find the common type for a series of if branches.
+ To find the common type for a series of match arms.
+ To find the common type for array elements.
+ To find the type for the return type of a closure with multiple return statements.
+ To check the type for the return type of a function with multiple return statements.

In each such case, there are a set of types `T0..Tn` to be mutually coerced
to some target type `T_t`, which is unknown to start. Computing the LUB
coercion is done iteratively. The target type `T_t` begins as the type `T0`.
For each new type `Ti`, we consider whether

+ If `Ti` can be coerced to the current target type `T_t`, then no change is made.
+ Otherwise, check whether `T_t` can be coerced to `Ti`; if so, the `T_t` is
changed to `Ti`. (This check is also conditioned on whether all of the source
expressions considered thus far have implicit coercions.)
+ If not, try to compute a mutual supertype of `T_t` and `Ti`, which will become the new target type.
{==+==}
## 最小上界强转

在某些情况下，编译器必须将多个类型强转在一起，以尝试找到最通用的类型。
这被称为 "最小上界强转" (Least Upper Bound coercion，简称LUB 强转) 。LUB 强转仅在以下情况下使用：

+ 为一系列 if 分支查找公共类型。
+ 为一系列 match 分支查找公共类型。
+ 为数组元素查找公共类型。
+ 为带有多个返回语句的闭包查找返回类型。
+ 检查带有多个返回语句的函数的返回类型。

在每种情况下，存在一组要相互强转为某个目标类型 `T_t` 的类型 `T0..Tn` ，该目标类型最初是未知的。
计算 LUB 强转是通过迭代完成的。目标类型 `T_t` 开始是类型 `T0` 。对于每个新类型 `Ti` ，我们考虑以下内容：

+ 如果 `Ti` 可以强转为当前目标类型 `T_t` ，则不进行任何更改。
+ 否则，检查是否可以将 `T_t` 强转为 `Ti` ；如果可以，则将 `T_t` 更改为 `Ti` 。 (此检查还取决于迄今为止考虑的所有源表达式是否具有隐式强转。)
+ 如果不行，则尝试计算 `T_t` 和 `Ti` 的共同超类，该超类将成为新的目标类型。
{==+==}


{==+==}
### Examples:

```rust
# let (a, b, c) = (0, 1, 2);
// For if branches
let bar = if true {
    a
} else if false {
    b
} else {
    c
};

// For match arms
let baw = match 42 {
    0 => a,
    1 => b,
    _ => c,
};

// For array elements
let bax = [a, b, c];

// For closure with multiple return statements
let clo = || {
    if true {
        a
    } else if false {
        b
    } else {
        c
    }
};
let baz = clo();

// For type checking of function with multiple return statements
fn foo() -> i32 {
    let (a, b, c) = (0, 1, 2);
    match 42 {
        0 => a,
        1 => b,
        _ => c,
    }
}
```
{==+==}
### 例如:

```rust
# let (a, b, c) = (0, 1, 2);
// 用于 if 条件分支
let bar = if true {
    a
} else if false {
    b
} else {
    c
};

// 用于 match 匹配分支
let baw = match 42 {
    0 => a,
    1 => b,
    _ => c,
};

// 用于数组元素
let bax = [a, b, c];

// 用于多个返回语句的闭包
let clo = || {
    if true {
        a
    } else if false {
        b
    } else {
        c
    }
};
let baz = clo();

// 用于多个返回语句的函数类型检查
fn foo() -> i32 {
    let (a, b, c) = (0, 1, 2);
    match 42 {
        0 => a,
        1 => b,
        _ => c,
    }
}
```
{==+==}


{==+==}
In these examples, types of the `ba*` are found by LUB coercion. And the
compiler checks whether LUB coercion result of `a`, `b`, `c` is `i32` in the
processing of the function `foo`.

### Caveat

This description is obviously informal. Making it more precise is expected to
proceed as part of a general effort to specify the Rust type checker more
precisely.
{==+==}
在这些示例中， `ba*` 的类型是通过最小上界 LUB 强转来确定的。编译器在处理函数 `foo` 时检查 `a` ， `b` ， `c` 的 LUB 强转结果是否为 `i32` 。

### 注意事项

这个描述显然是非正式的。更精确的描述将作为规范 Rust 类型检查器的泛型尝试的一部分进行。
{==+==}


{==+==}
[RFC 401]: https://github.com/rust-lang/rfcs/blob/master/text/0401-coercions.md
[RFC 1558]: https://github.com/rust-lang/rfcs/blob/master/text/1558-closure-to-fn-coercion.md
[subtype]: subtyping.md
[object safe]: items/traits.md#object-safety
[type cast operator]: expressions/operator-expr.md#type-cast-expressions
[`Unsize`]: ../std/marker/trait.Unsize.html
[`CoerceUnsized`]: ../std/ops/trait.CoerceUnsized.html
{==+==}

{==+==}