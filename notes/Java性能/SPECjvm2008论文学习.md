# SPECjvm2008论文学习

## 1. SPECjvm2008介绍

### 1.1 什么是SPECjvm2008

SPECjvm2008（Java虚拟机基准测试）是用来测试Java运行环境（JRE）性能的一个基准测试套件，其中包含了一些核心的Java功能实现的基准测试程序。该测试套件测试了处理器和内存子系统的性能，但是对文件系统的I/O依赖度很低，并且不包含机器间的网路系统。SPECjvm2008工作负载测试模仿的是各种常见的通用应用计算场景，这些基准测试可以测试机器上的Java虚拟机性能。

SPECjvm2008免费提供。它可以从[SPEC网站](https://www.spec.org/jvm2008)下载。

### 1.2 一些基本信息

**SPECjvm2008 是来自 SPEC 的一个新的多线程 Java 基准测试，它取代了过时的单线程基准测试SPECjvm98** 。 该基准测试旨在通过替换 DB、Chart、Javac 来解决 SPECjvm98 中早期工作负载的几个缺点； 删除 Jess，在 Scimark 工作负载的缓存和缓存外版本中添加 XML、Serial、Crypto。 它的目标是**测量 JVM 和硬件系统的性能**。 

SPECjvm98和SPECjvm2008的比较如下图所示：

![SPECjvm98和SPECjvm2008的比较](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230701214207059.png)

SPECjvm2008包括许多多线程工作负载，这些工作负载代表了服务器和客户端的广泛Java应用程序集合。它可以用来评估Java虚拟机（JVM）和底层硬件系统的性能。它可以强调JVM中的各种组件，例如JavaRuntime Environment（JRE）、实时（JIT）代码生成、内存管理系统、线程和同步功能。SPECjvm2008在设计时也考虑到了现代多核处理器。一个运行工作负载的JVM实例将生成足够的线程来强调底层硬件系统。它有望用于评估许多硬件功能，如内核和处理器数量、处理器频率、整数和浮点运算、缓存层次结构和内存子系统的影响。

SPECjvm2008附带了一组分析工具，如插件分析框架，可以收集运行时信息，如堆和功耗。它还附带了一个报告器，用于显示测试运行的摘要图。它易于配置和运行，并为性能分析提供快速反馈。SPECjvm2008可能有点偏向于服务器性能，因为每个硬件线程的最低内存需求是512MB。

SPECjvm2008可以在两种模式下运行：**基本运行（Base）和峰值运行（Peak）**。基本运行模拟用户不调整软件以提高性能的环境。不允许配置或手动调整JVM。基本运行具有固定的运行持续时间：120秒预热，然后是240秒测量间隔。峰值运行模拟允许用户调整JVM以提高性能的环境。它还允许进行反馈优化和代码缓存。JVM可以使用命令行参数和属性文件进行配置，以获得尽可能好的分数，这必须在提交时进行解释。此外，峰值运行对预热时间或测量间隔没有限制。但每个工作负载只允许进行1次测量迭代。提交Peak首先需要提交Base。

其他 SPEC 基准可用于测量 Java 系统在更专业的企业场景中的性能。 SPECjbb2005 是一个模拟 3 层系统的 Java 程序，重点关注中间层。为了实现成熟的多层 Java 基准测试，SPEC 开发了全面的应用程序服务器基准测试 SPECjAppServer2004。 SPEC还有一个名为SPECjms2007的Java消息基准测试，它是第一个用于评估基于JMS（Java消息服务）的企业面向消息的中间件服务器性能的行业标准基准测试。

### 1.3 一些基本概念

- JVM：Java虚拟机，是Java的执行引擎。
- JRE：Java 运行时环境，包括 JVM 和类库。
- Valid（有效运行）：指产生正确结果的运行，可以应用于基准测试、子基准测试或整个套件。
- Compliant（合规运行）：指根据运行规则完成整个套件的运行，前提条件是Valid。
- Logical CPU（逻辑处理器）：使用 java 调用 `Runtime.getRuntime().availableProcessors()` 来确定其运行的系统中逻辑 CPU 的数量。
- `java`：本文指用于调用 Java 虚拟机的启动器。
- Workloads：SPECjvm2008包含了一组工作负载集合，希望表示不同的常见的计算类型。工作负载中的算法和操作是现实应用程序的组件，包括文本/字符处理、数值计算和按位计算（例如媒体处理）。每个工作负载都有特定的工作量需要完成，这使得它们本身就是一个小基准，并且其中几个基准还具有子基准。
- Base和Peak：SPECjvm2008 中有两个运行类别，称为 Base 和 Peak，还有一个附加运行类别，称为 Lagom。为了创建合规的结果，必须包含Base，也可以选择加入Peak。Base显示了未对 JVM 进行任何调整的系统性能，即“开箱即用”性能。 Base有一个限制，即不允许使用人员对 JVM 进行任何手动调整，并且不允许更改运行时间。但是Base允许调整操作系统和硬件（包括 BIOS 等固件）。Peak则显示了系统可以实现的目标，允许对 JVM 进行调整以获得最佳性能。**Base和Peak是通过两次单独调用 SPECjvm2008 基准套件来完成的，最初会创建两个不同的结果**，这些结果随后会合并到一个原始文件中以便提交。

- Lagom workload：Lagom 工作负载是**固定大小的工作负载**，这意味着它对每个基准测试执行一定数量的操作。这是对基本类别和峰值类别的补充，可以在首选运行固定量的工作时使用。工作负载将在每个基准测试线程中运行指定数量的操作（每个基准测试不同）。默认情况下，基准线程数将根据机器拥有的硬件线程数进行调整，因此为了在不同大小的两个不同系统上获得准确的工作量，应设置基准线程数。
- Operation：基准测试工作负载的每次调用都是一个操作。测试框架将多次调用基准测试，使其在一次迭代中执行多个操作。
- Iteration：一次迭代会持续一定时间，默认为 240 秒。在此期间，测试框架将启动多项操作，前一操作完成后就会启动新的操作。测试框架不会中止操作，而是等到操作完成才停止。测试框架预计在一次迭代内完成至少 5 个操作。迭代的持续时间不会小于指定时间，但如果操作花费的时间太长，根据预热期间的性能情况，可能会增加迭代时间。
- Warmup：第一次迭代是预热迭代，默认运行 120 秒。预热迭代的结果不包含在基准测试结果中。如果想跳过warmup，将预热时间设置为0即可。
- Parallelism：大多数基准测试都是并行运行的，也就是多个操作在单独的线程中同时启动。从测试框架的角度来看，这些线程相互独立工作，但工作负载的设计旨在引入一系列有趣的问题，包括在应用程序级别共享数据和工作，以及使用JVM内部共享的资源。
- Analyzers：我们可以使用 SPECjvm2008 基准测试工具来分析运行期间发生的情况（举例：在基准测试运行期间查看堆使用情况），从而了解和诊断产品。为了实现这个目标，框架可以在基准测试运行期间运行一个或多个分析器。这些分析器将收集信息，并与基准结果一起提供结果。它将在报告中的详细信息图表上绘制信息，其中绘制每个基准操作，并且可以报告每次迭代的摘要指标。分析器可以在基准测试运行期间轮询信息，或者根据事件报告结果实现回调方法。
- Startup Benchmark：启动基准测试测量JVM和应用程序的启动性能。ops/m指标是**使用新启动的JVM（通过java.lang.Runtime.exec()）运行每个工作负载一次操作所需的时间来计算的**。这既测试了基本的JVM启动时间，也测试了启动基准测试工作负载的时间，因为为了获得多个基准测试中的最佳得分，优化代码的热点区域至关重要。
- SciMark Large and Small Workloads：SciMark工作负载使用小型和大型数据集大小进行运行。小型数据集适应大多数现代CPU架构上可用的L2缓存，并旨在测试JVM代码优化和计算性能，同时确保数据集仅在缓存中访问。大型数据集足够大，无法适应标准的L2缓存，并旨在测试针对内存和内存子系统性能的JVM优化。scimark.monte_carlo工作负载仅运行一次，因为它不使用可修改的计算数据集。

### 1.4 Workloads和得分计算

SPECjvm2008由11组用于客户端和服务器的Java SE应用程序组成。每个组代表Java应用程序的一个独特领域。总得分是通过嵌套几何方法进行计算。

![image-20230701215007042](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230701215007042.png)

测试按顺序运行，即从startup.helloworld开始，到xml.validation结束。每个“startup”工作负载都会启动一个新的JVM instance。在运行完所有“startup”工作负载后，将启动一个JVM来运行其余的工作负载，即从compiler.compiler到xml.validation。因此，**运行每个工作负载所留下的环境可能会影响其后工作负载的性能**。

在给定的运行中，每个 SPECjvm2008 子基准测试都会生成一个以 ops/min（每分钟操作数）为单位的结果，该结果反映了系统能够完成该子基准测试工作负载调用的速率。运行结束时，SPECjvm2008 计算单个量，旨在反映运行期间执行的所有子基准上系统的整体性能。用于计算组合结果的基本方法是计算**几何平均值**。然而，由于希望或多或少平等地反映各个应用领域的性能，因此所完成的计算比子基准结果的直接几何平均值稍微复杂一些。

为了包括代表同一通用应用领域的多个子基准，同时仍然平等地对待各个应用领域，在将子基准组合成总体吞吐量结果之前，**对某些子基准组计算中间结果**。特别是，对于这些子基准组。

- COMPILER: compiler.compiler, compiler.sunflow
- CRYPTO: crypto.aes, crypto.rsa, crypto.signverify
- SCIMARK: scimark.fft.large, scimark.lu.large, scimark.sor.large, scimark.sparse.large, scimark.fft.small, scimark.lu.small, scimark.sor.small, scimark.sparse.small, scimark.monte_carlo
- STARTUP: {all sub-benchmarks having names beginning with startup. } 
- XML: xml.transform, xml.validation

计算每组中子基准结果的几何平均值。然后，将总体吞吐量结果计算为这些组结果和其他子基准结果的几何平均值。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1466733/1688115168492-b41a0275-1fd9-4a6e-bc5a-34d9b09a4ab8.png)

