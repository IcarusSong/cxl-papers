# Breaking Barriers: Expanding GPU Memory with  Sub-Two Digit Nanosecond Latency CXL Controller

## 作者与出版物
### 作者
- **Donghyun Gouk**：来自 Panmnesia, Inc.
- **Seungkwan Kang**：来自 Computer Architecture and Memory Systems Laboratory, KAIST
- **Hanyeoreum Bae**：来自 Computer Architecture and Memory Systems Laboratory, KAIST
- **Eojin Ryu**：来自 Panmnesia, Inc.
- **Sangwon Lee**：来自 Panmnesia, Inc.
- **Dongpyung Kim**：来自 Panmnesia, Inc.
- **Junhyeok Jang**：来自 Computer Architecture and Memory Systems Laboratory, KAIST
- **Myoungsoo Jung**：来自 Panmnesia, Inc. 和 Computer Architecture and Memory Systems Laboratory, KAIST

### 出版物
HOTSTORAGE ’24, July 8–9, 2024, Santa Clara, CA, USA




## 文章背景

该研究的背景是，随着大型深度学习模型（如大语言模型LLM）的兴起，这些模型的内存需求（包括参数、训练元数据、梯度等）已经远远超出了当前高端GPU（如图形处理器）的内存容量。例如，一个拥有1000亿参数的模型就需要远超当前GPU 80GB上限的内存。这导致GPU需要频繁地与主机内存和存储进行数据交换，从而产生显著的性能开销，并增加了编程的复杂性，限制了GPU的应用。因此，研究旨在提出一种高效的GPU内存扩展技术以解决这一挑战。

### 相关工作以及局限性

现有的GPU内存扩展或数据管理方案及其局限性主要包括：

* **GPU直接存储 (GPUDirect Storage)**: 该技术允许GPU直接访问SSD（固态硬盘），利用其巨大的存储容量。
    * **局限性**: GPUDirect因为其复杂性而很少被使用。它将SSD视为块设备，需要开发者手动管理文件系统、处理内存与块访问的I/O粒度差异，并手动处理数据传输（如 `cuFileWrite`），这使得编程模型变得复杂。

* **统一虚拟内存 (Unified Virtual Memory, UVM)**: UVM提供了一个CPU和GPU共享的虚拟内存空间，能够按需自动分配和迁移数据页。
    * **局限性**: 当GPU访问的数据不在本地内存时，会触发页面错误（page fault），需要主机端的运行时软件介入处理。这个过程会引入巨大的延迟，导致性能瓶颈，尤其是在随机访问模式下，会因数据迁移粒度（页）远大于实际所需（缓存行）而加剧问题。

* **现有CXL内存扩展器原型**: 许多行业厂商（如三星、美光、SK海力士）已经开发了CXL内存扩展器的原型。基于三星和Meta的报告，这些原型的端到端往返延迟约为250纳秒。
    * **局限性**: 尽管CXL技术允许异步访问，并支持多种存储介质，但现有原型的延迟较高。此外，当前GPU本身缺乏原生的CXL逻辑和子系统来支持将CXL设备作为内存扩展。

## 论文发现以及论文贡献

**论文发现**:
论文的核心发现是，通过在硬件RTL（寄存器传输级）层面设计和实现一个专用的、基于芯片（silicon-based）的CXL控制器，可以实现亚两位数（即低于100纳秒）的往返延迟，这比现有原型报告的250纳秒延迟快3倍以上。这一发现证明了通过优化硬件堆栈，CXL可以成为一种极低延迟的GPU内存扩展方案。

**论文贡献**:
该论文的主要贡献可以总结为以下三点：

