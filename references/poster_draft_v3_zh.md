# Poster 初稿 v3 中文版

## 标题

**从任务专用脑电解码器到脑电基座大模型：EEGNet、EEG Conformer 与 CBraMod 的复现和扩展评测**

备选标题：

- **从专家模型到基座模型：脑电解码的表征学习范式演进**
- **从 EEGNet 到 CBraMod：脑电基座大模型为什么重要**
- **面向统一 EEG Benchmark 的基座模型复现：以 CBraMod 为中心**

## 核心主线

EEG 解码模型的发展不是简单的 “CNN -> Transformer -> 大模型” 线性替代，而是从**任务专用专家模型**逐步走向**可迁移预训练表征**。我们选取 EEGNet 作为深度学习进入 EEG 解码领域的代表，选取 EEG Conformer 作为引入 Transformer 建模长程依赖的代表，再以 CBraMod 代表大规模预训练 EEG foundation model。Poster 的核心问题是：**为什么在专家模型已经能在单任务上取得强性能时，我们仍然需要脑电基座大模型？**

我们的回答是：专家模型在单个任务上通常更强、更轻量、更容易调优；基座大模型的优势不在于每个单任务都超过专家模型，而在于通过大规模预训练提供跨任务、跨数据集、少标签适配和统一评测的基础。Transformer 的出现使这种范式更可行：它用全局自注意力和并行训练能力支持大规模无标注预训练，而 CNN/RNN 更偏向局部或串行建模，难以在同等规模下高效学习通用可迁移表征。

---

# Background

## 为什么需要脑电基座大模型？

EEG 解码长期面临三个核心困难：

- **标注数据少**：多数 BCI、临床、情绪、睡眠数据集被试数量有限，标签成本高。
- **跨域差异大**：EEG 信号受被试、session、设备、导联、采样率和预处理流程影响明显。
- **评测体系碎片化**：不同论文常使用不同 split、滤波、输入尺度、窗口长度、下游头和选模策略，模型之间很难公平比较。

传统任务专用模型包括 CNN、RNN、Transformer 及其变体。它们通常在特定数据集上从零训练或强监督训练，优势是结构明确、参数量小、单任务性能强；劣势是缺少跨任务复用能力，换一个任务往往要重新设计、重新调参。

基座大模型改变的是训练范式：先通过大规模 EEG 数据预训练学习通用表征，再通过 LP 或 FT 适配不同下游任务。它的目标不是替代所有专家模型，而是提供一个可复用、可比较、可扩展的 EEG 表征底座。

## 为什么要大规模预训练？

大规模预训练直接对应 EEG 领域的核心痛点：

- **标注昂贵**：临床诊断、睡眠分期、运动想象等任务都需要专家标注或严格实验设计，难以持续扩充标签。
- **个体差异大**：同一任务在不同被试、session、设备上的分布差异明显，单任务监督模型容易过拟合本地数据。
- **信号噪声强**：EEG 低信噪比、伪迹多、跨通道关系复杂，需要从大量数据中学习鲁棒统计规律。
- **下游任务碎片化**：MI、睡眠、情绪、临床检测等任务各自建模，重复开发成本高。

预训练的目标是利用海量无标注或弱标注 EEG 数据学习通用、鲁棒的脑电表征。这样下游任务只需要少量标签，通过 LP 或 FT 就能适配，降低数据和开发成本。

## 为什么从专用模型走向基座模型？

专用模型的优势是单任务性能强，但它也带来碎片化问题：每个任务都要单独设计模型、预处理和训练策略；换数据集、换设备、换被试后往往需要重新调参。基座模型的目标是建立一个统一底座，让多任务共享同一个预训练表征，从而提高跨被试、跨设备、跨任务迁移能力。

这不是简单的模型变大，而是 EEG AI 的范式变化：

```text
单任务算法：dataset/task -> train expert model -> task-specific predictor

基座模型：large EEG corpus -> pretrain representation -> LP/FT for many tasks
```