虽然从仅执行选定子基准的运行中获得的吞吐量结果可能对研究目的很有趣且有用，但只有执行所有子基准（并满足几个其他条件）的运行的总体吞吐量结果才被视为代表SPECjvm2008本身的系统性能。

## 2. SPECjvm2008的具体组成

### 2.1 Startup

startup组通过启动一个新的JVM实例来启动每个工作负载，从而测量每个工作负载的JVM启动时间。这个组别中的每个工作负载都是单线程的。它们中的每一个都是套件中相应吞吐量测试的启动。唯一的例外是`helloworld`，它没有相关联的响应吞吐量测试。

### 2.2 Compiler

`compiler`组有两个工作负载：`compiler`和`sunflow`。`compiler.compiler`工作负载衡量了OpenJDK编译器的编译时间。`compiler.sunflow`工作负载衡量了`sunflow`基准测试的编译时间。因为本组工作负载的目的是**评估编译器的性能**，SPECjvm2008通过将输入数据存储到内存或者文件缓存中，次从而减小I/O的影响。

### 2.3 Compress

`compress`工作负载取自SPECjvm98，使用一种更改的Lempel-Ziv方法压缩和解压缩数据。输入数据从90KB扩大到3.36MB。为了减小I/O的影响，数据被存储在缓冲区中。算法使用大约为67KB的内部表和基于输入数据的伪随机访问。因为JVM生成和处理混合长度数据访问，此工作负载能够测试即时编译、内联、数组访问和缓存性能。

