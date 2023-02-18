{==+==}
# Functions
{==+==}
# 函数
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _Function_ :\
> &nbsp;&nbsp; _FunctionQualifiers_ `fn` [IDENTIFIER]&nbsp;[_GenericParams_]<sup>?</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; `(` _FunctionParameters_<sup>?</sup> `)`\
> &nbsp;&nbsp; &nbsp;&nbsp; _FunctionReturnType_<sup>?</sup> [_WhereClause_]<sup>?</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; ( [_BlockExpression_] | `;` )
>
> _FunctionQualifiers_ :\
> &nbsp;&nbsp; `const`<sup>?</sup> `async`[^async-edition]<sup>?</sup> `unsafe`<sup>?</sup> (`extern` _Abi_<sup>?</sup>)<sup>?</sup>
>
> _Abi_ :\
> &nbsp;&nbsp; [STRING_LITERAL] | [RAW_STRING_LITERAL]
>
> _FunctionParameters_ :\
> &nbsp;&nbsp; &nbsp;&nbsp; _SelfParam_ `,`<sup>?</sup>\
> &nbsp;&nbsp; | (_SelfParam_ `,`)<sup>?</sup> _FunctionParam_ (`,` _FunctionParam_)<sup>\*</sup> `,`<sup>?</sup>
>
> _SelfParam_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> ( _ShorthandSelf_ | _TypedSelf_ )
>
> _ShorthandSelf_ :\
> &nbsp;&nbsp;  (`&` | `&` [_Lifetime_])<sup>?</sup> `mut`<sup>?</sup> `self`
>
> _TypedSelf_ :\
> &nbsp;&nbsp; `mut`<sup>?</sup> `self` `:` [_Type_]
>
> _FunctionParam_ :\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup> (
>   _FunctionParamPattern_ | `...` | [_Type_] [^fn-param-2015]
> )
>
> _FunctionParamPattern_ :\
> &nbsp;&nbsp; [_PatternNoTopAlt_] `:` ( [_Type_] | `...` )
>
> _FunctionReturnType_ :\
> &nbsp;&nbsp; `->` [_Type_]
>
{==+==}

{==+==}


{==+==}
> [^async-edition]: The `async` qualifier is not allowed in the 2015 edition.
>
> [^fn-param-2015]: Function parameters with only a type are only allowed
>   in an associated function of a [trait item] in the 2015 edition.
{==+==}
> [^async-edition]: 在 2015 版中不允许使用 `async` 限定词。
>
> [^fn-param-2015]: 在 2015 版中，只有在一个 [trait 条目][trait item] 的关联函数中才允许使用仅带有类型的函数参数。
{==+==}


{==+==}
A _function_ consists of a [block], along with a name, a set of parameters, and an output type.
Other than a name, all these are optional.
Functions are declared with the keyword `fn`.
Functions may declare a set of *input* [*variables*][variables] as parameters, through which the caller passes arguments into the function, and the *output* [*type*][type] of the value the function will return to its caller on completion.
If the output type is not explicitly stated, it is the [unit type].
{==+==}
一个函数包括一个 [代码块][block] ，以及一个名称、一组参数和一个输出类型。
除名称外，其他是可选的。函数是用关键字 `fn` 声明。
函数可以声明一组 *输入* [变量][variables] 作为参数，通过这些变量，调用者将参数传递给函数，并且函数的 *输出* 类型为函数完成时将返回给其调用者的 [类型][type] 。
如果未显式指定输出类型，则为 [unit type] 。
{==+==}


{==+==}
When referred to, a _function_ yields a first-class *value* of the corresponding zero-sized [*function item type*], which when called evaluates to a direct call to the function.
{==+==}
当引用一个函数时，它会产生一个相应的零大小的 [*函数条目类型*][*function item type*] 的一类 *值* ，当调用它时会直接调用该函数。
{==+==}


{==+==}
For example, this is a simple function:
```rust
fn answer_to_life_the_universe_and_everything() -> i32 {
    return 42;
}
```
{==+==}
例如，这是一个简单的函数:
```rust
fn answer_to_life_the_universe_and_everything() -> i32 {
    return 42;
}
```
{==+==}


{==+==}
## Function parameters
{==+==}
## 函数参数
{==+==}


{==+==}
Function parameters are irrefutable [patterns], so any pattern that is valid in
an else-less `let` binding is also valid as a parameter:
{==+==}
函数参数是不可拒绝的 [模式][patterns] ，因此任何在没有 else-less `let` 绑定中有效的模式在参数中也是有效的:
{==+==}


