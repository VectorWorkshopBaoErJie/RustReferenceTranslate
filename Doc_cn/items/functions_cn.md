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
> [^async-edition]: 2015 版中不允许使用 `async` 限定词。
>
> [^fn-param-2015]: 在2015版中，只有在 [trait item] 的关联函数中，才允许仅有一个类型的函数参数。
{==+==}


{==+==}
A _function_ consists of a [block], along with a name, a set of parameters, and an output type.
Other than a name, all these are optional.
Functions are declared with the keyword `fn`.
Functions may declare a set of *input* [*variables*][variables] as parameters, through which the caller passes arguments into the function, and the *output* [*type*][type] of the value the function will return to its caller on completion.
If the output type is not explicitly stated, it is the [unit type].
{==+==}
一个 _函数_ 包括一个 [block] ，以及一个名称、一组参数和一个输出类型。
除了名称，其他是可选的。函数用关键字 `fn` 来声明。
函数可以声明一组 *input* [*variables*][variables] 作为参数，调用者通过这些参数向函数传递实参，
函数完成后将返回给调用者 *output* [*type*][type] 的值。
如果输出类型没有明确说明，就是 [unit type] "元组"。
{==+==}


{==+==}
When referred to, a _function_ yields a first-class *value* of the corresponding zero-sized [*function item type*], which when called evaluates to a direct call to the function.
{==+==}
当被引用时，一个 _函数_ 会得到一个零尺寸的 [*function item type*] "函数条目类型" 的第一类 *值* ，当被调用时，评估为直接调用该函数。
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
函数参数是不可驳斥的 [patterns] ，所以任何在 else-less `let` 绑定中有效的模式，作为参数也是有效的:
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
如果第一个参数是 _SelfParam_ ，这表明该函数是一个 [method] "方法"。
带有self参数的函数只能作为 [associated function] "关联函数" 出现在 [trait] 或 [implementation] 中。
{==+==}


{==+==}
A parameter with the `...` token indicates a [variadic function], and may only
be used as the last parameter of an [external block] function. The variadic
parameter may have an optional identifier, such as `args: ...`.
{==+==}
带有 `...` 标记的参数表示一个 [variadic function] 可变函数，只能作为 [external block] 函数的最后一个参数使用。
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
函数块在概念上被包装在一个绑定参数模式的块中，然后 `return` 函数块的值。
这意味着，可能会评估块尾部表达式，并最终返回给调用者。
像通常一样，如果到达函数体中的显式返回表达式，将"短路"隐式返回。
{==+==}


{==+==}
For example, the function above behaves as if it was written as:
{==+==}
例如，上面的函数表现为假设写成的这样:
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
没有主体块的函数是以分号结束。这种形式只能出现在 [trait] 或 [external block] 中。
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
_泛型函数_ 允许一个或多个 _参数化类型_ 出现在其签名中。
每个类型的参数必须明确地在角括号内和逗号分隔的列表中声明，在函数名之后。
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
在函数签名和主体中，类型参数的名称可以作为主体中类型名称使用。
可以为类型参数指定 [Trait] 限定，以允许对该类型的值调用具有该trait的方法，
是用 `where` 语法指定的:
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
当泛型函数被引用时，其类型会根据引用的上下文而实例化。例如，在这里调用 `foo` 函数:
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
类型参数也可以在函数名后面的尾部 [path] 部分中明确提供。
如果没有足够的上下文来确定类型参数，这可能是必要的。
例如， `mem::size_of::<u32>() == 4` 。
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
`extern` 函数修饰允许提供可以用特定ABI调用的函数 _定义_ :
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
这些通常与 [external block] 条目结合使用，后者提供了函数 _声明_ ，可用于调用函数而不提供其 _定义_ :
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
当省略 `FunctionQualifiers` 函数条目的 `"extern" Abi?*` 时，ABI 被分配 `"Rust"` 。例如:
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

{==+==}


{==+==}
Functions can be called by foreign code, and using an ABI that
differs from Rust allows, for example, to provide functions that can be
called from other programming languages like C:
{==+==}
函数可以被外部代码调用，使用与Rust不同的ABI准许，例如，提供可以从其他编程语言(如C)调用的函数:
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
就像 [external block] 一样，当使用 `extern` 关键字并且省略 `"ABI"` 时，使用的ABI默认为 `"C"` 。就是说，这样:
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
与 `"Rust"` 不同的ABI的函数不支持与Rust完全相同的方式"解旋"。因此，超过ABI的函数末端的"解旋"会导致进程中止。
{==+==}


{==+==}
> **Note**: The LLVM backend of the `rustc` implementation
aborts the process by executing an illegal instruction.
{==+==}
> **注意**:  `rustc` 实现的LLVM后端通过执行一条非法指令中止进程。
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
用 `const` 关键字限定的函数是 [const functions] ， [tuple struct]  和 [tuple variant] 构造器也是如此。
 _Const functions_ 可以从 [const contexts] "const上下文"中调用。
{==+==}