| 编号 | 测试用例                    | 说明                                                         |
| ---- | --------------------------- | ------------------------------------------------------------ |
| 1    | startup.helloworld          | 测试helloworld程序从运行开始到结束所需的时间                 |
| 2    | startup.compiler.compiler   | 普通java编译所需要的时间                                     |
| 3    | startup.compiler.sunflow    | 编译sunflow图像渲染引擎所需要的时间                          |
| 4    | startup.compress            | 测试压缩程序，单次压缩所需的时间                             |
| 5    | startup.crypto.aes          | 测试AES/DES加密算法，单次加解密所需的时间输入数据长度为100bytes,713KB |
| 6    | startup.crypto.rsa          | 测试RSA加密算法，单次加解密需要的时间输入数据长度为100bytes,16KB |
| 7    | startup.crypto.signverify   | 测试单次使用MD5withRSA,SHA1withRSA,SHA1withDSA,SHA256withRSA来签名，识别所需要的时间。输入数据长度为1KB,65KB,1MB |
| 8    | startup.mpegaudio           | 单次mpeg音频解码所需的时间                                   |
| 9    | startup.scimark.fft         | 单次快速傅立叶变换所需的时间                                 |
| 10   | startup.scimark.lu          | 单次LU分解所需的时间                                         |
| 11   | startup.scimark.monte_carlo | 单次运行蒙特卡罗算法所需的时间                               |
| 12   | startup.scimark.sor         | 单次运行jacobi逐次超松弛迭代法所需的时间                     |
| 13   | startup.scimark.sparse      | 单次稀疏矩阵乘积所需的时间                                   |
| 14   | startup.serial              | 单次通过socket传输java序列化对象到对端反序列化完成所需的时间（基于jboss serialization benchmark） |
| 15   | startup.sunflow             | 单次图片渲染处理所需的时间                                   |
| 16   | startup.xml.transform       | 单次xml转换所需的时间，转换包括dom,sax,stream方式            |
| 17   | startup.xml.validation      | 单次xmlschema校验所需的时间                                  |
| 18   | compiler.compiler           | 在规定时间内，多线程迭代测试普通java编译，得出ops/m          |
| 19   | compiler.sunflow            | 在规定时间内，多线程迭代测试sunflow图像渲染，得出ops/m       |
| 20   | compress                    | 在规定时间内，多线程迭代测试压缩，得出ops/m                  |
| 21   | crypto.aes                  | 在规定时间内，多线程迭代测试AES/DES加解密算法，得出ops/m     |
| 22   | crypto.rsa                  | 在规定时间内，多线程迭代测试RSA加解密算法，得出ops/m         |
| 23   | crypto.signverify           | 在规定时间内，多线程迭代测试使用MD5withRSA,SHA1withRSA,SHA1withDSA,SHA256withRSA来签名，识别，得出ops/m |
| 24   | derby                       | 在规定时间内，迭代测试数据库相关逻辑，包括数据库锁，BigDecimal计算等，最后得出ops/m |
| 25   | mpegaudio                   | 在规定时间内，多线程迭代mpeg音频解码，得出ops/m              |
| 26   | scimark.fft.large           | 在规定时间内，多线程迭代测试快速傅立叶变换，使用32M大数据集，最后得出ops/m |
| 27   | scimark.lu.large            | 在规定时间内，多线程迭代测试LU分解，使用32M大数据集，最后得出ops/m |
| 28   | scimark.sor.large           | 在规定时间内，多线程迭代测试jacobi逐次超松弛迭代法，使用32M大数据集，最后得出ops/m |
| 29   | scimark.sparse.large        | 在规定时间内，多线程迭代测试稀疏矩阵乘积，使用32M大数据集，最后得出ops/m |
| 30   | scimark.fft.small           | 在规定时间内，多线程迭代测试快速傅立叶变换，使用512K小数据集，最后得出ops/m |
| 31   | scimark.lu.small            | 在规定时间内，多线程迭代测试LU分解，使用512KB小数据集，最后得出ops/m |
| 32   | scimark.sor.small           | 在规定时间内，多线程迭代测试jacobi逐次超松弛迭代法，使用512KB小数据集，最后得出ops/m |
| 33   | scimark.sparse.small        | 在规定时间内，多线程迭代测试稀疏矩阵乘积，使用512KB小数据集，最后得出ops/m |
| 34   | scimark.monte_carlo         | 在规定时间内，多线程迭代测试蒙特卡罗算法，得出ops/m          |
| 35   | serial                      | 在规定时间内，多线程迭代测试通过socket传输java序列化对象到对端反序列化（基于jbossserializationbenchmark），得出ops/m |
| 36   | sunflow                     | 在规定时间内，利用sunflow多线程迭代测试图片渲染，得出ops/m   |
| 37   | xml.transform               | 在规定时间内，多线程迭代测试xml转换，得出ops/m               |
| 38   | xml.validation              | 在规定时间内，多线程迭代测试xmlschema验证，得出ops/m         |

