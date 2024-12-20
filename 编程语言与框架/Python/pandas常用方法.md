## pandas的常用方法和操作

1. 数据导入导出
```python
# 读取数据
df = pd.read_csv('file.csv')
df = pd.read_excel('file.xlsx')
df = pd.read_sql(sql_query, connection)

# 导出数据
df.to_csv('output.csv', index=False)
df.to_excel('output.xlsx')
df.to_sql('table_name', connection)
```

2. 数据查看和基本信息
```python
# 查看数据
df.head()        # 查看前几行
df.tail()        # 查看后几行
df.info()        # 查看数据信息
df.describe()    # 统计描述
df.shape         # 查看维度
df.columns       # 查看列名
df.dtypes        # 查看数据类型
```

3. 数据选择和过滤
```python
# 选择列
df['column_name']              # 选择单列
df[['col1', 'col2']]          # 选择多列

# 条件过滤
df[df['age'] > 25]                    # 单条件
df[(df['age'] > 25) & (df['age'] < 30)]  # 多条件

# 索引操作
df.iloc[0:5]     # 按位置选择行
df.loc['index']  # 按标签选择行
```

4. 数据处理和清洗
```python
# 处理缺失值
df.isna()            # 检查缺失值
df.fillna(0)         # 填充缺失值
df.dropna()          # 删除缺失值

# 删除重复值
df.drop_duplicates()

# 重命名列
df.rename(columns={'old_name': 'new_name'})

# 排序
df.sort_values('column_name', ascending=True)
```

5. 数据转换和运算
```python
# 类型转换
df['column'].astype('int')

# 数学运算
df['new_col'] = df['col1'] + df['col2']

# 应用函数
df['column'].apply(lambda x: x*2)
```

6. 分组和聚合
```python
# 分组操作
grouped = df.groupby('category')
grouped.mean()
grouped.sum()
grouped.count()

# 聚合多个操作
df.groupby('category').agg({
    'col1': 'sum',
    'col2': 'mean'
})
```

7. 合并数据
```python
# 合并 DataFrame
pd.concat([df1, df2])               # 垂直合并
pd.merge(df1, df2, on='key')        # 基于键合并
df1.join(df2)                       # 基于索引合并
```

8. 透视表和交叉表
```python
# 透视表
df.pivot_table(
    values='value',
    index='row_category',
    columns='col_category',
    aggfunc='sum'
)

# 交叉表
pd.crosstab(df.category1, df.category2)
```

9. 时间序列处理
```python
# 转换为时间格式
df['date'] = pd.to_datetime(df['date'])

# 设置时间索引
df.set_index('date', inplace=True)

# 重采样
df.resample('M').mean()  # 月度平均
```

使用建议：
1. 尽量使用向量化操作，避免循环
2. 链式操作时注意是否需要复制数据
3. 大数据集操作时注意内存使用
4. 适当使用 inplace=True 可以节省内存

