{==+==}
# Linkage

> Note: This section is described more in terms of the compiler than of
> the language.

The compiler supports various methods to link crates together both
statically and dynamically. This section will explore the various methods to
link crates together, and more information about native libraries can be
found in the [FFI section of the book][ffi].

[ffi]: ../book/ch19-01-unsafe-rust.html#using-extern-functions-to-call-external-code

In one session of compilation, the compiler can generate multiple artifacts
through the usage of either command line flags or the `crate_type` attribute.
If one or more command line flags are specified, all `crate_type` attributes will
be ignored in favor of only building the artifacts specified by command line.

* `--crate-type=bin`, `#![crate_type = "bin"]` - A runnable executable will be
  produced. This requires that there is a `main` function in the crate which
  will be run when the program begins executing. This will link in all Rust and
  native dependencies, producing a single distributable binary.
  This is the default crate type.
{==+==}
# 链接

> 注意: 这一部分更多地从编译器而非语言的角度进行描述。

编译器支持静态和动态地链接不同的 crate 。
本节将探讨链接 crate 的各种方法，有关本地库的更多信息可以在 [《Rust 程序设计语言》中的 FFI 章节][ffi] 中找到。

[ffi]: ../book/ch19-01-unsafe-rust.html#using-extern-functions-to-call-external-code

在一次编译会话中，编译器可以通过使用命令行标志或 `crate_type` 属性生成多个制品。
如果指定了一个或多个命令行标志，则忽略所有 `crate_type` 属性，仅构建由命令行指定制品。

* `--crate-type=bin` ， `#![crate_type = "bin"]` - 将生成可运行的可执行文件。在 crate 中需要有一个 `main` 函数，程序开始执行时首先运行。这将链接所有的 Rust 和本地依赖项，生成一个可分发的单个二进制文件。是默认的 crate 类型。
{==+==}


{==+==}
* `--crate-type=lib`, `#![crate_type = "lib"]` - A Rust library will be produced.
  This is an ambiguous concept as to what exactly is produced because a library
  can manifest itself in several forms. The purpose of this generic `lib` option
  is to generate the "compiler recommended" style of library. The output library
  will always be usable by rustc, but the actual type of library may change from
  time-to-time. The remaining output types are all different flavors of
  libraries, and the `lib` type can be seen as an alias for one of them (but the
  actual one is compiler-defined).

* `--crate-type=dylib`, `#![crate_type = "dylib"]` - A dynamic Rust library will
  be produced. This is different from the `lib` output type in that this forces
  dynamic library generation. The resulting dynamic library can be used as a
  dependency for other libraries and/or executables. This output type will
  create `*.so` files on Linux, `*.dylib` files on macOS, and `*.dll` files on
  Windows.

* `--crate-type=staticlib`, `#![crate_type = "staticlib"]` - A static system
  library will be produced. This is different from other library outputs in that
  the compiler will never attempt to link to `staticlib` outputs. The
  purpose of this output type is to create a static library containing all of
  the local crate's code along with all upstream dependencies. This output type
  will create `*.a` files on Linux, macOS and Windows (MinGW), and `*.lib` files
  on Windows (MSVC). This format is recommended for use in situations such as
  linking Rust code into an existing non-Rust application
  because it will not have dynamic dependencies on other Rust code.

* `--crate-type=cdylib`, `#![crate_type = "cdylib"]` - A dynamic system
  library will be produced.  This is used when compiling
  a dynamic library to be loaded from another language.  This output type will
  create `*.so` files on Linux, `*.dylib` files on macOS, and `*.dll` files on
  Windows.
{==+==}
* `--crate-type=lib`，`#![crate_type = "lib"]` - 将生成一个 Rust 库。这是一个广义概念，因为库可以以多种形式呈现。这个通用的 `lib` 选项的目的是生成 "编译器推荐" 的库风格。输出的库始终可由 rustc 使用，但实际的库类型可能会随时间而变化。其余的输出类型都是不同的库类型，而 `lib` 类型可以看作是其中一个的别名 (但实际的类型是由编译器定义的) 。

