{==+==}
# Items
{==+==}
# 条目
{==+==}


{==+==}
> **<sup>Syntax:<sup>**\
> _Item_:\
> &nbsp;&nbsp; [_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _VisItem_\
> &nbsp;&nbsp; | _MacroItem_
>
> _VisItem_:\
> &nbsp;&nbsp; [_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;  [_Module_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ExternCrate_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_UseDeclaration_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Function_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_TypeAlias_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Struct_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Enumeration_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Union_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ConstantItem_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_StaticItem_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Trait_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Implementation_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_ExternBlock_]\
> &nbsp;&nbsp; )
>
> _MacroItem_:\
> &nbsp;&nbsp; &nbsp;&nbsp; [_MacroInvocationSemi_]\
> &nbsp;&nbsp; | [_MacroRulesDefinition_]
{==+==}

{==+==}


{==+==}
An _item_ is a component of a crate. Items are organized within a crate by a
nested set of [modules]. Every crate has a single "outermost" anonymous module;
all further items within the crate have [paths] within the module tree of the
crate.
{==+==}
一个 _item_ "条目" 是crate的一个组成部分。条目在crate中由一组嵌套的[modules]组织。
每个crate都有一个 "最外层" 的匿名模块；crate中的所有其他条目在crate的模块树中都有[paths]。
{==+==}


{==+==}
Items are entirely determined at compile-time, generally remain fixed during
execution, and may reside in read-only memory.
{==+==}
条目完全在编译时确定，一般在执行过程中保持固定，并可能驻留在只读内存中。
{==+==}


{==+==}
There are several kinds of items:
{==+==}
有以下几种类型的条目:
{==+==}


{==+==}
* [modules]
* [`extern crate` declarations]
* [`use` declarations]
* [function definitions]
* [type definitions]
* [struct definitions]
* [enumeration definitions]
* [union definitions]
* [constant items]
* [static items]
* [trait definitions]
* [implementations]
* [`extern` blocks]
{==+==}
* [modules] 模块
* [`extern crate` declarations] `extern crate` 声明
* [`use` declarations] `use`  声明
* [function definitions] 函数定义
* [type definitions] 类型定义
* [struct definitions] 结构定义
* [enumeration definitions] 枚举定义
* [union definitions] 联合体定义
* [constant items] 常量条目
* [static items] 静态条目
* [trait definitions] trait 定义
* [implementations] impl
* [`extern` blocks] `extern` 块
{==+==}


{==+==}
Some items form an implicit scope for the declaration of sub-items. In other
words, within a function or module, declarations of items can (in many cases)
be mixed with the statements, control blocks, and similar artifacts that
otherwise compose the item body. The meaning of these scoped items is the same
as if the item was declared outside the scope &mdash; it is still a static item
&mdash; except that the item's *path name* within the module namespace is
qualified by the name of the enclosing item, or is private to the enclosing
item (in the case of functions). The grammar specifies the exact locations in
which sub-item declarations may appear.
{==+==}
一些条目形成了一个子条目声明的隐式作用域。
换言之，在一个函数或模块中，条目的声明可以(在许多情况下)与语句、控制块和其他构成条目体的类似制品相混合。
这些作用域内的条目的含义与在作用域外声明的条目相同&mdash；仍然是静态条目&mdash；只是该条目在模块名称空间内的 *路径名称* 由包围条目的名称限定，或者对于包围条目是私有的(在函数的情况下)。
语法规定了子条目声明可能出现的确切位置。
{==+==}


{==+==}
[_ConstantItem_]: items/constant-items.md
[_Enumeration_]: items/enumerations.md
[_ExternBlock_]: items/external-blocks.md
[_ExternCrate_]: items/extern-crates.md
[_Function_]: items/functions.md
[_Implementation_]: items/implementations.md
[_MacroInvocationSemi_]: macros.md#macro-invocation
[_MacroRulesDefinition_]: macros-by-example.md
[_Module_]: items/modules.md
[_OuterAttribute_]: attributes.md
[_StaticItem_]: items/static-items.md
[_Struct_]: items/structs.md
[_Trait_]: items/traits.md
[_TypeAlias_]: items/type-aliases.md
[_Union_]: items/unions.md
[_UseDeclaration_]: items/use-declarations.md
[_Visibility_]: visibility-and-privacy.md
[`extern crate` declarations]: items/extern-crates.md
[`extern` blocks]: items/external-blocks.md
[`use` declarations]: items/use-declarations.md
[constant items]: items/constant-items.md
[enumeration definitions]: items/enumerations.md
[function definitions]: items/functions.md
[implementations]: items/implementations.md
[modules]: items/modules.md
[paths]: paths.md
[static items]: items/static-items.md
[struct definitions]: items/structs.md
[trait definitions]: items/traits.md
[type definitions]: items/type-aliases.md
[union definitions]: items/unions.md
{==+==}

{==+==}