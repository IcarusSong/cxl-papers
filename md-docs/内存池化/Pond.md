
# Pond: CXL-Based Memory Pooling Systems for Cloud Platforms
## 作者以及出版物
### 作者
**Huaicheng Li** - 弗吉尼亚理工大学（Virginia Tech）  
**Daniel S. Berger** - 微软Azure（Microsoft Azure）  
**Lisa Hsu** - 无隶属机构（Unaffiliated）  
**Daniel Ernst** - 微软Azure（Microsoft Azure）  
**Pantea Zardoshti** - 微软Azure（Microsoft Azure）  
**Stanko Novakovic** - 谷歌（Google）  
**Monish Shah** - 微软Azure（Microsoft Azure）  
**Samir Rajadnya** - 微软Azure（Microsoft Azure）  
**Scott Lee** - 微软（Microsoft）  
**Ishwar Agarwal** - 英特尔（Intel）  
**Mark D. Hill** - 威斯康星大学麦迪逊分校（University of Wisconsin-Madison）  
**Marcus Fontoura** - 石头公司（Stone Co）  
**Ricardo Bianchini** - 微软Azure（Microsoft Azure）  
### 出版物
ASPLOS ’23

所有机构均位于美国（USA）。
## Abstract
云服务商们即希望硬件能够满足性能要求，同时也要降低成本，其中内存就是影响性能和成本的关键因素。恰好内存池化可以提高DRAM利用率，从而降低成本。但是在云服务器上实现池化是个有挑战性的事。
>内存池化是指将大量的内存进行池化，形成一个内存池，CPU在内存池中动态申请内存进行使用。
本文提出了Pond，这是首个既能满足云性能目标又能显著降低DRAM成本的内存池系统。Pond基于CXL实现了对内存池中内存的Load/Store语义访问，同时又提出了两个观察：
- 跨8-16个插槽的池化足以实现大部分收益。这使得小规模池设计能够实现低访问延迟。
- 通过机器学习模型可以准确预测为虚拟机（VM）分配多少本地内存和池内存，以实现与同NUMA节点内存相近的性能。

Pond能够将DRAM成本降低7%，同时性能与同NUMA节点VM分配相比仅相差1-5%。

## Introduction
云服务商为了保证云客户虚拟机(VM)的性能，常常将VM的所有内存都预分配到与其核心相同的NUMA节点上。预分配和静态固定内存还便于使用虚拟化加速器。
>内存性能的黄金标准是访问由发出请求的核心所在的同一NUMA节点提供服务，从而实现数十纳秒的延迟。

由于DRAM的可拓展性较差且替代方案不成熟，DRAM已经成为了硬件的主要即成本。比如：
- Azure中，DRAM可占服务器成本的50%
- Meta占机架成本的40%

由于VM的内存分配策略，导致了核心在分配给VM时，该核心仍有剩余内存不会被租用，这会造成内存搁置。随着更多核心分配给VM，高达25%的DRAM会被搁置。

现有技术应对搁浅问题仍然具有挑战性，如：
- 现有池化技术（RMDA/PCIe），闲置内存可返回到内存池中供其它VM使用，但是由于技术原因，访问延迟过高，并且当VM访问池内存时，若数据未提前加载到本地，需触发页面错误，由虚拟机管理程序（Hypervisor）介入，通过软件从池中动态迁移数据，延迟进一步增加。

Pond，这是首个在公有云平台上同时实现同NUMA节点内存性能和成本竞争力的系统。Pond结合了硬件（CXL）和系统技术（支持CXL池化）。CXL的延迟还是高于NUMA节点，Pond引入了支持CXL池化的系统技术，显著降低了这种高延迟的影响。

Pond的可行性基于四项**关键洞察**：
- 8-16个插槽的池大小足以实现足够的DRAM节省。在8-16个插槽的池中，CXL会增加70-90ns的访问延迟，而在机架级池化中会增加超过180ns的延迟。
- 43%的工作负载在CXL池内存延迟增加64ns时性能损失不超过5%，但21%的工作负载性能下降超25%。这促使Pond采用小规模CXL池（8-16节点）结合机器学习预测，将延迟敏感型和非敏感型工作负载差异化分配，最终实现7%的DRAM节省且95%以上VM性能损失控制在5%以内。
- 约50%的VM访问了不到其租用内存的50%，这意味着存在大量"闲置内存"可被重新利用。Pond将池内存作为零核心虚拟NUMA（zNUMA），zNUMA 将池内存呈现为一个"有内存但无 CPU 核心"的特殊 NUMA 节点，操作系统自然地将活跃内存集中在有CPU的NUMA节点，zNUMA内存保持空闲，避免了传统统一地址空间中潜在的性能干扰。

    >传统内存池化方案（如 Kang 等人的研究）采用统一地址空间设计，会导致操作系统无差别地使用本地和池内存，造成性能敏感型应用不可避免地访问高延迟的池内存。
