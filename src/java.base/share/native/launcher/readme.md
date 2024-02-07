# [`main.c#main`](main.c)

```c
/**
 * main 函数主要是为 JLI_Launch 函数解析设置参数
 */
JNIEXPORT int
main(int argc, char **argv)
{
    int margc;
    char** margv;
    int jargc = argc;
    char** jargv = argv;
    const jboolean const_javaw = JNI_FALSE;

    return JLI_Launch(margc, margv,
                jargc, (const char**) jargv,
                0, NULL,
                VERSION_STRING,
                DOT_VERSION,
                (const_progname != NULL) ? const_progname : *margv,
                (const_launcher != NULL) ? const_launcher : *margv,
                jargc > 0,
                const_cpwildcard, const_javaw, 0);
}
```

# [`java.c#JLI_Launch`](../libjli/java.c)

```c
JNIEXPORT int JNICALL
JLI_Launch(int argc, char ** argv,              /* main argc, argv */
        int jargc, const char** jargv,          /* java args */
        int appclassc, const char** appclassv,  /* app classpath */
        const char* fullversion,                /* full version defined */
        const char* dotversion,                 /* UNUSED dot version defined */
        const char* pname,                      /* program name */
        const char* lname,                      /* launcher name */
        jboolean javaargs,                      /* JAVA_ARGS */
        jboolean cpwildcard,                    /* classpath wildcard*/
        jboolean javaw,                         /* windows-only javaw */
        jint ergo                               /* unused */
)
{
    int mode = LM_UNKNOWN;
    // classpath
    char *what = NULL;
    // 有 main 方法的 class 文件
    char *main_class = NULL;
    int ret;
    InvocationFunctions ifn;
    // 启动时间
    jlong start = 0, end = 0;
    // JVM 的路径
    char jvmpath[MAXPATHLEN];
    // JRE 的路径
    char jrepath[MAXPATHLEN];

    /*
     * SelectVersion() has several responsibilities:
     * SelectVersion() 有几个职责:
     *
     *  1) Disallow specification of another JRE.  With 1.9, another
     *     version of the JRE cannot be invoked.
     *  1) 禁止指定其他 JRE。1.9 是另一个无法调用 JRE 版本。
     * 
     *  2) Allow for a JRE version to invoke JDK 1.9 or later.  Since
     *     all mJRE directives have been stripped from the request but
     *     the pre 1.9 JRE [ 1.6 thru 1.8 ], it is as if 1.9+ has been
     *     invoked from the command line.
     *  2) 允许 JRE 版本调用 JDK 1.9 或更高版本。
     *     自所有 mJRE 指令都已从请求中剥离，但是 1.9 之前的 JRE[1.6 到 1.8]，就像 1.9+ 一样从命令行调用。
     * 
     * 其他，从 jar 包中读取 META-INF/MAINFEST.MF 获取 main_class
     */
    SelectVersion(argc, argv, &main_class);

    // 创建 JVM 执行环境，确定数据模型，如 32/64 位 jvm
    // GetJREPath: 找到 JRE 路径
    // ReadKnownVMs: 根据 jvm.cfg 获取已知 JVM
    // CheckJvmType: 检查 JVM 类型
    // GetJVMPath: 根据 JVM 类型获取 JVM 路径
    CreateExecutionEnvironment(&argc, &argv,
                               jrepath, sizeof(jrepath),
                               jvmpath, sizeof(jvmpath),
                               jvmcfg,  sizeof(jvmcfg));

    ifn.CreateJavaVM = 0;
    ifn.GetDefaultJavaVMInitArgs = 0;

    // 加载 libjvm 动态链接库, 加载函数 
    // `JNI_CreateJavaVM`、
    // `JNI_GetDefaultJavaVMInitArgs`、
    // `JNI_GetCreatedJavaVMs`
    // 并绑定 `InvocationFunctions *ifn`
    if (!LoadJavaVM(jvmpath, &ifn)) {
        return(6);
    }

    ++argv;
    --argc;

    /* Parse command line options; if the return value of
     * ParseArguments is false, the program should exit.
     * 解析命令行参数；若参数如 '--help' 之类的则返回 true 退出程序
     */
    if (!ParseArguments(&argc, &argv, &mode, &what, &ret, jrepath)) {
        return(ret);
    }

    /* Override class path if -jar flag was specified */
    if (mode == LM_JAR) {
        SetClassPath(what);     /* Override class path */
    }

    // JVM 初始化后启动
    return JVMInit(&ifn, threadStackSize, argc, argv, mode, what, ret);
}
```



