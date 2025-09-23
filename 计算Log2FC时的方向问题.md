这两个表达式：

`np.mean(OE) - np.mean(ctrl)`

`np.mean(ctrl) - np.mean(OE)`

在数值上是互为相反数，但在生物学意义上，只有一种方向是“标准”的 log2 fold change（log2FC）表达方式  
✅ 正确方向：处理组减去对照组
```python
log2FC = np.mean(OE) - np.mean(ctrl)

```
这是标准的 log2FC 定义，表示：

处理组相对于对照组的表达变化倍数（以 log2 为单位）

如果结果是正值 → 表示处理组上调

如果结果是负值 → 表示处理组下调  
❌ 相反方向：对照组减去处理组
```python
log2FC = np.mean(ctrl) - np.mean(OE)
```
虽然数学上没错，但它会颠倒生物学解释：

正值 → 表示对照组上调（但你其实关心的是处理组）

负值 → 表示处理组上调（但方向反了）

这会导致你在火山图、富集分析、标记显著基因时出现混乱。


|表达式	|含义	|是否推荐
|---|---|---
|`np.mean(OE) - np.mean(ctrl)`|	处理组 vs 对照组	|✅ 推荐
|`np.mean(ctrl) - np.mean(OE)`|	对照组 vs 处理组	|❌ 不推荐
