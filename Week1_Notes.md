# STAT0023 Week 1 — Course Overview & R Revision
---

## 1. Course Overview（课程概览）

### 1.1 两大软件工具

本课程使用两种 statistical packages（统计软件包）：

| 特性 | **R** | **SAS** (Statistical Analysis System，统计分析系统) |
|---|---|---|
| 性质 | ==免费==开源软件 | **商业**付费软件 |
| 历史 | 基于 S language（S语言），现代化 | 1970s 起步，历史悠久 |
| 生态 | CRAN 上 20,000+ packages | 行业标准（制药、保险） |
| 界面 | 命令行 + RStudio | 命令行 + 点击界面 |
| 课程占比 | ==**65%**== | **35%** |

> [!tip] 为什么R占比更多？
> Programming concepts（编程概念）用 R 比 SAS 更容易展示和学习。

### 1.2 为什么用 Command-line（命令行）而非 Point-and-Click（点击界面）？

课程强调通过 **typing commands / programs**（编写命令/程序）来操作，而非 GUI。核心原因：

1. **Reproducibility（可重复性）**：命令保存在 script（脚本）文件中，可随时编辑和重新运行
2. **Documentation（文档化）**：脚本内可写 comments（注释）说明每步操作
3. **Debugging（调试）**：出错时可以精确定位问题所在
4. **Efficiency（效率）**：重复类似任务时，点击操作会变得 tedious（繁琐）且 error-prone（容易出错）

> [!important] 课程目标
> 不是教会你每一个 command，而是给你 **confidence to find appropriate new commands for yourself**（自主查找新命令的信心）。

---

## 2. R Revision（R语言复习）

### 2.1 R 基础知识清单

以下是进入 STAT0023 ==必须==已经掌握的 R 基础（来自 STAT0004 / STAT0006）：

#### 2.1.1 Data Structures（数据结构）

| 结构 | 说明 | 示例 |
|---|---|---|
| **Vector（向量）** | 一维，同类型元素 | `c(1, 2, 3)` |
| **Matrix（矩阵）** | 二维，同类型元素 | `matrix(1:6, nrow=2)` |
| **Array（数组）** | 多维，同类型元素 | `array(1:24, dim=c(2,3,4))` |
| **Data Frame（数据框）** | ==最常用==，二维，可混合类型 | `data.frame(x=1:3, y=c("a","b","c"))` |
| **Factor（因子）** | 分类变量 | `factor(c("male","female","male"))` |

变量类型包括：`numeric`（数值）、`integer`（整数）、`character`（字符）、`logical`（逻辑 TRUE/FALSE）、`factor`（因子）。

#### 2.1.2 Assignment Operator（赋值操作符）

```r
# 用 <- 将操作结果赋值给 object（对象）
x <- 5
species.data <- read.table("galapagos.dat", header=TRUE)
```

> [!note] `<-` vs `=`
> R 中推荐使用 `<-` 进行赋值，这是课程的标准写法。

#### 2.1.3 Subsetting（子集提取） — ==重点==

用 **`[]` 方括号**提取对象的部分内容，有两种方式：

**方式一：Logical condition（逻辑条件）**

```r
# 找出面积大于3000的岛屿
big.island <- (species.data$Area > 3000)  # 返回 TRUE/FALSE 向量
species.data[big.island, ]                 # 提取满足条件的所有行
```

**方式二：Numeric index（数字索引）**

```r
species.data[16, ]      # 提取第16行
species.data[, 3]       # 提取第3列
species.data[1:5, 2:4]  # 提取第1-5行、第2-4列
```

#### 2.1.4 `$` Operator（美元符号操作符）

用于访问 data frame 或 list 的 **named components（命名组件）**：

```r
species.data$Elevation   # 提取 Elevation 列
iris$Sepal.Length         # 提取 Sepal.Length 列
iris$Species              # 提取 Species 列（factor 类型）
```

#### 2.1.5 Object Inspection（对象检查函数）

| 函数 | 作用 | 中文说明 |
|---|---|---|
| `names()` | 返回对象的列名/组件名 | 查看有哪些变量 |
| `str()` | ==Structure（结构）==，显示类型和前几个值 | **最有用**，一目了然 |
| `summary()` | 各列的 summary statistics（汇总统计量） | 快速了解数据分布 |
| `class()` | 返回对象的类型 | 判断是 data.frame、numeric 等 |

