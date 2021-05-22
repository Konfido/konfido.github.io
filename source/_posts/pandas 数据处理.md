---
title: pandas 数据处理
alias: [data processing]
tags: [python]
date: 6/24/2018, 10:03:14
updated: 2019-07-09 20:00:30
blogged: true
urlname: data-processing-with-pandas
---



## 数据选取 pandas.DateFrame



### 读取

```shell
>>> a = pd.read_csv('datasets/results.csv')
>>> a = a.drop([1,3],axis=0)
>>> a
         date home_team   away_team  home_score  away_score
0  2018-06-03   Andorra  Cape Verde           0           0
2  2018-06-03  Zimbabwe    Botswana           1           1
4  2018-06-04    Serbia       Chile           0           1
5  2018-06-04  Slovakia     Morocco           1           2
6  2018-06-04   Armenia     Moldova           0           0
7  2018-06-04     India       Kenya           3           0
8  2018-06-05    Russia      Turkey           1           1
9  2018-06-05   Romania     Finland           2           0
```
### 生成（转成DataFrame）

- `df = pd.DataFrame([[1,1],[2,2]])`



### 统计

- 列数：`df.shape[1]`
- 行数：`df.shape[0]`
- 相同的个数：`df['id'].value_counts()`

### 行选择

- `df.head()/df.tail()` 展示前/后5行样例
```shell
>>> a.head()
         date home_team   away_team  home_score  away_score
0  2018-06-03   Andorra  Cape Verde           0           0
2  2018-06-03  Zimbabwe    Botswana           1           1
4  2018-06-04    Serbia       Chile           0           1
5  2018-06-04  Slovakia     Morocco           1           2
6  2018-06-04   Armenia     Moldova           0           0
```
- `slicing` 切片
```shell
>>> a[1:3]
         date home_team away_team  home_score  away_score
2  2018-06-03  Zimbabwe  Botswana           1           1
4  2018-06-04    Serbia     Chile           0           1
```
- `loc[ ]` 按数据标注的index选取,选取的结果包含下界
```shell
>>> a.loc[5:6]
         date home_team away_team  home_score  away_score
5  2018-06-04  Slovakia   Morocco           1           2
6  2018-06-04   Armenia   Moldova           0           0
```
- `iloc[ ]` 按数据从0开始的实际index选取，且选取结果不包含下界。
```shell
>>> a.iloc[3:5]
         date home_team away_team  home_score  away_score
5  2018-06-04  Slovakia   Morocco           1           2
6  2018-06-04   Armenia   Moldova           0           0
```

### 列选择
- 按列名选取
```shell
>>> a[['date','home_team']]
         date home_team
0  2018-06-03   Andorra
2  2018-06-03  Zimbabwe
4  2018-06-04    Serbia
5  2018-06-04  Slovakia
6  2018-06-04   Armenia
7  2018-06-04     India
8  2018-06-05    Russia
9  2018-06-05   Romania
```

### 删除列

```python
df.drop(['B', 'C'], axis=1)
df.drop(columns=['B', 'C'])
```

### 删除行

```python
df.drop([0, 1]) 	# 默认axis=0
df.drop(index=[0, 1])
```



### 选取数据

#### 按条件选取

```python
>>> df[df['ip_address']=="213.234.238.52"]
    ip_address		port	protocal    country	anonymous	response
0   213.234.238.52	8080.0	HTTPS	    Russia	Transparent	3407 ms
```

```python
>>> a.loc[a['date']>'2018-06-04']
         date home_team away_team  home_score  away_score
8  2018-06-05    Russia    Turkey           1           1
9  2018-06-05   Romania   Finland           2           0
```
```python
>>> b = a.loc[(a['date']=='2018-06-04')&(a['home_team']=='Slovakia')]
>>> b
         date home_team away_team  home_score  away_score
5  2018-06-04  Slovakia   Morocco           1           2
```
#### 由具体值选取
- `.values`
```
>>> b['away_team'].values[0]
'Morocco'
```
- `loc[]`
```shell
>>> a.loc[4,'date']
'2018-06-04'
```

## 数据清洗

### 重复值

- 查找重复值：`data.duplicated()`
- 删除重复值：`data.drop_duplicates()`

