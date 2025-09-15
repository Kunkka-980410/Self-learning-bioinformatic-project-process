```python
import pandas as pd

# 读取并处理第一个样本
df1 = pd.read_csv("C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-1.counts.txt", 
                 sep="\t", 
                 comment="#", 
                 header=0,   #header =0是根据文件元数据来定的，具体可查看《非标准
                 skiprows=1)
df1.columns = ["Geneid", "ctrl1"]
df1.set_index("Geneid", inplace=True)

# 读取并处理第二个样本
df2 = pd.read_csv("C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-2.counts.txt",  # 请替换为实际路径
                 sep="\t", 
                 comment="#", 
                 header=0,
                 skiprows=1)
df2.columns = ["Geneid", "ctrl2"]  # 注意列名改为ctrl2
df2.set_index("Geneid", inplace=True)

# 方法1：按索引合并（推荐）
merged_df = pd.concat([df1, df2], axis=1)  # axis=1表示按列合并
print("合并后的数据形状:", merged_df.shape)
print(merged_df.head())