```r
data(iris)
str(iris)       # 显示：150 obs. of 5 variables
summary(iris)   # 每个变量的 Min, Q1, Median, Mean, Q3, Max
```

---

### 2.2 Statistical Functions（统计函数）— ==核心==

#### 2.2.1 Summary Statistics（描述性统计量）

| 函数 | 含义 | 中文 |
|---|---|---|
| `mean()` | Mean（均值） | 平均数 |
| `var()` | Variance（方差） | 方差 |
| `sd()` | Standard deviation（标准差） | 标准差 |
| `median()` | Median（中位数） | 中位数 |
| `min()` / `max()` | 最小值 / 最大值 | — |
| `range()` | Range（范围） | 返回 min 和 max |
| `quantile()` | Quantile（分位数） | 默认返回0%, 25%, 50%, 75%, 100% |
| `table()` | Frequency table（频率表） | 计算各类别出现次数 |

```r
mean(iris$Sepal.Length)                    # 全部数据的均值
quantile(iris$Sepal.Length, prob=0.25)     # 下四分位数 (Q1)
```

#### 2.2.2 `tapply()` — ==按组计算的利器==

`tapply(X, INDEX, FUN)` 对向量 `X` 按照 `INDEX` 分组，对每组应用函数 `FUN`：

```r
# 按 Species 计算 Sepal.Length 的均值
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=mean)
#    setosa versicolor  virginica 
#     5.006      5.936      6.588 

# 按 Species 计算方差，并保留3位小数
round(tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=var), 3)

# 按 Species 计算下四分位数
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=quantile, prob=0.25)
```

> [!tip] `tapply` 的本质
> "t" 代表 "table"，它对一个向量按因子分组后逐组施加函数——是 **split-apply-combine（拆分-应用-合并）**策略的典型体现。

#### 2.2.3 Statistical Tests（统计检验）

| 函数 | 检验类型 | 中文 |
|---|---|---|
| `t.test()` | *t*-test for means（均值t检验） | 单/双样本均值检验 |
| `var.test()` | *F*-test for variances（方差F检验） | 两组方差是否相等 |
| `chisq.test()` | Chi-squared test（卡方检验） | 列联表独立性检验 |
| `fisher.test()` | Fisher's exact test（Fisher精确检验） | 小样本列联表检验 |

**Iris 数据中的实际应用**（比较 versicolor 和 virginica 的 sepal length）：

```r
# Step 1: 先检验方差是否相等
var.test(iris$Sepal.Length[iris$Species=="versicolor"],
         iris$Sepal.Length[iris$Species=="virginica"])

# Step 2: 根据方差检验结果，做 t-test（这里假设等方差）
t.test(iris$Sepal.Length[iris$Species=="versicolor"],
       iris$Sepal.Length[iris$Species=="virginica"],
       var.equal=TRUE)
```

> [!warning] 检验流程
> ==先做 `var.test()` 检验方差齐性，再决定 `t.test()` 中 `var.equal` 参数设为 TRUE 还是 FALSE。==这是标准的 two-sample *t*-test 流程。

---

### 2.3 Reading Data（数据读取）

```r
# 从文本文件读取数据
species.data <- read.table("galapagos.dat", header=TRUE)
# header=TRUE 表示第一行是列名

# 加载 R 内置数据集
data(iris)
```

#### `sink()` — Output Diversion（输出重定向）

```r
sink("Galapagos_Results.txt")   # 开始：所有输出写入文件
# ... 中间的 print/cat 输出都会写入文件 ...
sink()                           # 结束：恢复输出到 console
```

#### `cat()` 与 `print()` 的区别

- `cat()` — 输出纯文本，适合格式化标题/分隔符，==不自动换行==（需手动加 `\n`）
- `print()` — 输出 R 对象，保留格式信息

```r
cat("\nMean sepal lengths:\n")    # 输出标题文字
cat("--------------------\n")
print(mean(iris$Sepal.Length))    # 输出计算结果
```

---

### 2.4 Graphics（图形）— ==本周重点==

#### 2.4.1 基础图形函数

