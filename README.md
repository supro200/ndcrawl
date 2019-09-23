# CDP/LLDP Network Discovery Crawler for Cisco networks

Forked from yantisj/ndcraw, modified to generate .txt files for NetSQL tool supro200/netsql.

Main features of the original  **ndcraw** tool:
-	Not only discovers with CDP, but also tries to SSH
-	Reports all discovered devices, and the devices where SSH login was successful
-	Multithreads – discovers the network really fast
-	CLI options similar to NetSQL :)

Outputs to a txt file which can be used in NetSQL (in fact the output directory of this tool can be the input directory for NetSQL)
The output file name is the sitename in CLI stored in **out-txt** directory

To use it, unzip the file in a directory, change dir, and run in CLI     pip install -r requirements.txt

Usage:
```
python ndcrawl.py --user <username>  --seed  <device to start> --site_name <site_name>
```
Example:

```
python ndcrawl.py --user aupuser3 --seed 10.90.47.29 --site_name 300_queen_st

python ndcrawl.py --user aupuser3 --seed 10.174.41.35 --site_name 201_collins_st
```

Uses netmiko and some seed devices to scrape
your network and output CSV files of all neighbor topology data, as well as a
device list file. Uses threaded connections at each iteration, moving out from
seed devices to next level of devices until all devices are discovered.

Uses a BFS algorithm with netmiko, and calculates distances from seed devices.
Currently only works with SSH access to Cisco IOS and NXOS devices, but other
devices could be added easily. Will gather all neighbors and devices found, but
can only scrape cisco devices to discover next level devices at the moment.

## Usage Example: Scrape network starting with the core devices

* Note: The initial devices should have the same device ID in the cdp neighbors
  to avoid duplicate device entries and proper distance calculations

```./ndcrawl.py -seed core1.domain.com,core2.domain.com --user yantisj -nei_file nd.csv -dev_file devices.csv --debug 1```

## Config File Notes

Copy the ndcrawl-sample.ini to ndcrawl.ini and edit options. All CLI options can be specified
from the config file for daemonizing the script. CLI options always override the config file.

## Neighbor List Output Example
```
local_device_id,distance,remote_device_id,platform,local_int,remote_int,ipv4,os
core1.domain.com,0,mdcoobsw1.domain.com,WS-C4948,mgmt0,GigabitEthernet1/1,10.25.9.1,cisco_ios
core1.domain.com,0,servchas1.domain.com,WS-C6504-E,Ethernet7/25,TenGigabitEthernet3/1,10.24.70.51,cisco_ios
core1.domain.com,0,core2.domain.com,N7K-C7010,Ethernet7/26,Ethernet8/26,10.25.156.103,cisco_nxos
core1.domain.com,0,artmdf1.domain.com,N7K-C7010,Ethernet7/27,Ethernet2/26,10.25.80.103,cisco_nxos
```

## Device List Output Example
```
device_id,ipv4,platform,os
core1.domain.com,10.25.9.103,N7K-C7010,cisco_nxos
cnew1.domain.com,10.25.9.10,N9K-C93180YC-EX,cisco_nxos
```