* `--crate-type=dylib`，`#![crate_type = "dylib"]` - 将生成一个动态 Rust 库。这与 `lib` 输出类型不同，因为它强制生成动态库。生成的动态库可以用作其他库和/或可执行文件的依赖项。此输出类型将在 Linux 上创建 `*.so` 文件，在 macOS 上创建 `*.dylib` 文件，在 Windows 上创建 `*.dll` 文件。

* `--crate-type=staticlib`，`#![crate_type = "staticlib"]` - 将生成一个静态系统库。这与其他库输出不同，因为编译器永远不会尝试链接到 `staticlib` 输出。此输出类型的目的是创建一个包含所有本地 crate 代码以及所有上游依赖项的静态库。此输出类型将在 Linux、macOS 和 Windows (MinGW) 上创建 `*.a` 文件，在 Windows (MSVC) 上创建 `*.lib` 文件。此格式推荐在将 Rust 代码链接到现有的非 Rust 应用程序时使用，因为它不会对其他 Rust 代码产生动态依赖关系。

* `--crate-type=cdylib`，`#![crate_type = "cdylib"]` - 将生成一个动态系统库。这用于编译从另一种语言加载的动态库。此输出类型将在 Linux 上创建 `*.so` 文件，在 macOS 上创建 `*.dylib` 文件，在 Windows 上创建 `*.dll` 文件。
{==+==}


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
specified, then the compiler will produce each form of output at once without
having to recompile. However, this only applies for outputs specified by the
same method. If only `crate_type` attributes are specified, then they will all
be built, but if one or more `--crate-type` command line flags are specified,
then only those outputs will be built.
{==+==}
* `--crate-type=rlib`，`#![crate_type = "rlib"]` - 会生成一个 "Rust库" 文件。这是一个中间产物，可以看作是一个 "静态的Rust库" 。与 `staticlib` 文件不同，这些 `rlib` 文件将在未来的链接中由编译器进行解释。这基本上意味着， `rustc` 将像在动态库中查找元数据一样在 `rlib` 文件中查找元数据。这种输出形式用于生成静态链接的可执行文件以及 `staticlib` 输出。

* `--crate-type=proc-macro` ， `#![crate_type = "proc-macro"]` - 产生的输出未指定，但如果给它提供一个 `-L` 路径，编译器将识别制品为一个宏，并且它可以被程序加载。使用这种 crate 类型编译的 crate 必须只导出 [过程宏][procedural macros] 。编译器将自动设置 `proc_macro` [配置选项][configuration option] 。这些 crate 始终使用编译器本身构建时的相同目标进行编译。例如，如果你在 Linux 上使用 `x86_64` CPU 执行编译器，则目标将为 `x86_64-unknown-linux-gnu` ，即使该 crate 是另一个 crate 正在为不同目标而构建的依赖项。

请注意，这些输出是可重叠的，即可指定多个，则编译器将同时生成每种形式的输出，而无需重新编译。但是，这仅适用于由同一方法指定的输出。如果仅指定 `crate_type` 属性，则所有属性都将被构建，但如果指定一个或多个 `--crate-type` 命令行标志，则仅构建那些输出。
{==+==}


{==+==}
With all these different kinds of outputs, if crate A depends on crate B, then
the compiler could find B in various different forms throughout the system. The
only forms looked for by the compiler, however, are the `rlib` format and the
dynamic library format. With these two options for a dependent library, the
compiler must at some point make a choice between these two formats. With this
in mind, the compiler follows these rules when determining what format of
dependencies will be used:

1. If a static library is being produced, all upstream dependencies are
   required to be available in `rlib` formats. This requirement stems from the
   reason that a dynamic library cannot be converted into a static format.

   Note that it is impossible to link in native dynamic dependencies to a static
   library, and in this case warnings will be printed about all unlinked native
   dynamic dependencies.

2. If an `rlib` file is being produced, then there are no restrictions on what
   format the upstream dependencies are available in. It is simply required that
   all upstream dependencies be available for reading metadata from.

   The reason for this is that `rlib` files do not contain any of their upstream
   dependencies. It wouldn't be very efficient for all `rlib` files to contain a
   copy of `libstd.rlib`!
{==+==}
有了所有这些不同类型的输出，如果 crate A 依赖于 crate B ，那么编译器可以在系统中以各种不同的形式找到 B 。然而，编译器查找的唯一格式是 `rlib` 格式和动态库格式。有了这两个选项，编译器必须在这两种格式之间做出选择。考虑到这一点，编译器在确定将使用哪种依赖关系格式时遵循以下规则：

