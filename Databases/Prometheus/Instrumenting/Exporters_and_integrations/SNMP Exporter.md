---
tags:
  - Databases
  - Prometheus
  - Instrumenting
  - Exporters_and_integrations
---
# Core principles
## Definition
It is an executable [[Bash Scripting]] Script to be scraped by Prometheus (see [[Prometheus#Exporter]]). This exporter acts a [[SNMP|SNMP manager]].
The core idea is to scrape this SNMP manager process because we want every information that the SNMP manager obtains from agents to be formatted to [[Prometheus#Metric]] so as to store in Prometheus DataBase.
# Hands on
## How to use SNMP Exporter
### Configuration
Firstly, you will need to configure the SNMP Exporter through ```snmp.yml``` file. Here is where you define which [[SNMP#Definition|OIDs]] to request to [[SNMP|SNMP Agent]]. However, that file shouldn't be edited by user directly: use [[#SNMP Generator]] instead.
#### SNMP Generator
The [SNMP Generator](https://github.com/prometheus/snmp_exporter/tree/main/generator) is a "sub-tool" within SNMP Exporter whose aim is to auto-generate ```snmp.yml``` file based on two inputs: [[#Input 1 generator.yml file]] and [[#Input 2 MIBs]].
##### Input 1: generator.yml file
Here is where you exactly define which OIDs to request to [[SNMP|SNMP Agent]], as well as another SNMP configurations such as SNMP authentication methods. Those OIDs must be defined in some [[SNMP#MIB|MIBs]] inside ```generator/mibs``` directory. The Generator tool will then parse the OID list in ```generator.yml``` and the ```generator/mibs``` and will firstly check whether the specified OIDs exist in one or more MIBs.
See [[#How to configure generator.yml]] to configure this file.
##### How to configure generator.yml
See [[#Template]] as base template.
###### Authentication section
[[SNMP#Versions|SNMP authentication method]] to use against SNMP Agent (it depends on SNMP version). For instance, for SNMP v2c it's enough with setting a ```community_name```:
```
auths:
  my_authentication:
    version: 2 #The same as 2c for generator.yml
    community: <community_name>
```

We can call it ```my_authentication``` or whatever. Nonetheless, this name will be important [[#Execution|later]], when we are going to run SNMP Exporter.
###### Modules section
Here is where you set the OID list to request to SNMP Agent. However, writing all the OIDs can be extremely tedious, as often a lot of OIDs are required to be monitored. Therefore, what we will do is to specify the parent OID from which to perform a [[SNMP#SNMP Operations#WALK|SNMP WALK operation]].

```
auths:
  my_authentication:
    version: 2 #The same as 2c for generator.yml
    community: <community_name>

modules:
  my_mib:
    walk:
      - 1.3.6.1.4.1 #OID from which to perform a snmpwalk

    max_repetitions: 25  # How many objects to request with GET/GETBULK, defaults to 25.
                         # May need to be reduced for buggy devices.
    retries: 3   # How many times to retry a failed request, defaults to 3.
    timeout: 5s  # Timeout for each individual SNMP request, defaults to 5s.

    lookups:
     - source_indexes: [index_object_name]
       lookup: object_name_column
```

We can call it ```my_mib``` or whatever. Nonetheless, this name will be important [[#Execution|later]], when we are going to run SNMP Exporter.

In SNMP, a [[SNMP#SNMP Table]] has got an **index object**. By default, whenever you perform a [[SNMP#GET]] to retrieve an element inside the table, SNMP returns the value without specifying the column name, so It's difficult to infer what SNMP refers to. To solve this, you can use the ```lookups``` section, where you have to specify:
- The exact object name of the index object (e.g. ```hrDeviceIndex```)
- The exact column name (e.g. ```hrDeviceDescr```)
In this way, SNMP will return both the index object name and the metric column name, so  Prometheus will get the metric with both labels.
Example: providing you have this table:

| index_object_name | cpu_usage |
|:-----------------:|:---------:|
| 0 | 0.34 |
| 1 | 0.28 |
| 2 | 0.56 |
| 3 | 0.11 |
| 4 | 0.78 |

After running SNMP walk over the parent table OID to get all entries, if you don't specify any lookup, the result will be:
```
cpu_usage{index_object_name="0"} 0.34
cpu_usage{index_object_name="1"} 0.28
cpu_usage{index_object_name="2"} 0.56
cpu_usage{index_object_name="3"} 0.11
cpu_usage{index_object_name="4"} 0.78
```
Although the [[Prometheus#Metric]] (```cpu_usage```) can be self-descriptive, you might want to add a label with that metric name for different purposes (such as filtering time series or whatever). In case you specify ```lookups```:
```
cpu_usage{index_object_name="0", cpu_usage="0.34"} 0.34
cpu_usage{index_object_name="1", cpu_usage="0.28"} 0.28
cpu_usage{index_object_name="2", cpu_usage="0.56"} 0.56
cpu_usage{index_object_name="3", cpu_usage="0.11"} 0.11
cpu_usage{index_object_name="4", cpu_usage="0.78"} 0.78
```

On ```overrides:``` section of ```generator.yml``` It's possible to perform some transformations to the metric value (for instance, on condition that CPU is a value between 0-1 and we want it to be between 0-100 + offset, or if some value is UP or DOWN and we want to make it boolean, or we want to change data types from float to integer). We can either override the retrieved metric or create a new one apart from the retrieved with ```ignore:``` (if true, drops the original metric value. Otherwise, keep it).

It is also possible to apply filtering in SNMP walk. This can be very useful, since we might not want to get all the data of snmpwalk operation that we specified before, in ```walk:``` section.
We can specify which concrete OIDs to retrieve inside the parent ID from which the walk is performed:
```
filters:
    static:
      - targets:
        - 1.3.6.1.4.1
        indices: ["3", "6", "9"] #Just get OIDs 1.3.6.1.4.1.3, 1.3.6.1.4.1.6 y 1.3.6.1.4.1.9
```
 
On the other hand, there exists dynamic filters applied to SNMP tables. Suppose you have this table:

|ifNumber         | ifState       | Input_bits        | ifAddress      |
|:---------------:|:-------------:|:-----------------:|:--------------:|
|0                | UP            | 3456234           | A              |
|1                | UP            | 24356456          | B              |
|2                | DOWN          | 67                | C              |
|3                | UP            | 2355              | D              |

_And suppose the table OID is = 1.3.6.1.999, for example_
Then we want to visualize only the **Input_bits** column such that **ifState** == UP. This is called a dynamic filter based on ifState value applied to Input_bits:

```
dynamic:
    - oid: 1.3.6.1.999.3    #Target column values to filter (.3 = Input_bits)
    targets:
        - "1.3.6.1.999.2"   #Reference column (.2 = ifState coiumn)
    values: ["UP"]          #Filter value
```
###### Template
```yaml
auths:
  auth_name:
    version: 2  # SNMP version to use. Defaults to 2.
                # 1 will use GETNEXT, 2 and 3 use GETBULK.

    # Community string is used with SNMP v1 and v2. Defaults to "public".
    community: public

    # v3 has different and more complex settings.
    # Which are required depends on the security_level.
    # The equivalent options on NetSNMP commands like snmpbulkwalk
    # and snmpget are also listed. See snmpcmd(1).
    username: user  # Required, no default. -u option to NetSNMP.
    security_level: noAuthNoPriv  # Defaults to noAuthNoPriv. -l option to NetSNMP.
                                  # Can be noAuthNoPriv, authNoPriv or authPriv.
    password: pass  # Has no default. Also known as authKey, -A option to NetSNMP.
                    # Required if security_level is authNoPriv or authPriv.
    auth_protocol: MD5  # MD5, SHA, SHA224, SHA256, SHA384, or SHA512. Defaults to MD5. -a option to NetSNMP.
                        # Used if security_level is authNoPriv or authPriv.
    priv_protocol: DES  # DES, AES, AES192, AES256, AES192C, or AES256C. Defaults to DES. -x option to NetSNMP.
                        # Used if security_level is authPriv.
    priv_password: otherPass # Has no default. Also known as privKey, -X option to NetSNMP.
                             # Required if security_level is authPriv.
    context_name: context # Has no default. -n option to NetSNMP.
                          # Required if context is configured on the device.

modules:
  module_name:  # The module name. You can have as many modules as you want.
    walk:       # List of OIDs to walk. Can also be SNMP object names or specific instances.
      - 1.3.6.1.2.1.2              # Same as "interfaces"
      - sysUpTime                  # Same as "1.3.6.1.2.1.1.3"
      - 1.3.6.1.2.1.31.1.1.1.6.40  # Instance of "ifHCInOctets" with index "40"
      - 1.3.6.1.2.1.2.2.1.4        # Same as ifMtu (used for filter example)
      - bsnDot11EssSsid            # Same as 1.3.6.1.4.1.14179.2.1.1.1.2 (used for filter example)

    max_repetitions: 25  # How many objects to request with GET/GETBULK, defaults to 25.
                         # May need to be reduced for buggy devices.
    retries: 3   # How many times to retry a failed request, defaults to 3.
    timeout: 5s  # Timeout for each individual SNMP request, defaults to 5s.

    lookups:  # Optional list of lookups to perform.
              # The default for `keep_source_indexes` is false. Indexes must be unique for this option to be used.

      # If the index of a table is bsnDot11EssIndex, usually that'd be the label
      # on the resulting metrics from that table. Instead, use the index to
      # lookup the bsnDot11EssSsid table entry and create a bsnDot11EssSsid label
      # with that value.
      - source_indexes: [bsnDot11EssIndex]
        lookup: bsnDot11EssSsid
        drop_source_indexes: false  # If true, delete source index labels for this lookup.
                                    # This avoids label clutter when the new index is unique.

      # It is also possible to chain lookups or use multiple labels to gather label values.
      # This might be helpful to resolve multiple index labels to a proper human readable label.
      # Please be aware that ordering matters here.

      # In this example, we first do a lookup to get the `cbQosConfigIndex` as another label.
      - source_indexes: [cbQosPolicyIndex, cbQosObjectsIndex]
        lookup: cbQosConfigIndex
      # Using the newly added label, we have another lookup to fetch the `cbQosCMName` based on `cbQosConfigIndex`.
      - source_indexes: [cbQosConfigIndex]
        lookup: cbQosCMName

    overrides: # Allows for per-module overrides of bits of MIBs
      metricName:
        ignore: true # Drops the metric from the output.
        regex_extracts:
          Temp: # A new metric will be created appending this to the metricName to become metricNameTemp.
            - regex: '(.*)' # Regex to extract a value from the returned SNMP walks's value.
              value: '$1' # The result will be parsed as a float64, defaults to $1.
          Status:
            - regex: '.*Example'
              value: '1' # The first entry whose regex matches and whose value parses wins.
            - regex: '.*'
              value: '0'
        offset: 1.0 # Add the value to the same. Applied after scale.
        scale: 1.0 # Scale the value of the sample by this value.
        type: DisplayString # Override the metric type, possible types are:
                             #   gauge:   An integer with type gauge.
                             #   counter: An integer with type counter.
                             #   OctetString: A bit string, rendered as 0xff34.
                             #   DateAndTime: An RFC 2579 DateAndTime byte sequence. If the device has no time zone data, UTC is used.
                             #   DisplayString: An ASCII or UTF-8 string.
                             #   PhysAddress48: A 48 bit MAC address, rendered as 00:01:02:03:04:ff.
                             #   Float: A 32 bit floating-point value with type gauge.
                             #   Double: A 64 bit floating-point value with type gauge.
                             #   InetAddressIPv4: An IPv4 address, rendered as 192.0.0.8.
                             #   InetAddressIPv6: An IPv6 address, rendered as 0102:0304:0506:0708:090A:0B0C:0D0E:0F10.
                             #   InetAddress: An InetAddress per RFC 4001. Must be preceded by an InetAddressType.
                             #   InetAddressMissingSize: An InetAddress that violates section 4.1 of RFC 4001 by
                             #       not having the size in the index. Must be preceded by an InetAddressType.
                             #   EnumAsInfo: An enum for which a single timeseries is created. Good for constant values.
                             #   EnumAsStateSet: An enum with a time series per state. Good for variable low-cardinality enums.
                             #   Bits: An RFC 2578 BITS construct, which produces a StateSet with a time series per bit.

    filters: # Define filters to collect only a subset of OID table indices
      static: # static filters are handled in the generator. They will convert walks to multiple gets with the specified indices
              # in the resulting snmp.yml output.
              # the index filter will reduce a walk of a table to only the defined indices to get
              # If one of the target OIDs is used in a lookup, the filter will apply ALL tables using this lookup
              # For a network switch, this could be used to collect a subset of interfaces such as uplinks
              # For a router, this could be used to collect all real ports but not vlans and other virtual interfaces
              # Specifying ifAlias or ifName if they are used in lookups with ifIndex will apply to the filter to
              # all the OIDs that depend on the lookup, such as ifSpeed, ifInHcOctets, etc.
              # This feature applies to any table(s) OIDs using a common index
        - targets:
          - bsnDot11EssSsid
          indices: ["2","3","4"]  # List of interface indices to get

      dynamic: # dynamic filters are handed by the snmp exporter. The generator will simply pass on the configuration in the snmp.yml.
               # The exporter will do a snmp walk of the oid and will restrict snmp walk made on the targets
               # to the index matching the value in the values list.
               # This would be typically used to specify a filter for interfaces with a certain name in ifAlias, ifSpeed or admin status.
               # For example, only get interfaces that a gig and faster, or get interfaces that are named Up or interfaces that are admin Up
        - oid: 1.3.6.1.2.1.2.2.1.7
          targets:
            - "1.3.6.1.2.1.2.2.1.4"
          values: ["1", "2"]
```
##### Input 2: MIBs
To request some OIDs to the SNMP Agent, you will have to find out what MIB file each OID belongs to. You can check it on oidref ([[SNMP#Good references]]). Once you know the corresponding MIB for each OID, you will need to download them. You can download them from circitor ([[SNMP#Good references]]) or manufacturer's webpage. If cannot find the Download button, just copy and paste the MIB content into a ```.mib``` or ```.txt``` file, whatever you want, with the name you want. Remember to save the MIB file inside ```generator/mibs```, and remember that there are [[SNMP#MIB|MIB dependencies]], so you may need to download more MIBs.
The GitHub repository provides some default basic MIBs. You can get them by running:
```shell
make generator mibs
```
##### Output: snmp.yml
Run:
```shell
make generate
```
The output is the ```snmp.yml``` file, located inside ```/generator``` directory. Just move this resulting file into the directory where [[#Execution|SNMP Bash Script]] is located.
##### Troubleshooting
If you have errors when running Generator Tool, the SNMP Exporter developer provides a command with Docker container (for more information about Docker, go to [[Docker]], but not strictly necessary):
```shell
sudo docker run --rm -v "${PWD}:/opt/" prom/snmp-generator:v0.24.1 parse_errors
```
### Execution
Once you have the ```snmp.yml``` file configured, the next step is to download the [[#Reference|SNMP Bash Script Binaries]]. Decompress it and override the default ```snmp.yml``` with your own ```snmp.yml``` configuration. Then, you can [[Bash Scripting#Hands on#Run Script|run it]]. The SNMP exporter will run in ```localhost:9116``` by default.
### Usage
You can communicate with your running Exporter through HTTP while It is running, as many Prometheus Exporters. In this case, you need to communicate to ```/smmp``` endpoint and send the following _Query string_ parameters:
```
localhost:9116/snmp?target=<agent_IP>&auth=<auth_name>&module=<module_name>
```
Where:
- ```<agent_IP>``` is the SNMP agent's IP address
- ```<auth_name>``` is the name you chose in [[#Authentication section]] (```my_authentication```), i.e. the name that is defined in ```snmp.yml```
- ```<module_name>``` is the name you chose in [[#Modules section]] (```my_mib```), i.e. the name that is defined in ```snmp.yml```
Now you can [[Prometheus#prometheus.yml|configure Prometheus]] to scrape these HTTP URLs.
## Error C compiler

# Reference
- [SNMP Exporter GitHub repository](https://github.com/prometheus/snmp_exporter/tree/main/)
- [SNMP Exporter Bash Script Binaries](https://github.com/prometheus/snmp_exporter/releases)

