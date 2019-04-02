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
he example dataset is a rather well known machine learning dataset provided by NASA, and is taken from the "mlbench" package. It represents 9 independent variables of numeric data type, and 1 categorical dependent variable. The data features 58,000 observations, and is not in the realm of what most would consider "big data", but is ideal for demonstrating less than instantaneous computation of imputations.


---
## Let's practice!

```yaml
type: "FinalSlide"
key: "067a2175b2"
```

`@script`


