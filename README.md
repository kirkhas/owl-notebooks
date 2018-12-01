# OwlSpark DQ Pipelines

## Build rich data quality pipelines directly on dataframes using Owl's DQ suite.
Owl blends data science algorithms specifically targeted towards identifying DQ issues with the power of distributed processing for scale

## Auto Clean
<img src="https://owl-analytics.com/img/landing/clean-stacked.png" alt="clean" width="600">


## Outliers

```scala
// create a simple dataframe
val df = Seq(
  ("Kirk", "Haslbeck", "2018-02-24 8:30:45", 18),
  ("Kirk", "Haslbeck", "2018-02-23 8:30:45", 11),
  ("Kirk", "Haslbeck", "2018-02-22 8:30:45", 10),
  ("Kirk", "Haslbeck", "2018-02-21 8:30:45", 12),
  ("Kirk", "Haslbeck", "2018-02-20 8:30:45", 10),
  ("Cat",  "Stevens",  "2018-02-20 8:30:45", 44),
  ("Cat",  "Stevens",  "2018-02-20 8:30:45", 47),
  ("Cat",  "Stevens",  "2018-02-24 8:30:45", 144)
).toDF("fname","lname", "app_date", "price")

// Owl Outlier Detection in 1 line
val outlierDF  = new Owl(df, props)
    .outliersDF(dlKey = "fname", dlLb = 5, dateCol = "app_date", runDate = "2018-02-24")
```

| Key    | Column    | Value | Prediction | Confidence |
| -------|:---------:| -----:|-----------:|-----------:|
| Cat    | price     |   144 | 45.5       |34          |
| Kirk   | price     |    18 | 10.5       |77          |



## Categorical Outliers

```scala
val assetClass = Seq("STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK",
  "STOCK", "FUTURE",
  "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK", "STOCK", "FUTURE",
  "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK", "STOCK", "FUTURE",
  "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK", "STOCK", "FUTURE",
  "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK", "STOCK", "FUTURE",
  "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK", "STOCK", "FUTURE",
  "SWAP", "OPTION", "STOCK","SWAP", "SWAP", "FUTRS", "STOCK", "OPTION", "FUTURE",
  "OPTION", "STOCK" )

val df = assetClass.toDF("ASSET")
val outliers = new Owl(df).outliersDF("ASSET")
```

| Key    | Column    | Value | Prediction | Confidence |
| -------|:---------:|-------|------------|-----------:|
|        | ASSET     | FUTRS | FUTURES    |            |


## Profile

## Behavior Shifts

## Rules and Repair Strategies

## Duplicates
<img src="https://owl-analytics.com/img/dupe-img.png" alt="duplicates" width="300">

```scala
val l = Seq(
  ("Kirk", "Haslbeck", "2018-02-24 8:30:45", 18),
  ("Kirk", "Hasselbach", "2018-02-23 8:30:45", 11),
  ("Kirt", "Haslbeck", "2018-02-22 8:30:45", 10),
  ("Kirk", "hasselback", "2018-02-21 8:30:45", 12),
  ("kirk", "Haslbeck", "2018-02-20 8:30:45", 10)
)

val df = l.toDF("fname","lname", "app_date", "price")
val owl = new Owl(df).dupesDF
```