# [`java_md.c#JVMInit`](../../../unix/native/libjli/java_md.c)

```c
int
JVMInit(InvocationFunctions* ifn, jlong threadStackSize,
        int argc, char **argv,
        int mode, char *what, int ret)
{
    ShowSplashScreen();
    // ifn
    // threadStackSize: 若没有设置 `-Xss`, 则为 0
    // argc
    // argv
    // mode
    // what
    // ret
    return ContinueInNewThread(ifn, threadStackSize, argc, argv, mode, what, ret);
}
```

# [`java.c#ContinueInNewThread`](../libjli/java.c)

2. [CallJavaMainInNewThread](../../../unix/native/libjli/java_md.c) 阻塞当前线程创建新线程, 在新线程调用 `main` 静态方法

```c
int
ContinueInNewThread(InvocationFunctions* ifn, jlong threadStackSize,
                    int argc, char **argv,
                    int mode, char *what, int ret)
{
    { /* Create a new thread to create JVM and invoke main method */
      /* 创建一个新线程来创建 JVM 并调用 main 方法 */
        JavaMainArgs args;
        int rslt;

        args.argc = argc;
        args.argv = argv;
        args.mode = mode;
        args.what = what;
        args.ifn = *ifn;

        rslt = CallJavaMainInNewThread(threadStackSize, (void*)&args);
        /* If the caller has deemed there is an error we
         * simply return that, otherwise we return the value of
         * the callee
         */
        return (ret != 0) ? ret : rslt;
    }
}
```

# [`java_md.c#CallJavaMainInNewThread`](../../../unix/native/libjli/java_md.c)

```c
/*
 * Block current thread and continue execution in a new thread.
 * 阻塞当前线程并在新线程中继续执行。
 */
int
CallJavaMainInNewThread(jlong stack_size, void* args) {
    int rslt;
    pthread_t tid;
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

    // 获得线程栈末尾的警戒缓冲区大小
    pthread_attr_setguardsize(&attr, 0); // no pthread guard page on java threads

    // 创建新线程, 执行 `ThreadJavaMain` 逻辑
    if (pthread_create(&tid, &attr, ThreadJavaMain, args) == 0) {
        void* tmp;
        // 当前线程等待新线程完成
        pthread_join(tid, &tmp);
        rslt = (int)(intptr_t)tmp;
    }

    pthread_attr_destroy(&attr);
    return rslt;
}
```

# [`java_md.c#ThreadJavaMain`](../../../unix/native/libjli/java_md.c)

```c
/*
 * Signature adapter for pthread_create() or thr_create().
 * pthread_create() 或 thr_create() 的签名适配器。
 */
static void* ThreadJavaMain(void* args) {
    return (void*)(intptr_t)JavaMain(args);
}
```

# [java.c#JavaMain](../libjli/java.c)

