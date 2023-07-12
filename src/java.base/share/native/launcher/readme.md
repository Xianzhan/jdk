
`java Main` 最开始的代码即从 [`main.c`](main.c) 代码开始, \
将传参保存到 [JLI_List_](../libjli/jli_util.h) 结构体里, \
然后执行真正的启动函数 [JLI_Launch](main.c)

```mermaid
sequenceDiagram
    participant main.c
    participant args.c
    participant java.c

    main.c->>args.c: JLI_InitArgProcessing
    main.c->>java.c: JLI_Launch
```

JLI_Launch:
1. InitLauncher
2. DumpState
3. SelectVersion
4. CreateExecutionEnvironment
5. LoadJavaVM
6. ParseArguments
7. JVMInit
8. ShowSplashScreen
9. ContinueInNewThread

```mermaid
sequenceDiagram
    participant java.c
    participant java_md.c

    java.c->>java.c: InvocationFunctions ifn
    java.c->>java.c: InitLauncher
    java.c->>java.c: DumpState
    java.c->>java.c: SelectVersion
    java.c->>java.c: CreateExecutionEnvironment
    java.c->>java.c: LoadJavaVM
    java.c->>java.c: ParseArguments
    java.c->>java_md.c: JVMInit
    java_md.c->>java_md.c: ShowSplashScreen 平台相关
    java_md.c->>java.c: ContinueInNewThread
```

ContinueInNewThread:
1. GetDefaultJavaVMInitArgs 设置栈大小
2. CallJavaMainInNewThread 在新线程调用 `main` 静态方法