因此，专家模型和基座模型的评价标准不同：专家模型看单任务上限，基座模型看跨任务复用、少样本适配、统一评测和可扩展性。

## Transformer 和大规模预训练是什么关系？

Transformer 不是基座模型的唯一可能架构，但它非常适合大规模预训练：

- **全局自注意力**：能直接建模长程时间依赖和跨 patch 关系，适合 EEG 中跨时间段、跨频段、跨通道的复杂模式。
- **并行计算**：相比 RNN 的串行递推，Transformer 更适合大规模数据和多 GPU 训练。
- **通用表征接口**：token/patch 表示天然适合 masked modeling、contrastive learning、multi-task pretraining 等预训练目标。
- **可扩展性**：参数量、数据量和上下文长度都更容易扩展，符合 foundation model 的 scaling 需求。

相比之下，CNN 和 RNN 不是不能预训练，但有结构性限制：CNN 感受野偏局部，长程依赖需要堆很多层或复杂设计；RNN 存在串行计算瓶颈，训练效率和长程记忆都受限制。即便增大参数量，也不一定能获得同等规模预训练所需的效率和迁移能力。

在我们的主线中，EEG Conformer 的意义正是把 Transformer 引入 EEG 解码，使模型开始具备更适合预训练和全局表征学习的结构基础；CBraMod 则进一步将这种结构能力与大规模预训练结合。

## 发展脉络：任务专用模型 -> Transformer 增强 -> 大规模预训练

| 阶段 | 本 poster 选取的代表 | 为什么选它 | 主要价值 | 局限 |
| --- | --- | --- | --- | --- |
| 深度学习进入 EEG 解码 | **EEGNet** | 代表紧凑、领域感知的深度监督模型 | 用 temporal / spatial / separable convolution 高效学习 EEG 特征 | 仍是任务专用训练，跨任务复用弱 |
| 引入全局时序建模 | **EEG Conformer** | 代表 CNN 前端与 Transformer 注意力结合 | 在局部卷积特征之上建模更长程依赖，并提供 attention 可视化 | 仍主要依赖单任务监督训练 |
| 大规模预训练表征 | **CBraMod** | 代表 EEG foundation model | 通过预训练获得可迁移 channel-patch 表征，支持 LP/FT 多任务适配 | 单任务上不一定超过专家模型，且需要统一 benchmark 和解释性分析 |

**Poster 重点句：EEGNet 和 EEG Conformer 代表任务专用深度模型的发展，CBraMod 代表从“为每个任务训练专家模型”转向“预训练通用表征再适配下游任务”。**

---

# Methods

## 方法一：EEGNet 作为深度学习 EEG 解码代表

EEGNet 不是“CNN 阶段”等价物，而是我们选取的代表性模型：它用紧凑 CNN 结构把 EEG 领域知识融入深度学习，是 EEG 解码中非常经典的专家模型。

架构设计：

- Temporal convolution 学习频率相关的时间滤波器。
- Depthwise spatial convolution 学习通道空间模式。
- Separable convolution 降低参数量，提高紧凑性。
- 每个下游任务单独监督训练。

我们在项目中的定位：

- 作为任务专用深度模型 baseline。
- 复现若干下游任务上的 EEGNet 表现。
- 用于和 EEG Conformer、CBraMod 形成“专家模型 vs 基座模型”的对照。

**本版 poster 暂不填 EEGNet 的具体数据集和数值。**这部分由负责 EEGNet 的同学确认最终数据集、split、指标和表格口径后再补入。

## 方法二：EEG Conformer 作为 Transformer 增强专家模型

EEG Conformer 代表另一类任务专用专家模型：在卷积局部特征提取之后引入 Transformer，用 self-attention 建模 patch 之间的全局依赖。

架构设计：

- 卷积前端提取局部 EEG 特征并形成 patch embedding。
- Transformer encoder 建模长程时序依赖。
- 分类头将序列表征映射到下游标签。
- Attention map 可用于可视化分析。

