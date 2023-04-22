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
以下 [属性][attributes] 用于指定用于执行测试的函数。
将 crate 编译为 "test" 模式将启用构建测试函数以及用于执行测试的测试工具。
启用测试模式还启用 [`test` 条件编译选项][`test` conditional compilation option]。
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
 *`test`属性* 用于标记一个函数作为测试用例执行。这些函数只在测试模式下编译。
 测试函数必须是无参、单态函数，并且返回类型必须实现 [`Termination`] trait ，例如:
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
测试框架将调用返回值的 [`report`] 方法，并根据 [`ExitCode`] 代表的终止状态将测试分类为已通过或已失败。特别地:
* 返回 `()` 的测试只要终止且未发生恐慌即为通过。
* 返回 `Result<(), E>` 的测试只要返回 `Ok(())` 即为通过。
* 返回 `ExitCode::SUCCESS` 的测试通过，返回 `ExitCode::FAILURE` 的测试失败。
* 无法终止的测试既不通过也不失败。
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
一个被标记为 `test` 的函数也可以被标记为 `ignore` 。
 *`ignore` 属性* 告知测试框架不要将该函数作为测试执行，但在测试模式下它仍会被编译。
{==+==}


{==+==}
The `ignore` attribute may optionally be written with the [_MetaNameValueStr_]
syntax to specify a reason why the test is ignored.
{==+==}
 `ignore` 属性可以选择用 [_元名称值字符串_][_MetaNameValueStr_] 语法编写，以具体说明测试被忽略的原因。
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
> **注意**: `rustc` 测试框架支持 `--include-ignored` 标志，以强制运行被忽略的测试。
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
带有 `test` 属性的返回类型为 `()` 的函数还可以带有 `should_panic` 属性。 `should_panic` 属性 表示仅当函数实际恐慌时，测试才会通过。
{==+==}


{==+==}
The `should_panic` attribute may optionally take an input string that must
appear within the panic message. If the string is not found in the message,
then the test will fail. The string may be passed using the
[_MetaNameValueStr_] syntax or the [_MetaListNameValueStr_] syntax with an
`expected` field.
{==+==}
`should_panic` 属性可以选择性地接受一个输入字符串，该字符串必须出现在恐慌消息中。
如果在消息中未找到该字符串，则测试将失败。可以使用 [_元名称值字符串_][_MetaNameValueStr_] 语法或具有 `expected` 字段的 [_元列表名称值字符串_][_MetaListNameValueStr_] 语法传递该字符串。
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