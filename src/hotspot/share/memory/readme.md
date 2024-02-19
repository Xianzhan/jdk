
内存管理

# Universe

[Universe#initialize_heap](./universe.cpp): 初始内存，根据配置创建对应的 GC 收集器

# MetaspaceObj

- [MetaspaceObj](./allocation.hpp)

该类是作为存放在 Metaspace 元空间类的基类，不能执行 `delete`，否则会出现致命错误。

注意：该类没有定义虚函数，该类重载了 `new`操作符，主要用于共享只读或者共享读写的类分配内存。