我们在项目中的定位：

- 作为比 EEGNet 更复杂的任务专用模型 baseline。
- 复现若干下游任务表现。
- 进行“去掉 Transformer block”的消融，检验 attention 对单任务 EEG 解码的贡献。

**本版 poster 暂不填 EEG Conformer 的具体数据集和数值。**需要由负责 EEG Conformer 的同学确认 full model 与 w/o Transformer 消融的最终口径。

## 方法三：CBraMod 作为 EEG 基座大模型

CBraMod 是本 poster 的重点。它通过大规模预训练获得通用 EEG 表征，再迁移到不同下游任务。

下游适配方式：

- CBraMod backbone 输出 channel-patch 表征：`B x C x S x D`。
- 下游 FT 应尽量保留 channel-patch 结构：`B x C x S x D -> B x (C*S*D) -> MLP classifier`。
- LP 冻结预训练 backbone，只训练线性/浅层分类头。
- FT 同时更新 backbone 和分类头。
- LP 衡量预训练表征本身是否可读；FT 衡量最终下游适配能力。

CBraMod 复现流程：

1. 基于本地 baseline 脚本复现 CBraMod 原论文下游任务。
2. 使用 3 个 seed，与论文参考值比较。
3. 对低分任务排查 split、预处理、输入尺度和 downstream head。
4. 对下游 head 进行修正，避免错误压缩 channel-patch 表征。
5. 将 CBraMod 扩展到更多数据集，作为统一 EEG foundation benchmark 的基础。

## 扩展方法：NeuroGate 神经先验融合

基座大模型的一个重要问题是**可解释性不足**。专家模型或传统 EEG 特征通常能对应到频带能量、时域统计、复杂度、节律变化等可解释生理量；而基座模型的 embedding 虽然泛化能力强，但高维隐空间不直接暴露这些经典 EEG 描述符。因此，我们引入 NeuroGate 风格的神经先验融合，用显式 EEG 知识补充 CBraMod 表征。

基本做法：

1. 对每个 EEG segment 提取人工神经先验特征，例如时域统计、band power、频谱形状、复杂度特征等。
2. 将 CBraMod 输出的 backbone embedding 记为 `h`，将人工先验向量记为 `m`。
3. 用一个小的投影网络把 `m` 映射到与 `h` 可融合的空间。
4. 通过 gated fusion 学习一个门控权重 `g`，控制模型在多大程度上接受人工先验。
5. 融合后的表示可写作：`z = h + g * phi(m)`，再送入下游分类头。

这样做的目的不是让人工特征替代 foundation embedding，而是让模型在需要时利用可解释的 EEG 生理知识，同时避免噪声手工特征主导预训练表征。

实验解释：

- **LP**：backbone 冻结，最适合检验人工先验是否补充了 frozen embedding 中不易读出的信息。
- **FT**：backbone 和 fusion head 一起更新，用于检验神经先验在端到端适配中是否仍然有帮助。

---

# Results

## RQ1：CBraMod 下游任务能否复现？

主表使用每个数据集当前确认的最佳复现版本，同时报告 3-seed 平均值和 best seed。

| 数据集 | 最好版本 | Mean bAcc | Best bAcc | Mean kappa | Best kappa | 论文参考 | 结论 |
| --- | --- | ---: | ---: | ---: | ---: | --- | --- |
| BCIC2A | best confirmed FT | 0.6382 | 0.6709 | 0.5177 | 0.5612 | kappa 0.5138 | 对齐论文 |
| FACED | official-style FT | 0.5245 | 0.5340 | 0.4617 | 0.4718 | bAcc 0.5509 | 接近论文 |
| ISRUC-S1 | official-style FT | 0.7780 | 0.7850 | 0.7128 | 0.7373 | bAcc 0.7865 | 基本复现 |
| Physionet-MI | flatten FT | 0.5975 | 0.6181 | 0.4631 | 0.4903 | bAcc 0.6417 | 接近论文但仍有差距 |
| SEED-V | official-style FT | 0.3927 | 0.4018 | 0.2450 | 0.2592 | bAcc 0.4091 | 接近论文 |
| SHU-MI | flatten FT | 0.5851 | 0.6380 | 0.1700 | 0.2756 | bAcc 0.6370 | best seed 达到论文 |
| MDD | flatten FT | 0.9034 | 0.9674 | 0.8008 | 0.9099 | bAcc 0.9560 | best seed 超过论文 |
| TUEV | official-style FT | 0.5499 | 0.5755 | 0.5964 | 0.6343 | kappa 0.6671 | 接近但低于论文 |

