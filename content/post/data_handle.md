---
title: "第一章 学会数据处理的三件套"
draft: false
author: "jiuzi"
tags: [ "Pandas", "Numpy", "matplotlib"]
categories: ["技术教程","机器学习"]
date: 2025-07-15T12:00:00+08:00 # 确保是过去的时间
lastmod: 2025-07-15T12:00:00+08:00 #修改时间
description: "学习数据科学必经之路！本文只简单掌握Numpy、Pandas和Matplotlib这三个Python数据处理核心库的基础用法。"
featured_image: "https://cdn.jsdelivr.net/gh/cyankiler/my-blog-assets@main/images/pandas.png"
---


在数据科学的广阔世界里，有三件强大的兵器是我们必须掌握的，它们就是 **Numpy、Pandas 和 Matplotlib**。掌握了它们，就相当于拿到了进入数据处理殿堂的钥匙。

## 1. Numpy：科学计算的基础
Numpy 是 "Numerical Python" 的缩写，它是Python科学计算生态系统的核心。

### NumPy是什么，为什么它如此重要？

numpy最核心的特点是：
- 强大的N维数组对象 (`ndarray`)
- 成熟的广播（Broadcasting）功能
- 丰富的线性代数、傅里叶变换等数学函数

而numpy的优点和功能则是 **“快速”**
想象一下，Python的列表（List）是一个普通的“工具箱”，什么都能放，但速度一般。而NumPy的数组（ndarray）则是一个高度专业化的“零件工厂”，它只生产和处理数字，但速度极快、效率极高。

为什么快？

- 底层是C语言：NumPy的核心运算部分是用C语言写的，摆脱了Python的解释器性能限制。
- 内存连续：它的数据在内存中是紧凑、连续存储的，读取速度远超分散存储的Python列表。
- 向量化操作 (Vectorization)：这是NumPy的“魔法”。你不需要写for循环，可以直接对整个数组进行数学运算，NumPy会在底层用优化过的C代码高效完成。

### 核心对象：ndarray (N-dimensional array)

- NumPy的一切都围绕着这个对象。它是一个多维数组，有几个重要属性：
- ndarray.shape: 数组的“形状”，一个元组，表示每个维度的大小。比如 (3, 4) 代表3行4列。
- ndarray.ndim: 数组的“维度数量”。shape元组的长度。
- ndarray.dtype: 数组中元素的“数据类型”，如 int64, float64。
- ndarray.size: 数组中元素的“总数量”。

### 必备技能
#### a. 创建数组
```
import numpy as np # np是NumPy的官方和社区标准别名

# 从Python列表创建
my_list = [1, 2, 3]
arr1 = np.array(my_list) # -> array​([1, 2, 3])

# 创建特定形状的数组
zeros_arr = np.zeros((2, 3)) # -> 创建一个2行3列的全0数组
ones_arr = np.ones((3, 2))   # -> 创建一个3行2列的全1数组

# 创建序列数组
range_arr = np.arange(0, 10, 2) # -> array([0, 2, 4, 6, 8]) (从0到10，步长为2)
linspace_arr = np.linspace(0, 1, 5) # -> array([0. , 0.25, 0.5 , 0.75, 1. ]) (从0到1，生成5个等间距的点)

# 创建随机数组
random_arr = np.random.rand(3, 3) # -> 创建一个3x3的，0到1之间的随机浮点数数组

```

#### b. 数组的数学运算（向量化）
更加方便了
```
arr = np.array([1, 2, 3])
# 不需要for循环！
arr_plus_5 = arr + 5     # -> array([6, 7, 8])
arr_times_2 = arr * 2    # -> array([2, 4, 6])
arr_squared = arr ** 2   # -> array([1, 4, 9])

arr_b = np.array([4, 5, 6])
arr_sum = arr + arr_b    # -> array([5, 7, 9])

```
#### c. 索引与切片 (Indexing & Slicing)
和Python列表类似，但功能更强大。
```
data = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

# 获取单个元素 (行, 列)
element = data[1, 2] # -> 6 (第2行，第3列)

# 切片
first_row = data[0, :]   # -> array([1, 2, 3]) (第1行，所有列)
first_col = data[:, 0]   # -> array([1, 4, 7])​ (所有行，第1列)
sub_matrix = data[:2, 1:] # -> array([[2, 3], [5, 6]]) (前2行，从第2列到最后)

```

