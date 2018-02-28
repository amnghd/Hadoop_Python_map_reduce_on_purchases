# Hadoop Python map reduce on Anonymized Webserver Log (0.5GB)

We are performing three map-reduce tasks on anaonymized webserver log data set. The python codes, the shell scripts, and the results will be described in the readme section.


# Problem Definition

We are performing three tasks on the webserver log data provided from Cloudera. [Here](https://classroom.udacity.com/courses/ud617/lessons/313947755/concepts/24677385490923) is a link to the data set and explanations.


## Data Format

Each line represents a hit to the Web server. It includes the IP address which accessed the site, the date and time of the access, and the name of the page which was visited.

The logfile is in Common Log Format:

```
10.223.157.186 - - [15/Jul/2009:15:50:35 -0700] "GET /assets/js/lowpro.js HTTP/1.1" 200 10469

%h %l %u %t \"%r\" %>s %b

```

where: 

- %h is the IP address of the client
- %l is identity of the client, or "-" if it's unavailable
- %u is username of the client, or "-" if it's unavailable
- %t is the time that the server finished processing the request. The format is [day/month/year:hour:minute:second zone]
- %r is the request line from the client is given (in double quotes). It contains the method, path, query-string, and protocol or the request.
- %>s is the status code that the server sends back to the client. You will see  mostly status codes 200 (OK - The request has succeeded), 304 (Not Modified) and 404 (Not Found). See more information on status codes in W3C.org
- %b is the size of the object returned to the client, in bytes. It will be "-" in case of status code 304.


### Shell scripts

We need to unzip the data and bring it to the proper format.

```shell
cd ~training/udacity_training/code_web
gunzip ../data/access_log.gz
hadoop fs -put access_log
hadoop fs -mkdir myinput_access
hadoop fs -put access_log myinput_access
#giving permissions to the mapper and reducer
chmod 755 ~/training/udacity_training/code_web/mapper.py
chmod 755 ~/training/udacity_training/code_web/reducer.py
#running the first task
hs mapper.py reducer.py myinput_access myoutput_access1 _1
hadoop fs -cat myoutput_access1_1/part-0000>results1.txt
#running the second task

```

File is ready in the appropriate format at this points. We can go ahead and write down the mapper and reducer for different tasks.

## Q1: Number of hits for each different files on the website

- mapper 
```
#!/usr/bin/python
import sys

for line in sys.stdin:
    data = line.strip().split("\"")
        left_data, req_all, right_data = data
        req = req_all.split(" ")[1]
        print "{0}\t{1}".format(req, req)

```

- reducer

```
#!/usr/bin/python

import sys

total_number = 0
old_key = None
for line in sys.stdin:
    data_mapped = line.strip().split("\t")
    if len(data_mapped) != 2:
        continue

    this_key , this_req = data_mapped

    if old_key and old_key != this_key:
        print old_key, " ", total_number
        old_key = this_key
        total_number = 0

    old_key = this_key
    total_number += 1

if old_key!= None:
    print old_key, " ", total_number

```
- a part of the results

```
/assets/js/jquery.fancyzoom.min.js   23	
/assets/js/jquery.form.js   1803	
/assets/js/jquery.ifixpng.js   23	
/assets/js/jquery.shadow.js   23	
/assets/js/lightbox.js   297	
/assets/js/lowpro.js   293	
/assets/js/submit.form-plugin   1	
/assets/js/supersleight.js   13	
/assets/js/text/xml   4	
/assets/js/the-associates.js   2456
```


## Q2: Number of hits by each IP

- mapper

```
#!/usr/bin/python
import sys

for line in sys.stdin:
    data = line.strip().split("\"")
    if len(data) == 3:
        left_data, req_all, right_data = data
        ip = left_data.split(" ")[0]
        print "{0}\t{1}".format(ip, ip)
```


- reducer 

```
#!/usr/bin/python

import sys

total_number = 0
old_key = None
for line in sys.stdin:
    data_mapped = line.strip().split("\t")
    if len(data_mapped) != 2:
        continue

    this_key , this_req = data_mapped

    if old_key and old_key != this_key:
        print old_key, " ", total_number
        old_key = this_key
        total_number = 0

    old_key = this_key
    total_number += 1

if old_key!= None:
    print old_key, " ", total_number
```


- sample results

```
10.99.97.41   1	
10.99.97.54   1	
10.99.97.8   1	
10.99.98.150   2	
10.99.98.229   2	
10.99.98.77   1	
10.99.99.127   1	
10.99.99.186   6
```


## Q3 : Find the most popular file on the website

One important issue is that some of the pathnames begin with "http://www.the-associates.co.uk". We need to remove it from the pathnames in our mapper.

For solving this, we are going to use the results of the first question as our input.

- shell script

```
hadoop fs -cat myoutput_access1_1/part-0000>input3.txt
hadoop fs -put input3.txt
hadoop fs -mkdir myinput3_access
hadoop fs -put input3.txt myinput3_access
```

Since the data is small enough (3MB), we do not require map and reduce. One single python code can take care of it:

- python

```python
#!/usr/bin/python
import sys
req_set = set()
req_dict = {}
max_value = 0
max_key = None
for line in sys.stdin:
    data = line.strip().split("  ")
    if len(data) == 2:
        req, number = data
        number = float(number)
        req = req.strip()
        if len(req)>30:
            if req[:31] == 'http://www.the-associates.co.uk':
                req = req[31:]
        if req in req_set:
            req_dict[req] = number + req_dict[req]
        if req not in req_set:
            req_dict[req] = number
            req_set.update([req])

for k, v in req_dict.iteritems():
    if v > max_value:
        max_value = v
        max_key = k
print max_key, max_value

```


- results 

```
/assets/css/combined.css  117352.0

```