1. 如果正在生成静态库，则所有上游依赖项都必须以 `rlib` 格式可用。这个要求源于动态库无法转换为静态格式的原因。

   请注意，无法将本机动态依赖项链接到静态库中，在这种情况下，将打印所有未链接的本机动态依赖项的警告。

2. 如果正在生成一个 `rlib` 文件，则上游依赖项可用的格式没有限制。只需要所有上游依赖项都可用于读取元数据。

   这样做的原因是， `rlib` 文件不包含任何上游依赖项。所有 `rlib` 文件包含 `libstd.rlib` 的副本时效率不高！
{==+==}


{==+==}
3. If an executable is being produced and the `-C prefer-dynamic` flag is not
   specified, then dependencies are first attempted to be found in the `rlib`
   format. If some dependencies are not available in an rlib format, then
   dynamic linking is attempted (see below).

4. If a dynamic library or an executable that is being dynamically linked is
   being produced, then the compiler will attempt to reconcile the available
   dependencies in either the rlib or dylib format to create a final product.

   A major goal of the compiler is to ensure that a library never appears more
   than once in any artifact. For example, if dynamic libraries B and C were
   each statically linked to library A, then a crate could not link to B and C
   together because there would be two copies of A. The compiler allows mixing
   the rlib and dylib formats, but this restriction must be satisfied.

   The compiler currently implements no method of hinting what format a library
   should be linked with. When dynamically linking, the compiler will attempt to
   maximize dynamic dependencies while still allowing some dependencies to be
   linked in via an rlib.

   For most situations, having all libraries available as a dylib is recommended
   if dynamically linking. For other situations, the compiler will emit a
   warning if it is unable to determine which formats to link each library with.

In general, `--crate-type=bin` or `--crate-type=lib` should be sufficient for
all compilation needs, and the other options are just available if more
fine-grained control is desired over the output format of a crate.
{==+==}
3. 如果正在生成可执行文件且未指定 `-C prefer-dynamic` 标志，则首先尝试在 `rlib` 格式中查找依赖项。如果某些依赖项在 `rlib` 格式中不可用，则尝试动态链接 (见下文) 。

4. 如果正在生成动态库或正在以动态链接方式链接的可执行文件，则编译器将尝试在 rlib 或 dylib 格式中协调可用的依赖项以创建最终产品。

   编译器的主要目标是确保库在任何构件中都不会出现多次。例如，如果动态库 B 和 C 分别静态链接到库 A，那么一个 crate 就无法将 B 和 C 链接在一起，因为会有两个 A 的副本。编译器允许混合使用 rlib 和 dylib 格式，但必须满足此限制。

   目前，编译器没有实现提示链接库应使用哪种格式的方法。在动态链接时，编译器将尝试最大化动态依赖性，同时仍允许某些依赖性通过 rlib 进行链接。

   对于大多数情况，如果要动态链接，建议将所有库都作为 dylib 可用。对于其他情况，如果编译器无法确定链接每个库时要使用的格式，编译器将发出警告。

总的来说， `--crate-type=bin` 或 `--crate-type=lib` 应该足以满足所有编译需求，其他选项只是提供更细粒度的控制，以便更好地控制 crate 的输出格式。
{==+==}


{==+==}
## Static and dynamic C runtimes

The standard library in general strives to support both statically linked and
dynamically linked C runtimes for targets as appropriate. For example the
`x86_64-pc-windows-msvc` and `x86_64-unknown-linux-musl` targets typically come
with both runtimes and the user selects which one they'd like. All targets in
the compiler have a default mode of linking to the C runtime. Typically targets
are linked dynamically by default, but there are exceptions which are static by
default such as:

* `arm-unknown-linux-musleabi`
* `arm-unknown-linux-musleabihf`
* `armv7-unknown-linux-musleabihf`
* `i686-unknown-linux-musl`
* `x86_64-unknown-linux-musl`

The linkage of the C runtime is configured to respect the `crt-static` target
feature. These target features are typically configured from the command line
via flags to the compiler itself. For example to enable a static runtime you
would execute:

