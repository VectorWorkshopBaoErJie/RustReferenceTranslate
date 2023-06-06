{==+==}
# Type parameters
{==+==}
# 类型参数
{==+==}


{==+==}
Within the body of an item that has type parameter declarations, the names of
its type parameters are types:
{==+==}
在具有类型参数声明的条目的主体内部，其类型参数名称表示的类型:
{==+==}


{==+==}
```rust
fn to_vec<A: Clone>(xs: &[A]) -> Vec<A> {
    if xs.is_empty() {
        return vec![];
    }
    let first: A = xs[0].clone();
    let mut rest: Vec<A> = to_vec(&xs[1..]);
    rest.insert(0, first);
    rest
}
```
{==+==}
```rust
// 定义了一个泛型函数 to_vec，其类型参数为 A，它要求 A 类型实现了 Clone trait
fn to_vec<A: Clone>(xs: &[A]) -> Vec<A> {
    // 如果 xs 数组为空，则直接返回空的 Vec
    if xs.is_empty() {
        return vec![];
    }
    // 定义变量 first，类型是 A，值是 xs 数组的第一个元素的克隆
    let first: A = xs[0].clone();
    // 定义变量 rest，它的类型是 Vec<A>，它的值是调用 to_vec 函数得到的结果，
    // 参数是 xs 数组的第 2 个元素到最后一个元素组成的切片。
    let mut rest: Vec<A> = to_vec(&xs[1..]);
    // 在 rest 的开头插入 first 元素
    rest.insert(0, first);
    // 返回更新后的 rest
    rest
}
```
{==+==}


{==+==}
Here, `first` has type `A`, referring to `to_vec`'s `A` type parameter; and
`rest` has type `Vec<A>`, a vector with element type `A`.
{==+==}
这里， `first` 的类型为 `A` ，引用了 `to_vec` 的类型参数 `A` ; `rest` 的类型为 `Vec<A>` ，即元素类型为 `A` 的 vector 。
{==+==}