```c
int
JavaMain(void* _args)
{
    JavaMainArgs *args = (JavaMainArgs *)_args;
    int argc = args->argc;
    char **argv = args->argv;
    int mode = args->mode;
    char *what = args->what;
    InvocationFunctions ifn = args->ifn;

    JavaVM *vm = 0;
    JNIEnv *env = 0;
    jclass mainClass = NULL;
    jclass appClass = NULL; // actual application class being launched
    jobjectArray mainArgs;
    int ret = 0;

    // ifn->CreateJavaVM(vm, (void **)env, &args);
    // 执行 libjvm 的 JNI_CreateJavaVM() 函数
    // 经过一系列原子保证后调用
    // threads.cpp#Threads::create_vm((JavaVMInitArgs*) args, &can_try_again)
    // - ostream_init(); 初始化输出流模块，日志
    // - os::init(); 初始化操作系统模块
    // - Arguments::init_system_properties(); 初始化系统属性
    // - JDK_Version_init(); JDK 版本初始化
    // - Arguments::init_version_specific_system_properties(); 初始化版本系统属性
    // - LogConfiguration::initialize(create_vm_timer.begin_time()); VM 日志配置初始化
    // - MemTracker::initialize(); 初始化内存跟踪器

    // - Arguments::apply_ergo();
    //   - 根据参数选择 GC，若无，则选择默认 GC
    //   - 根据可用的物理内存设置堆大小
    //   - CDS 初始化
    //   - 初始化元空间标志和对齐
    //   - String 重复数据删除初始化
    //   - C1/C2 编译器初始化
    //   - 设置字节码重写标志

    // - os::init_2(); 本机操作系统的配置
    // - SafepointMechanism::initialize(); 安全点装置初始化
    // - JvmtiAgentList::load_agents(); 加载代理
    // - vm_init_globals(); vm 全局初始化
    // - JavaThread* main_thread = new JavaThread(); 将主线程附加到这个 os 线程
    // - ObjectMonitor::Initialize(); 监视器初始化
    // - ObjectSynchronizer::initialize();

    // - init_globals(); 初始化全局模块
    //   - management_init(); JVM 监控管理服务初始化
    //   - bytecodes_init(); 字节码初始化
    //   - classLoader_init1(); 类加载器初始化，加载本地 rt.jar 等
    //   - compilationPolicy_init(); 编译策略初始化
    //   - codeCache_init();
    //   - VM_Version_init();
    //   - initial_stubs_init();
    //   - universe_init();
    //   - ...

    // - init_globals2();
    //   - interpreter_init_code();
    //   - jni_handles_init();
    //   - MethodHandles::generate_adapters();

    // - VMThread::create();
    // - os::start_thread(vmthread); 启动 vm 线程
    // - initialize_java_lang_classes(main_thread, CHECK_JNI_ERR); 初始化一些 lang 包的类
    // - quicken_jni_functions();

    // - Management::record_vm_init_completed();

    // - ServiceThread::initialize(); 启动服务线程

    // - MonitorDeflationThread::initialize();

    // - initialize_jsr292_core_classes(CHECK_JNI_ERR);

    // - call_initPhase2(CHECK_JNI_ERR);
    // - call_initPhase3(CHECK_JNI_ERR);

    // - WatcherThread::run_all_tasks();
    if (!InitializeJVM(&vm, &env, &ifn)) {
        JLI_ReportErrorMessage(JVM_ERROR1);
        exit(1);
    }

    // `java -XshowSettings`
    if (showSettings != NULL) {
        ShowSettings(env, showSettings);
        CHECK_EXCEPTION_LEAVE(1);
    }

    // `java --show-resolved-modules`
    // show resolved modules and continue
    if (showResolvedModules) {
        ShowResolvedModules(env);
        CHECK_EXCEPTION_LEAVE(1);
    }

    // `java --list-modules`
    // list observable modules, then exit
    if (listModules) {
        ListModules(env);
        CHECK_EXCEPTION_LEAVE(1);
        LEAVE();
    }

    // `java [-d|--describe-module|--describe-module=] java.base[other.module]`
    // describe a module, then exit
    if (describeModule != NULL) {
        DescribeModule(env, describeModule);
        CHECK_EXCEPTION_LEAVE(1);
        LEAVE();
    }

    // `java [-version|--version|-showversion|--show-version]`
    if (printVersion || showVersion) {
        PrintJavaVersion(env);
        CHECK_EXCEPTION_LEAVE(0);
        if (printVersion) {
            LEAVE();
        }
    }

    // modules have been validated at startup so exit
    if (validateModules) {
        LEAVE();
    }

    /*
     * -Xshare:dump does not have a main class so the VM can safely exit now
     */
    if (dumpSharedSpaces) {
        CHECK_EXCEPTION_LEAVE(1);
        LEAVE();
    }

    /* If the user specified neither a class name nor a JAR file */
    if (printXUsage || printUsage || what == 0 || mode == LM_UNKNOWN) {
        PrintUsage(env, printXUsage);
        CHECK_EXCEPTION_LEAVE(1);
        LEAVE();
    }

    FreeKnownVMs(); /* after last possible PrintUsage */

    if (JLI_IsTraceLauncher()) {
        end = CurrentTimeMicros();
        JLI_TraceLauncher("%ld micro seconds to InitializeJVM\n", (long)(end-start));
    }

    /* At this stage, argc/argv have the application's arguments */
    if (JLI_IsTraceLauncher()){
        int i;
        printf("%s is '%s'\n", launchModeNames[mode], what);
        printf("App's argc is %d\n", argc);
        for (i=0; i < argc; i++) {
            printf("    argv[%2d] = '%s'\n", i, argv[i]);
        }
    }

    ret = 1;

    /*
     * See bugid 5030265.  The Main-Class name has already been parsed
     * from the manifest, but not parsed properly for UTF-8 support.
     * Hence the code here ignores the value previously extracted and
     * uses the pre-existing code to reextract the value.  This is
     * possibly an end of release cycle expedient.  However, it has
     * also been discovered that passing some character sets through
     * the environment has "strange" behavior on some variants of
     * Windows.  Hence, maybe the manifest parsing code local to the
     * launcher should never be enhanced.
     *
     * Hence, future work should either:
     *     1)   Correct the local parsing code and verify that the
     *          Main-Class attribute gets properly passed through
     *          all environments,
     *     2)   Remove the vestages of maintaining main_class through
     *          the environment (and remove these comments).
     *
     * This method also correctly handles launching existing JavaFX
     * applications that may or may not have a Main-Class manifest entry.
     */

    // LauncherHelper::checkAndLoadMain
    // LauncherHelper::loadModuleMainClass 加载 main 方法类
    //   - Class::forName
    //   - Class::forName0
    //   - Class.c#Java_java_lang_Class_forName0
    //   - jvm.cpp#JVM_FindClassFromCaller
    //   - jvm.cpp#find_class_from_class_loader
    //   - systemDictionary.cpp#SystemDictionary::resolve_or_fail
    //       - SystemDictionary::resolve_or_null
    //       - SystemDictionary::resolve_instance_class_or_null
    //       - SystemDictionary::load_instance_class
    //       - SystemDictionary::load_instance_class_impl
    //   - classLoader.cpp#ClassLoader::load_class
    //       - ClassLoader::file_name_for_class_name
    //       - ClassLoader::search_module_entries  stream
    //       - InstanceKlass* result = KlassFactory::create_from_stream(stream,
    //                                                                  name,
    //                                                                  loader_data,
    //                                                                  cl_info,
    //                                                                  CHECK_NULL);
    mainClass = LoadMainClass(env, mode, what);
    CHECK_EXCEPTION_NULL_LEAVE(mainClass);
    /*
     * In some cases when launching an application that needs a helper, e.g., a
     * JavaFX application with no main method, the mainClass will not be the
     * applications own main class but rather a helper class. To keep things
     * consistent in the UI we need to track and report the application main class.
     */
    appClass = GetApplicationClass(env);
    CHECK_EXCEPTION_NULL_LEAVE(appClass);

    /* Build platform specific argument array */
    mainArgs = CreateApplicationArgs(env, argv, argc);
    CHECK_EXCEPTION_NULL_LEAVE(mainArgs);

    if (dryRun) {
        ret = 0;
        LEAVE();
    }

    /*
     * PostJVMInit uses the class name as the application name for GUI purposes,
     * for example, on OSX this sets the application name in the menu bar for
     * both SWT and JavaFX. So we'll pass the actual application class here
     * instead of mainClass as that may be a launcher or helper class instead
     * of the application class.
     */
    PostJVMInit(env, appClass, vm);
    CHECK_EXCEPTION_LEAVE(1);

    /*
     * The main method is invoked here so that extraneous java stacks are not in
     * the application stack trace.
     * 
     * JEP 445: Unnamed Classes and Instance Main Methods (Preview)
     * 
     * 按以下顺序获取 main 方法
     * 
     * ```java
     * static void main(String[] args);
     * void main(String[] args);
     * static void main();
     * void main();
     * ```
     * 
     * 执行 jni_invoke_static | jni_invoke_nonstatic
     */
    if (!invokeStaticMainWithArgs(env, mainClass, mainArgs) &&
        !invokeInstanceMainWithArgs(env, mainClass, mainArgs) &&
        !invokeStaticMainWithoutArgs(env, mainClass) &&
        !invokeInstanceMainWithoutArgs(env, mainClass)) {
        ret = 1;
        LEAVE();
    }

    /*
     * The launcher's exit code (in the absence of calls to
     * System.exit) will be non-zero if main threw an exception.
     */
    ret = (*env)->ExceptionOccurred(env) == NULL ? 0 : 1;

    LEAVE();
}
```