```sh
rustc -C target-feature=+crt-static foo.rs
```

whereas to link dynamically to the C runtime you would execute:

```sh
rustc -C target-feature=-crt-static foo.rs
```
{==+==}
## 静态和动态C运行时

通常，标准库会尽可能地支持适当目标的静态链接和动态链接 C 运行时。例如， `x86_64-pc-windows-msvc` 和 `x86_64-unknown-linux-musl` 目标通常都带有两种运行时，用户可以选择其中一个。编译器中的所有目标都有链接到 C 运行时的默认模式。通常，默认情况下目标是动态链接的，但也有一些例外，默认情况下是静态链接的，例如：

* `arm-unknown-linux-musleabi`
* `arm-unknown-linux-musleabihf`
* `armv7-unknown-linux-musleabihf`
* `i686-unknown-linux-musl`
* `x86_64-unknown-linux-musl`

C 运行时的链接是配置为遵守 `crt-static` 目标特性的。这些目标特性通常是通过编译器本身的标志从命令行配置的。例如，要启用静态运行时，你将执行：

```sh
rustc -C target-feature=+crt-static foo.rs
```

而要动态链接到 C 运行时，则会执行：

```sh
rustc -C target-feature=-crt-static foo.rs
```
{==+==}


{==+==}
Targets which do not support switching between linkage of the C runtime will
ignore this flag. It's recommended to inspect the resulting binary to ensure
that it's linked as you would expect after the compiler succeeds.

Crates may also learn about how the C runtime is being linked. Code on MSVC, for
example, needs to be compiled differently (e.g. with `/MT` or `/MD`) depending
on the runtime being linked. This is exported currently through the
[`cfg` attribute `target_feature` option]:
{==+==}
不支持在 C 运行时链接方式之间切换的目标将忽略此标志。建议在编译器成功后检查生成的二进制文件，确保它被链接为你所期望的方式。

Crates 也可以了解 C 运行时的链接方式。例如，在 MSVC 上编写的代码需要根据链接的运行时以不同的方式编译 (例如使用 `/MT` 或 `/MD` ) 。这是通过 [`cfg` 属性 `target_feature` 选项][`cfg` attribute `target_feature` option] 导出的：
{==+==}


{==+==}
```rust
#[cfg(target_feature = "crt-static")]
fn foo() {
    println!("the C runtime should be statically linked");
}

#[cfg(not(target_feature = "crt-static"))]
fn foo() {
    println!("the C runtime should be dynamically linked");
}
```
{==+==}

{==+==}


{==+==}
Also note that Cargo build scripts can learn about this feature through
[environment variables][cargo]. In a build script you can detect the linkage
via:
{==+==}
还请注意，Cargo 构建脚本可以通过 [环境变量][cargo] 了解这个特性。在构建脚本中，你可以通过以下方式检测链接方式：
{==+==}


{==+==}
```rust
use std::env;

fn main() {
    let linkage = env::var("CARGO_CFG_TARGET_FEATURE").unwrap_or(String::new());

    if linkage.contains("crt-static") {
        println!("the C runtime will be statically linked");
    } else {
        println!("the C runtime will be dynamically linked");
    }
}
```
{==+==}

{==+==}


{==+==}
[cargo]: ../cargo/reference/environment-variables.html#environment-variables-cargo-sets-for-build-scripts
{==+==}

{==+==}


{==+==}
To use this feature locally, you typically will use the `RUSTFLAGS` environment
variable to specify flags to the compiler through Cargo. For example to compile
a statically linked binary on MSVC you would execute:
{==+==}
要在本地使用此功能，通常会使用 `RUSTFLAGS` 环境变量通过 Cargo 指定编译器的标志。例如，在 MSVC 上编译静态链接的二进制文件，你可以执行：
{==+==}


{==+==}
```sh
RUSTFLAGS='-C target-feature=+crt-static' cargo build --target x86_64-pc-windows-msvc
```
{==+==}

{==+==}


{==+==}
[`cfg` attribute `target_feature` option]: conditional-compilation.md#target_feature
[configuration option]: conditional-compilation.md
[procedural macros]: procedural-macros.md
{==+==}

{==+==}