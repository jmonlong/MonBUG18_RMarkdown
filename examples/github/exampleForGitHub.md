A title for the analysis
========================

I find this type of document perfect for tutorials. I used it to show the commands and output for a [R workshop](https://github.com/jmonlong/HGSS_Rworkshops/blob/master/Advanced-Tidyverse-Bioconductor-2018/HGSS-Rworkshop2018-advanced-liveScript.md).

It's also good to link code to a publication. For example, we created a GitHub repository with the code and outputs of our recent paper (see [epipopsv repo](https://github.com/jmonlong/epipopsv) and the `reports` folder). It shows how each figure/table was produced using R Markdown.

``` r
library(dplyr)
library(knitr)
library(broom)
library(ggplot2)
```

First part
----------

You can write a short description about what this part is about, what you want to achieve, etc. Potentially explain how you got the input data and what it is.

For example, let's say I want to create some fake normal data to later test differences between two groups.

``` r
N = 40
df = tibble(group = rep(1:2, N), var = rnorm(2 * N, 0, 10)) %>% mutate(data = rnorm(n(), 
    group) + var)
df %>% head %>% kable
```

|  group|         var|        data|
|------:|-----------:|-----------:|
|      1|    2.209994|    3.182857|
|      2|    8.046382|   10.229175|
|      1|    3.352837|    4.332096|
|      2|  -17.199034|  -15.385406|
|      1|    2.723654|    4.218162|
|      2|  -12.281116|  -10.885143|

Second part
-----------

Now we want to test differences between the two groups.

### t-test

``` r
tt.o = t.test(subset(df, group == 1)$data, subset(df, group == 2)$data)
tt.o
```

    ## 
    ##  Welch Two Sample t-test
    ## 
    ## data:  subset(df, group == 1)$data and subset(df, group == 2)$data
    ## t = -1.9097, df = 77.973, p-value = 0.05986
    ## alternative hypothesis: true difference in means is not equal to 0
    ## 95 percent confidence interval:
    ##  -8.8909740  0.1850986
    ## sample estimates:
    ## mean of x mean of y 
    ## -1.762175  2.590763

Not significant, the p-value is 0.0598558.

### Controlling for something

A linear model that controls for the `var` column.

``` r
library(broom)
lm(group ~ data + var, data = df) %>% tidy %>% kable
```

| term        |    estimate|  std.error|  statistic|   p.value|
|:------------|-----------:|----------:|----------:|---------:|
| (Intercept) |   1.2027495|  0.0792686|  15.173093|  0.00e+00|
| data        |   0.2075282|  0.0421613|   4.922241|  4.80e-06|
| var         |  -0.1989420|  0.0422468|  -4.709040|  1.08e-05|

Now it's significant, yeay !

Third part
----------

Maybe it's time for a graph.

``` r
library(ggplot2)
ggplot(df, aes(x = data, y = var, colour = factor(group))) + geom_point()
```

![](exampleForGitHub_files/figure-markdown_github/graph-1.png)
