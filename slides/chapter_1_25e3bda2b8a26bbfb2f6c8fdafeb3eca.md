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
Next, we are going to walk through an example of how to handle time consuming imputation jobs with parallel processing


---
## The 'Shuttle' Dataset - NASA

```yaml
type: "TwoColumns"
key: "f4c7d93c70"
code_zoom: 70
disable_transition: false
hide_title: false
```

`@part1`
> Data Summary:

- 'mlbench' package
- 58,000 observations
- 9 numeric ind variables
- 1 categorical dep variable


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
Our example data is a dataset provided by NASA, and is taken from the "mlbench" package. It represents 58,000 observations, 9 numeric independent variables, and 1 categorical dependent variable. While this not what most would consider "big data", it is ideal for demonstrating less than instantaneous imputation runtimes. We will start by loading our packages, and the Shuttle dataset


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
Now, briefly review each variable's percentage of missing data. As shown previously, a simple check of sparsity can be run using the colSums() and is.na() functions from base R. This shows that we have about 30% total sparsity, and the 'missingness' is spread across all variables.


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
5. Number of iterations
6. Number of imputed datasets specified


`@script`
While this data is only moderately sized, the time needed to impute the missing data can be considerable, depending on the type of local machine you are working on and available computing power. If this dataset were ten times larger, at 580,000 observations, the complexity would be compounded. The required runtime not only varies by individual machine, but also by degree of 'missingness', number of dimensions, type of imputation employed for each variable, number of iterations, and the number of imputed datasets.


---
## Estimating Imputation Runtime

```yaml
type: "TwoRows"
key: "1832f63d1a"
code_zoom: 70
```

`@part1`
> Using a random **10%** of the data...
- m = 6 _(# of imputed datasets)_
- maxit = 10 _(# of iterations per set)_
- runtime is **~ 1 minute**


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
Before you start to impute, it would be a good idea to get a rough time estimate.

A simple way to get a feel for the full runtime would be to run your imputation on only a random sample, and calculate the time it takes using the Sys.time() function. You may want to use the sample_n() function from the 'dplyr' package to sample to your data.

In this example, suppose you have decided to create 6 imputed datasets, with 10 iterations each. Within the mice() function, call your sample dataset, and specify m = 6, and maxit = 10. 

Running this imputation job on my machine using 10% of the data takes around 1 minute. Right about now, you might be thinking that it would take about 10 minutes to run the entire imputation...


---
## Estimating Imputation Runtime

```yaml
type: "TwoRows"
key: "2c56bfa2a8"
```

`@part1`
> Using a random **30%** of the data...
- m = 6 (# of imputed datasets)
- maxit = 10 (# of iterations per set)
- runtime is **~ 4 minutes**


`@part2`
- **Runtime is not always linear**... it can follow an exponential curve, a 2nd order polynomial, or some other complex function. 

- Utilizing a technique known as **'parallel processing'** can help you approach computationally expensive jobs more efficiently.


`@script`
...but running 30% of the data is around 4 minutes of runtime. So your full imputation task could be considerably longer. Algorithm runtime is generally not a linear function. Utilizing a technique known as 'parallel processing' can make your imputing a LOT more efficient.


---
## Determine Number of Cores

```yaml
type: "FullSlide"
key: "e6ca92e44f"
```

`@part1`
Parallel processing leverages multiple CPUs simultaneously

First, call the 'parallel' package, and use detectCores() to determine the number of CPUs available:

```{r}
library(parallel)

# print number of available cores
detectCores()

[1] 4
```


`@script`
Parallel processing allows you to use multiple cores, or CPUs, within your local machine. Again, the availability and performance will vary by machine - but the concept is still the same. 

To get started, first use a function called 'detectCores()' from the 'parallel' package. This will allow you to determine how many cores you have at your disposal. In this example, we have 4 cores to work with.


---
## Using parlmice() for Parallel Processing

```yaml
type: "TwoRows"
key: "1c05ef1df9"
code_zoom: 60
```

`@part1`
Since we need 6 imputed sets, with 10 iterations each, we can divide the work across 3 cores (4 available -1), 2 imputations per core.
- n.core: # cores to use, n.imp.core: # imputed datasets per core
- 1.75 minutes for 30%,


`@part2`
```{r}
# log the start time before imputing
start <- Sys.time()
shuttle_imp_parl <- parlmice(shuttle_sample, m = 6,
                              n.core = 3, 
                              n.imp.core = 2,
                              maxit = 10) 
# log completion time 
end <- Sys.time()

# calculate total runtime
parl_time <- end - start
parl_time #
```


`@script`
Next, inn order to leverage this additional computing power, the MICE package has a great wrapper function named "parlMice" which enables you to divide the work across multiple cores. 

It is considered best practice to use n-1 for the number of cores, since your local machine still needs CPU power to run its other processes. You run the risk of jamming your machine up if you exhaust all available power.  

Running the previous sample of 30% will only take 1.75 minutes, which is a big improvement.


---
## Other Approaches

```yaml
type: "FullSlide"
key: "6cc16b5119"
```

`@part1`
- Specify fewer imputed datasets
- Reduce the number of iterations
- Select less time consuming method (i.e. "norm" v. "pmm")


`@script`
Other options to speed things up would include specifying fewer imputed datasets, fewer iterations, or using less time consuming methods of imputing such as norm rather than predictive mean matching.


---
## Let's practice!

```yaml
type: "FinalSlide"
key: "067a2175b2"
```

`@script`
Now that you have seen an example of how to approach a time consuming imputation task, lets practice these concepts in the following exercises.