### 空值

- Note: 在pandas中空值显示为NaN
- 空值：`data.isnull()`
- 非空值：
  - dataframe: `df.notna()`
  - float: `np.isnan()`
- 空值数量：`data['column'].isnull().value_counts()`
- 处理空值：
  - 填充：
    - 填充0：`data.fillna(0)`
    - 用字典对不同列填充：`data.fillna({'col1':0,'col2':1})`
  - 删除：
    - 删除全部为NaN的行：`data.dropna(axis=0, how='all'))`
    - 删除在列名为"age"和"sex"中有NaN的行`df.dropna(subset=["age", "sex"])`
    - 删除任何含有NaN的列：`data.dropna(axis=1, how='any')`

### 空格

- 用null替代空格
	- `df["VIN"]=df["VIN"].apply(lambda x: np.NaN if str(x).isspace() else x)`
- 去除数据间的空格：
	- `a['column'] = a['column'].map(str.strip)`
	- `a['column'] = a['column'].map(str.lstrip)`
	- `a['column'] = a['column'].map(str.rstrip)`

### 大小写

- 全部大写：`a['column'] = a['column'].map(str.upper)`
- 全部小写：`a['column'] = a['column'].map(str.lower)`
- 首字母大写：`a['column'] = a['column'].map(str.title)`

### 极值/异常值

- 检查数据情况：`data.describe().astype(np.int64).T`
- 替换异常值：`data.replace([max, min],data['col'].mean())`

### 数据格式

- 数值类型转换：`data['col1'] = data['col1'].astype(np.int64)`
- 日期格式：`data['time'] = pd.to_datetime(data['time'])`



## 数据处理

### 统计

- 字符数：`string.count(" ")`
- `pd.value_counts(x)`

### 排序

- `df.sort_values(by = "a", ascending = False)`



### 新增/赋值数据

- 新增/赋值行：`df.loc['raw'] = value`
- 新增/赋值列：`df['column'] = value`
- 单个数据
  - `df.loc[index,'column'] = value`
  - `df.insert(idx, 'column', value)`

### Column重命名

- `df.rename(columns={'col':'new_col'},inplace=True)`

### 类型转换

- Str to Float: `pd.to_numeric(df)`/`df.apply(pd.to_numeric)`

- DataFrame to List：首先使用np.array()函数把DataFrame转化为np.ndarray()，再利用tolist()函数把np.ndarray()转为list

  ```python
  data = pd.read_csv("a.csv")
  data_array = np.array(data)
  data_list = data_array.tolist()
  ```

### 运算

- 单列运算：`map()`函数，用于**Series对象**（或DataFrame对象的一列），接收函数作为或字典对象作为参数，返回经过函数或字典映射处理后的值。例：对a['text']列中的每一项进行分句：

  ```python
  a['text'] = a['text'].map(lambda x:sent_tokenize(x))
  ```

- 多列运算：`apply()`函数，用于**DataFrame对象**。col3 = col1 + col2

  ```python
  a['col3'] = a.apply(lambda x: x['col1'] + x['col2'], axis=1)
  ```

- 平均值：mean(axis=1)

  - **axis**:  along which the means are computed. `axis=0` means along index, `axis=1` means along colunms.



### 遍历列

```python
for index, row in df.iterrows():
    print(row["c1"], row["c2"])
```



### 分组 groupby

- 使用`cut()`函数

  ```python
  interval = [0, 5, 10, 15, 20]  #根据数值大小分区间
  group_name = ['A', 'B', 'C', 'D', 'E']
  data['categories'] = pd.cut(data['col2'], interval, labels = group_names)
  ```

- 使用`groupby()`，`transform()`函数
  - 分组后每组的size： `df.groupby('type').size()`
  - 按A列分组，获取**其他列**的均值：`df.groupby('A').mean()`
  - 根据`type`列将数据分组后，对组内`col`的值进行求和。
    ```python
    >>> df.groupby('type')["col"].sum()
    type
    1     12.0
    2     23.1
    3     34.2
    Name: col, dtype: float64
    ```
  - 根据`type`列将数据分组后，对组内`col`的值进行多种计算。
    ```python
    df.groupby('type')['col'].agg({'sum','size'})
    ```
  - 添加一列数据，其按类显示每行数据`col`占总数的百分比
    ```python
    a['percentage'] = a.groupby('type')['col'].transform(lambda x: x / x.sum())
    ```