| 函数 | 图形类型 | 中文 | 适用场景 |
|---|---|---|---|
| `plot()` | Scatterplot（散点图） | 散点图 | 两个连续变量的关系 |
| `pairs()` | Scatterplot matrix（散点图矩阵） | 配对散点图 | 多变量间的两两关系 |
| `boxplot()` | Boxplot（箱线图） | 箱线图 | 比较不同组的分布 |
| `hist()` | Histogram（直方图） | 直方图 | 单变量的分布形状 |
| `barplot()` | Bar chart（条形图） | 条形图 | 分类数据的频率 |
| `density()` | Density plot（密度图） | 核密度图 | 平滑的分布估计 |

#### 2.4.2 Plot Customisation（图形自定义参数）— ==必须掌握==

```r
plot(x, y,
     type = "p",             # "p"=点, "l"=线, "b"=两者, "n"=空
     xlab = "X轴标签",       # x-axis label
     ylab = "Y轴标签",       # y-axis label
     main = "图标题",         # main title
     col  = "blue",          # colour（颜色）
     pch  = 15,              # plotting character（点的形状，0-25）
     cex  = 1.2,             # character expansion（点的大小，1为默认）
     lty  = 2,               # line type（线型，1=实线,2=虚线）
     lwd  = 2                # line width（线宽）
)
```

**常用 `pch` 值**：0=方形, 1=圆形, 2=三角, 15=实心方, 16=实心圆, 17=实心三角

#### 2.4.3 Boxplot with Formula（公式语法的箱线图）

```r
boxplot(Sepal.Length ~ Species, data=iris,
        xlab="Species", 
        ylab="Sepal length (cm)",
        main="Distributions of sepal lengths by species",
        col="salmon")
```

> [!note] Formula Syntax（公式语法）
> `y ~ x` 读作 "y as a function of x"（y关于x的函数），是 R 中指定模型/分组关系的标准写法。`Sepal.Length ~ Species` 意为 "按 Species 分组展示 Sepal.Length"。

#### 2.4.4 多组数据在同一图中 — Colour & Symbol 编码

```r
# 创建色板（colour-blind friendly）
plot.colours <- hcl.colors(3, "Dark 3")

# 绘制散点图，用颜色和符号区分 species
plot(iris$Petal.Length, iris$Sepal.Length,
     col = plot.colours[iris$Species],   # 按 species 着色
     pch = (15:17)[iris$Species],        # 按 species 选形状
     cex = 1.2,
     xlab = "Petal length (cm)",
     ylab = "Sepal length (cm)",
     main = "Sepal length vs petal length")

# 添加图例
legend("topleft", pch=15:17, col=plot.colours,
       legend=levels(iris$Species), cex=1.2,
       title="Species", title.col=grey(0.4))

# 加粗边框
box(lwd=2)
```

> [!tip] `(15:17)[iris$Species]` 的工作原理
> `iris$Species` 是 factor，其内部存储为整数 1, 2, 3。`(15:17)[iris$Species]` 等价于对每个观测值，用其 factor level 的编号（1/2/3）从向量 `c(15,16,17)` 中取对应的 pch 值。setosa→15(实心方)，versicolor→16(实心圆)，virginica→17(实心三角)。

#### 2.4.5 `ggplot2` — 分面图

```r
library(ggplot2)
ggplot(data=iris, mapping=aes(x=Petal.Length, y=Sepal.Length)) +
  geom_point() +
  facet_wrap(~ Species) +    # 按 Species 分面展示
  labs(x="Petal length (cm)", y="Sepal length (cm)",
       title="Sepal length vs petal length for three iris species") +
  theme(plot.title = element_text(hjust=0.5))
```

`facet_wrap(~ Species)` 将三个 species 的数据分别放在不同的 panel（面板）中，但保持**相同的坐标轴刻度**，方便比较。

#### 2.4.6 Saving Graphics（保存图形）

```r
# 方法一：dev.copy — 将当前屏幕图复制到文件
dev.copy(png, "IrisPlot1.png", width=8*72, height=6*72)
dev.off()   # 关闭设备，完成写入

# 方法二：dev.copy 到 PDF
dev.copy(pdf, "endemics.pdf", width=6, height=6)
dev.off()
```

> [!warning] ==一定要调用 `dev.off()`！==
> 如果忘记 `dev.off()`，文件不会正确关闭，图形文件可能损坏或为空。