- Pond可以通过正确预测以下两点，以同NUMA节点性能分配CXL内存：a）VM是否对延迟敏感；b）VM的未访问内存量。
    - 对于预测错误的情况，Pond引入了一种新颖的监控系统，检测内存性能不佳并触发缓解措施，将VM迁移到仅使用同NUMA节点内存的配置。


**贡献**：
1. 首次公开大型公有云提供商的内存搁置和未访问内存特性分析。
2. 首次分析不同CXL内存池大小的有效性和延迟。
3. 首个基于CXL的完整内存池系统，适用于云部署且性能优异。
4. 数据中心规模的准确延迟和资源管理预测模型。这些模型可实现1-5%的可配置性能降级。
5. 广泛的评估验证了Pond的设计，包括zNUMA和预测模型在生产环境中的性能。分析表明，Pond通过跨16个插槽的池可以将DRAM需求减少7%，对应大型云提供商数亿美元的成本节约。


## Background
### CXL访存
最后一级缓存（LLC）对CXL内存地址的未命中会转换为CXL端口上的请求，其响应会引入缺失的缓存行（图1）。类似地，LLC写回会转换为CXL数据写入。这些操作均不涉及页面错误或DMA。
![](https://pic1.imgdb.cn/item/67eceebb0ba3d5a1d7ea10c0.png)

## Memmory stranding & workload sensitivity to memory latency
### 内存搁置
在Microsoft Azure云环境中进行采集数据，在75内采集了100个云集群中的搁置情况，

![](https://pic1.imgdb.cn/item/67ecf4c90ba3d5a1d7ea1592.png)
图2展示了在虚拟机（VM）环境中，随着计划分配的CPU核心百分比增加，搁置内存（未被有效利用的DRAM）的比例呈现上升趋势；中位数数据显示，在75% CPU利用率时搁置内存约为6%，而在85% CPU利用率时超过10%；此外，数据的变异性主要由不同VM混合引起，例如计算密集型VM即使在低CPU利用率下也可能导致高搁置内存，而误差条和异常值进一步表明，在高CPU利用率区间，个别情况下搁置内存比例甚至可达到30%，突显了资源分配不均衡与潜在优化需求。

许多VM比较小，可以适配单个插槽。在双插槽系统上，Azure的虚拟机管理程序力求将此类VM完全（核心和内存）调度到单个NUMA节点上。只有很少的情况（2%~3%的VM核1%的页面）会发生跨插槽访问。

Azure目前未实现内存池化。然而，通过分析其VM到服务器的跟踪数据，我们可以估计通过池化可以节省的DRAM量。图3展示了当VM以固定百分比（10%、30%或50%）的池DRAM调度时，池化DRAM的平均减少量。
![](https://pic1.imgdb.cn/item/67ecf9700ba3d5a1d7eaf524.png)

### Azure中VM的内存使用情况
总体而言，我们发现虽然VM内存使用情况因集群而异，但所有集群中都有相当一部分VM存在未访问内存。第50百分位数为50%的未访问内存。

从这一分析中，我们得出以下关键观察和对Pond的启示：
- VM内存使用情况差异很大。
- 在未访问内存最少的集群中，仍有超过50%的VM有超过20%的未访问内存。因此，有大量未访问内存可以无性能损失地解聚到池中。
- 挑战在于（1）预测VM可能有多少未访问内存；（2）将VM的访问限制在本地内存中。Pond解决了这两个问题。

### 工作负载对内存延迟的敏感性
图4和图5显示了两种场景下工作负载相对于NUMA本地性能的降速。

![](https://pic1.imgdb.cn/item/67ecfb1d0ba3d5a1d7eaf5fb.png)

- 红色柱状图（Remote Memory） ：
    - 表示在所有内存均为远程内存的情况下，各工作负载的性能下降情况。
    - 多数工作负载的性能下降显著，尤其是某些特定任务（如GAPBS中的bc, bfs, cc, pr, sssp, tc等），性能下降高达80%以上。
    - 这表明这些工作负载对内存延迟非常敏感。

- 蓝色柱状图（Mixed Configuration） ：
    - 表示在混合配置下（部分内存为本地，部分为远程）的性能下降情况。
    - 相较于红色柱状图，蓝色柱状图的性能下降通常较低，但仍然存在明显的性能损失。
    - 例如，GAPBS中的某些任务在混合配置下的性能下降约为40%-60%，远低于完全远程内存的情况。

![](https://pic1.imgdb.cn/item/67ecfdcc0ba3d5a1d7eaf85c.png)
图5展示了在CXL架构下，不同远程内存延迟条件下的性能下降累积分布函数（CDF）。横轴表示性能下降百分比，纵轴表示累积概率。蓝色曲线代表142ns延迟（增加182%），红色曲线代表255ns延迟（增加222%）。

- 低延迟（142ns，蓝色曲线） ：
    - 大多数工作负载的性能下降较小，CDF曲线在低性能下降区间（<5%）上升平缓。
    - 高性能下降的工作负载比例较低。
- 高延迟（255ns，红色曲线） ：
    - 在高性能下降区间（>50%），红色曲线明显高于蓝色曲线，表明更多工作负载受高延迟影响显著。
    - 存在异常值：3个工作负载性能下降超过100%，最大达124%。
- 延迟差异的影响 ：
    - 两种延迟条件对性能下降较小的工作负载影响相近。
    - 高延迟加剧了性能下降较大的工作负载的性能损失。



某些计算密集型或对延迟敏感的工作负载（如图算法、分布式计算框架等）在高延迟环境下表现尤为脆弱，性能下降幅度巨大。此外，混合配置虽然可以缓解部分性能损失，但并不能完全消除延迟带来的负面影响。这一分析对于优化内存架构和调度策略具有重要参考价值。

## Pond设计
|设计目标|实现手段|
|---|---|
|性能对标NUMA本地DRAM|通过 CXL 低延迟访问（70-90ns）和小规模池（8-16 插槽）实现。|
|兼容虚拟化加速技术|静态预分配内存，避免动态分页带来的性能损失|
|兼容不透明的VM和客户操作系统/应用程序|轻量级硬件计数器遥测|
|低主机资源开销|轻量级硬件计数器遥测|


### 硬件层
Pond采用所有权模型，显示分配核权限控制管理池内存。Pond使用EMC（External memory controller）ASIC通过多个运行PCIe 5.0速度的CXL端口访问多个DDR通道实现池化。

![](https://pic1.imgdb.cn/item/67efebcb0ba3d5a1d7ed5e46.png)
- EMC
    - 功能：管理池内存的分配与权限，支持多主机通过CXL端口访问。
    - 提供多个CXL端口，对每个主机表现为SLD，在CXL3.0中被标准化为MHD。
    - 内存切片：池内存划分为1GB的内存切片，每个切片在同一时间仅属于一个主机。
    - 权限控制：通过权限表验证主机访问权限，非法访问出发内存错误。
    - 延迟优化：基于CXL3.0的MHD设计，减少交换机引入的额外延迟（对比传统交换机方案降低1/3延迟），8和16插槽的池仅比NUMA本地DRAM增加70-90ns延迟。
    ![](https://pic1.imgdb.cn/item/67efebed0ba3d5a1d7ed5e61.png)
    ![](https://pic1.imgdb.cn/item/67efeb9e0ba3d5a1d7ed5e22.png)

### 系统软件层
1. 内存动态管理
    - 分配与回收：
        - 分配：Pool Manager(PM)通过中断触发内存热插拔，将池内存切片动态分配给主机。
        - 回收：主机释放内存时，切片被标记为“未启用”，通过后台任务异步归还至池中，避免阻塞 VM 启动，避免碎片化（通过专用内存分区闲置非虚拟机内存分配）
        - 离线1GB切片的经验时间为10-100毫秒/GB。上线内存几乎瞬时完成，为微秒级/GB。
    - 权限同步：EMC维护全局权限表，主机通过管理总线与EMC通信更新权限。
2. zNUMA机制
    - 实现：将池内存暴露为无CPU的NUMA节点，利用Linux的NUMA感知内存分配策略。
    - 效果：虚拟机优先使用本地内存，未触及内存分配至zNUMA，减少池访问频率（实测池访问占比<0.5%）
3. 故障隔离
    - EMC故障：仅影响关联切片，其他池内存不受影响。
    - 主机故障：隔离后，池内存可重新分配给其他主机。

### 分布式平面
![](https://pic1.imgdb.cn/item/67f0cba50ba3d5a1d7eda16d.png)
图11显示了Pond控制平面的两个任务：（A）在VM调度期间预测内存分配；（B）QoS监控和解决。

![](https://pic1.imgdb.cn/item/67f0cc6c0ba3d5a1d7eda1cd.png)
Pond的VM调度（A）和QoS监控（B）算法依赖图13中两个预测模型

**预测模型**
- 预测模型的输入：
    - VM元数据：客户历史记录、VM类型、操作系统、地理位置等。
    - 硬件计数器：核心性能控制单元（PMU）数据（如内存绑定指标、DRAM访问延迟）
- 预测目标：
    - 延迟敏感性：判断虚拟机是否对池内存延迟敏感（若敏感则优先分配本地内存）
    - 未使用内存量（UM）：指虚拟机（VM）分配但未实际使用的内存量，用于确定池内存分配比例。
    ![](https://pic1.imgdb.cn/item/67f0d5210ba3d5a1d7eda4fc.png)

**QoS监控与动态调整**
- 监控指标：
    - 性能计数器：实时采集PMU（1s）数据（内存延迟、cach miss等）
    - 访问位扫描：定期检查虚拟机页表(30 min)，识别实际使用的内存范围。
- 触发条件：
    - 性能降级：若虚拟机实际性能下降超过预设阈值（PDM，如5%）
    - 预测偏差：未使用内存量预测过高（导致池内存被频繁访问）


## 实现
1. 硬件模拟  
   - 双插槽服务器模拟CXL池：禁用一插槽，内存作为池，延迟142ns（Intel）或25ms（AMD）。  
2. 软件扩展  
   - 虚拟机管理程序支持zNUMA，采样硬件计数器（每秒1次，开销<1ms）。  
3. 模型训练  
   - 延迟敏感性：随机森林（200+硬件特征，准确率30%池分配，FP率2%）。  
   - 未用内存：梯度提升回归（GBM），过预测率仅4%。  
4. 实验结果  
   - 性能：正确预测时，性能降幅<1%；错误预测触发迁移（50ms/GB）。  
   - 成本：16插槽池节省7% DRAM（对应数亿美金）。  
5. 生产验证  
   - 部署于Azure，zNUMA访问占比<0.5%，模型每日更新，支持百万级VM调度。

## 评估  
1. 实验设计  
   - 测试负载：158个云负载（数据库、HPC、内部服务等）。  
   - 场景模拟：  
     - 基线：NUMA本地内存（Intel 78ns，AMD 115ns）。  
     - CXL池：延迟增加182%（142ns）、222%（255ns）。  
     - zNUMA分配：预测未使用内存比例（0%-100%）。  

2. 性能结果  
   - 延迟敏感型负载：  
     - 182%延迟：21%负载性能下降>25%（如GAPBS图算法）。  
     - 正确预测时：池访问占比<0.5%，性能损失<1%。  
   - 生产负载：50% Azure内部服务降幅<1%（受益NUMA优化）。  

3. 模型准确性  
   - 延迟敏感性预测（随机森林）：  
     - 30%负载可安全分配至池，误判率（FP）仅2%。  
   - 未使用内存预测（GBM）：  
     - 过预测率4%（静态策略12%），预测25%未使用内存。  

4. 成本与扩展性  
   - 16插槽池：节省7% DRAM（222%延迟下），对应数亿美元成本。  
   - 释放速度：<1GB/s（99.99%场景），满足生产级调度需求。  

5. 生产验证  
   - 部署：集成至Azure调度器，支持实时监控与迁移。  
   - 稳定性：  
     - EMC故障仅影响关联切片，主机故障池内存可重分配。  
     - 模型每日更新，应对动态负载变化。  

**结论**：评估验证Pond在严苛云环境中平衡性能（95% VM性能损失<5%）、成本（7% DRAM节省）与鲁棒性，为首个实用的CXL内存池方案。