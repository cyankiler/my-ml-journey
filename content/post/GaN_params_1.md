---
title: "第一章 GaN功率器件寿命预测必要参数（1）"
draft: false
math: true
author: "cyankiler"
tags: [ "Pandas", "Numpy", "matplotlib"]
categories: ["氮化镓","寿命预测"]
date: 2025-07-25T12:00:00+17:00 # 确保是过去的时间
lastmod: 2025-07-25T12:00:00+17:00 #修改时间
description: "学习半导体模块的寿命预测所必须的参数"
---

## 一些经典的标准指南(1)

### AQG324

#### 失效标准

在AQG324中，我们在第九章Lifetime testing中可以看到它引用了**IEC 60749-34:2011标准第9节**的内容，这段文字是进行**功率循环测试（Power Cycling Test）**时，如何判断一个功率模块“寿命终结”（End of Life - EOL）的核心依据。

{{< figure src="/images/AQG324_degration.png" 
        title="图 1：AQG324标准中的失效标准" 
        class="custom-figure-style-1" >}}

这张表格列出了两个关键的失效判据：

1. 正向压降的增加 (Increase of forward voltage)

参数名称：

- 对于**IGBT**：$VCE,sat$ (饱和压降)
- 对于 **MOSFET**：$VDS$ (导通压降，通常指$Rds,on * Id$)
- 对于 **二极管**: $VF$ 或 $VFSD$ (正向压降)

**失效标准：** **比初始标准值增加 +5%**，$VCE,sat$ 或 $VDS$ 的增加，在功率循环测试中通常不是因为芯片本身退化，而是**封装结构损坏**的直接信号。最常见的原因是**键合线（Bond Wire）的脱落或断裂**。

想象一下，键合线就像连接芯片和外部引脚的“桥梁”。当这座桥梁因为反复的热胀冷缩而出现裂纹甚至断开时，电流通过的路径电阻就会增大，从而导致在同样电流下，芯片两端的压降显著增加。

**因此，监测这个参数是判断键合线健康状态的有效方法。**

2. 热阻的增加 (Increase of thermal resistance)

参数名称：

- $Rth,j-c$ (结到管壳的热阻)
- $Rth,j-s$ (结到散热器的热阻)
- $Rth,j-f$ (结到法兰的热阻)
- 或者可选地，监测结温的波动$∆Tvj$

**失效标准：** **比初始标准值增加 +20%**，热阻是衡量模块散热能力的关键指标。它的增加意味着模块的散热路径出了问题。

在功率循环中，导致热阻增加的最主要原因是**芯片下方的焊料层疲劳（Solder Fatigue）**。

芯片和基板（如DBC）之间的焊料层，在反复的温度变化下会产生微小的裂纹，并且这些裂纹会不断扩展。当裂纹扩展到一定程度时，就会在芯片和基板之间形成热量无法有效传导的“空洞”，导致热阻急剧上升。

**因此，监测热阻是判断焊料层健康状态的核心手段。**

#### 寿命模型
而在AQG324的附录二中，我们可以找到寿命模型的公式：
{{< figure src="/images/AQG324_fomula.png" 
        title="图 2：经验拟合公式" 
        class="custom-figure-style-1" >}}

这些公式并不是通过纯物理第一性原理推导出来的（不像F=ma），而是通过“半经验建模”（Semi-Empirical Modeling）的方法得出的。

这里的 **$Nf$** 指的是 **“失效循环次数” (Number of cycles to failure)**。

$Nf$ 不是一个孤立的数字，它的值是根据寿命模型公式计算出来的，其大小完全取决于代入公式的**输入参数**，也就是我们讨论的应力条件，假设我们把一组参数：

- ΔTj = 80°C
- Tj,mean = 100°C
- ton = 10s

代入那个复杂的寿命模型公式（比如公式5），经过计算得到 **$Nf = 50,000$**。

这意味着：根据我们的模型预测，如果一个全新的功率模块，始终在“温差80°C、平均温度100°C、每次开10秒”这种固定节奏下工作，那么它大概能在重复 **50,000次** 这样的循环后，其内部热阻会比初始值增加20%，此时我们便认为它达到了设计的寿命终点。

**公式6**：$$N_{f}=A\cdot\Delta T_{j}^{a}\cdot e^{(\frac{E_{a}}{k_{B}\cdot T_{j,mean}})}$$

 这个模型是两种经典物理失效模型的**直接乘积组合**。

