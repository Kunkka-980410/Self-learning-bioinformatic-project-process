df = pd.DataFrame([1,2],[3,4])
print(df)
<img width="177" height="178" alt="image" src="https://github.com/user-attachments/assets/c0d19c3a-cdb2-4c3c-a69e-626cd57d4a38" />

---
实际上，pd.DataFrame() 的构造函数期望的语法是：
```Python
pd.DataFrame(data, index, columns)
```

所以您的代码等价于：
```Python
df = pd.DataFrame(data=[1, 2], index=[3, 4])
```
# 正确输入方法
## 方式1：使用字典（最常用、最清晰）

```python
df = pd.DataFrame({'A': [1, 2], 'B': [3, 4]})
print(df)
```

输出：
```
   A  B
0  1  3
1  2  4
```

## 方法2：使用二维列表（指定data和columns）

```python
df = pd.DataFrame([[1, 2], [3, 4]], columns=['A', 'B'])
print(df)
```
输出：
```
   A  B
0  1  2
1  3  4
```

## 方式3：使用嵌套列表（您原本想实现的效果）

```python
df = pd.DataFrame([[1, 3], [2, 4]])  # 注意这里是双层括号
print(df)
```
输出：
```
   0  1
0  1  3
1  2  4
```
