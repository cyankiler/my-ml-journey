---
title: "第一章 学会数据处理的三件套"
draft: false
author: "jiuzi"
tags: [ "Pandas", "Numpy", "matplotlib"]
categories: ["技术教程"]
date: 2023-10-28T12:00:00+08:00 # 确保是过去的时间
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