$A * ΔT_j^a$ 部分 - 源自 “Coffin-Manson定律”：该定律用于描述材料在**低周热机械疲劳**下的寿命。在功率模块中，每次温度变化 ($ΔTj$) 都会导致不同材料（芯片、焊料、基板）因热膨胀系数不同而相互挤压和拉伸，产生机械应力。这种反复的应力会导致微裂纹的产生和扩展，最终导致失效（如焊料疲劳）。

$e^(E_a / (k_B * T_j,mean))$ 部分 - 源自 “Arrhenius定律”：该定律用于描述**化学反应速率或扩散过程**与温度的关系。在功率模块中，许多退化过程（如焊料中的金属间化合物（IMC）层增厚、材料蠕变）都是热激活过程。

**公式7**：$$N_f=K\cdot\Delta T_J^{\beta_1}\cdot e^{\left(\frac{\beta_2}{T_J+273}\right)}\cdot t_{on}^{\beta_3}\cdot I^{\beta_4}\cdot V^{\beta_5}\cdot D^{\beta_6}$$

这个模型承认**仅靠温度不足以描述全部现象**，必须引入更多在实验中被观察到有显著影响的因素，进行**多变量经验修正**。

$t_on^β3$ (脉冲时间项)：实验发现，即使$ΔTj$和$Tj,mean$相同，短脉冲和长脉冲造成的失效模式也不同。短脉冲主要损坏键合线，长脉冲主要损坏焊料层。引入$ton$项来量化这种**时间依赖效应**。通过拟合不同$ton$下的实验数据，得到$β3$的值，从而让模型能够区分长短脉冲的危害。

$I^β4$ 和 $V^β5$ (电应力项)：在某些情况下，大电流本身可能除了发热外，还会引起如**电迁移**等效应，直接导致金属层退化。直接将电流$I$和电压$V$作为独立变量引入模型，以捕捉那些**纯电学应力**或电热耦合的复杂效应。

$D^β6$ (几何尺寸项)：引入键合线直径$D$这样的几何参数，使得模型**不再局限于单一特定设计**，而是可以对不同设计的产品进行寿命预测，增强了模型的泛化能力。

**总结**：公式7的原理是在公式6的物理框架上，**“打上了多个经验补丁”**，让模型更贴近复杂的实验数据，大大提高了预测精度。

**公式8**：$$n_f(\Delta T_j,T_{jm},ar,t_{on})=A\cdot\Delta T_j^\alpha\cdot ar^{\beta_1\cdot\Delta T_j+\beta_0}\cdot\left(\frac{C+t_{on}^\gamma}{C+1}\right)\cdot\exp\left(\frac{E_\alpha}{k_B\cdot T_{jm}}\right)\cdot f_\mathrm{Diode}$$

这个模型代表了经验建模的更高水平，它不再满足于各因素的简单相乘，而是试图去描述**各应力因素之间复杂的非线性交互作用**。

$ar^(β1*ΔTj+β0)$ (交互式Arrhenius项)：Arrhenius项（$ar$）的影响力可能不是固定的，而是会**受到$ΔTj$大小的影响**。例如，当$ΔTj$很大时，平均温度的升高对寿命的负面影响可能会被不成比例地放大。这个复杂的指数项 $β1*ΔTj+β0$ 正是为了描述这种**交互作用（Interaction Effect）**。它意味着平均温度的影响不再是独立的，而是与温差动态相关。

$((C+t_on^γ)/(C+1))$ (高级时间依赖项)：$ton$对寿命的影响可能不是一个简单的幂函数关系，可能在$ton$很短和很长时，其影响力的变化率是不同的（例如，涉及到材料的蠕变效应）。 这个复杂的分数形式是为了更精细地、非线性地拟合$ton$对寿命的影响，比简单的$t_on^β3$更灵活，能捕捉更细微的趋势变化。

**总结：** 公式8的原理是**承认并试图量化复杂世界中的非线性耦合效应**。它是一个高度定制化、高度拟合的产物，通常是针对某种特定的封装技术开发的，以求达到最高的预测精度。

综上所述，构建先进的GaN功率器件寿命模型，必须在传统的以热机械应力（如温差ΔTj、平均温度Tj,mean和脉冲时间ton）为主导的物理框架基础上，进一步融入对GaN特有电应力的精细量化，特别是将栅极电压应力Vgs、开关瞬态的峰值电压Vds_peak、负载电流I、开关频率f_sw以及电压变化率dv/dt等关键参数作为核心变量。这本质上是承认了GaN的寿命不仅受封装热疲劳的限制，更深刻地受到其独特半导体物理特性下多种电应力耦合作用的共同驱动。