## 3. SPECjvm2008性能分析

### 3.1 Score

在论文给出的环境下，SPECjvm2008的得分如下所示：

![image-20230702210857813](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230702210857813.png)

我们观察到对于每一次运行，得分差距较大，偶尔会超过5%。另外，可以看出，从`cimark.lu.large`的5ops/min到`scimark.lu.small`和`scimark.monte_carlo`的4904ops/min，不同工作负载的得分差距太大。正是这一原因，促使基准测试开发人员选择使用几何平均值计算总得分，因为使用算术平均值会因为部分得分过高产生较大的误差。

### 3.2 Base and Peak

下表展示了通过调整和配置系统来优化基准测试性能的效果。我们看到基准测试的性能提高了近7%。对于某些组件，性能提升更高，对于`compiler.compiler`，几乎是40%。然而，另外一些组件的性能则会降低。由于**所有组件都需要使用相同的配置参数集**，从整体上最适合基准测试的选项对于其中一部分组件可能不是最佳的。使用所有配置参数来启动工作负载，需要更长时间tartup时间，特别是Hotspotas AggressiveOpt中提到的一个选项（在JIT中打开更复杂的编译器优化）。

![image-20230702214324253](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230702214324253.png)

我们还可以发现，`scimark.fft.large`的性能下降幅度最大，为0.697。此工作负载计算大数据集的fft，并在数据中有2^N的跨距；数据访问模式是这样的：由于缓存利用率不高，使用大页面实际上会损害性能，并且影响到30%。大多数其他组件都喜欢使用大页面。

