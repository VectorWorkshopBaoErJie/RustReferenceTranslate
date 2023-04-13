{==+==}
# The Rust Reference

[Introduction](introduction.md)

- [Notation](notation.md)

- [Lexical structure](lexical-structure.md)
    - [Input format](input-format.md)
    - [Keywords](keywords.md)
    - [Identifiers](identifiers.md)
    - [Comments](comments.md)
    - [Whitespace](whitespace.md)
    - [Tokens](tokens.md)

- [Macros](macros.md)
    - [Macros By Example](macros-by-example.md)
    - [Procedural Macros](procedural-macros.md)

- [Crates and source files](crates-and-source-files.md)

- [Conditional compilation](conditional-compilation.md)
{==+==}
# Rust 参考手册

[介绍](introduction.md)

- [符号约定](notation.md)

- [词法结构](lexical-structure.md)
    - [输入格式](input-format.md)
    - [关键字](keywords.md)
    - [标识符](identifiers.md)
    - [注释](comments.md)
    - [空白](whitespace.md)
    - [记号](tokens.md)

- [宏](macros.md)
    - [实例宏](macros-by-example.md)
    - [过程宏](procedural-macros.md)

- [Crate和源文件](crates-and-source-files.md)

- [条件编译](conditional-compilation.md)
{==+==}


{==+==}
- [Items](items.md)
    - [Modules](items/modules.md)
    - [Extern crates](items/extern-crates.md)
    - [Use declarations](items/use-declarations.md)
    - [Functions](items/functions.md)
    - [Type aliases](items/type-aliases.md)
    - [Structs](items/structs.md)
    - [Enumerations](items/enumerations.md)
    - [Unions](items/unions.md)
    - [Constant items](items/constant-items.md)
    - [Static items](items/static-items.md)
    - [Traits](items/traits.md)
    - [Implementations](items/implementations.md)
    - [External blocks](items/external-blocks.md)
    - [Generic parameters](items/generics.md)
    - [Associated Items](items/associated-items.md)
{==+==}
- [条目](items.md)
    - [模块](items/modules.md)
    - [外部Crate](items/extern-crates.md)
    - [Use 声明](items/use-declarations.md)
    - [函数](items/functions.md)
    - [类型别名](items/type-aliases.md)
    - [结构体](items/structs.md)
    - [枚举](items/enumerations.md)
    - [联合体](items/unions.md)
    - [常量条目](items/constant-items.md)
    - [静态条目](items/static-items.md)
    - [Trait](items/traits.md)
    - [实现](items/implementations.md)
    - [外部块](items/external-blocks.md)
    - [泛型参数](items/generics.md)
    - [关联条目](items/associated-items.md)
{==+==}


{==+==}
- [Attributes](attributes.md)
    - [Testing](attributes/testing.md)
    - [Derive](attributes/derive.md)
    - [Diagnostics](attributes/diagnostics.md)
    - [Code generation](attributes/codegen.md)
    - [Limits](attributes/limits.md)
    - [Type System](attributes/type_system.md)
{==+==}
- [属性](attributes.md)
    - [测试](attributes/testing.md)
    - [派生](attributes/derive.md)
    - [诊断](attributes/diagnostics.md)
    - [代码生成](attributes/codegen.md)
    - [约束](attributes/limits.md)
    - [类型系统](attributes/type_system.md)
{==+==}


{==+==}
- [Statements and expressions](statements-and-expressions.md)
    - [Statements](statements.md)
    - [Expressions](expressions.md)
        - [Literal expressions](expressions/literal-expr.md)
        - [Path expressions](expressions/path-expr.md)
        - [Block expressions](expressions/block-expr.md)
        - [Operator expressions](expressions/operator-expr.md)
        - [Grouped expressions](expressions/grouped-expr.md)
        - [Array and index expressions](expressions/array-expr.md)
        - [Tuple and index expressions](expressions/tuple-expr.md)
        - [Struct expressions](expressions/struct-expr.md)
        - [Call expressions](expressions/call-expr.md)
        - [Method call expressions](expressions/method-call-expr.md)
        - [Field access expressions](expressions/field-expr.md)
        - [Closure expressions](expressions/closure-expr.md)
        - [Loop expressions](expressions/loop-expr.md)
        - [Range expressions](expressions/range-expr.md)
        - [If and if let expressions](expressions/if-expr.md)
        - [Match expressions](expressions/match-expr.md)
        - [Return expressions](expressions/return-expr.md)
        - [Await expressions](expressions/await-expr.md)
        - [Underscore expressions](expressions/underscore-expr.md)
{==+==}
- [语句和表达式](statements-and-expressions.md)
    - [语句](statements.md)
    - [表达式](expressions.md)
        - [字面值表达式](expressions/literal-expr.md)
        - [路径表达式](expressions/path-expr.md)
        - [块表达式](expressions/block-expr.md)
        - [运算符表达式](expressions/operator-expr.md)
        - [分组表达式](expressions/grouped-expr.md)
        - [数组和索引表达式](expressions/array-expr.md)
        - [元组和索引表达式](expressions/tuple-expr.md)
        - [结构体表达式](expressions/struct-expr.md)
        - [调用表达式](expressions/call-expr.md)
        - [方法调用表达式](expressions/method-call-expr.md)
        - [字段访问表达式](expressions/field-expr.md)
        - [闭包表达式](expressions/closure-expr.md)
        - [循环表达式](expressions/loop-expr.md)
        - [区间表达式](expressions/range-expr.md)
        - [if 和 if let 表达式](expressions/if-expr.md)
        - [匹配表达式](expressions/match-expr.md)
        - [返回表达式](expressions/return-expr.md)
        - [等待表达式](expressions/await-expr.md)
        - [下划线表达式](expressions/underscore-expr.md)
{==+==}


