{==+==}
# Testing attributes
{==+==}
# 测试属性
{==+==}


{==+==}
The following [attributes] are used for specifying functions for performing
tests. Compiling a crate in "test" mode enables building the test functions
along with a test harness for executing the tests. Enabling the test mode also
enables the [`test` conditional compilation option].
{==+==}
以下 [属性][attributes] 用于指定执行测试函数。
在 "test" 模式下编译 crate，可以构建测试函数，以及测试控制下执行。
测试模式下也能够启用 [`test` 条件编译选项][`test` conditional compilation option]。
{==+==}


{==+==}
## The `test` attribute
{==+==}
## `test` 属性
{==+==}


{==+==}
The *`test` attribute* marks a function to be executed as a test. These
functions are only compiled when in test mode. Test functions must be free,
monomorphic functions that take no arguments, and the return type must implement the [`Termination`] trait, for example:
{==+==}
*`test`属性* 标记一个函数可被测试执行。这些函数只有在测试模式下才会被编译。
测试函数必须是自由的、无参的单态函数，并且返回类型必须实现 [`Termination`] trait ，例如:
{==+==}


{==+==}
* `()`
* `Result<T, E> where T: Termination, E: Debug`
* `!`
{==+==}

{==+==}


{==+==}
<!-- If the previous section needs updating (from "must take no arguments"
  onwards, also update it in the crates-and-source-files.md file -->
{==+==}

{==+==}


{==+==}
> Note: The test mode is enabled by passing the `--test` argument to `rustc`
> or using `cargo test`.
{==+==}
> 注意: 测试模式是通过向 `rustc` 传递 `--test` 参数或使用 `cargo test` 来启用的。
{==+==}


{==+==}
The test harness calls the returned value's [`report`] method, and classifies the test as passed or failed depending on whether the resulting [`ExitCode`] represents successful termination.
In particular:
* Tests that return `()` pass as long as they terminate and do not panic.
* Tests that return a `Result<(), E>` pass as long as they return `Ok(())`.
* Tests that return `ExitCode::SUCCESS` pass, and tests that return `ExitCode::FAILURE` fail.
* Tests that do not terminate neither pass nor fail.
{==+==}
测试控制调用返回值的 [`report`] 方法，并根据结果 [`ExitCode`] 获取成功状态，将测试分类为通过或失败。
特别:
* 返回 `()` 的测试只要结束且不恐慌就会通过。
* 返回 `Result<(), E>` 的测试，只要返回 `Ok(())` 就可以通过。
* 返回 `ExitCode::SUCCESS` 的测试通过，而返回 `ExitCode::FAILURE` 的测试失败。
* 没有结束的测试既不能通过也不能失败。
{==+==}


{==+==}
```rust
# use std::io;
# fn setup_the_thing() -> io::Result<i32> { Ok(1) }
# fn do_the_thing(s: &i32) -> io::Result<()> { Ok(()) }
#[test]
fn test_the_thing() -> io::Result<()> {
    let state = setup_the_thing()?; // expected to succeed
    do_the_thing(&state)?;          // expected to succeed
    Ok(())
}
```
{==+==}

{==+==}


{==+==}
## The `ignore` attribute
{==+==}
## `ignore` 属性
{==+==}


{==+==}
A function annotated with the `test` attribute can also be annotated with the
`ignore` attribute. The *`ignore` attribute* tells the test harness to not
execute that function as a test. It will still be compiled when in test mode.
{==+==}
用 `test` 属性注解的函数也可以用 `ignore` 属性来注解。
 *`ignore` 属性* 告知测试控制不要将该函数作为测试执行。在测试模式下，它仍然会被编译。
{==+==}


{==+==}
The `ignore` attribute may optionally be written with the [_MetaNameValueStr_]
syntax to specify a reason why the test is ignored.
{==+==}
 `ignore` 属性可以选择用 [_MetaNameValueStr_] 语法编写，以具体说明测试被忽略的原因。
{==+==}


{==+==}
```rust
#[test]
#[ignore = "not yet implemented"]
fn mytest() {
    // …
}
```
{==+==}

{==+==}


{==+==}
> **Note**: The `rustc` test harness supports the `--include-ignored` flag to
> force ignored tests to be run.
{==+==}
> **注意**: `rustc` 测试控制支持 `--include-ignored` 标志，以强制运行被忽略的测试。
{==+==}


{==+==}
## The `should_panic` attribute
{==+==}
## `should_panic` 属性
{==+==}


{==+==}
A function annotated with the `test` attribute that returns `()` can also be
annotated with the `should_panic` attribute. The *`should_panic` attribute*
makes the test only pass if it actually panics.
{==+==}
用 `test` 属性注解的函数，如果返回 `()` ，也可以用 `should_panic` 属性来注解。 * `should_panic` 属性* 使测试只在实际恐慌时通过。
{==+==}


{==+==}
The `should_panic` attribute may optionally take an input string that must
appear within the panic message. If the string is not found in the message,
then the test will fail. The string may be passed using the
[_MetaNameValueStr_] syntax or the [_MetaListNameValueStr_] syntax with an
`expected` field.
{==+==}
`should_panic` 属性可以有选择地接受一个输入字符串，该字符串必须出现在恐慌信息中。
如果在消息中没有找到该字符串，那么测试将失败。该字符串可以使用 [_MetaNameValueStr_] 或 [_MetaListNameValueStr_] 语法与 `expected` 字段来传递。
{==+==}


{==+==}
```rust
#[test]
#[should_panic(expected = "values don't match")]
fn mytest() {
    assert_eq!(1, 2, "values don't match");
}
```
{==+==}

{==+==}


{==+==}
[_MetaListNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[_MetaNameValueStr_]: ../attributes.md#meta-item-attribute-syntax
[`Termination`]: ../../std/process/trait.Termination.html
[`report`]: ../../std/process/trait.Termination.html#tymethod.report
[`test` conditional compilation option]: ../conditional-compilation.md#test
[attributes]: ../attributes.md
[`ExitCode`]: ../../std/process/struct.ExitCode.html
{==+==}

{==+==}