### 3.3 一些基本指标

我们再来看SPECjvm2008在一些基本指标上的表现，如下图所示。为了比较，该表格还包括SPECjbb2005和SPECjAppServer2004的相应数据。

![image-20230702223430916](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230702223430916.png)

从上表可以看出：

- SPECjvm2008需要很少的内核时间，处理器几乎一直（>99%）处于用户态，这点和SPECjbb2005很相似，与SPECjAppServer2004不同。

- SPECjAppServer2004具有很高级别的网络流量，需要大量的操作系统支持；SPECjvm2008没有相关的I/O需求。

- 乍一看，许多SpecJVM2008组件都有非常高级别的上下文切换。例如，Scimark.lu.large每个操作有5000多个上下文切换，另外四个工作负载在一次操作中约有超过1000次的切换。但是，在深入挖掘时，我们发现虽然速率有点高，但并不是太高。Scimark.lu.large每分钟只提供大约5个操作，这意味着我们在一分钟内只观察到大约26000个上下文切换。与SPECjAppServer2004相比，SPECjAppServer2004每个事务只经历了大约40次上下文切换（JOP），但每秒提供了大约2000个事务（JOP），我们可以看到SPECjAppServer2004中的上下文切换速率是Scimark.lu.large中的40倍。上下文切换速率最高的SPECjvm2008组件是Derby，其速率约为SPECjAppServer2004速率的30%。

  ### 3.4 对象分配率和垃圾回收率

  接下来，我们观察SPECjvm2008不同组件的对象分配率和垃圾回收率，同时，我们展示了SPECjbb2005和SPECjAppServer2004的相应数据方便对比。

  ![image-20230703205603243](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230703205603243.png)

上表给出了这些数据，我们可以立即看到，除了**compiler.compiler和compiler.sunflow这两个组件对JVM的垃圾回收基础设施有着非常高的要求**，其他组件对垃圾回收基础设施要求不高。其中两个组件实际上对垃圾收集器没有任何要求，另外11个组件在垃圾收集上花费的时间不到0.1%。另一方面，SPECjbb2005在GC中花费了超过2%的时间，specjappserver2004在GC中花费了7.5%的时间。

对象分配数据也类似，但是，`derby`、`sunflow`、`xml.transform`、`xml.validation`这4项基准测试表现出了较高的内存分配率和较低的垃圾收集使用率。由于调用GC的速率与内存分配速率直接相关，因此它们运行时GC速率较快，而GC花费的时间较少。我们可以推断，当调用GC时，这些组件的活动对象要少得多，但是我们还没有完全测试这个理论。例如，Derby具有很高的对象分配率，这是因为频繁分配不可变的longBigDecimal对象，而这些对象的生存时间不长。