#### 2.4.7 其他图形辅助函数

| 函数 | 作用 |
|---|---|
| `abline()` | 在图上叠加直线（如回归线） |
| `legend()` | 添加图例 |
| `box()` | 画图形边框 |
| `par()` | 设置图形参数 |
| `par(mfrow=c(2,2))` | 将画布分为 2×2 的网格 |
| `par(ask=TRUE)` | 每张图后暂停，等用户确认 |

---

### 2.5 Linear Regression（线性回归）— Galapagos 示例

```r
# 拟合线性回归模型
endemics.model <- lm(Endemics ~ Elevation, data=species.data)

# 查看结果
summary(endemics.model)

# 在散点图上叠加回归线
plot(species.data$Elevation, species.data$Endemics,
     xlab="Elevation (m)", ylab="No. of species",
     pch=15, col="blue")
abline(endemics.model, col="red", lty=2, lwd=2)

# 诊断图（residual plots 残差图）
par(mfrow=c(2,2))
plot(endemics.model)    # R 自动生成4张诊断图
par(mfrow=c(1,1))       # 恢复单图模式
```

`lm()` 的 4 张 diagnostic plots（诊断图）包括：
1. **Residuals vs Fitted**（残差 vs 拟合值）— 检查线性性和等方差性
2. **Normal Q-Q**（正态QQ图）— 检查残差是否正态分布
3. **Scale-Location**（标准化残差 vs 拟合值）— 检查等方差性
4. **Residuals vs Leverage**（残差 vs 杠杆值）— 识别 influential points（有影响力的点）

---

## 3. Exploratory Data Analysis, EDA（探索性数据分析）

### 3.1 EDA 的三大目标

| # | 目标 | 英文 |
|---|---|---|
| 1 | 初步了解数据集中的 **structure（结构）** | Preliminary understanding of structure |
| 2 | 发现可能的 **outliers（异常值）** 或 **data quality problems（数据质量问题）** | Look for outliers or data quality problems |
| 3 | 为后续 modelling（建模）提出合理的 **initial assumptions（初始假设）** | Suggest initial assumptions (e.g. normality, constant variance) |

### 3.2 Using Graphics Effectively（有效使用图形）— ==重要原则==

> **Key Principle（核心原则）**: 图形的 message（信息）应该 =="at a glance"（一目了然）==

三大准则：

**① Labelling（标注）**
- 必须有 **title（标题）**
- 必须有 **axis labels（坐标轴标签）**，包含 ==units of measurement（度量单位）==
- 需要时加 **legend（图例）**
- 文字大小要确保 **legible（清晰可读）**

**② Scaling（缩放）**
- 让数据尽量 **fill up the plotting region（填满绘图区域）**
- 有时需要使用 **common scales（统一刻度）**来方便比较不同数据集

**③ Colours / Symbols / Line Types（颜色/符号/线型）**
- ==**Never rely exclusively on colour**（绝不要仅依赖颜色）==
- 原因：**colour-blind（色盲/色弱）**人群无法区分，尤其 red-green（红绿色盲）最常见
- 最佳实践：同时使用不同 **colour + symbol + line type** 三重编码
- `hcl.colors()` 提供 colour-blind friendly 的调色板

---

## 4. Workshop Scripts 详解

### 4.1 Galapagos Script（加拉帕戈斯群岛脚本）

**数据背景**：Galapagos Islands（加拉帕戈斯群岛）的岛屿生物多样性数据，包含物种数量、特有种数量、面积、海拔等变量。

**脚本关键步骤**：

```r
# 1. 输出重定向
sink("Galapagos_Results.txt")

# 2. 读取数据
species.data <- read.table("galapagos.dat", header=TRUE)

# 3. 数据探索
str(species.data)         # 结构
summary(species.data)     # 汇总统计
plot(species.data)        # 散点图矩阵（快速overview）

# 4. 发现异常值
big.island <- (species.data$Area > 3000)
species.data[big.island, ]  # 打印出面积>3000的岛屿

# 5. 绑定关系可视化 + 线性回归
endemics.model <- lm(Endemics ~ Elevation, data=species.data)
abline(endemics.model, col="red", lty=2, lwd=2)

# 6. 诊断图
par(mfrow=c(2,2))
plot(endemics.model)

# 7. 恢复
sink(); par(mfrow=c(1,1))
```

