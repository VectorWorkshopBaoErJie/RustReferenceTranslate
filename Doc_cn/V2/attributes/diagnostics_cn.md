{==+==}
```rust
#[warn(missing_docs)]
pub mod m2 {
    #[allow(missing_docs)]
    pub mod nested {
        // Missing documentation is ignored here
        pub fn undocumented_one() -> i32 { 1 }

        // Missing documentation signals a warning here,
        // despite the allow above.
        #[warn(missing_docs)]
        pub fn undocumented_two() -> i32 { 2 }
    }

    // Missing documentation signals a warning here
    pub fn undocumented_too() -> i32 { 3 }
}
```
{==+==}
```rust
#[warn(missing_docs)] // 发出警告：缺失文档注释
pub mod m2 {
    #[allow(missing_docs)] // 忽略警告：缺失文档注释
    pub mod nested {
        // 缺失文档注释，被忽略
        pub fn undocumented_one() -> i32 { 1 }

        // 缺失文档注释，发出警告
        // 尽管上面有 allow 指示忽略警告
        #[warn(missing_docs)]
        pub fn undocumented_two() -> i32 { 2 }
    }

    // 缺失文档注释，发出警告
    pub fn undocumented_too() -> i32 { 3 }
}
```
{==+==}