### 合并 [merge](https://pandas-docs.github.io/pandas-docs-travis/user_guide/merging.html#brief-primer-on-merge-methods-relational-algebra)

- Merge on one key：`pd.merge(left,right)`

    ```python
    >>> left
        A   B key
    0  A0  B0  K0
    1  A1  B1  K1
    2  A2  B2  K2
    3  A3  B3  K3
    >>> right
        C   D key
    0  C0  D0  K0
    1  C1  D1  K1
    2  C2  D2  K2
    3  C3  D3  K3
    >>> pd.merge(left,right)
        A   B key   C   D
    0  A0  B0  K0  C0  D0
    1  A1  B1  K1  C1  D1
    2  A2  B2  K2  C2  D2
    3  A3  B3  K3  C3  D3
    ```

- Merge on two keys:

    ```python
    >>> left
        A   B key1 key2
    0  A0  B0   K0   K0
    1  A1  B1   K1   K1
    2  A2  B2   K2   K2
    3  A3  B3   K3   K3

    >>> right
        C   D key1 key3
    0  C0  D0   K0   K0
    1  C1  D1   K1   K1
    2  C2  D2   K2   K2
    3  C3  D3   K3   K4

    >>> pd.merge(left, right, on = 'key2',suffixes=['_left','_right'])	#默认合并方式how="inner"
        A   B key1_left key2   C   D key1_right
    0  A0  B0        K0   K0  C0  D0         K0
    1  A1  B1        K1   K1  C1  D1         K1
    2  A2  B2        K2   K2  C2  D2         K2
    ```

- 若合并的两个对象的列名不同，可以单独指定:
  `pd.merge(left, right, left_on='key1',right_on='key2')`

    ```shell
    >>> pd.merge(left, right, on=['key1','key2'])
        A   B key1 key2   C   D
    0  A0  B0   K0   K0  C0  D0
    1  A1  B1   K1   K1  C1  D1
    2  A2  B2   K2   K2  C2  D2
  
    >>> pd.merge(left, right, on = ['key1', 'key2'], how = 'left', indicator = True) #indicator展示合并方式
        A   B key1 key2    C    D     _merge
    0  A0  B0   K0   K0   C0   D0       both
    1  A1  B1   K1   K1   C1   D1       both
    2  A2  B2   K2   K2   C2   D2       both
    3  A3  B3   K3   K3  NaN  NaN  left_only
    ```
- right on index合并： `pd.merge(left,right,right_index=True,left_on=['key'])`

### 导出数据 csv

- `df.to_csv('data.csv')`
- `df.to_csv('data.csv',header=0,index=0) `不保留列名，索引



## 作图plot

```python
pd.plot(*args, **kwargs)
```

重要参数

```python
DataFrame.plot(x=None, y=None, kind='line', ax=None, subplots=False, sharex=None, sharey=False, layout=None,figsize=None, use_index=True, title=None, grid=None, legend=True, style=None, logx=False, logy=False, loglog=False, xticks=None, yticks=None, xlim=None, ylim=None, rot=None,xerr=None,secondary_y=False, sort_columns=False, **kwds)
```

- logx / logy：设置坐标轴刻度是否取对数，boolean
- xlim / ylim：设置坐标轴范围，2-tuple/list
- linestyle：可取：":", "--", "-.", "-"



## References

- [pandas的基本用法(七)——合并数据merge](http://noahsnail.com/2017/04/29/2017-4-29-pandas%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95(%E4%B8%83)%E2%80%94%E2%80%94%E5%90%88%E5%B9%B6%E6%95%B0%E6%8D%AEmerge/)
- [使用python进行数据清洗](http://bluewhale.cc/2016-08-21/python-data-cleaning.html)
- [Pandas分组运算（groupby）修炼](https://www.cnblogs.com/lemonbit/p/6810972.html)
- [详解pandas.DataFrame.plot()](https://blog.csdn.net/brucewong0516/article/details/80524442)