主要结论：

- CBraMod 在多个原论文任务上可以接近或达到论文参考。
- 但在单个专用任务上，EEGNet、EEG Conformer 等专家模型经过针对性调参后往往更有竞争力，甚至优于 CBraMod。
- 因此 CBraMod 的意义不应只用单任务最高分衡量，而应从跨任务迁移、少标签适配、统一 benchmark 和预训练表征复用角度评价。

## RQ2：CBraMod 复现中的关键经验

我们不把工程疏忽作为 poster 的主要科学贡献，但它们能说明一个重要问题：foundation model 的下游性能高度依赖表征读出方式。

关键经验是：EEG foundation model 的下游 head 不能随意压缩空间结构。CBraMod 输出可理解为：

```text
B x C x S x D
```

其中 `C` 是通道，`S` 是 patch/segment，`D` 是隐层维度。如果下游 head 过早对通道求平均：

```text
B x C x S x D -> mean over C -> B x S x D
```

就会丢失 MI 等任务依赖的空间分布信息。更合理的方式是保留 all-patch representation：

```text
B x C x S x D -> B x (C*S*D) -> MLP classifier
```

修正后，部分任务明显恢复：

| 数据集 | 修正前 | 修正后 | 论文参考 | 解释 |
| --- | ---: | ---: | ---: | --- |
| SHU-MI | best bAcc 0.5499 | best bAcc 0.6380 | 0.6370 | 保留 channel-patch 后达到论文 |
| Physionet-MI | best bAcc 0.5594 | best bAcc 0.6181 | 0.6417 | 明显缩小差距 |
| MDD | best bAcc 0.9098 | best bAcc 0.9674 | 0.9560 | best seed 超过论文 |

可讲结论：**预训练表征不是直接等于下游性能；foundation model 还需要与 EEG 结构匹配的 downstream readout。**

## RQ3：为什么需要统一 EEG Foundation Benchmark？

目前 EEG foundation model 的结果分散在多篇论文中，不同模型、数据集和任务的协议并不统一。我们整理的参考值来自多个基座模型论文或官方结果表，包括 CBraMod、CSBrain、BrainOmni、DeeperBrain、UNI-NTFM、KAST-BAR 等来源。它们可以作为外部参考线，但不一定与我们的 split、预处理和下游头完全同口径。

这正是统一 benchmark 的必要性：

- **公平比较**：同一数据集、同一 split、同一预处理、同一指标，才能比较不同 backbone。
- **发现泛化边界**：只看少数论文任务容易高估 foundation model，跨任务 benchmark 能暴露哪些任务仍然困难。
- **区分模型能力与协议差异**：统一协议能减少因数据处理不同导致的伪差距。
- **支持后续复现**：固定数据索引、seed、指标和日志格式，能让后续小组继续扩展。

我们保留表现接近或有代表性的扩展结果，删除明显低于参考且暂时难以解释的低分项，避免 poster 变成负结果清单。

