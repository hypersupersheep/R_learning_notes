## STAT0023 Week 1 — Course Overview & R Revision

---
## Links
[[Data Structure_R的数据结构|Data Structures in R]]
[[待修改 - Statistical Tests（统计检验）|Statistical Tests]]
[[ggplot2 使用指南|ggplot2 Guide]]
[[Linear Regression的统计知识|Linear Regression Notes]]

## 1. Course Overview

### 1.1 Two Main Software Tools

This course uses two `statistical packages`:

| Feature | **R** | **SAS** (Statistical Analysis System) |
| --- | --- | --- |
| License | ==Free== and open-source | **Commercial** (paid) |
| History | Modernized from S language | Started in the 1970s |
| Ecosystem | 20,000+ packages on CRAN | Industry standard (e.g., pharma, insurance) |
| Interface | Command line + RStudio | Command line + point-and-click interface |
| Course weight | ==**65%**== | **35%** |

> [!tip] Why more R?
> Programming concepts are generally easier to demonstrate and learn in R than in SAS.

### 1.2 Why Command-Line Instead of Point-and-Click?

The course emphasizes writing commands/scripts rather than only using GUI tools.

1. **Reproducibility**: commands are saved in script files and can be rerun.
2. **Documentation**: scripts can include comments explaining each step.
3. **Debugging**: errors can be located precisely.
4. **Efficiency**: repeated point-and-click operations are tedious and error-prone.

> [!important] Course goal
> The goal is not to memorize every command, but to build **confidence to find appropriate new commands for yourself**.

---

## 2. R Revision

### 2.1 Essential R Foundations

#### 2.1.1 Data Structures

[[Data Structure_R的数据结构|Data Structures in R]]

| Structure | Description | Example |
| --- | --- | --- |
| **Vector** | 1D, same type | `c(1, 2, 3)` |
| **Matrix** | 2D, same type | `matrix(1:6, nrow=2)` |
| **Array** | Multi-dimensional, same type | `array(1:24, dim=c(2,3,4))` |
| **Data Frame** | ==Most commonly used==, 2D, mixed types | `data.frame(x=1:3, y=c("a","b","c"))` |
| **Factor** | Categorical variable | `factor(c("male","female","male"))` |

Basic variable types include `numeric`, `integer`, `character`, `logical`, and `factor`.

#### 2.1.2 Assignment Operator

```r
# Assign an expression result to an object
x <- 5
species.data <- read.table("galapagos.dat", header=TRUE)
```

> [!note] `<-` vs `=`
> In this course, `<-` is the recommended assignment operator.

#### 2.1.3 Subsetting — ==Key Skill==

Use square brackets `[]` to extract subsets.

**Method A: logical condition**

```r
# Find islands with area > 3000
big.island <- (species.data$Area > 3000)  # TRUE/FALSE vector
species.data[big.island, ]                 # rows satisfying the condition
```

**Method B: numeric index**

```r
species.data[16, ]      # row 16
species.data[, 3]       # column 3
species.data[1:5, 2:4]  # rows 1-5, cols 2-4
```

#### 2.1.4 `$` Operator

Used to access named components in a data frame or list:

```r
species.data$Elevation
iris$Sepal.Length
iris$Species
```

#### 2.1.5 Object Inspection Functions

| Function | Purpose | Notes |
| --- | --- | --- |
| `names()` | Show variable/component names | Quick variable check |
| `str()` | ==Structure==: type + preview | **Most useful** at first glance |
| `summary()` | Summary statistics by variable | Fast distribution overview |
| `class()` | Object type | e.g., data.frame, numeric |

```r
data(iris)
str(iris)
summary(iris)
```

---

### 2.2 Statistical Functions — ==Core==

#### 2.2.1 Summary Statistics

| Function | Meaning |
| --- | --- |
| `mean()` | Mean |
| `var()` | Variance |
| `sd()` | Standard deviation |
| `median()` | Median |
| `min()` / `max()` | Minimum / Maximum |
| `range()` | Returns min and max |
| `quantile()` | Quantiles |
| `table()` | Frequency table |