1. **设计了集成CXL的GPU架构**: 设计了GPU的CXL根端口（root port）和内部架构，使GPU设备能够直接访问内存扩展器，无需主机CPU的干预。![](https://pic1.imgdb.cn/item/685e56d558cb8da5c876b6c6.png)

2. **展示了真实的、基于芯片的CXL控制器**: 为了实现高速内存扩展，论文展示了一个低延迟CXL控制器的芯片堆栈，并将其集成到GPU硬件的RTL级别。![](https://pic1.imgdb.cn/item/685e56f858cb8da5c876b83a.png)
   
3. **提出了投机性读取和确定性存储机制**: 论文设计了两种策略来优化对后端存储介质的访问：投机性读取（Speculative Read）可以预取目标数据页，而确定性存储（Deterministic Store）则通过并发写入来隐藏写操作延迟。

## 方法策略

为了利用其低延迟CXL控制器的发现来扩展GPU内存，研究团队设计了一套完整的硬件和软件策略。

**遇到的困难**:
1. **GPU缺乏CXL支持**: 当代GPU没有内置的CXL逻辑来直接连接和管理CXL内存设备。
2. **后端介质延迟**: 即使CXL控制器延迟极低，后端连接的存储介质（如DRAM或SSD）本质上仍比GPU本地内存慢。
3. **写操作的尾延迟**: 基于NVM（非易失性存储）的SSD在执行写操作时，可能会因为内部管理活动而出现性能波动或较长的尾延迟。

**设计的方法与策略**:

1. **CXL集成GPU架构设计**:
    ![](https://pic1.imgdb.cn/item/685e573a58cb8da5c876bd53.png)
    ![](https://pic1.imgdb.cn/item/685e575b58cb8da5c876c01a.png)
    * 为了解决GPU缺乏CXL支持的问题，论文设计了一个特殊的**CXL根复合体 (CXL Root Complex)**。
    * 该复合体包含一个**主机桥 (Host Bridge)**和**多个CXL根端口 (Root Ports)**。主机桥内有一个**HDM解码器 (HDM Decoder)**，负责管理每个根端口所连接的CXL设备的物理地址空间。
    * 当GPU的计算单元发出内存请求时，该请求被路由到CXL根复合体，HDM解码器根据地址将其映射到正确的根端口，然后CXL控制器将内存请求转换为CXL flit格式并发送给目标CXL设备（DRAM或SSD）。

2. **投机性读取 (Speculative Read, SR)**:
   ![](https://pic1.imgdb.cn/item/685e577858cb8da5c876c24a.png)
    * 为了隐藏后端介质的读取延迟，该策略利用了CXL 2.0的`MemSpecRd`功能。
    * 当一个加载（load）请求到达时，控制器会先生成一个SR请求，提前将可能很快被访问的地址发送给CXL端点设备，使其能够预取数据。
    * 为了避免给端点设备带来过大负担，该机制还会通过CXL的QoS遥测功能监控设备负载（DevLoad），并动态调整SR请求的频率。

3. **确定性存储 (Deterministic Store, DS)**:
    * 为了解决写操作延迟和性能不稳定的问题（尤其对SSD），DS策略采用了一种“即发即忘”(fire-and-forget)的方法。
    * 当一个写请求（store）发出时，它会**同时写入GPU本地预留内存和后端的CXL SSD**。写请求会立即完成，从而向上层计算单元隐藏了后端SSD的实际写入延迟。
    * 如果检测到SSD写入缓慢或出现尾延迟，后续的写入数据会暂时堆积在GPU内存的预留空间中，形成一个栈，并在后台被清空至SSD，从而保证了写入操作的确定性性能。

### 实验设置与实验结果

* **实验平台**:
    * 实验并非在真实的NUMA系统上模拟。论文团队首先使用**尖端硅工艺制造了CXL控制器ASIC**。
    * 然后，他们将这个ASIC和Vortex GPU（一种基于RISC-V的开源GPGPU）的RTL设计集成到一个**基于FPGA的定制AIC**上，构建了硬件原型。
    * 最终的性能评估是在一个**精确建模该硬件原型的模拟器**上进行的。该模拟器使用了从真实硬件（包括ASIC和FPGA原型）运行中提取的参数（如执行时间、流水线统计、总线延迟）进行校准，以确保其行为的准确性。

* **效果与对比**:
    * **对比指标**: 主要的性能评估指标是**内核执行时间（Normalized Execution Time）**和**IPC（每周期指令数）**。
    * **对比方案**:
        1. `UVM`: 基准的软件统一内存方案。
        2. `CXL-Proto`: 模拟采用业界报道的250ns延迟控制器的CXL方案。
        3. `CXL-Opt`: 采用本文提出的亚两位数纳秒延迟控制器的方案。
        4. `CXL-SR`: 在`CXL-Opt`基础上应用投机性读取策略。
        5. `CXL-SSD`: 使用CXL连接的Intel Optane SSD作为扩展内存。
        6. `CXL-DS`: 在`CXL-SSD`基础上应用确定性存储策略。
    * **对比公平性与全面性**: 对比是公平的，因为所有系统都在同一个模拟器上实现。实验选取了五种具有不同访存特性（读密集型、写密集型）的GPU内核作为工作负载，保证了评估的全面性。

* **实验结果**:
    ![](https://pic1.imgdb.cn/item/685e57dc58cb8da5c876c9f8.png)
    * **解决问题效果好**: `CXL-Opt`方案显著优于传统的`UVM`方案，性能提升高达3.5倍，因为它避免了页面错误带来的高昂开销。
    * **比相关工作好**: `CXL-Opt`比模拟业界原型的`CXL-Proto`性能高出1.34倍，这得益于其优化的低延迟CXL控制器。
    * **消融实验**:
        * `CXL-SR`相较于`CXL-Opt`性能提升了1.08倍，证明了投机性读取策略能有效隐藏读延迟。
        * 对于写密集型工作负载（如Conv和VecAdd），`CXL-DS`相较于`CXL-SSD`性能提升了1.65倍，证明了确定性存储策略能有效隐藏写延迟。这些对比清晰地展示了SR和DS各自的贡献。

### 前提假设与局限性

- 论文的核心主张是其自研的CXL控制器实现了亚100纳秒的往返延迟 ，这与业界原型（约250纳秒）相比优势显著，甚至接近原生DDR内存的延迟水平。论文将此归因于“从物理层到事务层的全面优化” ，并推测竞争方案的高延迟源于复用PCIe架构 。然而，文中并未提供任何关于**如何实现**这些优化的具体技术细节。这使得其关键技术贡献成了一个难以评估的“黑箱”，读者无法判断如此显著的延迟降低是源于何种架构创新，这无疑削弱了该核心主张的可信度与可复现性。
- 在探讨GPU内存扩展方案时，本文重点回顾了GPUDirect和UVM等技术 ，但遗漏了当前业界解决大规模模型训练最主流的方案之一：通过高速GPU互联技术（如NVIDIA NVLink/NVSwitch）构建统一内存池。这类技术直接服务于多GPU系统，以极高的带宽和低延迟扩展了单一GPU的可用内存边界。忽略这一性能强大的技术路线，使得文章的比较范围似乎被局限于软件或相对较慢的I/O方案，这可能让读者质疑，论文是否为了凸显其CXL方案的优势而进行了选择性的对比。
- 本文以大规模语言模型（LLM）的巨大内存需求作为核心应用挑战 。然而，LLM不仅是内存容量受限（memory-capacity-bound）的应用，更是典型的带宽敏感型（bandwidth-sensitive）工作负载。论文的核心贡献和评估指标却高度集中于优化与衡量延迟（latency）。更重要的是，实验部分选取的 benchmarks（如BFS, SpMV等）与LLM的计算与访存模式差异巨大，并且全文未提供任何关于带宽（bandwidth）的性能数据。这种评估与动机之间的不匹配，使得论文虽然证明了其方案在低延迟方面的优势，但未能直接回应其能否有效满足LLM这类关键应用的带宽需求，从而使其方案的实际应用价值存疑。