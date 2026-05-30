我会按照**复现项目海报**而不是**综述海报**来组织。

---

# 标题

**EEG解码模型的发展：从EEGNet到EEG Foundation Model的复现与分析**

副标题：

**基于 EEGNet、EEG Conformer 和 CBraMod 的比较研究**

---

# 1. 研究背景（Background）

## EEG解码的重要应用

* 脑机接口（BCI）
* 睡眠分期（Sleep Staging）
* 神经疾病辅助诊断
* 认知状态监测

---

## EEG解码面临的挑战

* 数据标注成本高
* 被试间差异显著
* 设备差异显著
* 模型泛化能力有限

---

## 项目目标

复现三个具有代表性的EEG解码模型：

```text
EEGNet
↓
EEG Conformer
↓
CBraMod
```

分析：

* 模型设计思路的演化
* 表征能力的变化
* 泛化能力的发展趋势

---

# 2. 模型复现（Methods）

## EEGNet（CNN时代）

### 核心思想

利用EEG领域知识设计轻量化卷积网络。

### 关键模块

* Temporal Convolution
* Depthwise Convolution
* Separable Convolution

### 特点

```text
局部时空特征提取
参数量小
适合小样本EEG任务
```

---

## EEG Conformer（CNN + Transformer时代）

### 核心思想

结合CNN与Transformer。

### 关键模块

* Convolution Module
* Multi-head Self-Attention

### 特点

```text
同时建模局部特征与全局依赖
增强长时程信息捕获能力
```

---

## CBraMod（Foundation Model时代）

### 核心思想

基于大规模脑电数据预训练。

### 关键模块

* EEG Tokenization
* Criss-Cross Attention
* Self-Supervised Pretraining

### 特点

```text
学习通用脑电表征
支持跨任务迁移
提升泛化能力
```

---

# 3. 复现结果（Results）

## 模型性能比较

放：

* Accuracy
* F1 Score
* Cohen's Kappa

（根据你们实际结果决定）

---

## 训练效率比较

放：

* 参数量
* 训练时间
* 推理速度

---

## 模型结构比较

| 模型            | CNN | Transformer | 预训练 |
| ------------- | --- | ----------- | --- |
| EEGNet        | ✓   | ✗           | ✗   |
| EEG Conformer | ✓   | ✓           | ✗   |
| CBraMod       | ✓   | ✓           | ✓   |

---

## 主要发现

```text
EEGNet
↓
EEG Conformer
↓
CBraMod
```

表现出：

* 更强的表征能力
* 更好的跨任务泛化能力
* 更复杂的模型结构

---

# 4. 分析与讨论（Discussion）

## 观察1：模型能力持续提升

```text
局部特征
↓
全局依赖
↓
通用表征
```

---

## 观察2：Foundation Model成为新的趋势

CBraMod通过大规模预训练：

* 利用海量无标签EEG
* 学习通用表示
* 支持多任务迁移

---

## 观察3：可解释性成为新的挑战

传统EEG分析：

```text
Alpha
Beta
Theta
Delta
```

容易与神经科学知识对应。

---

Foundation Model表征：

```text
Embedding 1
Embedding 2
...
Embedding N
```

虽然性能更强，

但其生理意义更难解释。

---

## 核心思考

```text
泛化能力 ↑

解释难度 ↑
```

随着Foundation Model的发展，

如何理解模型学到的内容，

正在成为新的研究问题。

---

# 5. 未来工作（Future Work）

## NeuroGate

目标：

```text
Foundation Model
+
Neuroscience Prior
```

---

思路：

```text
CBraMod Embedding
+
神经科学先验知识
↓
融合
↓
分类预测
```

---

研究问题：

> 是否能够在保持Foundation Model泛化能力的同时，提高模型的可解释性？

---

# 总结（Summary）

### 模型发展

```text
EEGNet
→ EEG Conformer
→ CBraMod
```

---

### 主要成果

```text
泛化能力持续提升
```

---

### 新挑战

```text
可解释性问题逐渐凸显
```

---

### 未来方向

```text
Foundation Model
+
Neuroscience Prior
```

实现：

```text
强泛化
+
强解释
```

---

### Red Team Test

**最大的优点**

这个结构完全符合老师熟悉的：

```text
Background
→ Methods
→ Results
→ Discussion
→ Future Work
```

评委不会迷路。

**最大的风险**

如果你们实际没有跑出很多结果，第三部分（Results）会显得空。到时候需要提前考虑：

* 放性能结果？
* 放结构比较？
* 放参数量比较？
* 放训练曲线？

否则会出现“Methods 很多、Results 很少”的失衡。

**我现在最建议做的事**

下一步不要继续改结构，而是列出：

> 你们最终能放进 Results 的所有图和表有哪些？

因为这会直接决定每个板块最终占多大面积。