#### d. 布尔索引 (Boolean Indexing) 
你可以用条件来筛选数组中的元素。(非常有用)
```
large_elements = data[data > 5] # -> array([6, 7, 8, 9])

```

#### e. 聚合运算 (Aggregation)
对数组进行统计。
```
data = np.array([[1, 2, 3], [4, 5, 6]])

total_sum = np.sum(data)      # -> 21 (所有元素求和)
col_sum = np.sum(data, axis=0) # -> array([5, 7, 9]) (沿列的方向求和)
# 这里的axis代表哪一个维度被压缩，也就是这一维度消失了（axis=0，那就是消失行，每一列内部的数相加，只剩下一行）
row_sum = np.sum(data[0, :]) # ->  (对第一行求和)

# 其他常用函数: np.mean(), np.std(), np.min(), np.max(), np.argmin(), np.argmax()

```
axis=0 是跨行操作（对每一列），axis=1 是跨列操作（对每一行）。

## 2. 实践项目演示
**项目：简单的学生成绩分析系统**
假设你有一批学生的成绩数据，你想用NumPy快速分析。
```
import numpy as np

# --- 1. 生成模拟数据 ---
# 假设有10名学生，5门课程（语文、数学、英语、物理、化学）
# 分数是0-100的整数
np.random.seed(42) # 设置随机种子，保证每次运行结果都一样
scores = np.random.randint(50, 101, size=(10, 5))

student_names = [f"学生_{i+1}" for i in range(10)]
subject_names​ = ["语文", "数学", "英语", "物理", "化学"]

print("--- 原始成绩单 (10行5列) ---")
print(scores)
print("-" * 30)


# --- 2. 数据分析 ---

# a. 计算每位学生的总分和平均分
# axis=1 代表沿着“课程”这个维度（跨列）进行计算
total_scores = np.sum(scores, axis=1)
average_scores = np.mean(scores, axis=1)

print("--- 每位学生的总分 ---")
for i, total in enumerate(total_scores):
    print(f"{student_names[i]}: {total}")

print("\n--- 每位学生的平均分 ---")
for i, avg in enumerate(average_scores):
    # .2f 表示保留两位小数
    print(f"{student_names[i]}: {avg:.2f}")
print("-" * 30)


# b. 计算每门课程的平均分和标准差
# axis=0 代表沿着“学生”这个维度（跨行）进行计算
subject_avg = np.mean(scores, axis=0)
subject_std = np.std(scores, axis=0)

print("--- 每门课程的平均分和标准差 ---")
for i, name in enumerate(subject_names):
    print(f"{name} - 平均分: {subject_avg[i]:.2f}, 标准差: {subject_std[i]:.2f}")
print("-" * 30)


# c. 找出总分最高的学生
top_student_index = np.argmax(total_scores) # argmax返回最大值的索引
print(f"🎉 总分最高的学生是: {student_names[top_student_index]}, 总分为: {total_scores[top_student_index]}")
print("-" * 30)


# d. 使用布尔索引，找出所有平均分高于85分的“优秀学生”
excellent_student_mask = average_scores > 85
excellent_student_names = np.array(student_names)[excellent_student_mask]
excellent_student_scores = average_scores[excellent_student_mask]

​print(f"--- 平均分高于85分的优秀学生 ---")
if excellent_student_names.size > 0:
    for name, score in zip(excellent_student_names, excellent_student_scores):
        print(f"{name}: 平均分 {score:.2f}")
else:
    print("没有平均分高于85分的学生。")
print("-" * 30)


```

## 3. 给你的挑战项目
现在轮到你了！这个项目会用到上面讲到的所有技能。  
**题目：模拟一年销售数据分析**  
**背景:**你是一家小型电商公司的销售数据分析师。你拿到了一份数据，记录了5种不同产品在过去12个月的销售额。  
**你的任务:**
1. **生成数据：**创建一个5x12的NumPy数组，代表5种产品（行）在12个月（列）的销售额。销售额是10到1000之间的随机整数。  
2. **数据分析：**  
- 计算每种产品一整年的总销售额。  
- 找出哪种产品是年度销售冠军（总销售额最高）。  
- 找出哪个月是销售额最高的月份。
- （挑战） 使用布尔索引，找出所有年度总销售额超过5000元的“爆款产品”。  
这个练习将极大地巩固你对NumPy的理解。祝你好运！  
下一节我们将学习pandas。  

**今日诗句：常将有日思无日，莫待无时思有时 ————————《警世通言》**