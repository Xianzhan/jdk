
# README

[JEP draft: JDK Source Structure](https://openjdk.org/jeps/8283227)<br/>
[Project Jigsaw](https://openjdk.org/projects/jigsaw/)<br>

- src
  - [hotspot](./src/hotspot/readme.md): HotSpot 源码
    - [share](./src/hotspot/share/readme.md)
      - [cds](./src/hotspot/share/cds/readme.md): class-data sharing(CDS)
      - [jfr](./src/hotspot/share/jfr/readme.md): Java Flight Recorder Java 飞行记录器
      - [logging](./src/hotspot/share/logging/readme.md): 统一 JVM 日志记录
      - [memory](./src/hotspot/share/memory/readme.md): 内存管理
      - [nmt](./src/hotspot/share/nmt/readme.md): Native Memory Tracking 本地内存跟踪
      - [oops](./src/hotspot/share/oops/readme.md): JVM 内部对象表示
      - [opto](./src/hotspot/share/opto/readme.md): C2 编译器
      - [prims](./src/hotspot/share/prims/readme.md): HotSpot 对外接口
      - [runtime](./src/hotspot/share/runtime/readme.md): 运行时
  - java.base
    - [`java Main` launcher 启动](src/java.base/share/native/launcher/readme.md)

# 名词解释

- CDS: [Class-Data Sharing(CDS)](https://openjdk.org/jeps/250) or [Cache Data Store](https://openjdk.org/jeps/8320264)
- gmk: [GNU MAKE FILE](https://www.gnu.org/software/make/manual/make.html)

# 环境变量

[_JAVA_LAUNCHER_DEBUG](./README_env.md#_java_launcher_debug)

# Welcome to the JDK!

For build instructions please see the
[online documentation](https://openjdk.org/groups/build/doc/building.html),
or either of these files:

- [doc/building.html](doc/building.html) (html version)
- [doc/building.md](doc/building.md) (markdown version)

See <https://openjdk.org/> for more information about the OpenJDK
Community and the JDK and see <https://bugs.openjdk.org> for JDK issue
tracking.
