{==+==}
Labelled block expressions are exactly like block expressions, except that they allow using `break` expressions within the block.
Unlike loops, `break` expressions within a labelled block expression *must* have a label (i.e. the label is not optional).
Similarly, labelled block expressions *must* begin with a label.

```rust
# fn do_thing() {}
# fn condition_not_met() -> bool { true }
# fn do_next_thing() {}
# fn do_last_thing() {}
let result = 'block: {
    do_thing();
    if condition_not_met() {
        break 'block 1;
    }
    do_next_thing();
    if condition_not_met() {
        break 'block 2;
    }
    do_last_thing();
    3
};
```
{==+==}
带标签的块表达式与普通的块表达式非常相似，但是允许在块中使用 `break` 表达式。
与循环不同，标签块表达式中的 `break` 表达式必须有一个标签 (即标签是必须的)。
同样地，标签块表达式 *必须* 以标签开始。

```rust
# fn do_thing() {}
# fn condition_not_met() -> bool { true }
# fn do_next_thing() {}
# fn do_last_thing() {}
let result = 'block: {
    do_thing();
    if condition_not_met() {
        break 'block 1;
    }
    do_next_thing();
    if condition_not_met() {
        break 'block 2;
    }
    do_last_thing();
    3
};
```
{==+==}