### Navitas

Navitas公司在2018年的美国应用电力电子会议中以ACF架构的充电器为例，提出了一种寿命模型。

它所提出的问题是，半导体器件的可靠性测试通常是在实验室的**严苛条件**（极高的温度和电压）下进行的，这样可以在较短的时间（如1000小时）内观察到其失效情况。但是，在**实际应用**（如充电器）中，器件承受的温度和电压要温和得多。那么，如何用短期的、严苛的实验结果来科学地预测长期的、温和的应用寿命呢？

答案就是使用基于物理模型的**“加速因子”（Acceleration Factor）**。其基本逻辑是：**实际应用的寿命 = 实验室测得的寿命 × 加速因子**。加速因子是一个远大于1的数字，它量化了实际工况比实验工况“温和”多少倍。

文章指出，器件的寿命主要受两大应力影响：温度和电压。因此需要分别计算这两种应力带来的加速效应。

1. 温度加速因子 (Temperature Acceleration Factor, AF_temp)

- **模型**：基于经典的**阿伦尼乌斯（Arrhenius）模型**，这是描述化学反应速率与温度关系的黄金定律，同样适用于半导体的热老化过程。

- **公式**：$$AF_temp = e ^ ((Ea/k) * (1/T_application - 1/T_reliability))$$

- **解释**：这个公式的核心是**活化能 $Ea$**（navitas给出为 **0.62eV**），它代表了特定失效模式发生所需的能量。公式计算了在实际应用温度（$T_application$）和实验室可靠性测试温度（$T_reliability$）下，器件寿命的差异倍率。

2. 电压加速因子 (Voltage Acceleration Factor, AF_voltage)

- **模型**：基于**幂律（Power Law）模型**，用于描述电压应力对器件寿命的影响。

- **公式**：$$AF_voltage = (V_reliability / V_application)^n$$

- **解释**：这个公式的核心是**电压加速指数 $n$**（navitas给出为 **21**），它代表了器件寿命对电压变化的敏感程度。$n$值越大，说明电压稍微降低一点，寿命就能延长很多倍。

有了这两个加速因子之后，下一步便是计算总加速因子，由于温度和电压应力同时作用于器件，总的加速效应便是两者相乘即可。

- **公式**：$$AF_Total = AF_TEMP × AF_VOLTAGE$$

- **解释**：这一步将两种不同物理应力的影响整合为一个总的加速倍率。

最后估算最终的应用寿命，将实验室测得的器件失效时间（$TTF_reliability$）乘以总加速因子，就得到了在实际应用中的预测寿命。得到公式：

$$应用寿命 = AF_Total × TTF_reliability$$

{{< figure src="/images/navitas.png" 
        title="图 1：navitas给出的寿命模型" 
        class="custom-figure-style-1" >}}

若要利用LSTM-PINN模型并结合Navitas物理模型来预测寿命，需要两类关键的电参数时间序列数据。第一类是定义外部应力的**核心物理输入**，主要包括**工作结温（Tj）、漏源电压（Vds）**，它们是驱动PINN中物理定律（如Arrhenius和Power-Law模型）的直接变量。第二类是反映器件内部健康状态的**动态监测指标**，如**导通电阻（Rds(on)）**、**栅极阈值电压（Vth）**以及**各项漏电流（Igss, Idss）**等。在模型中，LSTM负责从这些健康指标随时间退化的模式中学习并预测剩余寿命，而PINN则确保整个预测过程严格遵守由Tj和Vds决定的物理加速法则，从而使模型的预测结果兼具数据驱动的精确性和物理规律的可靠性。



#### 引用论文
【1】ECPE Guideline AQG 324 Qualification of Power Modules for Use in Power Electronics Converter Units in Motor Vehicles
【2】R. Bayerer, T. Herrmann, T. Licht, J. Lutz and M. Feller, "Model for Power Cycling lifetime of IGBT Modules - various factors influencing lifetime," 5th International Conference on Integrated Power Electronics Systems, Nuremberg, Germany, 2008, pp. 1-6.
【3】K. Mainka, M. Thoben and O. Schilling, "Lifetime calculation for power modules, application and theory of models and counting methods," Proceedings of the 2011 14th European Conference on Power Electronics and Applications, Birmingham, UK, 2011, pp. 1-8.

**今日诗句：大都好物不坚牢，彩云易散琉璃脆**