{==+==}
```rust
fn first((value, _): (i32, i32)) -> i32 { value }
```
{==+==}

{==+==}


{==+==}
If the first parameter is a _SelfParam_, this indicates that the function is a
[method]. Functions with a self parameter may only appear as an [associated
function] in a [trait] or [implementation].
{==+==}
如果第一个参数是 _SelfParam_ ，则表示该函数是一个 [方法][method] 。
带有 `self` 参数的函数只能作为 [trait] 或 [implementation] 中的关联函数出现。
{==+==}


{==+==}
A parameter with the `...` token indicates a [variadic function], and may only
be used as the last parameter of an [external block] function. The variadic
parameter may have an optional identifier, such as `args: ...`.
{==+==}
具有 `...` 符号的参数表示是一个 [可变参数函数][variadic function] ，且只能作为 [外部块函数][external block] 的最后一个参数。
可变参数可以有一个可选的标识符，例如 `args: ...` 。
{==+==}


{==+==}
## Function body
{==+==}
## 函数体
{==+==}


{==+==}
The block of a function is conceptually wrapped in a block that binds the
argument patterns and then `return`s the value of the function's block. This
means that the tail expression of the block, if evaluated, ends up being
returned to the caller. As usual, an explicit return expression within
the body of the function will short-cut that implicit return, if reached.
{==+==}
函数的代码块在概念上被包装在一个块中，该块绑定了参数模式，然后 `return` 函数块的值。
这意味着，如果块的尾表达式被评估，则将返回到调用者。
如常规一样，如果在函数体内有显式的返回表达式，则会立即返回，如果到达该隐式返回，则会被短路。
{==+==}


{==+==}
For example, the function above behaves as if it was written as:
{==+==}
例如，上面的函数的行为类似于以下写法:
{==+==}


{==+==}
<!-- ignore: example expansion -->
```rust,ignore
// argument_0 is the actual first argument passed from the caller
let (value, _) = argument_0;
return {
    value
};
```
{==+==}
<!-- ignore: example expansion -->
```rust,ignore
// argument_0是调用者实际传递的第一个参数。
let (value, _) = argument_0;
return {
    value
};
```
{==+==}


{==+==}
Functions without a body block are terminated with a semicolon. This form
may only appear in a [trait] or [external block].
{==+==}
函数没有函数体时以分号结束。这种形式只能出现在 [trait] 或 [external block] 中。
{==+==}


{==+==}
## Generic functions
{==+==}
## 泛型函数
{==+==}


{==+==}
A _generic function_ allows one or more _parameterized types_ to appear in its
signature. Each type parameter must be explicitly declared in an
angle-bracket-enclosed and comma-separated list, following the function name.
{==+==}
一种 _泛型函数_ 允许其签名中出现一个或多个 _参数化类型_ 。
每个类型参数必须在一个尖括号包围的、由逗号分隔的列表中显式声明，在函数名之后。
{==+==}


{==+==}
```rust
// foo is generic over A and B

fn foo<A, B>(x: A, y: B) {
# }
```
{==+==}
```rust
// foo 中 A 和 B 是泛型

fn foo<A, B>(x: A, y: B) {
# }
```
{==+==}


{==+==}
Inside the function signature and body, the name of the type parameter can be
used as a type name. [Trait] bounds can be specified for type
parameters to allow methods with that trait to be called on values of that
type. This is specified using the `where` syntax:
{==+==}
在函数签名和函数体内部，类型参数的名称可以用作类型名。
可以为类型参数指定 [Trait] 约束，以允许在该类型的值上调用该 Trait 的方法。可以使用 `where` 语法指定这个约束:
{==+==}


{==+==}
```rust
# use std::fmt::Debug;
fn foo<T>(x: T) where T: Debug {
# }
```
{==+==}

{==+==}


{==+==}
When a generic function is referenced, its type is instantiated based on the
context of the reference. For example, calling the `foo` function here:
{==+==}
当泛型函数被引用时，它的类型根据引用的上下文进行实例化。例如，在这里调用 `foo` 函数:
{==+==}


{==+==}
```rust
use std::fmt::Debug;

fn foo<T>(x: &[T]) where T: Debug {
    // details elided
}

foo(&[1, 2]);
```
{==+==}

{==+==}


{==+==}
will instantiate type parameter `T` with `i32`.
{==+==}
将用 `i32` 实例化类型参数 `T` 。
{==+==}


