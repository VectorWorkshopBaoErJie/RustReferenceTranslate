{==+==}
# Destructors

When an [initialized]&#32;[variable] or [temporary] goes out of
[scope](#drop-scopes), its *destructor* is run, or it is *dropped*. [Assignment]
also runs the destructor of its left-hand operand, if it's initialized. If a
variable has been partially initialized, only its initialized fields are
dropped.

The destructor of a type `T` consists of:

1. If `T: Drop`, calling [`<T as std::ops::Drop>::drop`]
2. Recursively running the destructor of all of its fields.
    * The fields of a [struct] are dropped in declaration order.
    * The fields of the active [enum variant] are dropped in declaration order.
    * The fields of a [tuple] are dropped in order.
    * The elements of an [array] or owned [slice] are dropped from the
      first element to the last.
    * The variables that a [closure] captures by move are dropped in an
      unspecified order.
    * [Trait objects] run the destructor of the underlying type.
    * Other types don't result in any further drops.

If a destructor must be run manually, such as when implementing your own smart
pointer, [`std::ptr::drop_in_place`] can be used.
{==+==}
# 析构函数

当一个 [已初始化过][initialized] 的 [变量][variable] 或 [临时值][temporary] 超出 [作用域](#drop-scopes) 时，会执行其 *析构函数* ，或者说会被 *销毁* 。
[赋值][Assignment] 时会运行其已初始化的左操作数的析构函数。
如果变量只有部分已初始化，那么只销毁已初始化字段。

类型 T 的析构函数包括:

1. 如果 `T: Drop` ，则调用 [`<T as std::ops::Drop>::drop`] 。
2. 递归运行其所有字段的析构函数。
     * [结构体][struct] 的字段按声明顺序被销毁。
     * [枚举类型][enum variant] 的激活变体字段按声明顺序被销毁。
     * [元组][tuple] 的字段按顺序被销毁。
     * [数组][array] 或拥有的 [切片][slice] 的元素从第一个元素到最后一个元素被销毁。
     * [闭包][closure] 通过移动捕获的变量按一个未指定的顺序被销毁。
     * [Trait 对象][Trait objects] 运行底层类型的析构函数。
     * 其他类型不会导致进一步的销毁。

如果必须手动运行析构函数，比如在实现自定义的智能指针时，可以使用 [`std::ptr::drop_in_place`] 。
{==+==}


{==+==}
Some examples:

```rust
struct PrintOnDrop(&'static str);

impl Drop for PrintOnDrop {
    fn drop(&mut self) {
        println!("{}", self.0);
    }
}

let mut overwritten = PrintOnDrop("drops when overwritten");
overwritten = PrintOnDrop("drops when scope ends");

let tuple = (PrintOnDrop("Tuple first"), PrintOnDrop("Tuple second"));

let moved;
// No destructor run on assignment.
moved = PrintOnDrop("Drops when moved");
// Drops now, but is then uninitialized.
moved;

// Uninitialized does not drop.
let uninitialized: PrintOnDrop;

// After a partial move, only the remaining fields are dropped.
let mut partial_move = (PrintOnDrop("first"), PrintOnDrop("forgotten"));
// Perform a partial move, leaving only `partial_move.0` initialized.
core::mem::forget(partial_move.1);
// When partial_move's scope ends, only the first field is dropped.
```
{==+==}
一些例子:

```rust
struct PrintOnDrop(&'static str);

impl Drop for PrintOnDrop {
    fn drop(&mut self) {
        println!("{}", self.0);
    }
}

// 当左操作数被覆盖时会销毁
let mut overwritten = PrintOnDrop("drops when overwritten"); 
// 当作用域结束时会销毁
overwritten = PrintOnDrop("drops when scope ends"); 
// Tuple 的元素按顺序销毁
let tuple = (PrintOnDrop("Tuple first"), PrintOnDrop("Tuple second")); 

let moved;
// 在赋值时不会运行析构函数
moved = PrintOnDrop("Drops when moved"); 
// 现在被销毁，但后来处于未初始化状态
moved;

// 未初始化不会被销毁
let uninitialized: PrintOnDrop;

// 在部分移动后，只有剩余的字段会被销毁
let mut partial_move = (PrintOnDrop("first"), PrintOnDrop("forgotten"));
// 进行部分移动，只剩下 `partial_move.0` 被初始化
core::mem::forget(partial_move.1);
// 当 `partial_move` 的作用域结束时，只有第一个字段被销毁。
```
{==+==}


{==+==}
## Drop scopes

Each variable or temporary is associated to a *drop scope*. When control flow
leaves a drop scope all variables associated to that scope are dropped in
reverse order of declaration (for variables) or creation (for temporaries).

Drop scopes are determined after replacing [`for`], [`if let`], and
[`while let`] expressions with the equivalent expressions using [`match`].
Overloaded operators are not distinguished from built-in operators and [binding
modes] are not considered.

Given a function, or closure, there are drop scopes for:

* The entire function
* Each [statement]
* Each [expression]
* Each block, including the function body
    * In the case of a [block expression], the scope for the block and the
      expression are the same scope.
* Each arm of a `match` expression

Drop scopes are nested within one another as follows. When multiple scopes are
left at once, such as when returning from a function, variables are dropped
from the inside outwards.
{==+==}
## 析构作用域

每个变量或临时变量都与 *析构作用域* 相关联。
当控制流离开析构作用域时，与该作用域相关联的所有变量按照声明 (对于变量) 或创建 (对于临时变量) 的相反顺序进行销毁。
在使用 [`match`] 表达式替换 [`for`] 、 [`if let`] 和 [`while let`] 表达式之后，确定析构作用域。
重载的操作符与内置的操作符没有区别，不考虑绑定模式。
对于函数或闭包，有以下析构作用域:

* 整个函数
* 每个 [语句][statement]
* 每个 [表达式][expression]
* 每个块，包括函数体  
    * 对于 [块表达式][block expression] 块和表达式的作用域是同一作用域。
* `match` 表达式的每个分支

析构作用域按如下嵌套。当一次离开多个作用域时，例如从函数返回时，变量从内向外进行销毁。
{==+==}


{==+==}
* The entire function scope is the outer most scope.
* The function body block is contained within the scope of the entire function.
* The parent of the expression in an expression statement is the scope of the
  statement.
* The parent of the initializer of a [`let` statement] is the `let` statement's
  scope.
* The parent of a statement scope is the scope of the block that contains the
  statement.
* The parent of the expression for a `match` guard is the scope of the arm that
  the guard is for.
* The parent of the expression after the `=>` in a `match` expression is the
  scope of the arm that it's in.
* The parent of the arm scope is the scope of the `match` expression that it
  belongs to.
* The parent of all other scopes is the scope of the immediately enclosing
  expression.
{==+==}
* 整个函数的作用域是最外层的作用域。
* 函数体块包含在整个函数的作用域内。
* 表达式语句中表达式的父级是该语句的作用域。
* [`let` 语句][`let` statement] 中初始化器的父级是 `let` 语句的作用域。
* 语句作用域的父级是包含该语句的块的作用域。
* `match` 守卫中表达式的父级是守卫所属分支的作用域。
* `=>` 后的表达式在 `match` 表达式中所属分支的作用域内。
* 分支作用域的父级是所属的 `match` 表达式的作用域。
* 其他所有作用域的父级是最近的封闭表达式的作用域。
{==+==}


{==+==}
### Scopes of function parameters

All function parameters are in the scope of the entire function body, so are
dropped last when evaluating the function. Each actual function parameter is
dropped after any bindings introduced in that parameter's pattern.
{==+==}
### 函数参数的作用域

所有函数参数都在整个函数体的作用域内，参数被最后丢弃。
函数的实参在该参数模式中引入后，其绑定之后被丢弃。
{==+==}


{==+==}
```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
// Drops `y`, then the second parameter, then `x`, then the first parameter
fn patterns_in_parameters(
    (x, _): (PrintOnDrop, PrintOnDrop),
    (_, y): (PrintOnDrop, PrintOnDrop),
) {}

// drop order is 3 2 0 1
patterns_in_parameters(
    (PrintOnDrop("0"), PrintOnDrop("1")),
    (PrintOnDrop("2"), PrintOnDrop("3")),
);
```
{==+==}
```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
// 按顺序丢弃 `y`、第二个参数、`x`，最后第一个参数
fn patterns_in_parameters(
    (x, _): (PrintOnDrop, PrintOnDrop),
    (_, y): (PrintOnDrop, PrintOnDrop),
) {}

// 丢弃顺序为 3 2 0 1
patterns_in_parameters(
    (PrintOnDrop("0"), PrintOnDrop("1")),
    (PrintOnDrop("2"), PrintOnDrop("3")),
);
```
{==+==}


{==+==}
### Scopes of local variables

Local variables declared in a `let` statement are associated to the scope of
the block that contains the `let` statement. Local variables declared in a
`match` expression are associated to the arm scope of the `match` arm that they
are declared in.
{==+==}
### 局部变量的作用域

在 `let` 语句中声明的局部变量与包含 `let` 语句的块的作用域相关联。
在 `match` 表达式中声明的局部变量与它们声明在的 `match` 分支的作用域相关联。
{==+==}


{==+==}
```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
let declared_first = PrintOnDrop("Dropped last in outer scope");
{
    let declared_in_block = PrintOnDrop("Dropped in inner scope");
}
let declared_last = PrintOnDrop("Dropped first in outer scope");
```
{==+==}

{==+==}


{==+==}
If multiple patterns are used in the same arm for a `match` expression, then an
unspecified pattern will be used to determine the drop order.

### Temporary scopes

The *temporary scope* of an expression is the scope that is used for the
temporary variable that holds the result of that expression when used in a
[place context], unless it is [promoted].

Apart from lifetime extension, the temporary scope of an expression is the
smallest scope that contains the expression and is one of the following:

* The entire function body.
* A statement.
* The body of an [`if`], [`while`] or [`loop`] expression.
* The `else` block of an `if` expression.
* The condition expression of an `if` or `while` expression, or a `match`
  guard.
* The expression for a match arm.
* The second operand of a [lazy boolean expression].
{==+==}
如果在 `match` 表达式的同一个 `arm` 中使用了多个模式，则将使用非特定顺序的模式来确定释放。

### 临时作用域

表达式的 *临时作用域* 是在 [占位上下文][place context] 中使用该表达式的结果时用于保存临时变量的作用域，
除非它被 [提升][promoted] 。除了生命周期扩展之外，表达式的临时作用域是包含该表达式的最小作用域，并且是以下之一：

* 整个函数体。
* 一个语句。
* [`if`] ， [`while`] 或 [`loop`] 表达式的主体。
* `if` 表达式的 `else` 块 。
* `if` 或 `while` 表达式的条件表达式，或 `match` 守卫。
* match 分支的表达式。
* [惰性布尔表达式][lazy boolean expression] 的第二个操作数。
{==+==}


{==+==}
> **Notes**:
>
> Temporaries that are created in the final expression of a function
> body are dropped *after* any named variables bound in the function body, as
> there is no smaller enclosing temporary scope.
>
> The [scrutinee] of a `match` expression is not a temporary scope, so
> temporaries in the scrutinee can be dropped after the `match` expression. For
> example, the temporary for `1` in `match 1 { ref mut z => z };` lives until
> the end of the statement.
{==+==}
> **注意**:
> 
> 在函数体的最终表达式中创建的临时变量将在函数体绑定的任何命名变量之后释放，因为没有更小的封闭临时作用域。
> 
> `match` 表达式的 [被匹配项][scrutinee] 不是临时作用域，因此可以在 `match` 表达式之后丢弃被匹配项中的临时变量。
> 例如，在 `match 1 { ref mut z => z };` 中的 `1` 的临时变量将一直存在到语句的结尾。
{==+==}


{==+==}
Some examples:

```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
let local_var = PrintOnDrop("local var");

// Dropped once the condition has been evaluated
if PrintOnDrop("If condition").0 == "If condition" {
    // Dropped at the end of the block
    PrintOnDrop("If body").0
} else {
    unreachable!()
};

// Dropped at the end of the statement
(PrintOnDrop("first operand").0 == ""
// Dropped at the )
|| PrintOnDrop("second operand").0 == "")
// Dropped at the end of the expression
|| PrintOnDrop("third operand").0 == "";

// Dropped at the end of the function, after local variables.
// Changing this to a statement containing a return expression would make the
// temporary be dropped before the local variables. Binding to a variable
// which is then returned would also make the temporary be dropped first.
match PrintOnDrop("Matched value in final expression") {
    // Dropped once the condition has been evaluated
    _ if PrintOnDrop("guard condition").0 == "" => (),
    _ => (),
}
```
{==+==}
一些例子:

```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
let local_var = PrintOnDrop("局部变量");

// 一旦条件被评估，将被丢弃
if PrintOnDrop("if条件").0 == "if条件" {
    // 在块的末尾被丢弃
    PrintOnDrop("if块体").0
} else {
    unreachable!()
};

// 在语句的末尾被丢弃
(PrintOnDrop("第一个操作数").0 == ""
// 在 ) 处被丢弃
|| PrintOnDrop("第二个操作数").0 == "")
// 在表达式的末尾被丢弃
|| PrintOnDrop("第三个操作数").0 == "";

// 在函数的末尾被丢弃，在局部变量之后。
// 将其更改为包含返回表达式的语句将使临时变量在局部变量之前被释放。
// 将其绑定到变量并返回该变量也会使临时变量首先被释放。
match PrintOnDrop("匹配的值，用于最终表达式") {
    // 一旦条件被评估，将被丢弃
    _ if PrintOnDrop("守卫条件").0 == "" => (),
    _ => (),
}
```
{==+==}


{==+==}
### Operands

Temporaries are also created to hold the result of operands to an expression
while the other operands are evaluated. The temporaries are associated to the
scope of the expression with that operand. Since the temporaries are moved from
once the expression is evaluated, dropping them has no effect unless one of the
operands to an expression breaks out of the expression, returns, or panics.
{==+==}
### 操作数

在表达式中，为了保存操作数的结果，也会创建临时变量，而计算其他操作数。
这些临时变量与该操作数的表达式作用域相关联。
由于临时变量在计算表达式后被移动，除非表达式的操作数之一中断表达式、返回或抛出异常，否则将其丢弃不会产生副作用。
{==+==}


{==+==}
```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
loop {
    // Tuple expression doesn't finish evaluating so operands drop in reverse order
    (
        PrintOnDrop("Outer tuple first"),
        PrintOnDrop("Outer tuple second"),
        (
            PrintOnDrop("Inner tuple first"),
            PrintOnDrop("Inner tuple second"),
            break,
        ),
        PrintOnDrop("Never created"),
    );
}
```
{==+==}
```rust
# struct PrintOnDrop(&'static str);
# impl Drop for PrintOnDrop {
#     fn drop(&mut self) {
#         println!("drop({})", self.0);
#     }
# }
loop {
    // 元组表达式没有完成计算，因此操作数按相反的顺序丢弃
    (
        PrintOnDrop("外部元组第一个"),
        PrintOnDrop("外部元组第二个"),
        (
            PrintOnDrop("内部元组第一个"),
            PrintOnDrop("内部元组第二个"),
            break, // 循环中断，后面的表达式不会执行
        ),
        PrintOnDrop("永远不会创建"),
    );
}
```
{==+==}


{==+==}
### Constant promotion

Promotion of a value expression to a `'static` slot occurs when the expression
could be written in a constant and borrowed, and that borrow could be dereferenced
where
the expression was originally written, without changing the runtime behavior.
That is, the promoted expression can be evaluated at compile-time and the
resulting value does not contain [interior mutability] or [destructors] (these
properties are determined based on the value where possible, e.g. `&None`
always has the type `&'static Option<_>`, as it contains nothing disallowed).
{==+==}
### 常量晋升

将值表达式晋升为 `'static` 发生在表达式写成常量并借用时，其借用可以在不改变运行时行为的情况下，在表达式原始写法的位置进行解引用。
也就是说，晋升的表达式可以在编译时计算，所得到的值不包含 [内部可变性][interior mutability] 或 [析构函数][destructors] 
(这些属性在可能的情况下基于值来确定，例如， `&None` 始终具有类型 `&'static Option<_>` ，因为它不包含任何被禁止的内容)。
{==+==}


{==+==}
### Temporary lifetime extension

> **Note**: The exact rules for temporary lifetime extension are subject to
> change. This is describing the current behavior only.

The temporary scopes for expressions in `let` statements are sometimes
*extended* to the scope of the block containing the `let` statement. This is
done when the usual temporary scope would be too small, based on certain
syntactic rules. For example:
{==+==}
### 临时值生命周期延长

> **注意**: 临时值生命周期延长的确切规则可能会发生变化。本文描述的是当前行为。

在 `let` 语句中的表达式的临时值作用域有时会被 *延长* 到包含 `let` 语句的块的作用域。
这是根据某些语法规则判断，当通常的临时值作用域太小时进行的。例如：
{==+==}


{==+==}
```rust
let x = &mut 0;
// Usually a temporary would be dropped by now, but the temporary for `0` lives
// to the end of the block.
println!("{}", x);
```
{==+==}
```rust
let x = &mut 0;
// 通常此时一个临时变量已经被丢弃了，但是 `0` 的临时变量会一直存活到该块的结尾。
println!("{}", x);
```
{==+==}


{==+==}
If a [borrow][borrow expression], [dereference][dereference expression],
[field][field expression], or [tuple indexing expression] has an extended
temporary scope then so does its operand. If an [indexing expression] has an
extended temporary scope then the indexed expression also has an extended
temporary scope.

#### Extending based on patterns

An *extending pattern* is either

* An [identifier pattern] that binds by reference or mutable reference.
* A [struct][struct pattern], [tuple][tuple pattern], [tuple struct][tuple
  struct pattern], or [slice][slice pattern] pattern where at least one of the
  direct subpatterns is a extending pattern.

So `ref x`, `V(ref x)` and `[ref x, y]` are all extending patterns, but `x`,
`&ref x` and `&(ref x,)` are not.

If the pattern in a `let` statement is an extending pattern then the temporary
scope of the initializer expression is extended.
{==+==}
如果一个 [借用表达式][borrow expression] 、 [解引用表达式][dereference expression] 、 [字段表达式][field expression] 或 [元组索引表达式][tuple indexing expression] 具有扩展临时作用域，则其操作数也具有扩展临时作用域。
如果一个 [索引表达式][indexing expression] 具有扩展临时作用域，则索引表达式也具有扩展临时作用域。

#### 基于模式的扩展

一个 *扩展模式* 可以是：

* 一个通过引用或可变引用绑定的 [标识符模式][identifier pattern] 。
* 一个 [结构体模式][struct pattern] 、 [元组模式][tuple pattern] 、 [元组结构体模式][tuple struct pattern] 或 [切片模式][slice pattern] ，其中至少一个直接子模式是扩展模式。
因此， `ref x` 、 `V(ref x)` 和 `[ref x, y]` 都是扩展模式，但 `x` 、 `&ref x` 和 `&(ref x,)` 不是。如果 `let` 语句中的模式是扩展模式，则初始化表达式的临时作用域会被扩展。
{==+==}


{==+==}
#### Extending based on expressions

For a let statement with an initializer, an *extending expression* is an
expression which is one of the following:

* The initializer expression.
* The operand of an extending [borrow expression].
* The operand(s) of an extending [array][array expression], [cast][cast
  expression], [braced struct][struct expression], or [tuple][tuple expression]
  expression.
* The final expression of any extending [block expression].

So the borrow expressions in `&mut 0`, `(&1, &mut 2)`, and `Some { 0: &mut 3 }`
are all extending expressions. The borrows in `&0 + &1` and `Some(&mut 0)` are
not: the latter is syntactically a function call expression.

The operand of any extending borrow expression has its temporary scope
extended.
{==+==}
#### 基于表达式的扩展

对于带有初始化器的 `let` 语句，一个 *扩展表达式* 是以下表达式之一:

* 初始化表达式。
* 扩展的 [借用表达式][borrow expression] 的操作数。
* 扩展的 [数组表达式][array expression] 、 [强制类型转换表达式][cast expression] 、 [括号结构体表达式][struct expression] 或 [元组表达式][tuple expression] 的操作数。
* 任何扩展的 [block 表达式][block expression] 的最后一个表达式。

因此， `&mut 0` 、 `(&1, &mut 2)` 和 `Some {0: &mut 3}` 中的借用表达式都是扩展表达式。 `&0 + &1` 和 `Some(&mut 0)` 中的借用表达式不是扩展表达式：后者在语法上是一个函数调用表达式。

任何扩展借用表达式的操作数都会扩展其临时作用域。
{==+==}


{==+==}
#### Examples

Here are some examples where expressions have extended temporary scopes:
{==+==}
#### 示例

以下是一些表达式具有扩展临时作用域的示例:
{==+==}


{==+==}
```rust
# fn temp() {}
# trait Use { fn use_temp(&self) -> &Self { self } }
# impl Use for () {}
// The temporary that stores the result of `temp()` lives in the same scope
// as x in these cases.
let x = &temp();
let x = &temp() as &dyn Send;
let x = (&*&temp(),);
let x = { [Some { 0: &temp(), }] };
let ref x = temp();
let ref x = *&temp();
# x;
```
{==+==}
```rust
# fn temp() {}
# trait Use { fn use_temp(&self) -> &Self { self } }
# impl Use for () {}
// 存储 `temp()` 结果的临时变量在以下这些情况下与 x 具有相同的作用域。
let x = &temp();
let x = &temp() as &dyn Send;
let x = (&*&temp(),);
let x = { [Some { 0: &temp(), }] };
let ref x = temp();
let ref x = *&temp();
# x;
```
{==+==}


{==+==}
Here are some examples where expressions don't have extended temporary scopes:
{==+==}
以下是一些表达式没有扩展临时作用域的示例:
{==+==}


{==+==}
```rust,compile_fail
# fn temp() {}
# trait Use { fn use_temp(&self) -> &Self { self } }
# impl Use for () {}
// The temporary that stores the result of `temp()` only lives until the
// end of the let statement in these cases.

let x = Some(&temp());         // ERROR
let x = (&temp()).use_temp();  // ERROR
# x;
```
{==+==}
```rust,compile_fail
# fn temp() {}
# trait Use { fn use_temp(&self) -> &Self { self } }
# impl Use for () {}
// 存储`temp()`结果的临时变量在以下这些情况下只能在 let 语句结束之前使用。

let x = Some(&temp());         // 错误
let x = (&temp()).use_temp();  // 错误
# x;
```
{==+==}


{==+==}
## Not running destructors

[`std::mem::forget`] can be used to prevent the destructor of a variable from being run,
and [`std::mem::ManuallyDrop`] provides a wrapper to prevent a
variable or field from being dropped automatically.

> Note: Preventing a destructor from being run via [`std::mem::forget`] or other means is safe even if it has a type that isn't `'static`.
> Besides the places where destructors are guaranteed to run as defined by this document, types may *not* safely rely on a destructor being run for soundness.
{==+==}
## 不运行析构函数

[`std::mem::forget`] 可以用来防止变量的析构函数被执行，而 [`std::mem::ManuallyDrop`] 提供了一个包装器来防止变量或字段被自动释放。

> 注意：通过 [`std::mem::forget`] 或其他方式防止析构函数被执行即使具有不是 `'static` 类型的类型也是安全的。
> 除了在本文档中定义的保证运行析构函数的地方之外，类型不能依赖析构函数被运行来确保安全性。
{==+==}


{==+==}
[Assignment]: expressions/operator-expr.md#assignment-expressions
[binding modes]: patterns.md#binding-modes
[closure]: types/closure.md
[destructors]: destructors.md
[expression]: expressions.md
[identifier pattern]: patterns.md#identifier-patterns
[initialized]: glossary.md#initialized
[interior mutability]: interior-mutability.md
[lazy boolean expression]: expressions/operator-expr.md#lazy-boolean-operators
[place context]: expressions.md#place-expressions-and-value-expressions
[promoted]: destructors.md#constant-promotion
[scrutinee]: glossary.md#scrutinee
[statement]: statements.md
[temporary]: expressions.md#temporaries
[variable]: variables.md

[array]: types/array.md
[enum variant]: types/enum.md
[slice]: types/slice.md
[struct]: types/struct.md
[Trait objects]: types/trait-object.md
[tuple]: types/tuple.md

[slice pattern]: patterns.md#slice-patterns
[struct pattern]: patterns.md#struct-patterns
[tuple pattern]: patterns.md#tuple-patterns
[tuple struct pattern]: patterns.md#tuple-struct-patterns

[array expression]: expressions/array-expr.md#array-expressions
[block expression]: expressions/block-expr.md
[borrow expression]: expressions/operator-expr.md#borrow-operators
[cast expression]: expressions/operator-expr.md#type-cast-expressions
[dereference expression]: expressions/operator-expr.md#the-dereference-operator
[field expression]: expressions/field-expr.md
[indexing expression]: expressions/array-expr.md#array-and-slice-indexing-expressions
[struct expression]: expressions/struct-expr.md
[tuple expression]: expressions/tuple-expr.md#tuple-expressions
[tuple indexing expression]: expressions/tuple-expr.md#tuple-indexing-expressions

[`for`]: expressions/loop-expr.md#iterator-loops
[`if let`]: expressions/if-expr.md#if-let-expressions
[`if`]: expressions/if-expr.md#if-expressions
[`let` statement]: statements.md#let-statements
[`loop`]: expressions/loop-expr.md#infinite-loops
[`match`]: expressions/match-expr.md
[`while let`]: expressions/loop-expr.md#predicate-pattern-loops
[`while`]: expressions/loop-expr.md#predicate-loops

[`<T as std::ops::Drop>::drop`]: ../std/ops/trait.Drop.html#tymethod.drop
[`std::ptr::drop_in_place`]: ../std/ptr/fn.drop_in_place.html
[`std::mem::forget`]: ../std/mem/fn.forget.html
[`std::mem::ManuallyDrop`]: ../std/mem/struct.ManuallyDrop.html
{==+==}

{==+==}