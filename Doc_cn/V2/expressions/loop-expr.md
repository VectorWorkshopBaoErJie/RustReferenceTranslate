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


{==+==}

