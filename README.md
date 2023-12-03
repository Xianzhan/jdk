
# README

[JEP draft: JDK Source Structure](https://openjdk.org/jeps/8283227)<br/>
[Project Jigsaw](https://openjdk.org/projects/jigsaw/)<br>

- doc
  - [building 构建 JDK 文档](doc/building.md)
- make
  - [JDK 构建脚本](make/readme.md)
- src
  - [hotspot](./src/hotspot/readme.md): HotSpot 源码
    - [cpu](./src/hotspot/cpu/readme.md)
    - [os](./src/hotspot/os/readme.md)
    - [os_cpu](./src/hotspot/os_cpu/readme.md)
    - [share](./src/hotspot/share/readme.md)
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
  - java.base
    - [`java Main` launcher 启动](src/java.base/share/native/launcher/readme.md)
  - [utils](src/utils/readme.md)
  - [test](src/test/readme.md)<br/>

# Welcome to the JDK!

For build instructions please see the
[online documentation](https://openjdk.org/groups/build/doc/building.html),
or either of these files:

- [doc/building.html](doc/building.html) (html version)
- [doc/building.md](doc/building.md) (markdown version)

See <https://openjdk.org/> for more information about the OpenJDK
Community and the JDK and see <https://bugs.openjdk.org> for JDK issue
tracking.
