---
title: "第二章 学习Pandas以及matplotlib"
draft: false
author: "jiuzi"
tags: [ "Pandas", "Numpy", "matplotlib"]
categories: ["技术教程","机器学习"]
date: 2025-07-15T12:00:00+08:00 # 确保是过去的时间
lastmod: 2025-07-15T12:00:00+08:00 #修改时间
description: "这一章延续上一章内容，继续学习另外两个工具pandas和matplotlib。"
featured_image: "https://cdn.jsdelivr.net/gh/cyankiler/my-blog-assets@main/images/pandas.png"
---

在掌握了NumPy这块坚实的 **“地基”** 之后，Pandas和Matplotlib就是建造主体框架的 **“钢梁”** 和 **“玻璃幕墙”**。

- Pandas：你的数据处理中心，一个超级强大的Excel。
- Matplotlib：你的数据可视化工作室，让数据开口说话。

## 1. 核心知识与技能

### Pandas：数据分析的瑞士军刀
如果说NumPy的核心是ndarray，那么Pandas的核心就是DataFrame。

#### 1. 核心数据结构
Series (一维)：把它想象成一个带标签的NumPy数组。它就像表格中的一列。每个元素都有一个与之关联的索引标签。
```
import pandas as pd
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
```
普通的Python列表 [10, 20, 30]：就像一个普通的药瓶，里面装着三颗药丸。你想拿到第二颗药丸，你只能说“请给我第2个位置的药丸”（在编程里是 my_list[1]）。你必须记住它的位置。
Pandas的 Series：就像一个贴了标签的“智能药盒”。
[10, 20, 30] 是药盒里的三颗药丸。
index=['a', 'b', 'c'] 是你给每个格子贴上的标签，比如“早餐吃的”、“午餐吃的”、“晚餐吃的”。
**这显然更加直观**

#### 2. 必备技能
##### a. 读取和写入数据 (I/O)
这是所有分析的起点。Pandas可以轻松读取各种格式的数据。
```
# 从CSV文件读取数据，这是最常见的操作
df = pd.read_csv('your_data.csv')

# 将DataFrame写入CSV文件
# index=False表示不把行索引写入文件
df.to_csv('output.csv', index=False)

```
##### b. 查看和检查数据 (Viewing & Inspecting)
拿到数据后，你首先要做的就是“看”它几眼。
```
df.head()      # 查看前5行
df.tail()      # 查看后5行
df.info()      # 查看每列的数据类型和非空值数量，非常有用！
df.describe()  # 对所有数字列进行快速统计（计数、均值、标准差、最小值、最大值等）
df.shape       # 查看DataFrame的形状（行数, 列数）

```
##### c. 选择和过滤数据 (Selection & Filtering)
这是Pandas最强大、最灵活的部分。
```
# 选择单列 (返回一个Series)
ages = df['Age']

# 选择多列 (返回一个新的DataFrame)
subset = df[['Name', 'Age', 'City']]

# --- 按标签选择数据 .loc ---
# 选择特定行
row_0 = df.loc[0]
# 选择特定行和列
name_of_row_0 = df.loc[0, 'Name']

# --- 按整数位置选择数据 .iloc ---
# 选择第一行
row_0_iloc = df.iloc[0]
# 选择第一行、第一列的元素
element_0_0 = df.iloc[0​, 0]

# --- 布尔过滤（最常用！）---
# 选择所有年龄大于30岁的行
adults = df[df['Age'] > 30]

# 选择所有来自'New York'且年龄大于30岁的行
ny_adults = df[(df['City'] == 'New York') & (df['Age'] > 30)]

```
##### d. 分组聚合 (.groupby())
这是Pandas的“必杀技”，用于实现“拆分-应用-合并”的操作。
# 按城市分组，然后计算每个城市的平均年龄
avg_age_by_city = df.groupby('City')['Age'].mean()

### Matplotlib：数据可视化
#### 1. 核心思想：Figure 和 Axes
- Figure：整个画布，可以把它想象成一张白纸或一个画框。
- Axes：画布上的一个子图（一个具体的图表）。一个Figure可以包含一个或多个Axes。
通常我们使用plt.subplots()来同时创建Figure和Axes，这是最推荐的做法。

