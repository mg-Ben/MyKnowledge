---
tags:
  - Data_visualization_and_analysis
---
# Core principles
# Hands on
## Exploring data
### Prometheus database
#### PromQL queries
On condition that you have these metrics:
```
temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 81
temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 57
temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 47
temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 78
temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 80
# Some metrics are inside the labels, i.e. they are constant over time:
cpuDescr{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 1
# Other metrics which are constant over time might not be inside labels:
cpuCores{cpuIndex="1"} 3
cpuCores{cpuIndex="2"} 5
cpuCores{cpuIndex="3"} 4
cpuCores{cpuIndex="4"} 3
cpuCores{cpuIndex="5"} 4
# Other metrics may have the same information than another ones:
cpuProcesses{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 12
cpuProcesses{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 443
cpuProcesses{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 3003
cpuProcesses{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 34
cpuProcesses{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 44
```
However, as Prometheus is running periodically, there would be an additional hidden label, _Time_, which represents the timestamp when the metric was taken. This is, although the metrics are like the example above, actually those metrics are for a certain time instant. Therefore, in fact the metrics should look like this:
```
Timestamp: 03-04-2023 13:33:23:
	temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 81
	temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 57
	temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 47
	temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 78
	temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 80
	cpuDescr{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 1
	cpuCores{cpuIndex="1"} 3
	cpuCores{cpuIndex="2"} 5
	cpuCores{cpuIndex="3"} 4
	cpuCores{cpuIndex="4"} 3
	cpuCores{cpuIndex="5"} 4
	cpuProcesses{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 12
	cpuProcesses{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 443
	cpuProcesses{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 3003
	cpuProcesses{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 34
	cpuProcesses{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 44
```

```
Timestamp: 03-04-2023 13:38:23:
	temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 85
	temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 56
	temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 47
	temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 77
	temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 81
	cpuDescr{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 1
	cpuCores{cpuIndex="1"} 3
	cpuCores{cpuIndex="2"} 5
	cpuCores{cpuIndex="3"} 4
	cpuCores{cpuIndex="4"} 3
	cpuCores{cpuIndex="5"} 4
	cpuProcesses{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 133
	cpuProcesses{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 450
	cpuProcesses{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 3060
	cpuProcesses{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 35
	cpuProcesses{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 48
```

```
Timestamp: 03-04-2023 13:43:23:
	temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 85
	temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 55
	temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 45
	temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 80
	temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 88
	cpuDescr{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 1
	cpuDescr{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 1
	cpuCores{cpuIndex="1"} 3
	cpuCores{cpuIndex="2"} 5
	cpuCores{cpuIndex="3"} 4
	cpuCores{cpuIndex="4"} 3
	cpuCores{cpuIndex="5"} 4
	cpuProcesses{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 23
	cpuProcesses{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 788
	cpuProcesses{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 809
	cpuProcesses{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 35
	cpuProcesses{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 56
```
Let's explore some queries you can perform to get some visualizations.

##### Get label value(s)
Whether you want to take some label values to show them as a dropdown list selection in Grafana dashboard or you want to create a dashboard variable with a certain label value:
```
label_values(metric{<filters>}, label)
```
Example 1:
```
label_values(temperature, machine)
```
Query result:
```
server1
server2
database1
database2
database3
```
Example 2:
```
label_values(temperature{cpu="Intel"}, machine)
```
Query result:
```
server1
server2
database1
```
Example 3:
```
label_values(temperature{cpuIndex="3"}, machine)
```
Query result:
```
database1
```
Example 4:
```
label_values(cpuDescr, cpuDescr)
```
Query result:
```
Intel Silver 1234 @ 2.4 GHz
AMD Ryzen 3700 @ 2.4 GHz
AMD Ryzen 3800X @ 2.4 GHz
```
##### Table Visualization
###### Simplest query
```
temperature
```
Query result:

| Time                | cpuIndex | cpu   | machine   | temperature |
| ------------------- | -------- | ----- | --------- | ----------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | 81          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | 85          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | 85          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | 57          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | 56          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | 55          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | 47          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | 47          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | 45          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 4        | AMD   | database2 | 78          |
| 03-04-2023 13:38:23 | 4        | AMD   | database2 | 77          |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 | 80          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 5        | AMD   | database3 | 80          |
| 03-04-2023 13:38:23 | 5        | AMD   | database3 | 81          |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 | 88          |

###### Filter by some label value
```
temperature{cpu = "Intel"}
```
Query result:

| Time                | cpuIndex | cpu   | machine   | temperature |
| ------------------- | -------- | ----- | --------- | ----------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | 81          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | 85          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | 85          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | 57          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | 56          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | 55          |
| ...                 |          |       |           |             |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | 47          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | 47          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | 45          |
###### Hide/Rename/Rearrange columns
The only way to get this is through Grafana Transformations. Go to Transformations in Grafana Panel and select _Organize fields_ option. Then you will be able to re-order, hide or rename the columns of the table as you want.
###### Lookup metrics (JOIN operation)
Here there are two use-cases:
- You want to relate one metric to some label value inside a target metric (e.g. ```temperature``` to ```cpuDescr``` label values)
- You want to relate two different metrics (e.g. ```temperature``` to ```cpuCores```)
**For the first one:**
Imagine you want to show [[#PromQL queries|the temperature for each CPU description]], like this:

| Time                | cpuDescr                    | temperature |
| ------------------- | --------------------------- | ----------- |
| 03-04-2023 13:33:23 | Intel Silver 1234 @ 2.4 GHz | 81          |
| 03-04-2023 13:38:23 | Intel Silver 1234 @ 2.4 GHz | 85          |
| 03-04-2023 13:43:23 | Intel Silver 1234 @ 2.4 GHz | 85          |
| ...                 |                             |             |
| 03-04-2023 13:33:23 | Intel Silver 1234 @ 2.4 GHz | 57          |
| 03-04-2023 13:38:23 | Intel Silver 1234 @ 2.4 GHz | 56          |
| 03-04-2023 13:43:23 | Intel Silver 1234 @ 2.4 GHz | 55          |
| ...                 |                             |             |
| 03-04-2023 13:33:23 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:38:23 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:43:23 | Intel Silver 1234 @ 2.4 GHz | 45          |
| ...                 |                             |             |
| 03-04-2023 13:33:23 | AMD Ryzen 3700 @ 2.4 GHz    | 78          |
| 03-04-2023 13:38:23 | AMD Ryzen 3700 @ 2.4 GHz    | 77          |
| 03-04-2023 13:43:23 | AMD Ryzen 3700 @ 2.4 GHz    | 80          |
| ...                 |                             |             |
| 03-04-2023 13:33:23 | AMD Ryzen 3800X @ 2.4 GHz   | 80          |
| 03-04-2023 13:38:23 | AMD Ryzen 3800X @ 2.4 GHz   | 81          |
| 03-04-2023 13:43:23 | AMD Ryzen 3800X @ 2.4 GHz   | 88          |
Then you have to relate both ```temperature``` and ```cpuDescr``` metrics through ```cpuIndex``` label.
In other words, you take the ```cpuIndex``` label from ```temperature``` metric and, for that index, retrieve the ```cpuDescr``` **label value** (not the metric value!) from ```cpuDescr``` metric. Then it is attached at left or right (group_left or group_right):
```
                     ↓
temperature{cpuIndex="1", cpu="Intel", machine="server1", ...} 81
temperature{cpuIndex="2", cpu="Intel", machine="server2", ...} 57
temperature{cpuIndex="3", cpu="Intel", machine="database1", ...} 47
temperature{cpuIndex="4", cpu="AMD", machine="database2", ...} 78
temperature{cpuIndex="5", cpu="AMD", machine="database3", ...} 80

                                ↓
cpuDescr{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz"} 1
cpuDescr{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz"} 1

Resulting metric:
temperature{cpuIndex="1", cpuDescr="Intel Silver 1234 @ 2.4 GHz", cpu="Intel", machine="server1", ...} 81
temperature{cpuIndex="2", cpuDescr="Intel Silver 1234 @ 2.4 GHz", cpu="Intel", machine="server2", ...} 57
temperature{cpuIndex="3", cpuDescr="Intel Silver 1234 @ 2.4 GHz", cpu="Intel", machine="database1", ...} 47
temperature{cpuIndex="4", cpuDescr="AMD Ryzen 3700 @ 2.4 GHz", cpu="AMD", machine="database2", ...} 78
temperature{cpuIndex="5", cpuDescr="AMD Ryzen 3800X @ 2.4 GHz", cpu="AMD", machine="database3", ...} 80
```

```
<metric> * on(<index_or_any_unique_value>) group_left(<target_label>) <target_metric>
```
_In Grafana, it doesn't matter whether you use group_left or group_right, since columns are alphabetically-ordered_
Example:
```
temperature * on(cpuIndex) group_left(cpuDescr) cpuDescr
```
As a matter of fact, it would be the same as:
```
temperature * on(cpuIndex) group_left(cpuDescr) cpuProcesses
```
Because in ```cpuProcesses``` the ```cpuDescr``` is also present. The result consequently would be:

| Time                | cpuIndex | cpu   | machine   | cpuDescr                    | temperature |
| ------------------- | -------- | ----- | --------- | --------------------------- | ----------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 81          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 85          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 85          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 57          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 56          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 55          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 45          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 4        | AMD   | database2 | AMD Ryzen 3700 @ 2.4 GHz    | 78          |
| 03-04-2023 13:38:23 | 4        | AMD   | database2 | AMD Ryzen 3700 @ 2.4 GHz    | 77          |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 | AMD Ryzen 3700 @ 2.4 GHz    | 80          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 5        | AMD   | database3 | AMD Ryzen 3800X @ 2.4 GHz   | 80          |
| 03-04-2023 13:38:23 | 5        | AMD   | database3 | AMD Ryzen 3800X @ 2.4 GHz   | 81          |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 | AMD Ryzen 3800X @ 2.4 GHz   | 88          |

You can even perform filters, such as:
```
temperature{cpu="Intel"} * on(cpuIndex) group_left(cpuDescr) cpuProcesses{cpu="Intel"}
```

| Time                | cpuIndex | cpu   | machine   | cpuDescr                    | temperature |
| ------------------- | -------- | ----- | --------- | --------------------------- | ----------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 81          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 85          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | Intel Silver 1234 @ 2.4 GHz | 85          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 57          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 56          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | Intel Silver 1234 @ 2.4 GHz | 55          |
| ...                 |          |       |           |                             |             |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 47          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | Intel Silver 1234 @ 2.4 GHz | 45          |
Remember that you can [[#Take the last/max/mean... value]] and later [[#Hide/Rename/Rearrange columns]] to get the last temperature and then hide some non-interesting columns.

**For the second one:**
Imagine that you want to [[#PromQL queries|relate two separate metrics]]: ```temperature``` with ```cpuCores```. The result you want to obtain is:

| Time                | cpuIndex | cpu   | machine   | cpuCores | temperature |
| ------------------- | -------- | ----- | --------- | -------- | ----------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | 3        | 81          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | 3        | 85          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | 3        | 85          |
| ...                 |          |       |           |          |             |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | 5        | 57          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | 5        | 56          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | 5        | 55          |
| ...                 |          |       |           |          |             |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | 4        | 47          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | 4        | 47          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | 4        | 45          |
| ...                 |          |       |           |          |             |
| 03-04-2023 13:33:23 | 4        | AMD   | database2 | 3        | 78          |
| 03-04-2023 13:38:23 | 4        | AMD   | database2 | 3        | 77          |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 | 3        | 80          |
| ...                 |          |       |           |          |             |
| 03-04-2023 13:33:23 | 5        | AMD   | database3 | 4        | 80          |
| 03-04-2023 13:38:23 | 5        | AMD   | database3 | 4        | 81          |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 | 4        | 88          |
To this end, you would need to query both the temperature and the CPU cores metrics in Grafana, and set the format for each one as _Table_.
![[PanelFormatSelectorTable.png]]

![[2queriesExample.png]]

The result would look like this (see table below). However, since each query would be a separate table, Grafana will separate each query when you want to visualize it in the panel.

| Time                | cpuIndex | cpu   | machine   | temperature | cpuCores |
| ------------------- | -------- | ----- | --------- | ----------- | -------- |
| 03-04-2023 13:33:23 | 1        | Intel | server1   | 81          |          |
| 03-04-2023 13:38:23 | 1        | Intel | server1   | 85          |          |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | 85          |          |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 2        | Intel | server2   | 57          |          |
| 03-04-2023 13:38:23 | 2        | Intel | server2   | 56          |          |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | 55          |          |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 3        | Intel | database1 | 47          |          |
| 03-04-2023 13:38:23 | 3        | Intel | database1 | 47          |          |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | 45          |          |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 4        | AMD   | database2 | 78          |          |
| 03-04-2023 13:38:23 | 4        | AMD   | database2 | 77          |          |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 | 80          |          |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 5        | AMD   | database3 | 80          |          |
| 03-04-2023 13:38:23 | 5        | AMD   | database3 | 81          |          |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 | 88          |          |
| 03-04-2023 13:33:23 | 1        | Intel | server1   |             | 3        |
| 03-04-2023 13:38:23 | 1        | Intel | server1   |             | 3        |
| 03-04-2023 13:43:23 | 1        | Intel | server1   |             | 3        |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 2        | Intel | server2   |             | 5        |
| 03-04-2023 13:38:23 | 2        | Intel | server2   |             | 5        |
| 03-04-2023 13:43:23 | 2        | Intel | server2   |             | 5        |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 3        | Intel | database1 |             | 4        |
| 03-04-2023 13:38:23 | 3        | Intel | database1 |             | 4        |
| 03-04-2023 13:43:23 | 3        | Intel | database1 |             | 4        |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 4        | AMD   | database2 |             | 3        |
| 03-04-2023 13:38:23 | 4        | AMD   | database2 |             | 3        |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 |             | 3        |
| ...                 |          |       |           |             |          |
| 03-04-2023 13:33:23 | 5        | AMD   | database3 |             | 4        |
| 03-04-2023 13:38:23 | 5        | AMD   | database3 |             | 4        |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 |             | 4        |

After the query, go to _Transformations > Join by field_ and set as _Mode = OUTER_ and _field = The index field (in this case, cpuIndex)_. The result will do both:
- Take the last value of each query for each cpuIndex
- Collapse by columns each query by cpuIndex
However, as there are separate queries, the common fields in both queries (such as _Time_ or _cpuIndex_) will be repeated and you will need to [[#Hide/Rename/Rearrange columns|hide the duplicate fields]].

| Time                | cpuIndex | cpu   | machine   | temperature | cpuCores |
| ------------------- | -------- | ----- | --------- | ----------- | -------- |
| 03-04-2023 13:43:23 | 1        | Intel | server1   | 85          | 3        |
| 03-04-2023 13:43:23 | 2        | Intel | server2   | 55          | 5        |
| 03-04-2023 13:43:23 | 3        | Intel | database1 | 45          | 4        |
| 03-04-2023 13:43:23 | 4        | AMD   | database2 | 80          | 3        |
| 03-04-2023 13:43:23 | 5        | AMD   | database3 | 88          | 4        |
You can also apply [[#Filter by some label value|filters]] if you want.
###### Take the last/max/mean... value
The only solution is to use Grafana transformations. The operation to compute in this case is a **Reduce**, as you have to convert a timestamped data to a single value.
Select _Time series_ as Format:
![[PanelFormatSelectorTimeSeries.png.png]]
Go to Transformations and choose _Series to rows_. In _Calculations_, select the operation to perform over the entire timestamped data (Last, Mean, Max or whatever). Select _Labels to fields_ to show each label as a column.

Result:

| cpuIndex | cpu   | machine   | temperature |
| -------- | ----- | --------- | ----------- |
| 1        | Intel | server1   | 85          |
| 2        | Intel | server2   | 55          |
| 3        | Intel | database1 | 45          |
| 4        | AMD   | database2 | 80          |
| 5        | AMD   | database3 | 88          |

##### Time series Visualization