| 数据集 | 任务类型 | 3-seed avg bAcc | Best bAcc | 外部参考 | 说明 |
| --- | --- | ---: | ---: | --- | --- |
| HMC | 睡眠分期 | 0.7199 | 0.7308 | 0.7269 ± 0.0041 | 接近参考，适合作为 sleep benchmark 示例 |
| MDD | 临床抑郁检测 | 0.8853 | 0.9098 | 0.8710 ± 0.0480 | 当前 pipeline 下高于参考 |
| Siena_EEG | 癫痫相关临床 EEG | 0.7840 | 0.8118 | 0.7317 ± 0.0647 | 高于参考，但需注明协议可能不同 |
| SleepEDF_full | 睡眠分期 | 0.7391 | 0.7726 | - | 暂无可靠同口径参考，但可扩展任务覆盖 |

建设性建议：

- **统一数据与预处理**：优先使用公开、多中心、多被试数据集，例如 BCIC-2a、Sleep-EDF、TUAB/TUEV 等；统一 BIDS 或等价数据格式；统一通道命名、通道对齐、重采样、滤波和去伪迹流程；归一化只在训练集计算，避免数据泄露。
- **统一评估协议**：至少覆盖群体解码、逐被试自身训练测试、跨被试零样本迁移、留一被试零样本、留一被试微调、留一被试微调后的性能衰减/遗忘分析。
- **统一模型适配规则**：基座模型固定比较 LP 和 Full FT；统一分类头结构、学习率范围、epoch、早停策略和 seed；同时纳入 EEGNet、DeepConvNet、CTNet、EEG Conformer、SVM 等传统强基线。
- **统一指标与报告**：分类任务报告 bAcc、weighted-F1、macro-F1、kappa、AUC；回归任务报告 MSE 和 Pearson correlation；必须报告 mean ± std、best/worst seed、参数量、训练耗时和推理成本。
- **统一结果记录**：建立 dataset card、split registry、preprocessing recipe、training config 和 prediction file，保证后续模型能在同一比赛规则下复现。
- **同时评价专家模型和基座模型**：专家模型检验单任务上限，基座模型检验跨任务复用、低标签适配和统一下游扩展能力。

## RQ4：NeuroGate 是否能增强和解释预训练表征？

### LP：冻结 backbone 后，神经先验更容易体现价值

我们的 CBraMod LP matched ablation 显示，NeuroGate 在睡眠任务上提升明显：

| 数据集 | CBraMod LP bAcc | NeuroGate LP bAcc | Delta |
| --- | ---: | ---: | ---: |
| HMC | 0.6149 ± 0.0256 | 0.6922 ± 0.0172 | +0.0773 |
| ISRUC-S1 | 0.5962 ± 0.0579 | 0.6934 ± 0.0156 | +0.0972 |
| MDD | 0.8907 ± 0.0477 | 0.9062 ± 0.1101 | +0.0154 |
| Physionet-MI | 0.2719 ± 0.0176 | 0.2875 ± 0.0083 | +0.0156 |

这说明人工 EEG 先验能够补充 frozen CBraMod embedding 中不易被线性头读出的信息，尤其在睡眠分期等依赖频谱和节律结构的任务上更明显。

### FT：我们的 selected-12 扩展实验结论

FT 部分采用我们 selected-12 扩展实验的总体结论，而不是只看局部复刻的 3 个 MI/BCIC 任务。

我们的结论是：

- 在 selected 12 datasets 上，LP channel-gated fusion 相比 backbone-only 平均提升 **+0.0389 BA**。
- LP 中 gated fusion 在 **40/48** 个 dataset-backbone 单元上为正。
- CBraMod flatten-FT gated fusion 在 **9/12** 个数据集上为正，平均提升 **+0.0078 BA**。
- FT best-of-gated/logit 平均提升 **+0.0097 BA**。

可讲结论：**NeuroGate 的主证据在 LP，说明显式神经先验能补充 frozen foundation embedding；FT 中提升较小但在我们的 selected-12 结果中总体为正，可作为辅助证据。**

---

# Summary

## 主要结论