# [java.c#invokeStaticMainWithArgs](java.c)

```c
/*
 * Invoke a static main with arguments. Returns 1 (true) if successful otherwise
 * processes the pending exception from GetStaticMethodID and returns 0 (false).
 */
int
invokeStaticMainWithArgs(JNIEnv *env, jclass mainClass, jobjectArray mainArgs) {
    // 找到 `static void main(String[] args)` 方法
    jmethodID mainID = (*env)->GetStaticMethodID(env, mainClass, "main",
                                  "([Ljava/lang/String;)V");
    // 执行 `static` 方法
    (*env)->CallStaticVoidMethod(env, mainClass, mainID, mainArgs);
    return 1;
}
```

**Hotspot**

# [jni.cpp#jni_CallStaticVoidMethod](../../../../hotspot/share/prims/jni.cpp)

```c
JNI_ENTRY(void, jni_CallStaticVoidMethod(JNIEnv *env, jclass cls, jmethodID methodID, ...))
  HOTSPOT_JNI_CALLSTATICVOIDMETHOD_ENTRY(env, cls, (uintptr_t) methodID);
  DT_VOID_RETURN_MARK(CallStaticVoidMethod);

  va_list args;
  va_start(args, methodID);
  JavaValue jvalue(T_VOID);
  JNI_ArgumentPusherVaArg ap(methodID, args);
  jni_invoke_static(env, &jvalue, nullptr, JNI_STATIC, methodID, &ap, CHECK);
  va_end(args);
JNI_END
```

