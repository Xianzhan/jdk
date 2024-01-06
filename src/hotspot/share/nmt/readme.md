
# [Native Memory Tracking](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/nmt-8.html)

概述

本机内存跟踪 （NMT） 是 Java Hotspot VM 的一项功能，用于跟踪 HotSpot JVM 的内部内存使用情况。您可以使用 jcmd 实用程序访问 NMT 数据。此发行版中的 NMT 不跟踪第三方本机代码内存和 JDK 类库。此外，此版本不包括 HotSpot for JMC 中的 NMT MBean 。

主要特点

本机内存跟踪具有以下功能，当与 jcmd 一起使用时，可以跟踪不同级别的内存使用情况。
- 默认情况下，热点虚拟机的 NMT 处于关闭状态。使用 JVM 命令行选项打开此功能。请参见[本机内存跟踪命令手册页](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.ml#BABCBGHF)。
- 可以使用 jcmd 实用程序访问内存跟踪数据。请参见使用 [jcmd 访问 NMT 数据](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/nmt-8.html#use_jcmd)。
- 生成摘要和详细报告。
- 建立早期基线以供以后比较。
- 使用 JVM 命令行选项在 JVM 出口处请求内存使用情况报告。请参阅 VM 出口处的 NMT。
- NMT 可以使用 jcmd 实用程序关闭，但不能使用 jcmd 启动/重新启动。

如何使用本机内存跟踪

首先启用 NMT，然后使用 jcmd 访问到目前为止收集的数据。

启用 NMT

使用以下命令行启用 NMT。请注意，启用此功能将导致 5-10% 的性能开销。<br/>
`-XX:NativeMemoryTracking=[off|summary|detail]`
| | |
|---|---|
|`off`|默认情况下，NMT 处于关闭状态|
|`summary`|仅收集按子系统聚合的内存使用情况。|
|`detail`|按各个呼叫站点收集内存使用情况。|

使用 jcmd 访问 NMT 数据

使用 jcmd 转储收集的数据，并选择性地将其与上一个基线进行比较。<br>
`jcmd <pid> VM.native_memory [summary|detail|baseline|summary.diff|detail.diff|shutdown] [scale=KB|MB|GB]`
| | |
|---|---|
|`summary`|打印按类别汇总的摘要。|
|`detail`|1. 按类别汇总的打印内存使用情况; 2.打印虚拟内存映射; 3. 按调用站点聚合的打印内存使用情况|
|`baseline`|创建一个新的内存使用情况快照来比较。|
|`summary.diff`|根据上一个基线打印新的摘要报告。|
|`detail.diff`|根据上一个基线打印新的详细报表。|
|`shutdown`|关闭 NMT。|

VM 出口处的 NMT

启用本机内存跟踪时，使用以下 VM 诊断命令行选项获取 VM 退出时的最后内存使用情况数据。详细程度基于跟踪级别。<br/>
`-XX:+UnlockDiagnosticVMOptions -XX:+PrintNMTStatistics`

有关如何监视 VM 内部内存分配和诊断 VM 内存泄漏的信息，请[参阅故障排除指南](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/index.html)。