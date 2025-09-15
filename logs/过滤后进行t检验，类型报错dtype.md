#3️⃣log2 转换（标准化） 
```python
df_log = df3_filtered.copy() 
df_log.iloc[:, 1:] = np.log2(df_log.iloc[:, 1:] + 1) df_log
```
#4️⃣ 差异表达分析（IVDD vs Normal） 
```python
import scipy import scipy.stats from scipy.stats import ttest_ind
p_values = [] log2FC = [] 
for idx, row in df_log.iterrows(): 
  ivdd = row[['IVDD_1', 'IVDD_2', 'IVDD_3']].values 
  normal = row[['Normal_1', 'Normal_2', 'Normal_3']].values 
  stat, p = ttest_ind(ivdd, normal, equal_var=False) 
  p_values.append(p) 
  log2FC.append(np.mean(ivdd) - np.mean(normal))
# 构建结果表 
results = pd.DataFrame({ 'Geneid': df_log['Geneid'], 
'log2FC': log2FC, 
'p_value': p_values })
```
AttributeError: 'numpy.dtypes.ObjectDType' object has no attribute 'dtype'

你的代码逻辑是完全正确的，但这个报错：

AttributeError: 'numpy.dtypes.ObjectDType' object has no attribute 'dtype'

说明在某一行的 row[['IVDD_1', 'IVDD_2', 'IVDD_3']] 或 row[['Normal_1', 'Normal_2', 'Normal_3']] 中，
数据类型不是数值型，而是 object 类型（比如字符串），导致 ttest_ind() 无法处理。

✅ 快速解决方案：强制转换为数值型
你只需要在提取表达值时加上 .astype(float)：
```python
p_values = []
log2FC = []

for idx, row in df_log.iterrows():
    try:
        ivdd = row[['IVDD_1', 'IVDD_2', 'IVDD_3']].astype(float).values
        normal = row[['Normal_1', 'Normal_2', 'Normal_3']].astype(float).values
        stat, p = ttest_ind(ivdd, normal, equal_var=False)
        p_values.append(p)
        log2FC.append(np.mean(ivdd) - np.mean(normal))
    except Exception as e:
        p_values.append(np.nan)
        log2FC.append(np.nan)
        print(f"Error at row {idx}: {e}")
```

🧠 为什么会出现这种错误？
虽然你已经对 df3_filtered 做了 np.log2() 转换，但如果原始数据中某些值是字符串（比如 '5.248896' 而不是 5.248896），
那么 np.log2() 会保留 object 类型，导致后续无法做数值运算。

你可以提前统一转换整个表达矩阵：

```python
df_log.iloc[:, 1:] = df_log.iloc[:, 1:].apply(pd.to_numeric, errors='coerce')
```
这一步会把所有表达值列变成 float64，无法转换的值变成 NaN，确保后续分析稳定。

✅ 最安全的做法是：
```python
# 只对表达值部分做转换
df_log.iloc[:, 1:] = df_log.iloc[:, 1:].apply(pd.to_numeric, errors='coerce')

```
这一步确保：

所有表达值都是数值型

非法值变成 NaN（你可以后续过滤掉）

不影响 Geneid 或其他非表达列

🧠 总结：
|情况|	是否会引入误差
|:---|:---
|原始数据是合法数字	|❌ 不会
|有非法字符或缺失值	|✅ 可能变成 NaN


转换了非表达列	✅ 可能错位或报错