#### 2. 常用图表类型
```
import matplotlib.pyplot as plt

# 准备数据
x = ['A', 'B', 'C']
y = [10, 25, 15]

# a. 条形图 (Bar Chart) - 用于比较类别
fig, ax = plt.subplots()
ax.bar(x, y)
ax.set_title('Category Comparison')
ax.set_xlabel('Category')
ax.set_ylabel('Value')
plt.show()

# b. 折线图 (Line Plot) - 用于显示趋势（尤其是时间序列）
time = [1, 2, 3, 4, 5]
sales = [100, 120, 115, 130, 145]
fig, ax = plt.subplots()
ax.plot(time, sales)
plt.show()​

# c. 散点图 (Scatter Plot) - 用于观察两个变量之间的关系
height = [160, 170, 180, 165, 175]
weight = [55, 65, 75, 60, 70]
fig, ax = plt.subplots()
ax.scatter(height, weight)
plt.show()

# d. 直方图 (Histogram) - 用于观察单个变量的分布
data = np.random.randn(1000) # 生成1000个标准正态分布的随机数
fig, ax = plt.subplots()
ax.hist(data, bins=30) # bins表示分成多少个小条
plt.show()
```

## 2. 实践项目演示
**项目：分析年度产品销售数据**
可以将上次NumPy挑战项目的概念拿过来，用Pandas和Matplotlib进行一次“升级”。
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# --- 1. 生成更真实的数据 ---
# 我们不用随机数了，直接创建DataFrame
data = {
    'Month': ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
    'Product_A_Sales': [200, 220, 250, 240, 260, 280, 270, 290, 300, 310, 320, 340],
    'Product_B_Sales': [150, 160, 170, 180, 190, 200, 210, 220, 230, 240, 250, 260],
    'Product_C_Sales': [100, 110, 90, 80, 120, 130, 140, 150, 160, 170, 180, 190]
}
sales_df =​ pd.DataFrame(data)

# --- 2. 探索数据 ---
print("--- 销售数据概览 ---")
print(sales_df.head())
sales_df.info()

# --- 3. 分析与可视化 ---

# 问题1: 哪种产品的年度总销售额最高？
# 首先计算每种产品的总销售额
total_sales = sales_df[['Product_A_Sales', 'Product_B_Sales', 'Product_C_Sales']].sum()
print("\n--- 各产品年度总销售额 ---")
print(total_sales)

# 然后用条形图可视化
fig, ax = plt.subplots(figsize=(8, 5)) # figsize可以设置画布大小
total_sales.plot(kind='bar', ax=ax, color=['skyblue', 'salmon', 'lightgreen'])
ax.set_title('Annual Total Sales by Product', fontsize=16)
ax.set_ylabel('Total Sales')
ax.tick_params(axis='x', rotation=0) # 让x轴标签保持水平
plt.show()


# 问题2: 全公司的月度总销售额趋势如何？
# 首先创建一个新列'Total_Sales'
sales_df['Total_Sales'] = sales_df['Product_A_Sales'] + sales_df['Product_B_Sales'] + sales_df['Product_C_Sales']

# 用折线图可视化月度总销售额
fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(sales_df['Month'], sales_df['Total_Sales'], marker='o', linestyle='--') # marker让每个数据点更明显
ax.set_title('Total Monthly Sales Trend', fontsize=16)
ax.set_xlabel('Month')
ax.set_ylabel('Total Sales')
ax.grid(True) # 显示网格线
plt.show()

```

## 3. 给你的挑战项目
**题目：泰坦尼克号幸存者数据初步分析**
你是一位历史学家，拿到了一份著名的泰坦尼克号乘客数据，你想通过数据分析来回答一些关于幸存情况的基本问题。
你可以从这个链接下载数据。
[text](https://github.com/datasciencedojo/datasets/blob/master/titanic.csv)
你的任务：
1. 加载数据：使用pd.read_csv(url)从上面的链接直接加载数据到一个名为titanic_df的DataFrame中。
2. 初步探索：使用.head()和.info()来熟悉数据。
3. 分析问题并可视化：  
问题A：性别与存活率：乘客的性别（Sex列）是否与存活（Survived列，1代表存活，0代表死亡）有关？  
问题B：船舱等级与存活率：不同船舱等级（Pclass列：1=一等舱, 2=二等舱, 3=三等舱）的乘客，其存活率有何不同？  
问题C：乘客年龄分布：船上乘客的年龄（Age列）分布是怎样的？  

这个项目将让你把Pandas的数据处理能力和Matplotlib的可视化能力完美地结合起来！祝你分析愉快！

**今日诗句：君以为易，其难也将至矣。————————《国语·晋语》**