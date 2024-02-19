
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
      - [adlc](./src/hotspot/share/adlc/readme.md): 平台描述文件
      - [asm](./src/hotspot/share/asm/readme.md): 汇编器
      - [c1](./src/hotspot/share/c1/readme.md): C1 编译器
      - [cds](./src/hotspot/share/cds/readme.md): class-data sharing(CDS)
      - [ci](./src/hotspot/share/ci/readme.md): 动态编译器
      - [classfile](./src/hotspot/share/classfile/readme.md): `.class` 文件解析和类的链接
      - [code](./src/hotspot/share/code/readme.md): 机器码生成
      - [compiler](./src/hotspot/share/compiler/readme.md): 调用动态编译器的接口
      - [gc](./src/hotspot/share/gc/readme.md): GC 接口及实现
      - [include](./src/hotspot/share/include/readme.md): JVM 头文件
      - [interpreter](./src/hotspot/share/interpreter/readme.md): 解释器
      - [jfr](./src/hotspot/share/jfr/readme.md): Java Flight Recorder Java 飞行记录器
      - [libadt](./src/hotspot/share/libadt/readme.md): 抽象数据结构
      - [logging](./src/hotspot/share/logging/readme.md): 统一 JVM 日志记录
      - [memory](./src/hotspot/share/memory/readme.md): 内存管理
      - [nmt](./src/hotspot/share/nmt/readme.md): Native Memory Tracking 本地内存跟踪
      - [oops](./src/hotspot/share/oops/readme.md): JVM 内部对象表示
      - [opto](./src/hotspot/share/opto/readme.md): C2 编译器
      - [prims](./src/hotspot/share/prims/readme.md): HotSpot 对外接口
      - [runtime](./src/hotspot/share/runtime/readme.md): 运行时
      - [services](./src/hotspot/share/services/readme.md): JMX 接口
      - [utilities](./src/hotspot/share/utilities/readme.md): 内部工具类和公共函数
  - java.base
    - [`java Main` launcher 启动](src/java.base/share/native/launcher/readme.md)
  - [utils](src/utils/readme.md)
  - [test](test/readme.md)

# 名词解释

- gmk: [GNU MAKE FILE](https://www.gnu.org/software/make/manual/make.html)

# Welcome to the JDK!

For build instructions please see the
[online documentation](https://openjdk.org/groups/build/doc/building.html),
or either of these files:

- [doc/building.html](doc/building.html) (html version)
- [doc/building.md](doc/building.md) (markdown version)

See <https://openjdk.org/> for more information about the OpenJDK
Community and the JDK and see <https://bugs.openjdk.org> for JDK issue
tracking.
