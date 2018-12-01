# OwlSpark DQ Pipelines

### Build your own rich data quality pipelines directly on dataframes using Owl's DQ suite
Owl Predictive Data Science **+** Spark Scale

## Auto Clean
<img src="https://owl-analytics.com/img/landing/clean-stacked.png" alt="clean" width="600">

```scala
val df = Seq(
  ("Kirk",  "brian",  "11",  "222-33-4444",  "410-489-8095"),
  ("kirk",  "kirk",   "22",  "111-33-4444",  "410-489-8095"),
  ("bryan", "Kirk",   "11",  "555-33-4444",  "443-885-8989"),
  ("Dan",   "dan",    "33",  "666-33-4444",  "(212) 333-4444"),
  ("",      "brian",  "11",  "222-33-4444",  "(212) 333-4444"),
  ("kirk",  "kirk",   "21",  "111-33-4444",  "(212) 333-4444"),
  ("brianne","kirk",  "18",  "555-33-4444",  "3015859966"),
  ("dan",   "dan",    "31",  "666-33-4444",  "3015859966"),
  ("kirk",  "brian",  "66",  "222-33-4444",  "3015859966"),
  ("kirk",   null,    "age-27", "111-33-4444","3015859966"),
  ("brianne","Kirt",  "13",  "555-33-4444",  "3015859966"),
  ("dan",   "DAN",    "35",  "666-33-4444",  "3015859966"),
  ("kirk",  "brian",  "16",  "222-33-4444",  "410-489-8095"),
  ("kirk",  "kirk",   "27",  "111-33-4444",  "410-489-8095"),
  ("brian", "kirt",   "18",  "17",           "3015859966"),
  ("dan",   "dan",    "39",  "666-33-4444",  "3015859966"),
  ("",      "brian",  "12",  "222-33-4444",  "3015859966"),
  ("kirk",  "kirk",   "22",  "111-33-4444",  "3015859966"),
  ("",      "brian",  "kirk", "1",          "3015859966"),
  ("dan",    null,    "34",  "666-33-4444",  "3015859966"),
  ("brian", "brian",  "10",  "222-33-4444",  "3015859966")
).toDF("first","last","age","ssn","phone")

// Clean this dataset in 1 line!
val clean = new Owl(df).clean
```


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
val assetClass = 
Seq("STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "STOCK","OPTION","FUTURE","STOCK","OPTION","FUTURE","STOCK","STOCK", "FUTURE",
    "SWAP", "OPTION", "STOCK","SWAP", "SWAP", "FUTRS", "STOCK", "OPTION","FUTURE",
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

```scala
// create OwlSpark Context
val owl = new Owl(df)

// add 2 rules
owl.addRule("kirk fname rule", "first = 'Kirk'")
owl.addRule("age less than 105", "age < 105")
  
// Execute the rules in the Pipeline
owl.rulesDF
```

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

| distance | records                                                                                      | 
| --------:|:---------------------------------------------------------------------------------------------|
|   0.16   | DUPE [score: 83 ][Kirk,Hasselbach,2018-02-23 8:30:45]  [Kirt,Haslbeck,2018-02-22 8:30:45]  |
|   0.083  | DUPE [score: 91 ][Kirk,Haslbeck,2018-02-24 8:30:45]  [Kirt,Haslbeck,2018-02-22 8:30:45]    |
|   0.083  | DUPE [score: 91 ][Kirk,Haslbeck,2018-02-24 8:30:45]  [Kirk,Hasselbach,2018-02-23 8:30:45]  |
