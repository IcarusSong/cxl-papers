# Demystifying 
CXL Memory with Genuine CXL Ready Systems and Devices
## 作者以及出版物
### 作者
1. **Yan Sun**  伊利诺伊大学厄巴纳分校
2. **Yifan Yuan**  英特尔实验室  
3. **Zeduo Yu**  伊利诺伊大学厄巴纳分校  
4. **Reese Kuper**  伊利诺伊大学厄巴纳分校 
5. **Chihun Song**    伊利诺伊大学厄巴纳分校 
6. **Jinghan Huang**   伊利诺伊大学厄巴纳分校 
7. **Houxiang Ji**    伊利诺伊大学厄巴纳分校  
8. **Siddharth Agarwal**    伊利诺伊大学厄巴纳分校 
9. **Jiaqi Lou**   伊利诺伊大学厄巴纳分校 
10. **Ipoom Jeong**     伊利诺伊大学厄巴纳分校 
11. **Ren Wang**      英特尔实验室  
12. **Jung Ho Ahn**      首尔国立大学  
13. **Tianyin Xu**      伊利诺伊大学厄巴纳分校 
14. **Nam Sung Kim**      伊利诺伊大学厄巴纳分校 

### 出版物
MICRO ’23


### 1. 新型应用对内存系统提出挑战性需求

随着大数据、人工智能、深度学习推荐系统以及云计算等新型应用的快速发展，系统对内存提出了前所未有的需求。如今的应用不仅需要更大容量的内存来存储海量数据，而且对内存带宽的要求也大幅提高，以满足高并发、低延迟的数据处理需求。同时，在功耗不断受限的情况下，还要求在尽可能低的能耗下实现这些目标。典型的场景包括：  
- **实时数据处理与分析**：例如股票交易、视频处理、在线广告推荐等，需要快速响应且计算密集。  
- **大规模机器学习与深度学习**：如 DLRM（深度学习推荐模型）等模型，其嵌入操作与大规模特征查找对内存带宽要求极高。  
- **云计算与微服务架构**：多租户环境下，不同应用共享内存资源，既要求单应用低延迟也要求系统整体吞吐高。这些要求共同推动了内存系统必须在更低功耗下提供更大容量与更高带宽的转型升级。


### 2. 内存技术逼近缩放极限

传统内存技术在过去几十年间通过工艺改进不断提升频率、降低时延、增加容量，满足了大部分计算需求。然而，随着技术发展到一定阶段，内存技术逐渐逼近其物理与工艺的极限：  
- **工艺挑战**：例如 DRAM 工艺受制于晶体管的尺寸和电容特性，不能无限制降低时延，同时高速信号在 PCB 板上传输时也会引入更多干扰和能耗。  
- **成本与可靠性问题**：随着频率和带宽的攀升，设计和制造成本呈指数级上升，可靠性和散热问题也变得越来越突出。  
- **能耗瓶颈**：在高频率与高速数据交换环境中，每比特的数据传输能耗急剧上升，这与低功耗要求相冲突。  

因此，传统内存体系结构的单一路径（如 DDR 内存技术）已经难以再通过单纯扩展接口和增加通道来满足新兴应用对性能和能效的双重要求。


### 3. DDR 拓展的局限性

DDR（双倍数据率同步动态随机存储器）作为目前主流的内存技术，其在扩容和提高带宽方面存在一些固有的局限性：  
- **物理接口与引脚数限制**：DDR5 甚至需要高达 288 个引脚才能满足每个通道的要求，在 CPU 包装引脚数量有限的情况下，很难通过增加通道数来进一步提升总带宽。  
- **信号完整性与时序挑战**：高速 DDR 内存传输依赖多条并行信号通道，高速并行信号容易受到噪声、串扰和时钟同步等问题的制约，导致能耗急剧上升且稳定性难以保证。  
- **能耗与散热问题**：内存频率的提升直接导致电路开销和功耗增加，在大规模数据中心或者高性能计算中，会带来较大的散热压力。  