{==+==}
The type parameters can also be explicitly supplied in a trailing [path]
component after the function name. This might be necessary if there is not
sufficient context to determine the type parameters. For example,
`mem::size_of::<u32>() == 4`.
{==+==}
函数的类型参数还可以在函数名后跟一个后缀 [path] 组件来明确指定。
如果没有足够的上下文来确定类型参数，则可能需要这样做。例如，`mem::size_of::<u32>() == 4`。
{==+==}


{==+==}
## Extern function qualifier
{==+==}
## 外部函数修饰
{==+==}


{==+==}
The `extern` function qualifier allows providing function _definitions_ that can
be called with a particular ABI:
{==+==}
`extern` 函数修饰符允许提供函数 _定义_ ，这些函数可以使用特定的 ABI 进行调用：
{==+==}


{==+==}
<!-- ignore: fake ABI -->
```rust,ignore
extern "ABI" fn foo() { /* ... */ }
```
{==+==}

{==+==}


{==+==}
These are often used in combination with [external block] items which provide
function _declarations_ that can be used to call functions without providing
their _definition_:
{==+==}
这些通常与 [external block] 条目结合使用，后者提供可以用来调用函数而不提供其定义的函数声明:
{==+==}


{==+==}
<!-- ignore: fake ABI -->
```rust,ignore
extern "ABI" {
  fn foo(); /* no body */
}
unsafe { foo() }
```
{==+==}

{==+==}


{==+==}
When `"extern" Abi?*` is omitted from `FunctionQualifiers` in function items,
the ABI `"Rust"` is assigned. For example:
{==+==}
当在函数条目中省略 `"extern" Abi?*` 时，ABI 被分配 `"Rust"` 。例如：
{==+==}


{==+==}
```rust
fn foo() {}
```

is equivalent to:

```rust
extern "Rust" fn foo() {}
```
{==+==}
```rust
fn foo() {}
```

等价于:

```rust
extern "Rust" fn foo() {}
```
{==+==}


{==+==}
Functions can be called by foreign code, and using an ABI that
differs from Rust allows, for example, to provide functions that can be
called from other programming languages like C:
{==+==}
函数可以被外部代码调用，使用与 Rust 不同的 ABI 允许提供可以从其他编程语言 (如C) 调用的函数:
{==+==}


{==+==}
```rust
// Declares a function with the "C" ABI
extern "C" fn new_i32() -> i32 { 0 }

// Declares a function with the "stdcall" ABI
# #[cfg(target_arch = "x86_64")]
extern "stdcall" fn new_i32_stdcall() -> i32 { 0 }
```
{==+==}
```rust
// 用 "C" ABI声明函数
extern "C" fn new_i32() -> i32 { 0 }

// 用 "stdcall" ABI声明函数
# #[cfg(target_arch = "x86_64")]
extern "stdcall" fn new_i32_stdcall() -> i32 { 0 }
```
{==+==}


{==+==}
Just as with [external block], when the `extern` keyword is used and the `"ABI"`
is omitted, the ABI used defaults to `"C"`. That is, this:
{==+==}
与 [external block] 类似，当使用 `extern` 关键字但省略了 `"ABI"` 时，使用的 ABI 默认为 `"C"` 。例如：
{==+==}


{==+==}
```rust
extern fn new_i32() -> i32 { 0 }
let fptr: extern fn() -> i32 = new_i32;
```

is equivalent to:

```rust
extern "C" fn new_i32() -> i32 { 0 }
let fptr: extern "C" fn() -> i32 = new_i32;
```
{==+==}
```rust
extern fn new_i32() -> i32 { 0 }
let fptr: extern fn() -> i32 = new_i32;
```

等价于:

```rust
extern "C" fn new_i32() -> i32 { 0 }
let fptr: extern "C" fn() -> i32 = new_i32;
```
{==+==}


{==+==}
Functions with an ABI that differs from `"Rust"` do not support unwinding in the
exact same way that Rust does. Therefore, unwinding past the end of functions
with such ABIs causes the process to abort.
{==+==}
函数的 ABI (Application Binary Interface) 和 Rust 不同的函数的 ABI 不能像 Rust 一样支持展开 (unwind) 方式，因此尝试在这些 ABI 中展开超过函数末尾的部分会导致进程中止。
{==+==}


{==+==}
> **Note**: The LLVM backend of the `rustc` implementation
aborts the process by executing an illegal instruction.
{==+==}
> **注意**: `rustc` 的 LLVM 后端通过执行非法指令来使进程中止。
{==+==}


