# 过滤低表达基因 - 更严格的策略
```python
print("过滤前的基因数量:", len(merged_df))

# 方法1: 保留在至少X个样本中表达量 > Y 的基因
min_samples = 2  # 至少在2个样本中表达
min_expression = 5  # 表达量阈值

# 计算每个基因在多少样本中表达量 > 阈值
expressed_in_samples = (merged_df > min_expression).sum(axis=1)
filtered_genes = merged_df[expressed_in_samples >= min_samples]

print("过滤后的基因数量:", len(filtered_genes))
print("过滤掉的比例: {:.1%}".format(1 - len(filtered_genes)/len(merged_df)))

# 查看过滤后的数据
print("\n过滤后的数据前5行:")
print(filtered_genes.head())
```
先过滤再对数处理（log2(counts+1))
```python
import numpy as np
import pandas as pd

# 假设 filtered_genes 是你已经过滤后的表达矩阵（DataFrame）
# 每行是一个基因，每列是一个样本，值为整数 count

# ✅ log2 转换（避免 log(0)）
log_counts = np.log2(filtered_genes + 1)

# ✅ 可选：查看转换后的前几行
print("log2 转换后的表达矩阵（前5行）:")
print(log_counts.head())
```