综上所述，DDR 内存体系在面对不断升级的应用需求时，已展现出扩展带宽和容量的天花板，迫切需要寻找新的技术路径来突破这一瓶颈。


### 4. 引入 CXL：突破传统局限的新路径

为了突破 DDR 内存的局限性，Compute Express Link（CXL）技术应运而生。CXL 是一种基于 PCIe 物理层构建的新型互连标准，其核心优势体现在以下几个方面：  
- **更高的传输速率**：CXL 借助 PCIe 高速串行传输特性，每个通道可以实现比 DDR 更高的比特传输率。例如，PCIe 4.0 每通道传输速率可达到 16 Gbps，而 DDR4 每引脚传输速率仅约 3.2 Gbps。  
- **低功耗**：得益于串行传输以及更高的传输效率，CXL 在每比特能耗上有显著降低。  
- **解耦内存技术与 CPU 接口**：CXL 提供一层控制器，可以将内存技术与 CPU 的专用接口解耦，使得内存设备可以采用更加灵活和多样化的技术，不局限于传统 DDR。  
- **支持缓存一致性**：CXL 标准在设计上允许 CPU 与设备共享缓存一致性协议，这使得 CXL 内存可以更高效地与 CPU 协作，保证数据一致性。  

因此，CXL 被视为未来内存扩展的关键技术，可以在保证低功耗和高带宽的同时，提供更大的内存容量，从而满足新型应用对内存系统的挑战性要求。


### 5. 当前 CXL 内存的实现现状：NUMA 模拟

由于真实的 CXL 内存设备尚处于研发或初步部署阶段，当前大多数有关 CXL 的研究工作尚无法直接在商业化硬件上实验。为此，研究人员常常采用以下方法：  
- **利用远程 NUMA 节点模拟 CXL 内存**：在多 socket 系统中，通过将 DDR 内存部分暴露在远程 NUMA 节点的方式来模拟 CXL 内存的行为。  
- **模拟环境的局限性**：这种模拟方法虽然能在一定程度上复现 CXL 内存的访问行为，但在延迟、带宽、缓存一致性处理等关键指标上，与未来真实的 CXL 内存设备会存在较大差异。  

论文中揭示了一个重要问题，即基于 NUMA 节点模拟 CXL 内存的性能评估结果，可能会存在根本性的误差，从而导致对 CXL 内存特性及其在实际系统中的应用效果做出错误的判断。因此，进行基于真实 CXL 内存设备的测试和对比，成为确保评估结果准确性和实际应用价值的关键一步。


### 6. 模拟 CXL 内存与真实 CXL 内存之间的根本性差异

通过实验数据和对比研究，论文指出模拟 CXL 内存（即通过远程 NUMA 节点实现的内存扩展方案）与真实 CXL 内存在以下几个方面存在显著差异：

- **延迟差异**：  
  模拟 CXL 内存由于采用 NUMA 节点技术，当 CPU 发出内存请求时，需要通过远程通信（如通过 UPI 等高速互连）完成缓存一致性检查和数据传输。因此，相比直接访问本地 DDR 内存，延迟显著增加；而真实 CXL 内存则通过内置的缓存一致性加速结构和专用控制器优化了这一过程，尽可能地缩减延迟开销。在实验中，某些真实 CXL 内存在特定内存访问指令下延迟比模拟 CXL 内存低了 20%～30% 甚至更多。

- **带宽效率差异**：  
  由于控制器设计和所采用 DRAM 技术的不同，真实 CXL 内存在不同的读写操作下的带宽表现存在较大差异。实验显示，对于同一 DDR 技术，真实 CXL 内存设备在特定模式（如交错读写模式）下可以实现比模拟内存更高的带宽利用率。这一点在带宽密集型应用（例如 DLRM 的嵌入聚合阶段）中的表现尤为明显。

- **缓存一致性与数据路径**：  
  模拟 CXL 内存依赖于远程 NUMA 节点实现时，必须经过远程 CPU 的缓存一致性检查，再通过长链路传输到本地系统；而真实 CXL 内存由于直接通过专用控制器进行本地高速数据交换，能较好地保证缓存一致性，同时减轻长链路带来的延迟和带宽衰减。

