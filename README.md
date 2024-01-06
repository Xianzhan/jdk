
# README

[JEP draft: JDK Source Structure](https://openjdk.org/jeps/8283227){:target="_blank"}<br/>
[Project Jigsaw](https://openjdk.org/projects/jigsaw/){:target="_blank"}<br>

- doc
  - [building 构建 JDK 文档](doc/building.md){:target="_blank"}
- make
  - [JDK 构建脚本](make/readme.md){:target="_blank"}
- src
  - [hotspot](./src/hotspot/readme.md){:target="_blank"}: HotSpot 源码
    - [cpu](./src/hotspot/cpu/readme.md){:target="_blank"}
    - [os](./src/hotspot/os/readme.md){:target="_blank"}
    - [os_cpu](./src/hotspot/os_cpu/readme.md){:target="_blank"}
    - [share](./src/hotspot/share/readme.md)
      - [adlc](./adlc/readme.md){:target="_blank"}: 平台描述文件
      - [asm](./asm/readme.md){:target="_blank"}: 汇编器
      - [c1](./c1/readme.md){:target="_blank"}: C1 编译器
      - [ci](./ci/readme.md){:target="_blank"}: 动态编译器
      - [classfile](./classfile/readme.md){:target="_blank"}: `.class` 文件解析和类的链接
      - [code](./code/readme.md){:target="_blank"}: 机器码生成
      - [compiler](./compiler/readme.md){:target="_blank"}: 调用动态编译器的接口
      - [gc](./gc/readme.md){:target="_blank"}: GC 接口及实现
      - [interpreter](./interpreter/readme.md){:target="_blank"}: 解释器
      - [libadt](./libadt/readme.md){:target="_blank"}: 抽象数据结构
      - [memory](./memory/readme.md){:target="_blank"}: 内存管理
      - [oops](./oops/readme.md){:target="_blank"}: JVM 内部对象表示
      - [opto](./opto/readme.md){:target="_blank"}: C2 编译器
      - [prims](./prims/readme.md){:target="_blank"}: HotSpot 对外接口
      - [runtime](./runtime/readme.md){:target="_blank"}: 运行时
      - [services](./services/readme.md){:target="_blank"}: JMX 接口
      - [utilities](./utilities/readme.md){:target="_blank"}: 内部工具类和公共函数
  - java.base
    - [`java Main` launcher 启动](src/java.base/share/native/launcher/readme.md){:target="_blank"}
    - share
      - classes
        - java
          - [net 网络处理](src/java.base/share/classes/java/net/readme.md){:target="_blank"}
  - [utils](src/utils/readme.md){:target="_blank"}
  - [test](src/test/readme.md){:target="_blank"}

# Welcome to the JDK!

For build instructions please see the
[online documentation](https://openjdk.org/groups/build/doc/building.html),
or either of these files:

- [doc/building.html](doc/building.html) (html version)
- [doc/building.md](doc/building.md) (markdown version)

See <https://openjdk.org/> for more information about the OpenJDK
Community and the JDK and see <https://bugs.openjdk.org> for JDK issue
tracking.