### 3.4 硬件评价指标

接下来分析SPECjvm2008各组件的`CPI`和`Pathlength`，同样提供SPECjbb2005和SPECjAppServer2004的相应数据。

- CPI（Cycle Per Instruction，平均指令周期数 ）是计算机体系结构和处理器性能的重要指标之一，表示执行某个程序的指令平均周期数，可以用来衡量计算机运行速度。
- Pathlength：指令路径长度是执行计算机程序的一部分所需的机器代码指令的数量。整个程序的总路径长度可以被视为算法在特定计算机硬件上性能的衡量标准。

从下图中可以看出，CPI 的范围很大（0.35->37），但是，只有少数组件的CPI值接近SPECjbb2005和SPECjAppServer2004。Pathlength（每个基准操作执行的指令数）的范围类似（8.6亿->350亿）。有趣的是，SPECjvm2008组件的路径长度远大于SPECjAppServer2004和SPECjbb2005的路径长度。新基准测试的开发人员将每个组件的操作定义为一个底层组件事务包，从而导致路径长度显著增加。

![image-20230703220919120](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230703220919120.png)

接下来，我们深入研究CPI数据。由于CPI的范围很广，而且工作负载的高CPI值通常是由于强内存依赖性，因此我们比较了每个组件的内存需求，并将数据显示在下表中。

![image-20230706103227895](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230706103227895.png)

有趣的是，虽然确实存在一种相关性，并且一些较低内存带宽需求的工作负载具有较低的CPI，但是具有最高CPI的工作负载却显示出非常低的带宽需求。具体来说，`scimark.fft.large`的CPI是37，但是内存带宽要求为9 MB/s。作为比较，SPECJB2005的CPI为1.22，内存带宽要求为6 GB/s。

Inter处理器提供一系列性能事件计数器。在下表中，我们检查了一些关键指标，最后一级缓存（LLC）MPI（每条指令的未命中）、ITLB和DTLB未命中、浮点指令数以及HITM指标，以了解每个组件的共享行为。

LLC MPI数据为我们提供了一个清晰的答案，指出了四个`scimark`组件高CPI的原因。CPI最高的工作负载`scimark.fft.large`的MPI为0.05，即每20条指令有1次缓存未命中，该速率大约是SPECjbb2005和SPECJAPPServer2004中缓存未命中率的**20倍**。这些缓存未命中造成的内存延迟会导致高CPI。高CPI极大地限制了工作负载的性能，并且由此产生的低吞吐量造成了低内存带宽需求的出现。因此，这四种工作负载的性能极大依赖于内存延迟。

在其余的组件中，有几个具有可忽略的缓存未命中，而少数具有中等CPI的组件（`compiler.*`、`xml.*`、`sunflow`、`derby`）具有与SPECjbb2005和SPECjAppServer2004中相同数量级的MPI。`derby`具有不可变BigDecimal对象的高分配率，MPI为0.0057，同样解释得通。

![image-20230706110829422](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230706110829422.png)

从上表可以看出，SPECjbb2005 和 SPECjAppServer2004 可能存在的一个问题是浮点指令使用率低。 在SPECjvm2008 中，一些组件具有大量的浮点指令使用， 尤其是 `Derby`，其浮点指令使用率为 0.01，即每 100 条指令中有 1 条。

大多数组件的代码占用量都很小。` Derby` 再次成为例外，每 2500 条指令就有一次 ITLB 未命中。 所有工作负载都没有面临太大的 DTLB 压力。 一些 DTLBmiss 指标看起来很高，但是考虑到这些工作负载的长Pathlength，也说得通。

下表展示了随着处理器内核数量的增加，每个组件在1个处理器上的相对性能。由于我们的系统有16个处理器，我们已经测试了从2到16个处理器的扩展。

![image-20230710123252306](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230710123252306.png)

