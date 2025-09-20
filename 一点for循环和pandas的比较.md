```python
df.columns = annotated
df.columns
list1 = []
for i in range(df.columns.size):
    column = df.columns[i]
    if "AF" in column and "III" in column:
        list1.append(i)
print(list1)
for column_index in list1:
    print(df.columns[column_index])
```
```
# 作用就是统计每一行对应包含“AF”和“III”的元素之和
# 获取第n行
# list_AF_and_III = []
# for column_index in list1:
#     total = 0
#     # 获取第m列
#     for row_index in range(df.index.size):
#         # 将第n行第m个数据累加到total
#         total = total + df.iloc[row_index, column_index]
#     list_AF_and_III.append({df.index[row_index]: total})
#     # print(df.index[row_index] + ": " + total.astype(str))
#     # print(list_AF_and_III)

# # 计算每行的平均值
# avg_list_AF_and_III = []
# for row_index in range(df.index.size):
#     row_sum = df.iloc[row_index, list1].sum()
#     avg_list_AF_and_III.append((df.index[row_index], row_sum/len(list1)))
# # print(avg_list_AF_and_III)

# # 计算第1列与平均值的差值
# x_list_AF_and_III = []
# for row_index in range(df.index.size):
#     for column_index in list1:
#         x = df.iloc[row_index, column_index] - avg_list_AF_and_III[row_index][1]
#         # print(avg_list_AF_and_III[row_index][0])
#         # print(df.columns[column_index])
#         x_list_AF_and_III.append((avg_list_AF_and_III[row_index][0], df.columns[column_index], x))
#     if row_index > 1000:
#         break
# print(x_list_AF_and_III)

# 第一步：筛选出列头含有“AF”和“III”的数据
# 输出：行数不变，但是只有所需的列==>截取出对应的df表
df_sub = df.iloc[:, list1]
df_sub

# 第二步：计算每一行的平均值
# 输出：一个list，每个元素是每行的平均值
row_means = df_sub.mean(axis=1)
row_means

# 第三步：计算每一行的每一个元素与平均值的差值，
# 输出：对应一个新的df表，列名行名不变，但是每格的数据存的是差值
diff_df = df_sub.sub(row_means, axis=0)
diff_df
```