{==+==}
Const functions may use the [`extern`] function qualifier, but only with the `"Rust"` and `"C"` ABIs.
{==+==}
构造函数可以使用 [`extern`] 函数限定符，但只适用于 `"Rust"` 和 `"C"` ABI。
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
函数可以用 async "异步" 限定 ，也可以与 `unsafe` 限定词结合。
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
在调用异步函数时不做任何工作: 而是它们将其参数捕获到一个 future 。
当轮询时，此 future 将执行该函数体。
(译注：future 是编译环节的类型)
{==+==}


{==+==}
An async function is roughly equivalent to a function
that returns [`impl Future`] and with an [`async move` block][async-blocks] as
its body:
{==+==}
一个异步函数大致相当于一个返回 [`impl Future`] 并以 [`async move` block][async-blocks] 作为其主体的函数。
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
大致相当于:
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
实际的去除语法糖工作更加复杂:
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
- 脱糖时返回类型假定为捕获来自 `async fn` 声明的所有生命周期参数。
  可以从上面脱糖的例子中看出，它明显的生命更长，因此捕获了 `'a` 。
- 主体中的 [`async move` block][async-blocks] 捕获了所有的函数参数，包括那些未使用的、或者与 `_` 模式绑定的参数。
  这确保了函数参数的丢弃(drop)顺序与函数非异步情况下相同，只是丢弃发生在返回的 future 彻底等待的时候。
{==+==}


{==+==}
关于 async 的更多信息, 参见 [`async` blocks][async-blocks] 。
{==+==}

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
> **版本差异**: 异步函数只从Rust 2018开始提供。
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
声明既是 async 且 unsafe 的函数是合规的。
由此产生的函数在调用时是不安全的，并且 (像任何异步函数一样) 返回一个 future 。
这个 future 只是一个普通的 future ，因此不需要一个 `unsafe` 的上下文来 "await" 它。
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
请注意，这种行为是对一个返回 `impl Future` 的函数进行脱糖的结果 -- 在这种情况下，脱糖的函数是一个 `unsafe` 的函数，但返回值保持不变。
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
Unsafe用于异步函数的方式与用于其他函数的方式完全相同: 表明确保函数的健全性，将由其调用者保障。
和其他unsafe函数一样，初始条件很有可能延伸到调用本身之外 -- 例如，在上面的片段中， `unsafe_example` 函数接收了一个指针 `x` 作为参数，然后 (当等待时) 取消对该指针的引用。这意味着 `x` 必须在future执行完毕之前一直有效，而这一点需要调用者确保。
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
[Outer attributes][attributes] 函数允许 [Outer attributes][attributes] 。 [Inner attributes][attributes] 可以直接在其 [block] 内的 `{` 之后使用。
{==+==}


{==+==}
This example shows an inner attribute on a function. The function is documented
with just the word "Example".
{==+==}
这个例子展示了函数中的内部属性。该函数的文档中只有 "Example" 二字。
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
> 注意: 除了 lints，仅在函数条目上使用外部属性是惯例。
{==+==}


{==+==}
The attributes that have meaning on a function are [`cfg`], [`cfg_attr`], [`deprecated`],
[`doc`], [`export_name`], [`link_section`], [`no_mangle`], [the lint check
attributes], [`must_use`], [the procedural macro attributes], [the testing
attributes], and [the optimization hint attributes]. Functions also accept
attributes macros.
{==+==}
对函数有意义的属性是 [`cfg`] , [`cfg_attr`], [`deprecated`] , [`doc`], [`export_name`] , [`link_section`] , [`no_mangle`] , [the lint check attributes] , [`must_use`] , [the procedural macro attributes] , [the testing attributes] , 和 [the optimization hint attributes] 。函数也接受属性宏。
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
允许在函数参数上使用 [Outer attributes][attributes] ，允许的 [built-in attributes] 被限制为 `cfg` , `cfg_attr` , `allow` , `warn` , `deny` , 和 `forbid` 。
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
也允许应用于条目的过程性宏属性所使用的惰性辅助属性，但要注意，不要在最终 `TokenStream` 中包含惰性属性。
{==+==}


{==+==}
For example, the following code defines an inert `some_inert_attribute` attribute that
is not formally defined anywhere and the `some_proc_macro_attribute` procedural macro is
responsible for detecting its presence and removing it from the output token stream.
{==+==}
例如，下面的代码定义了一个惰性的 `some_inert_attribute` 属性，它还没有在任何地方正式定义，
`some_proc_macro_attribute` 过程性宏负责检测其存在并从输出标记流中删除它。
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