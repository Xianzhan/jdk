
包含 Hotspot 源代码。Hotspot 代码是用 C++ 编写的。

[share](./share/readme.md): 公共代码

# 变量

[JavaVM](../java.base/share/native/include/readme.md): 虚拟机在 JNI 的表示，一个 JVM 中只有一个 JavaVM 实例，这个实例时线程共享的。 \
[JNIEnv](../java.base/share/native/include/readme.md): 由虚拟机传入，与线程相关的变量。

# JIT

## C1

C1 编译器大概分为 3 个编译阶段，其主要关注点时局部优化，没有对程序执行太多的全局优化，因为全局优化比较耗时。
1. 将字节码转换为 HIR
2. 将 HIR 变换为 LIR
3. 将 LIR 转换为本地代码

# C2

C2 编译器在方法内联上比较激进，并且能迭代优化：
- 迭代式全局值标号（iterative GVN）
- 条件常量传播（conditional constant propagation，CCP）
- 循环优化（Ideal Loop），包括消除数组边界检查（RCE）、循环不变量外提（LICM）、循环展开（loop unrolling）、循环剥离（loop peeling）、基于superword的循环再合并、循环向量化等等
- 逃逸分析（escape analysis）与标量替换（scalar replacement）、锁消除（ lock elision）搭配使用
- Java代码模式优化，例如字符串拼接优化（string concatenation optimization）、消除自动装箱（autoboxing elimination）等
- 增量式方法内联（incremental inlining）
