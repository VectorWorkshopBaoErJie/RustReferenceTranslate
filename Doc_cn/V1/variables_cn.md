{==+==}
# Variables

A _variable_ is a component of a stack frame, either a named function parameter,
an anonymous [temporary](expressions.md#temporaries), or a named local
variable.

A _local variable_ (or *stack-local* allocation) holds a value directly,
allocated within the stack's memory. The value is a part of the stack frame.

Local variables are immutable unless declared otherwise. For example:
`let mut x = ...`.

Function parameters are immutable unless declared with `mut`. The `mut` keyword
applies only to the following parameter. For example: `|mut x, y|` and
`fn f(mut x: Box<i32>, y: Box<i32>)` declare one mutable variable `x` and one
immutable variable `y`.

Local variables are not initialized when allocated. Instead, the entire frame
worth of local variables are allocated, on frame-entry, in an uninitialized
state. Subsequent statements within a function may or may not initialize the
local variables. Local variables can be used only after they have been
initialized through all reachable control flow paths.

In this next example, `init_after_if` is initialized after the [`if` expression]
while `uninit_after_if` is not because it is not initialized in the `else` case.
{==+==}
# 变量

_变量_ 是栈帧的组成部分，包括函数的具名参数、匿名的 [临时变量](expressions.md#temporaries) 或具名的局部变量。

_局部变量_ (或称为 *栈局部变量* 分配) 将直接持有一个值，该值在内存栈中分配。该值是栈帧的一部分。

除非另有声明，否则局部变量是不可变的。例如: `let mut x = ...` 。

除非声明为 `mut` ，函数参数是不可变的，`mut` 关键字仅适用于其后面的参数。
例如: `|mut x, y|` 和 `fn f(mut x: Box<i32>, y: Box<i32>)` 声明了可变变量 `x` 和不可变变量 `y`。

局部变量在分配时不会初始化。整个栈帧的局部变量在进入栈帧时以未初始化状态进行分配。
函数内的语句有可能会或不会初始化局部变量。只有在通过所有可达控制流路径初始化后，局部变量才能被使用。

译注: 栈帧是指对一个函数所使用的局部变量在内存栈上的一次分配操作。

在下面的例子中，`init_after_if` 在 [`if` 表达式][`if` expression] 后被初始化，而 `uninit_after_if` 没有被初始化，因为它在 `else` 分支中没有被初始化。
{==+==}


{==+==}
```rust
# fn random_bool() -> bool { true }
fn initialization_example() {
    let init_after_if: ();
    let uninit_after_if: ();

    if random_bool() {
        init_after_if = ();
        uninit_after_if = ();
    } else {
        init_after_if = ();
    }

    init_after_if; // ok
    // uninit_after_if; // err: use of possibly uninitialized `uninit_after_if`
}
```
{==+==}
```rust
# fn random_bool() -> bool { true }
fn initialization_example() {
    let init_after_if: ();
    let uninit_after_if: ();

    if random_bool() {
        init_after_if = ();
        uninit_after_if = ();
    } else {
        init_after_if = ();
    }

    init_after_if; // ok
    // uninit_after_if; // err: 使用可能未初始化 `uninit_after_if`
}
```
{==+==}


{==+==}
[`if` expression]: expressions/if-expr.md#if-expressions
{==+==}

{==+==}