1. **EEGNet 和 EEG Conformer 是任务专用专家模型代表，不是简单的历史阶段标签。** 我们选择它们是为了展示从紧凑深度模型到 Transformer 增强模型的专家模型路线。
2. **CBraMod 代表另一种范式：大规模预训练 + 下游适配。** 它的优势不一定是单任务最高分，而是跨任务复用、少标签迁移和 benchmark 扩展。
3. **专家模型和基座模型应该互补评价。** 专家模型衡量单任务上限，基座模型衡量通用表征和迁移能力。
4. **CBraMod 的下游性能依赖合理 readout。** 保留 channel-patch 结构比过早通道池化更适合 EEG 任务，尤其是运动想象等空间结构敏感任务。
5. **NeuroGate 提供了可解释性补充。** 通过 gated fusion 注入显式 EEG 神经先验，可以解释和补充 frozen foundation embedding，LP 证据最强，FT 证据可作为辅助支持。
6. **统一 benchmark 是 EEG foundation model 发展的必要基础。** 否则不同论文中的结果难以公平比较，也难以判断差距来自模型、数据还是协议。

## 好的 EEG 基座模型应该具备什么？

- 跨被试、跨 session、跨设备鲁棒性。
- 保留时间、频率、空间/通道结构。
- 在 LP 下可读，在 FT 下可适配。
- 能覆盖 MI、睡眠、情绪、临床、认知等多类任务。
- 既能与专家模型比较单任务性能，也能展示跨任务迁移价值。
- 能通过神经先验、特征组消融或 probing 提供一定可解释性。

## 推荐 Poster 排版

第一栏：**Background**

- EEG 解码痛点：标注贵、个体差异大、噪声强、评测碎片化。
- 专家模型强在单任务，弱在复用。
- 为什么需要大规模预训练：用无标注 EEG 学通用鲁棒表征，降低下游标注成本。
- 主线：EEGNet / EEG Conformer 代表专家模型路线，CBraMod 代表预训练基座模型路线。

第二栏：**Methods**

- 三类模型结构对比图。
- EEGNet：紧凑 CNN 专家模型。
- EEG Conformer：CNN + Transformer，解释 Transformer 为什么更适合大规模预训练。
- CBraMod：预训练 backbone + LP/FT。
- NeuroGate：CBraMod embedding + neural prior gated fusion。

第三栏：**Results**

- CBraMod 原论文任务复现主表。
- 下游 readout 经验：保留 channel-patch 结构。
- 统一 benchmark 扩展：只展示接近参考或有代表性的任务。

第四栏：**Summary**

- 专家模型 vs 基座模型：不是谁完全替代谁。
- NeuroGate LP/FT 结论。
- 统一 benchmark 建设建议。

---

# References

1. Lawhern et al., **EEGNet: A Compact Convolutional Neural Network for EEG-based Brain-Computer Interfaces**, Journal of Neural Engineering, 2018.
2. Song et al., **EEG Conformer: Convolutional Transformer for EEG Decoding and Visualization**, IEEE Transactions on Neural Systems and Rehabilitation Engineering, 2023.
3. Wang et al., **CBraMod: A Criss-Cross Brain Foundation Model for EEG Decoding**, ICLR, 2025.
4. NeuroGate / NeuroKE internal reference: neural-prior gated fusion experiments under LP and FT.
5. 本地复现记录：`cbramod_reproduction_results_summary.md`、`matched_lp_ablation.md`、`matched_flatten_ft_ablation.md`。
6. 多基座模型官方参考值整理：`eeg_foundation_model_dataset_reorg.md`。

---

# 组会讨论前需要补齐的内容

- EEGNet 最终使用哪些数据集、指标和数值，由负责同学确认后再填。
- EEG Conformer full 和 w/o Transformer 的最终消融表，由负责同学确认后再填。
- 如果 poster 空间有限，CBraMod 主表可只保留 5-6 个代表任务，把完整表放到 appendix/QR code。
- 统一 benchmark 部分建议只讲代表性结果和建设方案，不展开所有低分任务。
