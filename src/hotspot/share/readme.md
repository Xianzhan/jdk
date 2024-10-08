
包含共享的跨平台代码。

- [adlc](./adlc/readme.md): 平台描述文件
- [asm](./asm/readme.md): 汇编器
- [c1](./c1/readme.md): C1 编译器
- [ci](./ci/readme.md): 动态编译器
- [classfile](./classfile/readme.md): `.class` 文件解析和类的链接
- [code](./code/readme.md): 机器码生成
- [compiler](./compiler/readme.md): 调用动态编译器的接口
- [gc](./gc/readme.md): GC 接口及实现
- [interpreter](./interpreter/readme.md): 解释器
- [libadt](./libadt/readme.md): 抽象数据结构
- [memory](./memory/readme.md): 内存管理
- [oops](./oops/readme.md): JVM 内部对象表示
- [opto](./opto/readme.md): C2 编译器
- [prims](./prims/readme.md): HotSpot 对外接口
- [runtime](./runtime/readme.md): 运行时
- [services](./services/readme.md): JMX 接口
- [utilities](./utilities/readme.md): 内部工具类和公共函数

# Klass 模型

- [MetaspaceObj](./memory/readme.md#MetaspaceObj)
  - [Metadata](./oops/readme.md#Metadata)
    - [Klass](./oops/readme.md#Klass)
      - [ArrayKlass](./oops/readme.md#ArrayKlass)
        - [ObjArrayKlass]()
        - [TypeArrayKlass]()
      - [InstanceKlass](./oops/readme.md#InstanceKlass)
        - [InstanceClassLoaderKlass](./oops/readme.md#InstanceClassLoaderKlass)
        - [InstanceMirrorKlass](./oops/readme.md#InstanceMirrorKlass)
        - [InstanceRefKlass](./oops/readme.md#InstanceRefKlass)
    
    - [Method](./oops/readme.md#Method)

    - [ConstantPool](./oops/readme.md#ConstantPool)

  - [Symbol](./oops/readme.md#Symbol)

# Oop 模型

- [oopDesc](./oops/readme.md#oopDesc)
  - [arrayOopDesc](./oops/readme.md#arrayOopDesc)
    - [objArrayOopDesc](./oops/readme.md#objArrayOopDesc)
    - [typeArrayOopDesc](./oops/readme.md#typeArrayOopDesc)
  - [instanceOopDesc](./oops/readme.md#instanceOopDesc)

# Handle

- [Handle](./runtime/readme.md#Handle)