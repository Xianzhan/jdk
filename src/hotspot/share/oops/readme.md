
JVM 内部对象表示

# Metadata

- [Metadata](./metadata.hpp)

`Metadata` 是内部表示类相关元数据的一个基类。

## Klass

- [Klass](./klass.hpp)

一个 `Klass` 提供两方面的功能：
1. 实现 Java 语言层面的类
2. 提供多态方法的支持

C++ 类实例通过保存 typeinfo 指针实现 RTTI，通过 vtbl 指针实现多态，Hotspot 的 **Oop-Klass** 模型将这两者整合到 `Klass` 中，Java 类实例只需保留一个 `Klass` 指针即可实现 RTTI 和多态，能够有效降低指针的内存占用。

大致方案是用 `Oop` 表示 Java 实例，主要用于表示实例数据，不提供任何虚函数功能，`Oop` 保存了对应 `Klass` 的指针，所有方法调用通过 `Klass` 完成并通过 `Klass` 获取类型信息，`Klass` 基于 C++ 的虚函数提供对 Java 多态的支持。

### ArrayKlass

- [ArrayKlass](./arrayKlass.hpp)

`ArrayKlass` 继承自 `Klass`，是所有数组类的抽象基类

### InstanceKlass

- [InstanceKlass](./instanceKlass.hpp)

`InstanceKlass` 是 `java.lang.Class` 在 JVM 层面的内存表示，包含了类在执行过程中需要的所有信息。

#### InstanceClassLoaderKlass

- [InstanceClassLoaderKlass](./instanceClassLoaderKlass.hpp)

`InstanceClassLoaderKlass`，没有添加新的字段，增加了新的 oop 遍历方法，主要用于类加载器依赖遍历使用。

#### InstanceMirrorKlass

- [InstanceMirrorKlass](./instanceMirrorKlass.hpp)

`InstanceMirrorKlass`，用于表示特殊的 `java.lang.Class` 类，`java.lang.Class` 对应的 `OopDesc` 实例用于保存类的静态属性，因此他们的实例大小不同，需要特殊的方式来计算他们的大小以及属性遍历。`Klass` 的属性 `_java_mirror` 就指向保存该类静态字段的 `OopDesc` 实例，可通过该属性访问类的静态字段。

#### InstanceRefKlass

- [InstanceRefKlass](./instanceRefKlass.hpp)

`InstanceRefKlass`，用于表示 `java.lang.ref.Reference` 的子类，这些类需要垃圾回收器特殊处理，因此改写了原有的 `oop_oop_iterate` 中用于垃圾回收的相关方法。

## Method

- [Method](./method.hpp)

`Method` 用于表示一个 Java 方法，因为一个应用有成千上万个方法，因此保证 `Method` 类在内存中短小非常有必要。为了本地 GC 方便，`Method` 把所有的指针变量和方法大小放在 `Method` 内存布局的前面，方法本身的不可变数据如字节码用 `ConstMethod` 表示，可变数据如 `Profile` 统计的性能数据等用 `MethodData` 表示，都通过指针访问。如果是本地方法，`Method` 内存结构的最后是 `native_function` 和 `signature_handler`，按照解释器的要求，这两个必须在固定的偏移处。`Method` 没有子类。

## ConstantPool

- [ConstantPool](./constantPool.hpp)

每个 `Klass` 都有对应的 `ConstantPool`，两者是一一对应的。常量池的数据大部分是在 class 文件解析的时候写入的，可以安全访问；但是 `CONSTANT_Class_info` 类型的常量数据是在这个类被解析时修改，这时只能通过解析状态判断这条数据是否修改完成。

# Symbol

- [Symbol](./symbol.hpp)

表示一个规范化的字符串形式的描述符，如方法 `Object m(int i, double d, Thread t)` 对应的方法描述符就是 `(IDLjava/lang/Thread;)Ljava/lang/Object`。

# oopDesc

- [oopDesc](./oop.hpp)

```cpp
class oopDesc {
    private:
        // 对象头，volatile 保证对象状态在各 CPU 同步
        volatile markWord _mark;
        union _metadata {
            Klass* _klass;
            narrowKlass _compressed_klass;
        } _metadata;
}
```

## arrayOopDesc

- [arrayOopDesc](./arrayOop.hpp)

所有数组 `oopDesc` 的基类，保存了数组的长度。

### objArrayOopDesc

- [objArrayOopDesc](./objArrayOop.hpp)

用于表示 Java 对象的数组。

### typeArrayOopDesc

- [typeArrayOopDesc](./typeArrayOop.hpp)

用于表示基本类型的数组，如：`int`/`long`

## instanceOopDesc

- [instanceOopDesc](./instanceOop.hpp)

表示普通 Java 对象，由 `new` 关键字创建，即 Java 每次调用 `new`，都会创建一个 `instanceOopDesc`
