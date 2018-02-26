# Hadoop Python map reduce on purchases
We are performing three map-reduce tasks on purchases data set. The python codes, the shell scripts, and the results will be described in the readme section.


# Problem Definition

We are performing three tasks on the purchase data provided from Cloudera. [Here](https://docs.google.com/document/d/1v0zGBZ6EHap-Smsr3x3sGGpDW-54m82kDpPKC2M6uiY/edit?usp=sharing) is a link to the data set and explanations.

- Give us a sales breakdown by product category across all of our stores.

- Find the monetary value for the highest individual sale for each separate store

- Find the total sales value across all the stores, and the total number of sales. Assume there is only one reducer


## Question 1: product categiry and sales

shell script:

```shell
hadoop fs mapper.py reducer.py myinput myoutput1
```


Python code:

- mapper

```python
import sys

for line in sys.stdin:
    data = line.strip().split("\t")
    if len(data) == 6:
        date, time, store, item, cost, payment = data
        print "{0}\t{1}".format(item, cost)
```

- reducer

```python
import sys

for line in sys.stdin:
    data = line.strip().split("\t")
    if len(data) == 6:
        date, time, store, item, cost, payment = data
        print "{0}\t{1}".format(item, cost)
```


- log output

```

```


- results head -25

```

```