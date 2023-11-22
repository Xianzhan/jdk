
运行时

- [JavaCalls](./javaCalls.hpp): 所有对 Java 的调用都必须通过 `JavaCalls`。设置堆栈帧并确保 `last_Java_frame` 指针被正确链接。
    - `JavaCalls::call_helper`: entry_point
- [CallStub](./stubRoutines.hpp): Java 方法描述
