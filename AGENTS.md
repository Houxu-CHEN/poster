---
name: file-versioning-convention
description: 文件版本号递增规则 —— 修改时始终保留原文件，按优先级递增版本号
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 33d97608-3636-4fa5-8e3a-2c60a534ea86
---

修改任何带版本号的文件（如 `poster_v8.2.tex`）时，必须保留原始文件不动。按以下优先级创建新版本号的文件：

1. **优先递增大数字**：`poster_v8.2.tex` → `poster_v8.3.tex`
2. **如果大数字递增后的文件已存在**，则递增/添加小数字：`poster_v8.2.tex` → `poster_v8.2.1.tex`
3. **如果该小数字版本也已存在**，继续递增小数字：`poster_v8.2.1.tex` → `poster_v8.2.2.tex`

**为什么：** 防止意外覆盖，保留完整的版本历史。用户需要反复迭代 LaTeX 海报，每个快照都应当可回溯。

**如何执行：** 修改版本化文件之前，先检查目录中已有的版本，然后按上述规则确定新文件名。写入新文件，绝不覆盖原始文件。