```r
mean(iris$Sepal.Length)
quantile(iris$Sepal.Length, prob=0.25)  # Q1
```

#### 2.2.2 `tapply()` — ==Grouped Computation==

`tapply(X, INDEX, FUN)` applies a function `FUN` to `X` within each group defined by `INDEX`.

```r
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=mean)
round(tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=var), 3)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=quantile, prob=0.25)
```

> [!tip] Intuition
> `tapply` is a classic `split-apply-combine` tool.

#### 2.2.3 Statistical Tests

[[待修改 - Statistical Tests（统计检验）|Statistical Tests]]

| Function | Test |
| --- | --- |
| `t.test()` | *t*-test for means |
| `var.test()` | *F*-test for equal variances |
| `chisq.test()` | Chi-squared test |
| `fisher.test()` | Fisher's exact test |

Example with iris (`versicolor` vs `virginica`):

```r
var.test(iris$Sepal.Length[iris$Species=="versicolor"],
         iris$Sepal.Length[iris$Species=="virginica"])

t.test(iris$Sepal.Length[iris$Species=="versicolor"],
       iris$Sepal.Length[iris$Species=="virginica"],
       var.equal=TRUE)
```

Additional patterns:

```r
t.test(x, mu = mu0)                    # one-sample
t.test(x, y)                           # two independent samples (Welch default)
t.test(x, y, var.equal = TRUE)         # equal variances assumed
t.test(before, after, paired = TRUE)   # paired test
```

> [!warning] Standard workflow
> ==Check variance equality first with `var.test()`, then choose `var.equal=TRUE/FALSE` in `t.test()`.==

---

### 2.3 Reading Data

==`header=TRUE` means the first row contains column names.==

```r
species.data <- read.table("galapagos.dat", header=TRUE)
data(iris)
```

#### `sink()` — Output Diversion

`sink()` redirects text output only (not plots).

```r
sink("Galapagos_Results.txt")
# print()/cat() output goes to file
sink()  # restore console output
```

#### `cat()` vs `print()`

- `cat()`: plain text output, no automatic newline.
- `print()`: prints R objects with structure/format.

```r
cat("\nMean sepal lengths:\n")
cat("--------------------\n")
print(mean(iris$Sepal.Length))
```

---

### 2.4 Graphics — ==Weekly Focus==

#### 2.4.1 Basic Plot Functions

| Function | Plot Type | Typical Use |
| --- | --- | --- |
| `plot()` | Scatterplot | Relationship between two continuous variables |
| `pairs()` | Scatterplot matrix | Pairwise relations in multivariate data |
| `boxplot()` | Boxplot | Compare distributions across groups |
| `hist()` | Histogram | Distribution shape of one variable |
| `barplot()` | Bar chart | Frequencies for categories |
| `density()` | Density curve | Smoothed distribution estimate |

#### 2.4.2 Plot Customization — ==Must Know==

```r
plot(x, y,
     type = "p",             # points/lines/both/none
     xlab = "X label",
     ylab = "Y label",
     main = "Title",
     col  = "blue",
     pch  = 15,
     cex  = 1.2,
     lty  = 2,
     lwd  = 2
)
```

Common `pch` values: 0 square, 1 circle, 2 triangle, 15 filled square, 16 filled circle, 17 filled triangle.

#### 2.4.3 Boxplot with Formula Syntax

```r
boxplot(Sepal.Length ~ Species, data=iris,
        xlab="Species",
        ylab="Sepal length (cm)",
        main="Distributions of sepal lengths by species",
        col="salmon")
```

> [!note] Formula syntax
> `y ~ x` means “y as a function of x” and is standard in R for model/group relationships.

#### 2.4.4 Multiple Groups in One Plot

