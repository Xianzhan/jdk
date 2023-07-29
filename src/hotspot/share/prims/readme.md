[jni.cpp#JNI_CreateJavaVM](./jni.cpp)

1. [JNI_CreateJavaVM_inner](./jni.cpp) 内部创建 JVM

[jni.cpp#JNI_CreateJavaVM_inner](./jni.cpp)

1. [Threads::create_vm((JavaVMInitArgs*) args, &can_try_again);](../runtime/threads.cpp) 
    1. [vm_init_globals](../runtime/init.cpp) 初始化全局数据结构并在堆中创建系统类
        1. `basic_types_init` 基础类型初始化
        2. `mutex_init` 锁初始化
    2. [init_globals](../runtime/init.cpp) 初始化全局模块
        1. `management_init` 管理初始化
        2. `bytecodes_init` 字节码初始化
        3. `classLoader_init1` 类加载器初始化, 加载 Java 本地代码
        4. `universe_init` 根据回收算法(G1/ZGC/..)初始化堆, 初始化 TLAB, 元空间初始化
        5. `interpreter_init_stub` 解释器初始化
    3. [init_globals2](../runtime/init.cpp) 初始化全局模块 2
        1. `universe2_init` 符号初始化等
        2. `interpreter_init_code` 解释器初始化字节码 Bytecodes
2. [CompileBroker::compilation_init_phase1(CHECK_JNI_ERR)](../runtime/threads.cpp) 初始化 JIT 编译器
3. [initialize_jsr292_core_classes](../runtime/init.cpp) 初始化 `MethodHandle`...
