{==+==}
# Await expressions
{==+==}
# Await 表达式
{==+==}


{==+==}
> **<sup>Syntax</sup>**\
> _AwaitExpression_ :\
> &nbsp;&nbsp; [_Expression_] `.` `await`
{==+==}
> **<sup>语法</sup>**\
> _Await表达式_ :\
> &nbsp;&nbsp; [_表达式_][_Expression_] `.` `await`
{==+==}


{==+==}
An `await` expression is a syntactic construct for suspending a computation
provided by an implementation of `std::future::IntoFuture` until the given
future is ready to produce a value.
The syntax for an await expression is an expression with a type that implements the [`IntoFuture`] trait, called the *future operand*, then the token `.`, and then the `await` keyword.
Await expressions are legal only within an [async context], like an [`async fn`] or an [`async` block].

More specifically, an await expression has the following effect.
{==+==}
`await` 表达式是语法结构，用于暂停由实现了 `std::future::IntoFuture` 的计算，直到给定的 future 准备好产生值。
`await` 表达式的语法是一个实现了 [`IntoFuture`] trait 的表达式，称为 *future 操作数* ，后跟 `.` 和 `await` 关键字。
`await` 表达式只能在 [async 上下文][async context] 中使用，比如 [`async fn`] 或 [`async` 块][`async` block] 。更具体来说，`await` 表达式具有以下效果。
{==+==}


{==+==}
1. Create a future by calling [`IntoFuture::into_future`] on the future operand.
2. Evaluate the future to a [future] `tmp`;
3. Pin `tmp` using [`Pin::new_unchecked`];
4. This pinned future is then polled by calling the [`Future::poll`] method and passing it the current [task context](#task-context);
5. If the call to `poll` returns [`Poll::Pending`], then the future returns `Poll::Pending`, suspending its state so that, when the surrounding async context is re-polled,execution returns to step 3;
6. Otherwise the call to `poll` must have returned [`Poll::Ready`], in which case the value contained in the [`Poll::Ready`] variant is used as the result of the `await` expression itself.

> **Edition differences**: Await expressions are only available beginning with Rust 2018.
{==+==}
1. 通过在 future 操作数上调用 [`IntoFuture::into_future`] 创建 future。
2. 将 future 求解为 [future] `tmp` ；
3. 使用 [`Pin::new_unchecked`] 固定 `tmp` ；
4. 然后通过调用 [`Future::poll`] 方法并传递当前的 [任务上下文](#task-context) 来对这个固定的 future 进行轮询；
5. 如果对 `poll` 的调用返回 [`Poll::Pending`] ，那么 future 返回 `Poll::Pending` ，暂停它的状态，以便当包围的 async 上下文重新被轮询时，执行返回到步骤 3 ；
6. 否则对 `poll` 的调用必须返回 [`Poll::Ready`] ，在这种情况下， [`Poll::Ready`] 变体中包含的值将被用作 `await` 表达式本身的结果。
   
> **版本差异**: `await` 表达式只从 Rust 2018 开始可用。
{==+==}


{==+==}
## Task context

The task context refers to the [`Context`] which was supplied to the current [async context] when the async context itself was polled.
Because `await` expressions are only legal in an async context, there must be some task context available.
{==+==}
## 任务上下文

任务上下文指的是在当前 [async 上下文][async context] 被轮询时提供给它的 [`Context`]。
因为 `await` 表达式只能在 async 上下文中合法使用，所以必须有一些任务上下文可用。
{==+==}


{==+==}
## Approximate desugaring

Effectively, an await expression is roughly equivalent to the following non-normative desugaring:
{==+==}
## 近似解糖

实际上，一个 await 表达式大致相当于以下非规范的解糖:
{==+==}


{==+==}
<!-- ignore: example expansion -->
```rust,ignore
match operand.into_future() {
    mut pinned => loop {
        let mut pin = unsafe { Pin::new_unchecked(&mut pinned) };
        match Pin::future::poll(Pin::borrow(&mut pin), &mut current_context) {
            Poll::Ready(r) => break r,
            Poll::Pending => yield Poll::Pending,
        }
    }
}
```
{==+==}

{==+==}


{==+==}
where the `yield` pseudo-code returns `Poll::Pending` and, when re-invoked, resumes execution from that point.
The variable `current_context` refers to the context taken from the async environment.
{==+==}
其中 `yield` 伪代码返回 `Poll::Pending`，并在重新调用时从该点恢复执行。
变量 `current_context` 指的是从异步环境中获取的上下文。
{==+==}


{==+==}
[_Expression_]: ../expressions.md
[`async fn`]: ../items/functions.md#async-functions
[`async` block]: block-expr.md#async-blocks
[`context`]: ../../std/task/struct.Context.html
[`future::poll`]: ../../std/future/trait.Future.html#tymethod.poll
[`pin::new_unchecked`]: ../../std/pin/struct.Pin.html#method.new_unchecked
[`poll::Pending`]: ../../std/task/enum.Poll.html#variant.Pending
[`poll::Ready`]: ../../std/task/enum.Poll.html#variant.Ready
[async context]: ../expressions/block-expr.md#async-context
[future]: ../../std/future/trait.Future.html
[`IntoFuture`]: ../../std/future/trait.IntoFuture.html
[`IntoFuture::into_future`]: ../../std/future/trait.IntoFuture.html#tymethod.into_future
{==+==}

{==+==}