```r
plot.colours <- hcl.colors(3, "Dark 3")

plot(iris$Petal.Length, iris$Sepal.Length,
     col = plot.colours[iris$Species],
     pch = (15:17)[iris$Species],
     cex = 1.2,
     xlab = "Petal length (cm)",
     ylab = "Sepal length (cm)",
     main = "Sepal length vs petal length")

legend("topleft", pch=15:17, col=plot.colours,
       legend=levels(iris$Species), cex=1.2,
       title="Species", title.col=grey(0.4))
box(lwd=2)
```

> [!tip] Why `(15:17)[iris$Species]` works
> `iris$Species` is a factor internally coded as 1/2/3, used to index `c(15,16,17)`.

#### 2.4.5 `ggplot2` Faceting

[[ggplot2 使用指南|ggplot2 Guide]]

```r
library(ggplot2)
ggplot(data=iris, mapping=aes(x=Petal.Length, y=Sepal.Length)) +
  geom_point() +
  facet_wrap(~ Species) +
  labs(x="Petal length (cm)", y="Sepal length (cm)",
       title="Sepal length vs petal length for three iris species") +
  theme(plot.title = element_text(hjust=0.5))
```

`facet_wrap(~ Species)` creates separate panels by species with comparable scales.

#### 2.4.6 Saving Graphics

```r
# Copy current plot to PNG
dev.copy(png, "IrisPlot1.png", width=8*72, height=6*72)
dev.off()

# Copy current plot to PDF
dev.copy(pdf, "endemics.pdf", width=6, height=6)
dev.off()
```

> [!warning] Always call `dev.off()`
> If omitted, output files may be incomplete or corrupted.

#### 2.4.7 Additional Graphics Helpers

| Function | Use |
| --- | --- |
| `abline()` | Add a straight line (e.g., regression line) |
| `legend()` | Add legend |
| `box()` | Draw border |
| `par()` | Set graphics parameters |
| `par(mfrow=c(2,2))` | 2×2 plotting layout |
| `par(ask=TRUE)` | Pause between plots |

---

[[Linear Regression的统计知识|Linear Regression Notes]]
### 2.5 Linear Regression — Galapagos Example

```r
endemics.model <- lm(Endemics ~ Elevation, data=species.data)
summary(endemics.model)

plot(species.data$Elevation, species.data$Endemics,
     xlab="Elevation (m)", ylab="No. of species",
     pch=15, col="blue")
abline(endemics.model, col="red", lty=2, lwd=2)

par(mfrow=c(2,2))
plot(endemics.model)
par(mfrow=c(1,1))
```

The four diagnostic plots from `plot(lm_model)`:
1. **Residuals vs Fitted** — linearity and homoscedasticity
2. **Normal Q-Q** — normality of residuals
3. **Scale-Location** — constant variance
4. **Residuals vs Leverage** — influential points

Classical linear model assumptions (about errors):
1. **Linearity**: $E(Y \mid X) = \beta_0 + \beta_1 X$
2. **Independence**: observations/errors are independent
3. **Homoscedasticity**: $\mathrm{Var}(\varepsilon \mid X) = \sigma^2$
4. **Normality of errors**: $\varepsilon \sim N(0,\sigma^2)$

---

## 3. Exploratory Data Analysis (EDA)

### 3.1 Three Main Goals

| # | Goal |
| --- | --- |
| 1 | Preliminary understanding of data structure |
| 2 | Detect outliers or data-quality problems |
| 3 | Suggest initial assumptions for modeling |

### 3.2 Using Graphics Effectively — ==Key Principles==

> **Key principle**: the message should be clear **at a glance**.

**A) Labelling**
- Include a title.
- Include axis labels with units.
- Add legend when needed.
- Keep text legible.

**B) Scaling**
- Let data reasonably fill the plotting region.
- Use common scales when fair comparisons are needed.

**C) Colours / Symbols / Line Types**
- ==Never rely on colour alone.==
- Consider color-blind accessibility (especially red-green confusion).
- Prefer combined encoding: colour + symbol + line type.
- `hcl.colors()` offers color-blind-friendly palettes.

