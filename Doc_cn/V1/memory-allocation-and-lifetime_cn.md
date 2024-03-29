{==+==}
# Memory allocation and lifetime

The _items_ of a program are those functions, modules, and types that have their
value calculated at compile-time and stored uniquely in the memory image of the
rust process. Items are neither dynamically allocated nor freed.

The _heap_ is a general term that describes boxes.  The lifetime of an
allocation in the heap depends on the lifetime of the box values pointing to
it. Since box values may themselves be passed in and out of frames, or stored
in the heap, heap allocations may outlive the frame they are allocated within.
An allocation in the heap is guaranteed to reside at a single location in the
heap for the whole lifetime of the allocation - it will never be relocated as
a result of moving a box value.
{==+==}
# 内存分配及生命周期

程序中函数、模块和类型值等 _条目_ 是静态的，在编译后计算确定，存储在已加载进程中时，将有确定的内存块占用。
条目不是动态分配，也不会被释放。

_堆_ 是一个泛指的术语，用于描述堆箱。在堆中分配的内存的生命周期取决于指向它的堆箱值的生命周期。
由于堆箱值本身可能在帧之间传递，或者存储在堆中，因此堆分配可能会超出它们分配的所在帧的生命周期。
在堆中分配的内存保证在整个分配的生命周期中都驻留在堆的单个位置 - 它永远不会由于移动堆箱值而被移动。

译注: 这一语言特性不是 Rust 独有，编译性语言总是这样的，由源码编译形成确定的二进制文件，并且其二进制文件加载到内存中时结构也是确定的。运行时所需要的空间 '栈' 和 '堆' 根据需要向操作系统申请。那么值得深入思考的原则是 "凡是在编译时无法确定条目的大小和地址的语法都是错误！"
{==+==}