### 4.2 Iris Script（鸢尾花脚本）

**数据背景**：Fisher-Anderson iris data（Fisher-Anderson 鸢尾花数据），R 内置数据集，包含 3 种 iris species（鸢尾花品种）各 50 个样本的花瓣和花萼测量值。

| 变量 | 含义 |
|---|---|
| `Sepal.Length` | Sepal length（花萼长度，cm） |
| `Sepal.Width` | Sepal width（花萼宽度，cm） |
| `Petal.Length` | Petal length（花瓣长度，cm） |
| `Petal.Width` | Petal width（花瓣宽度，cm） |
| `Species` | Species（品种）：setosa, versicolor, virginica |

**脚本关键步骤**：

```r
# 1. 加载包
library(ggplot2); library(rgl)

# 2. 数据探索
data(iris)
str(iris)
summary(iris)

# 3. 分组统计 — tapply
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=mean)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=var)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=median)

# 4. 箱线图比较
boxplot(Sepal.Length ~ Species, data=iris, col="salmon")

# 5. 统计检验
var.test(...)   # F-test 方差齐性
t.test(...)     # Two-sample t-test

# 6. ggplot2 分面散点图
ggplot(...) + geom_point() + facet_wrap(~ Species)

# 7. Base R 多组散点图（颜色+符号编码）
plot(..., col=plot.colours[iris$Species], pch=(15:17)[iris$Species])

# 8. 3D 交互散点图
rgl::plot3d(...)
```

---

## 5. R Help System（帮助系统）

| 命令 | 功能 | 中文说明 |
|---|---|---|
| `?mean` | 查看 `mean` 的帮助文档 | 已知函数名时使用 |
| `??regression` | 搜索包含 "regression" 的帮助 | 不确定函数名时使用 |
| `help(mean)` | 同 `?mean` | — |
| `help.search("regression")` | 同 `??regression` | — |
| `example(boxplot)` | 运行 `boxplot` 的示例代码 | 快速看效果 |

---

## 6. Quick Reference（速查表）

### 核心函数一览

| 类别 | 函数 |
|---|---|
| **数据读取** | `read.table()`, `data()` |
| **数据检查** | `str()`, `summary()`, `names()`, `class()`, `head()`, `tail()` |
| **描述统计** | `mean()`, `var()`, `sd()`, `median()`, `quantile()`, `range()`, `table()` |
| **分组计算** | `tapply()` |
| **统计检验** | `t.test()`, `var.test()`, `chisq.test()`, `fisher.test()` |
| **建模** | `lm()` |
| **图形** | `plot()`, `boxplot()`, `hist()`, `pairs()`, `barplot()`, `density()` |
| **图形辅助** | `legend()`, `abline()`, `box()`, `par()`, `dev.copy()`, `dev.off()` |
| **输出控制** | `cat()`, `print()`, `sink()`, `round()` |
| **帮助** | `?`, `??`, `help()`, `example()` |

### 常见 Subsetting 模式

```r
df$column              # 取某列
df[rows, cols]         # 取特定行列
df[condition, ]        # 条件筛选行
df[, "colname"]        # 按列名取列
```

---

## 7. 本周 To-Do

- [ ] 运行 `Workshop1_Galapagos.r`，理解每一行代码
- [ ] 运行 `Workshop1_Iris.r`（需先安装 `ggplot2` 和 `rgl`）
- [ ] 用 `?` 查看不熟悉的函数帮助
- [ ] 完成 Moodle quizzes
- [ ] 确保能独立写出：数据读取 → 探索 → 可视化 → 统计检验 的完整流程

---

> [!summary] Week 1 核心收获
> 1. 课程使用 **R (65%)** 和 **SAS (35%)**，强调 command-line programming（命令行编程）
> 2. R 的核心操作：data frame、`$` 和 `[]` 子集提取、`tapply()` 分组计算
> 3. 统计检验的标准流程：先 `var.test()` 后 `t.test()`
> 4. EDA 三大目标 + 图形设计三原则（Labelling, Scaling, Colour/Symbol）
> 5. ==永远不要仅靠颜色区分数据==，要同时使用符号和线型