---

## 4. Workshop Scripts Explained

### 4.1 Galapagos Script

**Context**: biodiversity data from the Galapagos Islands (species counts, endemic species, area, elevation, etc.).

```r
sink("Galapagos_Results.txt")

species.data <- read.table("galapagos.dat", header=TRUE)

str(species.data)
summary(species.data)
plot(species.data)

big.island <- (species.data$Area > 3000)
species.data[big.island, ]

endemics.model <- lm(Endemics ~ Elevation, data=species.data)
abline(endemics.model, col="red", lty=2, lwd=2)

par(mfrow=c(2,2))
plot(endemics.model)

sink()
par(mfrow=c(1,1))
```

### 4.2 Iris Script

**Context**: Fisher-Anderson iris dataset (3 species × 50 samples each).

| Variable | Meaning |
| --- | --- |
| `Sepal.Length` | Sepal length (cm) |
| `Sepal.Width` | Sepal width (cm) |
| `Petal.Length` | Petal length (cm) |
| `Petal.Width` | Petal width (cm) |
| `Species` | setosa, versicolor, virginica |

```r
library(ggplot2); library(rgl)

data(iris)
str(iris)
summary(iris)

tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=mean)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=var)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=median)

boxplot(Sepal.Length ~ Species, data=iris, col="salmon")

var.test(...)
t.test(...)

ggplot(...) + geom_point() + facet_wrap(~ Species)

plot(..., col=plot.colours[iris$Species], pch=(15:17)[iris$Species])

rgl::plot3d(...)
```

---

## 5. R Help System

| Command | Purpose |
| --- | --- |
| `?mean` | Open help page for `mean` |
| `??regression` | Search help pages for “regression” |
| `help(mean)` | Same as `?mean` |
| `help.search("regression")` | Same as `??regression` |
| `example(boxplot)` | Run example code for `boxplot` |

---

## 6. Quick Reference

### Core Functions

| Category | Functions |
| --- | --- |
| **Data import** | `read.table()`, `data()` |
| **Data inspection** | `str()`, `summary()`, `names()`, `class()`, `head()`, `tail()` |
| **Summary stats** | `mean()`, `var()`, `sd()`, `median()`, `quantile()`, `range()`, `table()` |
| **Grouped stats** | `tapply()` |
| **Tests** | `t.test()`, `var.test()`, `chisq.test()`, `fisher.test()` |
| **Modeling** | `lm()` |
| **Graphics** | `plot()`, `boxplot()`, `hist()`, `pairs()`, `barplot()`, `density()` |
| **Graphics helpers** | `legend()`, `abline()`, `box()`, `par()`, `dev.copy()`, `dev.off()` |
| **Output control** | `cat()`, `print()`, `sink()`, `round()` |
| **Help** | `?`, `??`, `help()`, `example()` |

### Common Subsetting Patterns

```r
df$column
df[rows, cols]
df[condition, ]
df[, "colname"]
```

---

## 7. Week 1 To-Do

- [ ] Run `Workshop1_Galapagos.r` and understand each line.
- [ ] Run `Workshop1_Iris.r` (install `ggplot2` and `rgl` first).
- [ ] Use `?` to read help pages for unfamiliar functions.
- [ ] Complete Moodle quizzes.
- [ ] Be able to write a full workflow independently: import → explore → visualize → test.

---

> [!summary] Week 1 Key Takeaways
> 1. The course uses **R (65%)** and **SAS (35%)**, with emphasis on command-line programming.  
> 2. Core R operations: data frames, `$`, `[]`, and grouped computation via `tapply()`.  
> 3. Standard testing flow: `var.test()` first, then `t.test()`.  
> 4. EDA goals + graphics principles (Labelling, Scaling, Colour/Symbol).  
> 5. ==Never use colour alone to encode categories.==