# [jni.cpp#jni_invoke_static](../../../../hotspot/share/prims/jni.cpp)

```c
static void jni_invoke_static(JNIEnv *env, JavaValue* result, jobject receiver, JNICallType call_type, jmethodID method_id, JNI_ArgumentPusher *args, TRAPS) {
  methodHandle method(THREAD, Method::resolve_jmethod_id(method_id));

  // Create object to hold arguments for the JavaCall, and associate it with
  // the jni parser
  ResourceMark rm(THREAD);
  int number_of_parameters = method->size_of_parameters();
  JavaCallArguments java_args(number_of_parameters);

  assert(method->is_static(), "method should be static");

  // Fill out JavaCallArguments object
  args->push_arguments_on(&java_args);
  // Initialize result type
  result->set_type(args->return_type());

  // Invoke the method. Result is returned as oop.
  JavaCalls::call(result, method, &java_args, CHECK);

  // Convert result
  if (is_reference_type(result->get_type())) {
    result->set_jobject(JNIHandles::make_local(THREAD, result->get_oop()));
  }
}
```

# [javaCalls.cpp#JavaCalls::call](../../../../hotspot/share/runtime/javaCalls.cpp)

```c
void JavaCalls::call(JavaValue* result, const methodHandle& method, JavaCallArguments* args, TRAPS) {
  // Check if we need to wrap a potential OS exception handler around thread.
  // This is used for e.g. Win32 structured exception handlers.
  // Need to wrap each and every time, since there might be native code down the
  // stack that has installed its own exception handlers.
  os::os_exception_wrapper(call_helper, result, method, args, THREAD);
}

void JavaCalls::call_helper(JavaValue* result, const methodHandle& method, JavaCallArguments* args, TRAPS) {

  JavaThread* thread = THREAD;
  assert(method.not_null(), "must have a method to call");
  assert(!SafepointSynchronize::is_at_safepoint(), "call to Java code during VM operation");
  assert(!thread->handle_area()->no_handle_mark_active(), "cannot call out to Java here");

  // Verify the arguments
  if (JVMCI_ONLY(args->alternative_target().is_null() &&) (DEBUG_ONLY(true ||) CheckJNICalls)) {
    args->verify(method, result->get_type());
  }
  // Ignore call if method is empty
  if (JVMCI_ONLY(args->alternative_target().is_null() &&) method->is_empty_method()) {
    assert(result->get_type() == T_VOID, "an empty method must return a void value");
    return;
  }

#ifdef ASSERT
  { InstanceKlass* holder = method->method_holder();
    // A klass might not be initialized since JavaCall's might be used during the executing of
    // the <clinit>. For example, a Thread.start might start executing on an object that is
    // not fully initialized! (bad Java programming style)
    assert(holder->is_linked(), "rewriting must have taken place");
  }
#endif

  CompilationPolicy::compile_if_required(method, CHECK);

  // Since the call stub sets up like the interpreter we call the from_interpreted_entry
  // so we can go compiled via a i2c. Otherwise initial entry method will always
  // run interpreted.
  address entry_point = method->from_interpreted_entry();
  if (JvmtiExport::can_post_interpreter_events() && thread->is_interp_only_mode()) {
    entry_point = method->interpreter_entry();
  }

  // Figure out if the result value is an oop or not (Note: This is a different value
  // than result_type. result_type will be T_INT of oops. (it is about size)
  BasicType result_type = runtime_type_from(result);
  bool oop_result_flag = is_reference_type(result->get_type());

  // Find receiver
  Handle receiver = (!method->is_static()) ? args->receiver() : Handle();

  // When we reenter Java, we need to re-enable the reserved/yellow zone which
  // might already be disabled when we are in VM.
  thread->stack_overflow_state()->reguard_stack_if_needed();

  // Check that there are shadow pages available before changing thread state
  // to Java. Calculate current_stack_pointer here to make sure
  // stack_shadow_pages_available() and map_stack_shadow_pages() use the same sp.
  address sp = os::current_stack_pointer();
  if (!os::stack_shadow_pages_available(THREAD, method, sp)) {
    // Throw stack overflow exception with preinitialized exception.
    Exceptions::throw_stack_overflow_exception(THREAD, __FILE__, __LINE__, method);
    return;
  } else {
    // Touch pages checked if the OS needs them to be touched to be mapped.
    os::map_stack_shadow_pages(sp);
  }

  // do call
  { JavaCallWrapper link(method, receiver, result, CHECK);
    { HandleMark hm(thread);  // HandleMark used by HandleMarkCleaner

      // NOTE: if we move the computation of the result_val_address inside
      // the call to call_stub, the optimizer produces wrong code.
      intptr_t* result_val_address = (intptr_t*)(result->get_value_addr());
      intptr_t* parameter_address = args->parameters();
#if INCLUDE_JVMCI
      // Gets the alternative target (if any) that should be called
      Handle alternative_target = args->alternative_target();
      if (!alternative_target.is_null()) {
        // Must extract verified entry point from HotSpotNmethod after VM to Java
        // transition in JavaCallWrapper constructor so that it is safe with
        // respect to nmethod sweeping.
        address verified_entry_point = (address) HotSpotJVMCI::InstalledCode::entryPoint(nullptr, alternative_target());
        if (verified_entry_point != nullptr) {
          thread->set_jvmci_alternate_call_target(verified_entry_point);
          entry_point = method->adapter()->get_i2c_entry();
        }
      }
#endif
      StubRoutines::call_stub()(
        (address)&link,
        // (intptr_t*)&(result->_value), // see NOTE above (compiler problem)
        // 函数返回值地址
        result_val_address,          // see NOTE above (compiler problem)
        // 函数返回类型
        result_type,
        // 当前要执行的方法。通过此参数可以获取到 Java 方法所有的元数据信息，包括最重要的字节码信息，这样就可以根据字节码信息解释执行这个方法了
        method(),
        // HotSpot 每次在调用 Java 函数时，必然会调用 CallStub 函数指针，
        // 这个函数指针的值取自 _call_stub_entry，HotSpot 通过 _call_stub_entry 指向被调用函数地址。
        // 在调用函数之前，必须要先经过 entry_point，
        // HotSpot 实际是通过 entry_point 从 method() 对象上拿到 Java 方法对应的第1个字节码命令，这也是整个函数的调用入口
        entry_point,
        // 描述 Java 函数的入参信息
        parameter_address,
        // 描述 Java 函数的入参数量
        args->size_of_parameters(),
        // 当前线程对象
        CHECK
      );

      result = link.result();  // circumvent MS C++ 5.0 compiler bug (result is clobbered across call)
      // Preserve oop return value across possible gc points
      if (oop_result_flag) {
        thread->set_vm_result(result->get_oop());
      }
    }
  } // Exit JavaCallWrapper (can block - potential return oop must be preserved)

  // Check if a thread stop or suspend should be executed
  // The following assert was not realistic.  Thread.stop can set that bit at any moment.
  //assert(!thread->has_special_runtime_exit_condition(), "no async. exceptions should be installed");

  // Restore possible oop return
  if (oop_result_flag) {
    result->set_oop(thread->vm_result());
    thread->set_vm_result(nullptr);
  }
}
```

此处, JVM 就完成初始化并执行 Java 的 `public static void main(String[])` 方法。