{==+==}
- [Patterns](patterns.md)

- [Type system](type-system.md)
    - [Types](types.md)
        - [Boolean type](types/boolean.md)
        - [Numeric types](types/numeric.md)
        - [Textual types](types/textual.md)
        - [Never type](types/never.md)
        - [Tuple types](types/tuple.md)
        - [Array types](types/array.md)
        - [Slice types](types/slice.md)
        - [Struct types](types/struct.md)
        - [Enumerated types](types/enum.md)
        - [Union types](types/union.md)
        - [Function item types](types/function-item.md)
        - [Closure types](types/closure.md)
        - [Pointer types](types/pointer.md)
        - [Function pointer types](types/function-pointer.md)
        - [Trait object types](types/trait-object.md)
        - [Impl trait type](types/impl-trait.md)
        - [Type parameters](types/parameters.md)
        - [Inferred type](types/inferred.md)
    - [Dynamically Sized Types](dynamically-sized-types.md)
    - [Type layout](type-layout.md)
    - [Interior mutability](interior-mutability.md)
    - [Subtyping and Variance](subtyping.md)
    - [Trait and lifetime bounds](trait-bounds.md)
    - [Type coercions](type-coercions.md)
    - [Destructors](destructors.md)
    - [Lifetime elision](lifetime-elision.md)
{==+==}
- [模式](patterns.md)

- [类型系统](type-system.md)
    - [类型](types.md)
        - [布尔类型](types/boolean.md)
        - [数字类型](types/numeric.md)
        - [文本类型](types/textual.md)
        - [永远不存在的类型](types/never.md)
        - [元组类型](types/tuple.md)
        - [数组类型](types/array.md)
        - [切片类型](types/slice.md)
        - [结构体类型](types/struct.md)
        - [枚举类型](types/enum.md)
        - [联合类型](types/union.md)
        - [函数条目类型](types/function-item.md)
        - [闭包类型](types/closure.md)
        - [指针类型](types/pointer.md)
        - [函数指针类型](types/function-pointer.md)
        - [Trait 对象类型](types/trait-object.md)
        - [实现 Trait 的类型](types/impl-trait.md)
        - [类型参数](types/parameters.md)
        - [推导出的类型](types/inferred.md)
    - [动态大小类型](dynamically-sized-types.md)
    - [类型布局](type-layout.md)
    - [内部可变性](interior-mutability.md)
    - [子类型和协变](subtyping.md)
    - [Trait 和生命周期边界](trait-bounds.md)
    - [类型强制转换](type-coercions.md)
    - [析构函数](destructors.md)
    - [生命周期省略](lifetime-elision.md)
{==+==}


{==+==}
- [Special types and traits](special-types-and-traits.md)

- [Names](names.md)
    - [Namespaces](names/namespaces.md)
    - [Scopes](names/scopes.md)
    - [Preludes](names/preludes.md)
    - [Paths](paths.md)
    - [Name resolution](names/name-resolution.md)
    - [Visibility and privacy](visibility-and-privacy.md)

- [Memory model](memory-model.md)
    - [Memory allocation and lifetime](memory-allocation-and-lifetime.md)
    - [Variables](variables.md)

- [Linkage](linkage.md)

- [Inline assembly](inline-assembly.md)
{==+==}
- [特殊类型和Trait](special-types-and-traits.md)

- [名称](names.md)
    - [命名空间](names/namespaces.md)
    - [作用域](names/scopes.md)
    - [预定义](names/preludes.md)
    - [路径](paths.md)
    - [名称解析](names/name-resolution.md)
    - [可见性和私有性](visibility-and-privacy.md)

- [内存模型](memory-model.md)
    - [内存分配和生命周期](memory-allocation-and-lifetime.md)
    - [变量](variables.md)

- [链接](linkage.md)

- [内联汇编](inline-assembly.md)
{==+==}


{==+==}
- [Unsafety](unsafety.md)
    - [The `unsafe` keyword](unsafe-keyword.md)
    - [Behavior considered undefined](behavior-considered-undefined.md)
    - [Behavior not considered unsafe](behavior-not-considered-unsafe.md)

- [Constant Evaluation](const_eval.md)

- [Application Binary Interface](abi.md)

- [The Rust runtime](runtime.md)

- [Appendices](appendices.md)
    - [Macro Follow-Set Ambiguity Formal Specification](macro-ambiguity.md)
    - [Influences](influences.md)
    - [Glossary](glossary.md)
{==+==}
- [Unsafety](unsafety.md)
    - [`unsafe` 关键字](unsafe-keyword.md)
    - [被视为未定义行为的行为](behavior-considered-undefined.md)
    - [不被视为不安全的行为](behavior-not-considered-unsafe.md)

- [常量求值](const_eval.md)

- [应用程序二进制接口](abi.md)

- [Rust 运行时](runtime.md)

- [附录](appendices.md)
    - [宏 Follow-Set 歧义正式规范](macro-ambiguity.md)
    - [影响](influences.md)
    - [术语表](glossary.md)
{==+==}