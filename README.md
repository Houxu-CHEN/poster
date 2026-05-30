# EEG 解码模型 Poster

> 从 EEGNet 到 EEG Foundation Model 的复现与分析
- 目前最新版：v8.3

## 目录结构

```
├── tex/            ← LaTeX 源文件（所有版本）
├── pdf/            ← 编译输出的 PDF
├── references/     ← 参考文献、模板、草稿
├── CLAUDE.md
├── file-versioning-convention.md
└── README.md
```

## 版本演变

### 核心叙事

EEG 解码模型从任务专用专家模型（EEGNet → EEG Conformer）逐步走向大规模预训练基座模型（CBraMod），泛化能力持续提升，但可解释性成为新的挑战。NeuroGate 尝试将神经科学先验重新注入 Foundation Model，作为未来方向。

### v4 (poster_demo)

HTML/CSS 原型，预 LaTeX 阶段的概念验证。快速验证了海报的整体视觉方向和内容布局。


### v5

继续使用 `tikzposter`，精炼为标准的学术五段式结构：

```
Background → Methods → Results → Discussion → Future Work
```

基于 poster_draft_v5.md 的"复现项目海报"定位组织内容。

### v6

tikzposter 精简版。首次引入 `\placeholder` 命令用占位图标记待补充的图表位置，为后续逐步填充实际内容做准备。

### v7 — 重大重构 🔥

从 `tikzposter` 文档类切换到 `article` + `multicol` + 自定义 `geometry`（A0 84.1×135cm）。

**动因：** tikzposter 的 block 式布局对精细排版控制不足，切换到手写双栏方案后能自由控制标题样式、字体大小、块间距和图表的嵌入方式。

### v8

article 架构稳定后继续打磨内容。七个 placeholder 标记着待填充的图表位置。

### v8.1

内容大幅扩充（+37 行）：
- **新增 NeuroGate 章节**：CBraMod Embedding + Neuroscience Prior → gated fusion
- **强化"可解释性挑战"叙事**：泛化能力 ↑ 但 解释难度 ↑
- 为 Future Work 和 Discussion 板块补充了更具体的论据

### v8.2

针对 v8.1 做减法，精简冗余表述，使整体更紧凑。

### v8.3 — 当前最终版 ✅

微调间距和排版细节。当前采用的最终版本。

### v9 — 实验分支

重新设计了 Introduction 叙事结构：从"为什么需要脑电基座大模型？"切入，回归 poster_draft_v3 的核心问题意识。结构变化较大（如将 Background 拆分为更细的子问题），未被采用为最终版。

## 技术栈

- **LaTeX**: article + multicol + geometry（自定义 A0 海报布局）
- **字体**: Fandol（中文）+ XeLaTeX 编译
- **图表**: TikZ + booktabs

## 相关文件

- `file-versioning-convention.md` — 版本号递增规则
- `references/` — 三篇核心论文 (EEGNet, EEG Conformer, CBraMod) + 课程模板 + 草稿
