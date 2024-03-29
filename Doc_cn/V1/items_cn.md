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
> **<sup>语法:<sup>**\
> _条目_:\
> &nbsp;&nbsp; [_外围属性_][_OuterAttribute_]<sup>\*</sup>\
> &nbsp;&nbsp; &nbsp;&nbsp; _可见性条目_\
> &nbsp;&nbsp; | _宏条目_
>
> _可见性条目_:\
> &nbsp;&nbsp; [_可见性_][_Visibility_]<sup>?</sup>\
> &nbsp;&nbsp; (\
> &nbsp;&nbsp; &nbsp;&nbsp; &nbsp;&nbsp;  [_模块_][_Module_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_外部Crate_][_ExternCrate_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Use声明_][_UseDeclaration_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_函数_][_Function_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_类型别名_][_TypeAlias_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_结构体_][_Struct_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_枚举_][_Enumeration_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_联合体_][_Union_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_常量条目_][_ConstantItem_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_静态条目_][_StaticItem_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_Trait_][_Trait_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_实现_][_Implementation_]\
> &nbsp;&nbsp; &nbsp;&nbsp; | [_外部块_][_ExternBlock_]\
> &nbsp;&nbsp; )
>
> _宏条目_:\
> &nbsp;&nbsp; &nbsp;&nbsp; [_宏调用语句_][_MacroInvocationSemi_]\
> &nbsp;&nbsp; | [_宏规则定义_][_MacroRulesDefinition_]
{==+==}


{==+==}
An _item_ is a component of a crate. Items are organized within a crate by a
nested set of [modules]. Every crate has a single "outermost" anonymous module;
all further items within the crate have [paths] within the module tree of the
crate.
{==+==}
_条目_ 是 crate 的具体组件。条目在 [模块][modules] 中排列。
所有条目都有自己的 [路径][paths] 以决定处在模块树的位置。
{==+==}


{==+==}
Items are entirely determined at compile-time, generally remain fixed during
execution, and may reside in read-only memory.
{==+==}
条目在编译时将完全确定，在运行时通常静态不变，且在内存中只读。
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
* [模块][modules]
* [`extern crate` 声明][`extern crate` declarations]
* [`use` 声明][`use` declarations] 
* [函数定义][function definitions] 
* [类型定义][type definitions] 
* [结构体定义][struct definitions] 
* [枚举定义][enumeration definitions] 
* [联合体定义][union definitions] 
* [常量条目][constant items] 
* [静态条目][static items] 
* [trait 定义][trait definitions] 
* [实现][implementations]
* [`extern` 块][`extern` blocks] 
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
某些条目会形成隐式的作用域，从而包含子条目。
也就是说，在函数或模块内，条目的声明 (在许多情况下) 可以与语句、控制块等混合，
该条目的含义与在作用域外部声明相同 &mdash; 但条目仍然是静态的 &mdash; 有所不同的是，该条目在命名空间内的 *路径* 被所包围条目的名称限定，或者仅对于所包围条目是私有的 (在函数的情况下) 。
相关条目的语法解释了子条目声明可能出现的确切位置。
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