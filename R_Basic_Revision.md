# R Revision

---
## 1. R Revision

### 1.1 Essential R Foundations

#### 1.1.1 Data Structures

| Structure | Description | Example |
| --- | --- | --- |
| **Vector** | 1D, same type | `c(1, 2, 3)` |
| **Matrix** | 2D, same type | `matrix(1:6, nrow=2)` |
| **Array** | Multi-dimensional, same type | `array(1:24, dim=c(2,3,4))` |
| **Data Frame** | ==Most commonly used==, 2D, mixed types | `data.frame(x=1:3, y=c("a","b","c"))` |
| **Factor** | Categorical variable | `factor(c("male","female","male"))` |

Basic variable types include `numeric`, `integer`, `character`, `logical`, and `factor`.

#### 1.1.2 Assignment Operator

```r
# Assign an expression result to an object
x <- 5
species.data <- read.table("galapagos.dat", header=TRUE)
```

> [!note] `<-` vs `=`
> In this course, `<-` is the recommended assignment operator.

#### 1.1.3 Subsetting — ==Key Skill==

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

#### 1.1.4 `$` Operator

Used to access named components in a data frame or list:

```r
species.data$Elevation
iris$Sepal.Length
iris$Species
```

#### 1.1.5 Object Inspection Functions

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

### 1.2 Statistical Functions — Core

#### 1.2.1 Summary Statistics

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

#### 1.2.2 `tapply()` — Grouped Computation

`tapply(X, INDEX, FUN)` applies a function `FUN` to `X` within each group defined by `INDEX`.

```r
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=mean)
round(tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=var), 3)
tapply(iris$Sepal.Length, INDEX=iris$Species, FUN=quantile, prob=0.25)
```

> [!tip] Intuition
> `tapply` is a classic `split-apply-combine` tool.

#### 1.2.3 Statistical Tests

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

### 1.3 Reading Data

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

### 1.4 Graphics

#### 1.4.1 Basic Plot Functions

| Function | Plot Type | Typical Use |
| --- | --- | --- |
| `plot()` | Scatterplot | Relationship between two continuous variables |
| `pairs()` | Scatterplot matrix | Pairwise relations in multivariate data |
| `boxplot()` | Boxplot | Compare distributions across groups |
| `hist()` | Histogram | Distribution shape of one variable |
| `barplot()` | Bar chart | Frequencies for categories |
| `density()` | Density curve | Smoothed distribution estimate |

#### 1.4.2 Plot Customization

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

#### 1.4.3 Boxplot with Formula Syntax

```r
boxplot(Sepal.Length ~ Species, data=iris,
        xlab="Species",
        ylab="Sepal length (cm)",
        main="Distributions of sepal lengths by species",
        col="salmon")
```

> [!note] Formula syntax
> `y ~ x` means “y as a function of x” and is standard in R for model/group relationships.

#### 1.4.4 Multiple Groups in One Plot
example
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

#### 1.4.5 `ggplot2` Faceting

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

#### 1.4.6 Saving Graphics

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

#### 1.4.7 Additional Graphics Helpers

| Function | Use |
| --- | --- |
| `abline()` | Add a straight line (e.g., regression line) |
| `legend()` | Add legend |
| `box()` | Draw border |
| `par()` | Set graphics parameters |
| `par(mfrow=c(2,2))` | 2×2 plotting layout |
| `par(ask=TRUE)` | Pause between plots |

---

### 1.5 Linear Regression — Galapagos Example

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

## 2. Exploratory Data Analysis (EDA)

### 2.1 Three Main Goals

| # | Goal |
| --- | --- |
| 1 | Preliminary understanding of data structure |
| 2 | Detect outliers or data-quality problems |
| 3 | Suggest initial assumptions for modeling |

### 2.2 Using Graphics Effectively — ==Key Principles==

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

## 3. R Help System

| Command | Purpose |
| --- | --- |
| `?mean` | Open help page for `mean` |
| `??regression` | Search help pages for “regression” |
| `help(mean)` | Same as `?mean` |
| `help.search("regression")` | Same as `??regression` |
| `example(boxplot)` | Run example code for `boxplot` |

---

## 4. Quick Reference

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

> Key Takeaways
> 1. Core R operations: data frames, `$`, `[]`, and grouped computation via `tapply()`.  
> 2. Standard testing flow: `var.test()` first, then `t.test() # var.equal = TRUE or FALSE`.  
> 3. EDA goals + graphics principles (Labelling, Scaling, Colour/Symbol).  
> 4. Never use colour alone to encode categories.