{==+==}
## Const functions
{==+==}
## Const 函数
{==+==}


{==+==}
Functions qualified with the `const` keyword are [const functions], as are
[tuple struct] and [tuple variant] constructors. _Const functions_  can be
called from within [const contexts].
{==+==}
被 `const` 修饰的函数是 [常量函数][const functions] ，同样的构造函数 [tuple struct] 和 [tuple variant] 也是。 _常量函数_ 可以在 [常量上下文][const contexts] 中被调用。
{==+==}


{==+==}
Const functions may use the [`extern`] function qualifier, but only with the `"Rust"` and `"C"` ABIs.
{==+==}
`const` 函数可以使用 [`extern`] 函数限定符，但是只能使用 `"Rust"` 和 `"C"` ABI。
{==+==}


{==+==}
Const functions are not allowed to be [async](#async-functions).
{==+==}
Const 函数不允许是 [async](#async-functions) 。
{==+==}


{==+==}
## Async functions
{==+==}
## 异步函数
{==+==}


{==+==}
Functions may be qualified as async, and this can also be combined with the
`unsafe` qualifier:
{==+==}
函数可以被标记为 async "异步" 的，这也可以与 `unsafe` 关键字组合使用。
{==+==}


{==+==}
```rust
async fn regular_example() { }
async unsafe fn unsafe_example() { }
```
{==+==}

{==+==}


{==+==}
Async functions do no work when called: instead, they
capture their arguments into a future. When polled, that future will
execute the function's body.
{==+==}
异步函数可以标记为 `async` 。在调用时，异步函数不会立即执行，而是将其参数捕获为一个 future 。当该 future 被轮询时，它会执行函数的主体。
{==+==}


{==+==}
An async function is roughly equivalent to a function
that returns [`impl Future`] and with an [`async move` block][async-blocks] as
its body:
{==+==}
一个 async 异步函数大致等同于一个返回 [`impl Future`] 的函数，并且具有一个 [`async move` 块][async-blocks]作为函数体:
{==+==}


{==+==}
```rust
// Source
async fn example(x: &str) -> usize {
    x.len()
}
```
{==+==}

{==+==}


{==+==}
is roughly equivalent to:
{==+==}
大致等同于:
{==+==}


{==+==}
```rust
# use std::future::Future;
// Desugared
fn example<'a>(x: &'a str) -> impl Future<Output = usize> + 'a {
    async move { x.len() }
}
```
{==+==}
```rust
# use std::future::Future;
// 去除语法糖
fn example<'a>(x: &'a str) -> impl Future<Output = usize> + 'a {
    async move { x.len() }
}
```
{==+==}


{==+==}
The actual desugaring is more complex:
{==+==}
实际的脱糖更加复杂:
{==+==}


{==+==}
- The return type in the desugaring is assumed to capture all lifetime
  parameters from the `async fn` declaration. This can be seen in the
  desugared example above, which explicitly outlives, and hence
  captures, `'a`.
- The [`async move` block][async-blocks] in the body captures all function
  parameters, including those that are unused or bound to a `_`
  pattern. This ensures that function parameters are dropped in the
  same order as they would be if the function were not async, except
  that the drop occurs when the returned future has been fully
  awaited.
{==+==}
- `async fn` 声明中的所有生命周期参数会被假定为被返回类型所包含。这在上面的展开示例中可以看到，显式地包含了 `'a` 生命周期。
- 函数体中的 [`async move` 块][async-blocks] 捕获了所有函数参数，包括那些未使用或绑定到 `_` 模式的参数。
  这确保了函数参数以与非异步函数相同的顺序被释放，除了释放操作在完全等待返回的 future 后发生。
{==+==}


{==+==}
For more information on the effect of async, see [`async` blocks][async-blocks].
{==+==}
有关 `async` 的更多信息，请参见 [`async` 块][async-blocks]。
{==+==}


{==+==}
[async-blocks]: ../expressions/block-expr.md#async-blocks
[`impl Future`]: ../types/impl-trait.md
{==+==}

{==+==}


{==+==}
> **Edition differences**: Async functions are only available beginning with
> Rust 2018.
{==+==}
> **版本差异**: 异步函数 (Async functions) 仅在 Rust 2018 开始提供。
{==+==}


{==+==}
### Combining `async` and `unsafe`
{==+==}
### 组合 `async` 和 `unsafe`
{==+==}


{==+==}
It is legal to declare a function that is both async and unsafe. The
resulting function is unsafe to call and (like any async function)
returns a future. This future is just an ordinary future and thus an
`unsafe` context is not required to "await" it:
{==+==}
在 Rust 中，声明既是 `async` 又是 `unsafe` 的函数是合法的。
这样产生的函数是不安全的，不能安全地调用，但它 (像任何异步函数一样) 返回一个 future。
这个 future 是普通的 future，因此在 "await" 它的时候不需要使用 `unsafe` 上下文。
{==+==}


{==+==}
```rust
// Returns a future that, when awaited, dereferences `x`.
//
// Soundness condition: `x` must be safe to dereference until
// the resulting future is complete.
async unsafe fn unsafe_example(x: *const i32) -> i32 {
  *x
}

async fn safe_example() {
    // An `unsafe` block is required to invoke the function initially:
    let p = 22;
    let future = unsafe { unsafe_example(&p) };

    // But no `unsafe` block required here. This will
    // read the value of `p`:
    let q = future.await;
}
```
{==+==}
```rust
// 返回一个 future ，当被等待时，解除对 `x` 的引用。
//
// 必要条件： `x` 必须是安全的，在产生的 future 完成之前，可以取消引用。
async unsafe fn unsafe_example(x: *const i32) -> i32 {
  *x
}

async fn safe_example() {
    // 初始需要一个 `unsafe` 块来调用这个函数:
    let p = 22;
    let future = unsafe { unsafe_example(&p) };

    // 但这里不需要 `unsafe` 块。这将读取 `p` 的值:
    let q = future.await;
}
```
{==+==}


{==+==}
Note that this behavior is a consequence of the desugaring to a
function that returns an `impl Future` -- in this case, the function
we desugar to is an `unsafe` function, but the return value remains
the same.
{==+==}
注意，这个行为是由将函数展开为返回 `impl Future` 的行为所决定的。
在这个例子中，我们展开的函数是一个 `unsafe` 函数，但是返回值仍然是一样的。
{==+==}


{==+==}
Unsafe is used on an async function in precisely the same way that it
is used on other functions: it indicates that the function imposes
some additional obligations on its caller to ensure soundness. As in any
other unsafe function, these conditions may extend beyond the initial
call itself -- in the snippet above, for example, the `unsafe_example`
function took a pointer `x` as argument, and then (when awaited)
dereferenced that pointer. This implies that `x` would have to be
valid until the future is finished executing, and it is the caller's
responsibility to ensure that.
{==+==}
`unsafe` 关键字在异步函数中的使用方式与其在其他函数中的使用方式相同：它表示该函数对其调用者有一些额外的义务，以确保其安全性。
与任何其他不安全函数一样，这些条件可能会超出初始调用本身的范围。
例如，在上面的代码段中， `unsafe_example` 函数接受一个指针 `x` 作为参数，然后 (当等待时) 对该指针进行解引用。
这意味着 `x` 必须在 future 执行完成之前保持有效，并且调用者有责任确保这一点。
{==+==}


{==+==}
## Attributes on functions
{==+==}
## 函数的属性
{==+==}


{==+==}
[Outer attributes][attributes] are allowed on functions. [Inner
attributes][attributes] are allowed directly after the `{` inside its [block].
{==+==}
函数可以使用 [外部属性][attributes] 。 [内部属性][attributes] 可以直接在函数的 [block] 里的 `{` 后面使用。
{==+==}


{==+==}
This example shows an inner attribute on a function. The function is documented
with just the word "Example".
{==+==}
以下示例展示了在函数中使用内部属性。该函数的文档注释只包含单词 "Example" 。
{==+==}


{==+==}
```rust
fn documented() {
    #![doc = "Example"]
}
```
{==+==}

{==+==}


{==+==}
> Note: Except for lints, it is idiomatic to only use outer attributes on
> function items.
{==+==}
> 注意: 除了用于 lint ，通常仅在函数条目上使用外部属性是惯用的。
{==+==}


{==+==}
The attributes that have meaning on a function are [`cfg`], [`cfg_attr`], [`deprecated`],
[`doc`], [`export_name`], [`link_section`], [`no_mangle`], [the lint check
attributes], [`must_use`], [the procedural macro attributes], [the testing
attributes], and [the optimization hint attributes]. Functions also accept
attributes macros.
{==+==}
函数可用的属性包括 [`cfg`] 、 [`cfg_attr`] 、 [`deprecated`] 、 [`doc`] 、 [`export_name`] 、 [`link_section`] 、 [`no_mangle`] 、[lint 检查属性][the lint check attributes] 、 [`must_use`] 、 [过程宏属性][the procedural macro attributes] 、 [测试属性][the testing attributes] 和 [优化提示属性][the optimization hint attributes] 。此外，函数还接受属性宏。
{==+==}


{==+==}
## Attributes on function parameters
{==+==}
## 函数参数的属性
{==+==}


{==+==}
[Outer attributes][attributes] are allowed on function parameters and the
permitted [built-in attributes] are restricted to `cfg`, `cfg_attr`, `allow`,
`warn`, `deny`, and `forbid`.
{==+==}
函数参数上允许使用 [外部属性][attributes] ，其中允许使用的 [内置属性][built-in attributes] 仅限于 `cfg` ， `cfg_attr` ， `allow` ， `warn` ， `deny` 和 `forbid`。
{==+==}


{==+==}
```rust
fn len(
    #[cfg(windows)] slice: &[u16],
    #[cfg(not(windows))] slice: &[u8],
) -> usize {
    slice.len()
}
```
{==+==}

{==+==}


{==+==}
Inert helper attributes used by procedural macro attributes applied to items are also
allowed but be careful to not include these inert attributes in your final `TokenStream`.
{==+==}
在应用于条目的过程宏属性中使用的惰性辅助属性也是允许的，但是请注意不要将这些惰性属性包含在最终的 `TokenStream` 中。
{==+==}


{==+==}
For example, the following code defines an inert `some_inert_attribute` attribute that
is not formally defined anywhere and the `some_proc_macro_attribute` procedural macro is
responsible for detecting its presence and removing it from the output token stream.
{==+==}
例如，以下代码定义了一个名为 `some_inert_attribute` 的惰性属性，它在任何地方都没有正式定义，
而 `some_proc_macro_attribute` 过程宏负责检测其是否存在，并将其从输出令牌流中删除。
{==+==}


{==+==}
<!-- ignore: requires proc macro -->
```rust,ignore
#[some_proc_macro_attribute]
fn foo_oof(#[some_inert_attribute] arg: u8) {
}
```
{==+==}

{==+==}


{==+==}
[IDENTIFIER]: ../identifiers.md
[RAW_STRING_LITERAL]: ../tokens.md#raw-string-literals
[STRING_LITERAL]: ../tokens.md#string-literals
[_BlockExpression_]: ../expressions/block-expr.md
[_GenericParams_]: generics.md
[_Lifetime_]: ../trait-bounds.md
[_PatternNoTopAlt_]: ../patterns.md
[_Type_]: ../types.md#type-expressions
[_WhereClause_]: generics.md#where-clauses
[_OuterAttribute_]: ../attributes.md
[const contexts]: ../const_eval.md#const-context
[const functions]: ../const_eval.md#const-functions
[tuple struct]: structs.md
[tuple variant]: enumerations.md
[`extern`]: #extern-function-qualifier
[external block]: external-blocks.md
[path]: ../paths.md
[block]: ../expressions/block-expr.md
[variables]: ../variables.md
[type]: ../types.md#type-expressions
[unit type]: ../types/tuple.md
[*function item type*]: ../types/function-item.md
[Trait]: traits.md
[attributes]: ../attributes.md
[`cfg`]: ../conditional-compilation.md#the-cfg-attribute
[`cfg_attr`]: ../conditional-compilation.md#the-cfg_attr-attribute
[the lint check attributes]: ../attributes/diagnostics.md#lint-check-attributes
[the procedural macro attributes]: ../procedural-macros.md
[the testing attributes]: ../attributes/testing.md
[the optimization hint attributes]: ../attributes/codegen.md#optimization-hints
[`deprecated`]: ../attributes/diagnostics.md#the-deprecated-attribute
[`doc`]: ../../rustdoc/the-doc-attribute.html
[`must_use`]: ../attributes/diagnostics.md#the-must_use-attribute
[patterns]: ../patterns.md
[`export_name`]: ../abi.md#the-export_name-attribute
[`link_section`]: ../abi.md#the-link_section-attribute
[`no_mangle`]: ../abi.md#the-no_mangle-attribute
[built-in attributes]: ../attributes.html#built-in-attributes-index
[trait item]: traits.md
[method]: associated-items.md#methods
[associated function]: associated-items.md#associated-functions-and-methods
[implementation]: implementations.md
[variadic function]: external-blocks.md#variadic-functions
{==+==}

{==+==}