## 模拟limma分析
得到了基因表达矩阵后，模拟limma分析
虽然 limma 是 R 语言中非常经典的差异表达分析工具（基于线性模型 + 贝叶斯方法），但我们可以在 Python 中模拟其核心流程，尤其是线性模型拟合和统计检验部分

**🧬limma 的核心流程简述**
1.构建表达矩阵（genes × samples）

2.构建设计矩阵（分组信息）

3.拟合线性模型（每个基因一个模型）

4.计算统计量（t 值、p 值）

5.多重检验校正（如 Benjamini-Hochberg）

🧪 Python 模拟 limma 分析流程
我们用 `statsmodels` 来模拟线性模型拟合，用 `pandas` 和 `numpy` 处理数据：

✅ 1. 准备表达矩阵和分组信息
```python
import pandas as pd
import numpy as np

# 模拟表达矩阵：1000 个基因，6 个样本
np.random.seed(42)
expr = pd.DataFrame(
    np.random.normal(loc=5, scale=1, size=(1000, 6)),
    index=[f"Gene_{i}" for i in range(1000)],
    columns=["Sample_1", "Sample_2", "Sample_3", "Sample_4", "Sample_5", "Sample_6"]
)

# 分组信息：前 3 个是 Control，后 3 个是 Treatment
group = pd.Series(["Control"] * 3 + ["Treatment"] * 3, index=expr.columns)
```

✅ 2. 构建设计矩阵
```python
import patsy

# 构建设计矩阵（one-hot 编码）
design = patsy.dmatrix("C(group)", {"group": group}, return_type="dataframe")
```

✅ 3. 拟合线性模型并计算统计量
```python
import statsmodels.api as sm

results = []

for gene in expr.index:
    y = expr.loc[gene]
    model = sm.OLS(y, design)
    fit = model.fit()
    
    # 提取 Treatment vs Control 的比较结果
    coef = fit.params[1]  # Treatment 的系数
    pval = fit.pvalues[1]
    
    results.append({"gene": gene, "logFC": coef, "pval": pval})
```

✅ 4. 多重检验校正（Benjamini-Hochberg）
```python
from statsmodels.stats.multitest import multipletests

res_df = pd.DataFrame(results)
res_df["adj_pval"] = multipletests(res_df["pval"], method="fdr_bh")[1]
```

✅ 5. 查看差异表达结果
```python
# 按显著性排序
de_genes = res_df.sort_values("adj_pval").query("adj_pval < 0.05")
print(de_genes.head())
```

📊 可选：火山图可视化
```python
import matplotlib.pyplot as plt

res_df["-log10(pval)"] = -np.log10(res_df["pval"])

plt.scatter(res_df["logFC"], res_df["-log10(pval)"], alpha=0.5)
plt.xlabel("log Fold Change")
plt.ylabel("-log10(p-value)")
plt.title("Volcano Plot")
plt.show()
```

✅ 总结一句话：
虽然 `limma` 是 R 中的工具，但它的核心思想（线性模型 + 多重检验）可以在 Python 中用 `statsmodels` 和 `pandas` 完美复刻，适合 dry lab 场景下灵活定制分析流程。

