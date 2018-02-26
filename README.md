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


- results head -25

```
Baby 	57491808.44
Books 	57450757.91
CDs 	57410753.04
Cameras 	57299046.64
Children's Clothing 	57624820.94
Computers 	57315406.32
***Consumer Electronics 	57452374.13***
Crafts 	57418154.5
DVDs 	57649212.14
Garden 	57539833.11
Health and Beauty 	57481589.56
Men's Clothing 	57621279.04
Music 	57495489.7
Pet Supplies 	57197250.24
Sporting Goods 	57599085.89
***Toys 	57463477.11***
Video Games 	57513165.58
Women's Clothing 	57434448.97

```