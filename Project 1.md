# Hadoop Python map reduce on purchases
We are performing three map-reduce tasks on purchases data set. The python codes, the shell scripts, and the results will be described in the readme section.


# Problem Definition

We are performing three tasks on the purchase data provided from Cloudera. [Here](https://docs.google.com/document/d/1v0zGBZ6EHap-Smsr3x3sGGpDW-54m82kDpPKC2M6uiY/edit?usp=sharing) is a link to the data set and explanations.

- Give us a sales breakdown by product category across all of our stores.

- Find the monetary value for the highest individual sale for each separate store

- Find the total sales value across all the stores, and the total number of sales. Assume there is only one reducer


## Question 1: product category and sales

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

salesTotal = 0
oldKey = None

for line in sys.stdin:
    data_mapped = line.strip().split("\t")
    if len(data_mapped) != 2:
        # Something has gone wrong. Skip this line.
        continue
		

    thisKey, thisSale = data_mapped

    if oldKey and oldKey != thisKey:
        print oldKey, "\t", salesTotal
        oldKey = thisKey;
        salesTotal = 0

    oldKey = thisKey
    salesTotal += float(thisSale)

if oldKey != None:
    print oldKey, "\t", salesTotal
```


- results ```head -25```

```
Baby 	57491808.44
Books 	57450757.91
CDs 	57410753.04
Cameras 	57299046.64
Children's Clothing 	57624820.94
Computers 	57315406.32
Consumer Electronics 	57452374.13
Crafts 	57418154.5
DVDs 	57649212.14
Garden 	57539833.11
Health and Beauty 	57481589.56
Men's Clothing 	57621279.04
Music 	57495489.7
Pet Supplies 	57197250.24
Sporting Goods 	57599085.89
Toys 	57463477.11
Video Games 	57513165.58
Women's Clothing 	57434448.97

```


## Question 2: highest individual sale for each seperate store

- shell
```shell
hadoop fs mapper.py reducer.py myinput myoutput1
```

- mapper 

```python
import sys

for line in sys.stdin:
    data = line.strip().split("\t")
    if len(data) == 6:
        date, time, store, item, cost, payment = data
        print "{0}\t{1}".format(store, cost)
```

- reducer

```python
import sys

salesTotal = 0
oldKey = None
maxSale=0
for line in sys.stdin:
    data_mapped = line.strip().split("\t")
    if len(data_mapped) != 2:
        # Something has gone wrong. Skip this line.
        continue

    thisKey, thisSale = data_mapped
	thisSale = float(thisSale)
    if oldKey and oldKey != thisKey:
        print oldKey, "\t", maxSale
        oldKey = thisKey;
        maxSale = 0
    if thisSale>maxSale:
        maxSale = thisSale
    oldKey = thisKey
    oldSale = thisSale

if oldKey != None:
    print oldKey, "\t", maxSale

```

- results ```head -25```


```
Albuquerque 	499.98
Anaheim 	499.98
Anchorage 	499.99
Arlington 	499.95
Atlanta 	499.96
Aurora 	499.97
Austin 	499.97
Bakersfield 	499.97
Baltimore 	499.99
Baton Rouge 	499.98
Birmingham 	499.99
Boise 	499.98
Boston 	499.99
Buffalo 	499.99
Chandler 	499.98
Charlotte 	499.98
Chesapeake 	499.98
Chicago 	499.99

```

## Question 3: - total sales value across all the stores, and the total number of sales


- Shell script

```shell
hs mapper3.py reducer3.py myinput myoutput3_6
```

- mapper

```python
import sys

for line in sys.stdin:
    data = line.strip().split("\t")
    if len(data) == 6:
        date, time, store, item, cost, payment = data
        print "{0}\t{1}".format(cost,cost)
```

-reducer

```python
mport sys

for line in sys.stdin:
    data = line.strip().split("\t")
    if len(data) == 6:
        date, time, store, item, cost, payment = data
        print "{0}\t{1}".format(cost,cost)
```


- results

```
4138476 	1034457953.26
```