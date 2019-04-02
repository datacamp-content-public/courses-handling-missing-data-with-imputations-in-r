---
title: Insert title here
key: 25e3bda2b8a26bbfb2f6c8fdafeb3eca

---
## Parallel Processing for Larger Datasets

```yaml
type: "TitleSlide"
key: "1725a6e079"
```

`@lower_third`

name: Tim Flocke	
title: Data Scientist / Machine Learning Engineer


`@script`



---
## Working With Larger Datasets

```yaml
type: "FullSlide"
key: "c191cb6216"
```

`@part1`
Intro


`@script`



---
## The 'Shuttle' Dataset - NASA

```yaml
type: "TwoColumns"
key: "f4c7d93c70"
code_zoom: 70
disable_transition: false
```

`@part1`
> Data Summary:

- 'mlbench' package
- 9 numeric ind variables
- 1 categorical dep variable
- 58,000 observations


`@part2`
Load packages and data:
```{r}
library(mice)
library(mlbench)
library(dplyr)

# import data from from mlbench
data(Shuttle)
str(Shuttle)
```
![](https://assets.datacamp.com/production/repositories/4850/datasets/9e7146d75d014ca189068ed36a7dbe3363163b3f/str_data.PNG)


`@citations`
https://archive.ics.uci.edu/ml/datasets/Statlog+(Shuttle)


`@script`
The example dataset is a rather well known machine learning dataset provided by NASA, and is taken from the "mlbench" package. It represents 9 independent variables of numeric data type, and 1 categorical dependent variable. The data features 58,000 observations, and is not in the realm of what most would consider "big data", but is ideal for demonstrating less than instantaneous imputation runtime.


---
## Examining Sparsity

```yaml
type: "TwoRows"
key: "cbf1e26ddf"
code_zoom: 70
```

`@part1`
- Using the colSums() and is.na() functions from base R 
- Sparsity relatively even across variables
- The total sparsity is ~30% of the data


`@part2`
```{r}
# examine sparsity
sparsity <- data.frame(
                sparsity = colSums(is.na(shuttle))/nrow(shuttle)
                )
t(sparsity) #transpose
paste("Total sparsity is:", sum(sparsity$sparsity))
```
![](https://assets.datacamp.com/production/repositories/4850/datasets/cd044c4de2d7aa93d04c8bdc11fde7c841834d10/sparsity.PNG)


`@script`
Here we can examine each variable's percentage of missing data. As shown previously, a simple check of sparsity can be run using the colSums() and is.na() functions from base R. This shows that we have about 30% total sparsity, and the 'missingness' is spread across all variables


---
## Estimating Imputation Runtime

```yaml
type: "FullSlide"
key: "717f85ef98"
```

`@part1`
> Runtime needed depends on:

1. Individual machine,
2. Degree of 'missingness', 
3. Number of dimensions (variables)
4. Type of imputation employed for each variable, 
5. Number of iterations, and 
6. Number of imputed datasets specified


`@script`
While the Shuttle data is only moderately sized, the computation time needed to impute the missing data can be time consuming, depending on the type of local machine you are working on and available computing power. If this dataset were ten times larger, at 580,000 observations, this complexity would be compounded. The runtime needed not only varies by machine, but also by

Degree of missingness, number of dimensions (variables), type of imputation employed for each variable, number of iterations, and the number of imputed datasets that you specify.


---
## Estimating Imputation Runtime

```yaml
type: "TwoRows"
key: "1832f63d1a"
code_zoom: 65
```

`@part1`
> Using 10% of the data...

- m = 6 (# of imputed datasets)
- maxit = 10 (# of iterations per set)


`@part2`
```{r}
#take a random sample of 10%
shuttle_sample <- sample_n(shuttle, 
					0.10 * nrow(shuttle))
# calculate runtime
start <- Sys.time()
shuttle_imp <- mice(shuttle_sample,
					m = 6, maxit = 10)
end <- Sys.time()
runtime <- end-start
runtime # Time difference of 1.012093 mins
```


`@script`
Before imputing a larger dataset, it would be wise to get a feel for how long your imputing might take you. It might take you a few minutes, or much longer.

A simple step for getting a rough back-of-the-envelope runtime estimate would be to run your impuation on a random sample of your data, and calculate the time it takes using the Sys.time() function.

In this example, suppose you have decided to create 6 imputed datasets, with 10 iterations. Within the mice() function, call a sample dataset, and specify m = 6, maxit = 10. 

Running this imputation job on 10% of the data takes around 1 minute. Right about now, you might be thinking that it would take about 10 minutes to run your imputation job...


---
## Let's practice!

```yaml
type: "FinalSlide"
key: "067a2175b2"
```

`@script`