工作负载的启动时间不受可用处理器内核数量的影响，因为JVM初始化代码大多是单线程的。其他一些工作负载，如`compress`、`crypto.*`和`mpegaudio`，表现出极好的伸缩性，而一些工作负载表现出超线性行为。由于有足够的run-to-run变化，应谨慎处理此数据。虽然这些数据点很可能是有噪声的，但是这种缩放在理论上并非是不可能的。更多内核的可用性允许JVM使用更多线程进行编译和优化，这可以生成更好的代码。当然，这只是一种理论，它需要一些时间和额外的实验来滤除噪声数据。

Inter最近将发布一个新的Xeon微体系结构，i7。我们在一个预发布的平台上进行了一些实验，然后展示这些结果。表11显示了i7的Peak和Base之间的比率在大多数方面与Core2-Duo相似。但是，也有例外，`scimark.fft.small`，它的下性能会有14%的退化，而我们早期的结果显示有7%的增益。猜测该工作负载对数据布局敏感。由于两个处理器具有完全不同的缓存体系结构，所以数据布局更改会导致不同的效果。

![image-20230710131127908](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230710131127908.png)

i7处理器内核包括SMT（同步多线程），它允许两个软件线程在一个内核上同时执行。在下表中，我们查看了SMT对SPECjvm2008性能的影响，并注意到基准测试作为一个整体有22%的提升。几乎所有组件都在某种程度上受益，尤其是scimark.sor.small几乎翻了一番。SMT的好处通常会受到缓存可用性的限制。

![image-20230710131727197](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230710131727197.png)

最后，我们查看SPECjvm2008由于更快的处理器核心频率而带来的好处。我们在2.8 GHz和2.93 GHz两个频率下运行了基准测试的几个组件。在所有这些情况下，频率增加4.6%会使性能提高4-5%。甚至比其他组件更依赖内存的Derby也能从频率的增加中充分获益。i7平台使用QPI（快速路径互连），具有较低的内存访问延迟。通过改进硬件预取器，还可以减少实际内存延迟。

![image-20230710132440153](https://tuchuang-01.oss-cn-beijing.aliyuncs.com/img/image-20230710132440153.png)

## 4.分析总结

在研究Java性能的早期，使用SPECjvm98非常流行，主要原因是它是第一个规范java基准测试，使用简单，并且通过提供几个组件，它允许性能分析师研究工作负载对平台影响的不同方面。这个新的基准继续提供后两个益处。但是，SPECjvm2008包含大量具有显著不同系统特性的Java测试，这对于软件优化和测试系统来说是一个巨大的挑战。

报告的度量是11个组件的几何平均数，其中一些组件具有子组件，这一事实意味着仅对1个组件进行改进不太可能改变基准报告的度量。例如，将Derby的性能提高一倍，而不改变其他组件，只会使报告的SPECjvm08性能改变6%。因此，我们预计，人们不仅会关注总得分，还会将兴趣集中在各个组成部分的得分上。

SPECjvm2008中的工作负载为JVM提供了许多改进代码生成、线程、内存管理和锁算法调优的机会。许多这样的变化可能会对所有组件产生不同程度的影响。例如，对象分配方面的改进将使所有组件受益，但是像Derby这样的组件将受益更多。其他更改将以更本地化的方式受益。特别有趣的是浮点行为，以前的基准测试并没有强调浮点，在这方面研究平台和JVM的改进也没有被普遍接受的方法。像Derby这样的工作负载应该可以减轻这一点。针对XML组件，同样可以对字符串和XML行为进行更深的研究。

SPECjvm2008中的许多工作负载也可用于评估当前和未来的硬件特性，特别是内存子系统和锁优化。基于我们对这个新基准的第一次分析，我们的结论是，它似乎是对我们的工具箱的一个有价值的补充。虽然它不能取代SPECJB2005或SPECJAPPServer2004，而且它可能永远不会像这两个一样重要和具有代表性，但它提供的行为差异足以吸引性能分析师。

## 参考

1. Kumar Shiv, Kingsum Chow, Yanping Wang, and Dmitry Petrochenko. 2009. SPECjvm2008 Performance Characterization.
2. https://www.spec.org/jvm2008/docs/UserGuide.html
3. https://github.com/BlankSpacePlus/java-performance/wiki/SPECjvm2008%E5%AD%A6%E4%B9%A0%E8%AE%B0%E5%BD%95
4. https://en.wikipedia.org/wiki/Instruction_path_length

