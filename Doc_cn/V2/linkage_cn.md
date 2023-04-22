{==+==}
* `--crate-type=rlib`, `#![crate_type = "rlib"]` - A "Rust library" file will be
  produced. This is used as an intermediate artifact and can be thought of as a
  "static Rust library". These `rlib` files, unlike `staticlib` files, are
  interpreted by the compiler in future linkage. This essentially means
  that `rustc` will look for metadata in `rlib` files like it looks for metadata
  in dynamic libraries. This form of output is used to produce statically linked
  executables as well as `staticlib` outputs.

* `--crate-type=proc-macro`, `#![crate_type = "proc-macro"]` - The output
  produced is not specified, but if a `-L` path is provided to it then the
  compiler will recognize the output artifacts as a macro and it can be loaded
  for a program. Crates compiled with this crate type  must only export
  [procedural macros]. The compiler will automatically set the `proc_macro`
  [configuration option]. The crates are always compiled with the same target
  that the compiler itself was built with. For example, if you are executing
  the compiler from Linux with an `x86_64` CPU, the target will be
  `x86_64-unknown-linux-gnu` even if the crate is a dependency of another crate
  being built for a different target.

Note that these outputs are stackable in the sense that if multiple are
specified, then the compiler will produce each form of output without
having to recompile. However, this only applies for outputs specified by the
same method. If only `crate_type` attributes are specified, then they will all
be built, but if one or more `--crate-type` command line flags are specified,
then only those outputs will be built.
{==+==}
* `--crate-type=rlib` ， `#![crate_type = "rlib"]` - 将生成 "Rust库" 文件。这用作中间制品，可以看作是 "静态Rust库" 。
  这些 `rlib` 文件不像 `staticlib` 文件一样被编译器静态链接。
  编译器在将来的链接中将解释这些 `rlib`文件，这基本上意味着 `rustc` 将像在动态库中查找元数据一样查找 `rlib` 文件中的元数据。
  这种输出形式用于生成静态链接的可执行文件以及 `staticlib` 输出。

* `--crate-type=proc-macro` ， `#![crate_type = "proc-macro"]` - 产生的输出未指定，但如果提供了 `-L` 路径，则编译器将识别输出制品为宏，它可以加载到程序中。
  使用此 crate 类型编译的 crate 必须仅导出 [过程宏][procedural macros] 。编译器将自动设置 `proc_macro` [配置选项][configuration option] 。这些crate总是使用编译器自身构建的相同目标进行编译。
  例如，如果您从 Linux 执行编译器，并且使用 `x86_64` CPU，则目标将是 `x86_64-unknown-linux-gnu` ，即使 crate 是正在为不同目标构建的另一个 crate 的依赖项。

请注意，这些输出是可叠加的，这意味着如果指定多个，则编译器将生成每种形式的输出而无需重新编译。
但是，这仅适用于由同一方法指定的输出。如果仅指定了 `crate_type` 属性，则将构建所有输出，但是如果指定了一个或多个 `--crate-type` 命令行标志，则仅构建这些输出。
{==+==}