总之，模拟方法虽然能提供一种验证思路，但由于其在硬件架构和数据传输机制上的根本不同，可能导致对 CXL 内存特性（如延迟、带宽、能耗等）的定量评估存在偏差，需要在真实硬件支持下进一步深入研究。


### 7. 分析 CXL 内存与 DDR 内存的带宽和延迟特性

论文采用多个微基准测试工具（如 Intel MLC 及自研的 memo 工具），详细测量了不同内存设备的性能指标，对比了模拟 CXL 内存、真实 CXL 内存和本地 DDR 内存在不同访问模式下的表现。

- **延迟测量**  
![](https://pic1.imgdb.cn/item/67fa288088c538a9b5cc39fb.png)
  通过分别对负载（ld）、非时序负载（nt-ld）、存储（st）和非时序存储（nt-st）进行测量，得出以下结论：
  - 在串行访问场景下，由于传统工具无法充分利用全双工传输，延迟较高，而采用随机并行访问的 memo 工具则显著降低了延迟。
  - 真实 CXL 内存在延迟优化上有所突破，其利用 CPU 内部专用缓存一致性硬件结构，可使特定类型的内存访问延迟比模拟内存低 3%～10% 左右。
  - 模拟 CXL 内存由于需要经过远程 CPU 的缓存一致性检查，延迟相对较长，而 DDR 内存在优化的本地缓存架构支持下延迟最低。

- **带宽测量**  
![](https://pic1.imgdb.cn/item/67fa28b288c538a9b5cc3a13.png)
  带宽测量主要考察顺序访问场景下，各内存设备能提供的实际数据吞吐率，并以理论最大值进行标准化：
  - 实验发现，即便在相同的 DRAM 技术下，不同 CXL 内存设备（如 CXL-A、CXL-B、CXL-C）的带宽效率存在较大差异，受控于各自的控制器设计。
  - 在全读、混合读写等不同工作模式下，DDR 内存与真实 CXL 内存在带宽利用率上均表现出差异；例如，CXL-A 在交错读写模式下的带宽利用率提升明显，能够在内存带宽密集型应用中发挥优势。
  - 同时，带宽效率也反映了内存系统在应对多线程并发与缓存一致性检查时的响应能力，这为后续基于带宽调整内存分配策略提供了理论依据。

- **缓存与内部数据路径影响**  
![](https://pic1.imgdb.cn/item/67fa28dd88c538a9b5cc3a26.png)
  论文进一步探讨了 CPU 与内存之间的缓存层级交互，指出：
  - 对于本地 DDR 内存，由于每个 NUMA 节点内部 LLC 隔离较好，L2 缓存行仅会被写入本地 LLC slice，保证了较低的延迟和较高的缓存命中率。
  - 而当数据来源于 CXL 内存（不论是模拟还是真实），由于在 SNC 模式下打破了 LLC 的局部分离，CPU 核心在访问 CXL 内存时可以利用来自所有 SNC 节点的 LLC 资源，从而在缓存友好型场景中部分抵消较高的内存访问延迟。

这些细致的实验结果为后续提出基于内存性能实时调控策略（Caption）提供了数据支撑，也说明了在不同应用场景下，DDR 与 CXL 内存在延迟和带宽上的性能差距与互补性。


### 8. 使用 CXL 内存对应用性能的影响

![](https://pic1.imgdb.cn/item/67fa6e4188c538a9b5cc6c10.png)
论文不仅进行了微基准测试，还采用实际应用工作负载对系统进行综合评估，重点关注以下几类应用：

- **Redis（延迟敏感型应用）**  
  Redis 作为一个内存键值数据库，其响应时间在微秒级，对内存访问延迟十分敏感。实验结果显示：
  - 当全部页面分配给 CXL 内存时，在高 QPS（例如 85K）场景下，其 p99 尾延迟比纯使用 DDR 内存时高出 100% 以上；
  - 随着分配给 CXL 内存的页面比例增加，Redis 的 p99 延迟呈现逐步上升趋势，这主要由于 CXL 内存访问延迟较长所致；
  - 此外，通过应用透明页面放置（TPP）技术试图自动迁移页面来降低延迟，反而因频繁的页面迁移操作导致 p99 延迟进一步恶化（比静态分配 25% 页面给 CXL 内存时高出 174%）。
![](https://pic1.imgdb.cn/item/67fa6eb188c538a9b5cc6c62.png)
- **DLRM（内存带宽敏感型应用）**  
  DLRM 的嵌入查找及降维处理对内存带宽要求非常高：
  - 实验表明，当系统采用合理比例将页面分配给 CXL 内存后，整体内存带宽得到有效扩展，可使 DLRM 嵌入聚合阶段的吞吐率大幅提升。在 32 线程场景下，通过分配约 63% 的页面给 CXL 内存，吞吐量比全 DDR 配置高出近 88%；
  - 这表明在内存带宽成为瓶颈时，利用 CXL 内存扩展整体带宽是非常有效的办法，但前提是必须平衡延迟和带宽之间的矛盾。

- **DSB（微服务社交网络应用）**  
  针对复合型应用，实验分别测试了“发布动态”、“读取用户时间线”和“混合工作负载”等场景：
  - 对于这类延迟处于毫秒级的应用，多数延迟并不完全由数据访问内存决定，而是受到前端、逻辑处理以及 I/O 等诸多因素的综合影响；
  - 结果显示，当关键业务组件的内存页面全部采用 DDR 内存，而仅将大容量缓存和存储页面分配给 CXL 内存时，其整体 p99 延迟变化不大；
  - 说明在混合负载和多组件协作的复杂场景中，通过合理划分内存任务，CXL 内存可以在扩展带宽的同时避免对关键延迟敏感环节产生负面影响。

- **FIO（文件 I/O 基准测试）**  
![](https://pic1.imgdb.cn/item/67fa6eb188c538a9b5cc6c62.png)
  为评估操作系统页缓存性能的影响，论文利用 FIO 工具在不同 I/O 块大小下测试：
  - 在 4KB 块大小下，CXL 内存仅使 p99 延迟增加约 3%，而在 8KB 块大小下该延迟增幅达到 4.5%；
  - 当块大小进一步增大且页缓存命中率下降时，内存访问延迟差异对整体 I/O 延迟的影响相对减弱；
  - 此外，随着页缓存到存储设备的数据交换增加，CXL 内存受限于带宽的特性开始对 I/O 性能产生更大影响。

总体来看，不同应用对内存系统的敏感性各有侧重：延迟敏感型应用对 CXL 较高访问延迟非常敏感，而带宽敏感型应用则能够借助 CXL 内存扩展系统总带宽获得显著性能提升。合理的内存页面分配策略成为解决这一矛盾的关键。


### 9. 提出 Caption 策略：动态页面分配的解决方案

基于上述分析，论文提出了一种名为 **Caption** 的动态页面分配策略。其提出动机在于：  
- 当前系统中，操作系统默认的页面分配策略（例如默认将 50% 的页面分配给 CXL 内存）并不能适应各种应用的性能需求，可能会导致延迟、吞吐量等指标出现明显下降。  
- 针对不同的内存访问模式和应用负载，应该动态调节分配给 CXL 内存与 DDR 内存的页面比例，以充分发挥两者的优势，同时避免延迟敏感型应用因大量使用高延迟 CXL 内存而陷入性能瓶颈。

Caption 的基本思想是：  
- 通过实时采样 CPU 与内存子系统相关的多个性能计数器，评估当前系统的内存带宽利用、延迟以及缓存状态；
- 利用采样数据建立一个简单的性能估计模型（如线性回归模型），对系统整体内存子系统的状态进行评估；
- 当应用请求分配新页面时，根据当前系统性能指标与历史页面分配比例，采用贪心算法等调优策略自动调整新页面分配比例；
- 该策略可以动态适应运行时负载变化，实现DDR 内存与 CXL 内存之间的资源平衡，从而在保证延迟敏感型应用低延迟的同时，使带宽密集型应用获得足够的带宽扩展。

---

### 10. Caption 的设计细节

Caption 的设计主要包括以下三个模块：
![](https://pic1.imgdb.cn/item/67fa291688c538a9b5cc3a3c.png)
1. **监控模块（Monitoring Module）**  
   - 利用 Intel PCM 或其它工具周期性地采集包括 L1/L2 缓存失效率、内存读写延迟、内存带宽利用率、CPU 每周期指令数（IPC）等关键性能指标；
   - 这些计数器数据可以反映内存子系统当前的负载和瓶颈，例如当内存带宽接近饱和时，可能出现延迟激增。

2. **性能估计模块（Performance Estimation Module）**  
   - 将采集到的数据输入一个简单的线性模型或其他低复杂度模型，对系统整体内存子系统的性能进行实时估计；
   - 该模型能够基于历史数据与当前采样结果预测在不同内存页面分配比例下系统的吞吐率与延迟表现，帮助确定最佳调优方向。

3. **调优模块（Tuning Module）**  
   - 当应用请求新页面时，调优模块根据当前性能估计结果和历史页面分配策略，通过贪心算法或局部搜索方法，计算出下一批新页面中分配给 CXL 内存的比例；
   - 该模块将计算结果反馈给操作系统的内存管理模块，调整页面分配策略，使得系统能够在平衡内存带宽与访问延迟之间达到较优状态。

从整体设计上看，Caption 是一种**与 OS 内存管理机制高度耦合的自适应调控技术**，其核心目标在于通过实时监控和调优，实现两类内存资源的最优分配，进而提升内存带宽密集型工作负载的吞吐，同时对延迟敏感型应用保持低延迟特性。


### 11. Caption 的评估与实验结果

为验证 Caption 策略的有效性，论文设计了一系列实验，分别在不同负载情况下对系统进行评测。主要评估指标包括系统整体吞吐率、 p99 延迟以及在多核、多应用并发环境中的性能表现：
![](https://pic1.imgdb.cn/item/67fa6cbc88c538a9b5cc6b31.png)
- **在带宽密集型工作负载下**，例如 DLRM 的嵌入阶段实验表明：
  - 使用 Caption 策略动态调整页面分配比例后，系统能够有效突破默认静态分配策略的限制；
  - 在某些测试中，相对于全 DDR 内存配置，Caption 能使系统吞吐量提升最高达到 24% 甚至更高，这充分证明了 CXL 内存在扩展内存总带宽方面的优势；
  - 同时，通过动态调控，Caption 能自动收敛到一个经验上的最优配置比例，适应不同硬件特性（如不同 CXL 内存设备的带宽上限）。

- **在延迟敏感型负载下**，例如 Redis 的测试：
  - Caption 策略能够在保持低延迟的前提下，尽可能利用 CXL 内存带宽；
  - 由于 Redis 对内存延迟要求较高，系统会在需要时将大部分页面分配给低延迟的 DDR 内存，从而避免因过多依赖 CXL 内存而导致 p99 延迟急剧上升；
  - 实验结果表明，通过动态调整，Caption 能有效降低延迟敏感型应用中因静态页面分配策略产生的性能损失。

- **在多 SNC 节点交互场景下**，由于访问 CXL 内存会打破 LLC 隔离，导致不同 SNC 节点间相互干扰：
  - Caption 策略在这种情况下亦能发挥作用，通过调整页面分布，尽可能降低由缓存污染导致的吞吐率下降；
  - 实验数据证明，在 4 个 SNC 节点同时运行的场景中，合理分配内存页面可以避免因大量 CXL 内存访问而使得 LLC 空间被过度争用，从而提高各节点整体性能。
![](https://pic1.imgdb.cn/item/67fa6cf888c538a9b5cc6b55.png)

总体上，Caption 策略在多种应用场景和不同硬件平台下均表现出较强的适应性和鲁棒性。实验结果充分展示了其在动态调控内存资源、提升系统整体性能方面的优势，为未来基于 CXL 的内存扩展技术在数据中心中的应用提供了一种切实可行的优化方案。
