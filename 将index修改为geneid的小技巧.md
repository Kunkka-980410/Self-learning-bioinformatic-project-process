以GSSE70362为例子
```python
import pandas as pd

# 最简单的方法：使用comment参数跳过所有!开头的行
df = pd.read_csv("C:/Users/Windows/Desktop/GSE70362.txt", 
                 sep="\t",           # 制表符分隔
                 comment="!",        # 跳过所有!开头的行
                 header=0,           # 第一行作为列名
                 index_col=0)        #设定索引列

print("数据形状:", df.shape)
print("前5行数据:")
print(df.head())
```
```text
数据形状: (18989, 48)
前5行数据:
              GSM1725780  GSM1725781  GSM1725782  GSM1725783  GSM1725784  \
ID_REF                                                                     
100009676_at    2.486576    2.278065    2.617855    2.537123    2.775845   
10000_at        6.537100    7.019662    6.396292    6.661124    5.802611   
10001_at        7.471507    7.147493    7.645108    7.162567    7.601648   
10002_at        2.361454    2.098876    1.956571    2.032293    2.293340   
10003_at        1.818725    1.581736    1.773815    1.753550    1.766363   

              GSM1725785  GSM1725786  GSM1725787  GSM1725788  GSM1725789  ...  \
ID_REF                                                                    ...   
100009676_at    2.124545    2.287702    2.183543    2.733389    2.779922  ...   
10000_at        6.803504    6.057730    6.229028    6.692078    6.614862  ...   
10001_at        7.753484    7.824065    7.255507    7.379318    7.075547  ...   
10002_at        1.970012    2.231394    2.122924    2.262764    2.252584  ...   
10003_at        1.954792    1.597908    1.582124    1.452610    1.781442  ...   

              GSM1725818  GSM1725819  GSM1725820  GSM1725821  GSM1725822  \
ID_REF                                                                     
100009676_at    2.682800    2.704324    2.746875    2.794175    3.181916   
10000_at        7.046522    7.202161    6.353390    6.715380    6.305463   
10001_at        6.770941    7.169068    6.716004    6.704107    7.049178   
10002_at        2.093670    2.095452    2.193893    1.939394    1.937129   
10003_at        1.704457    1.927908    1.950861    1.689158    2.102072   

              GSM1725823  GSM1725824  GSM1725825  GSM1725826  GSM1725827  
ID_REF                                                                    
100009676_at    2.715256    2.461020    3.102944    2.382145    2.466616  
10000_at        6.487424    6.817949    6.901801    5.874831    6.840859  
10001_at        7.057312    7.696225    7.776353    7.202282    7.140623  
10002_at        2.084902    2.292439    2.247077    2.198508    2.237704  
10003_at        1.536400    1.754723    1.568703    1.771764    1.556003  

[5 rows x 48 columns]
```

要将`ID_REF`改成  `Geneid`，就是df中修改index名的操作，
```python
df = pd.read_csv("C:/Users/Windows/Desktop/GSE70362.txt",
                 sep="\t",
                 comment="!",
                 header=0,
                 index_col=0)  # 直接把 ID_REF 设为索引
df.index.name = 'Geneid'
```

首先，用`df.index`来确定`ID_REF`是否为index名  
不是的话
```python
# 如果 'ID_REF' 是列而不是索引
if 'ID_REF' in df.columns:
    df.set_index('ID_REF', inplace=True)

# 改名为 'Geneid'
df.index.name = 'Geneid'
```
或者一步一步操作
```python
df.index  #查看索引
if 'ID_REF' in df.columns:
    df.set_index('ID_REF', inplace=True)  #将 `'ID_REF'`设置为index

df.reset_index(inplace=True)   #重新设置index
df.rename(columns={'ID_REF':'Geneid'},inplace =True)  #将index从'ID_REF'重命名为'Geneid'
df.set_index('Geneid',inplace =True)    # 将'Geneid'设置为index
df.index.name      #查看重新设置好的indexname
```
