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

{==+==}
