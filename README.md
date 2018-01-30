# Intersection

## Detect IPv4 subnet overlaps/intersections in one or multiple excel spreadsheets

In enterprise security, management of per site IP address lists is complex, often with multiple information sources with slight variations in data. My approach is the add all sources to one excel spreadsheet, then use intersection.py to highlight all duplicates. Order the data with the most reliable sources first, so that the less reliable sources become flagged. 

Spreadsheet before intersection: 
![image of france_without_paris.xlsx before intersection](/examples/france_without_paris.png) 
```
$ python3 intersection.py examples/france_without_paris.xlsx 
  192.168.1.0/24 -> 192.168.0.0/23  examples/france_without_paris.xlsx:A4 -> examples/france_without_paris.xlsx:A3
```

Same spreadsheet after intersection. The subnet in cell A4 is flagged as overlapping A3:
![image of france_without_paris.xlsx after intersection](/examples/france_without_paris_intersected.png) 

The result is saved to the spreadsheet to allow easy sorting and editing of overlapping subnets. 

Intersection can also find overlaps through out multiple spreadsheets, to ensure each spreadsheet only contain a list of unique subnets:
```
$ python3 intersection.py examples/france_without_paris.xlsx examples/paris_branch_office.xlsx 
  192.168.1.0/24 -> 192.168.0.0/23  examples/france_without_paris.xlsx:A4 -> examples/france_without_paris.xlsx:A3
  172.16.20.0/30 -> 172.16.20.0/30  examples/paris_branch_office.xlsx:A2 -> examples/france_without_paris.xlsx:A2
  10.0.1.0/24 -> 10.0.0.0/16  examples/paris_branch_office.xlsx:A3 -> examples/france_without_paris.xlsx:A6
  192.168.1.0/26 -> 192.168.0.0/23  examples/paris_branch_office.xlsx:A4 -> examples/france_without_paris.xlsx:A3
  192.168.1.64/26 -> 192.168.0.0/23  examples/paris_branch_office.xlsx:A5 -> examples/france_without_paris.xlsx:A3
  192.168.1.128/26 -> 192.168.0.0/23  examples/paris_branch_office.xlsx:A6 -> examples/france_without_paris.xlsx:A3
  192.168.1.192/26 -> 192.168.0.0/23  examples/paris_branch_office.xlsx:A7 -> examples/france_without_paris.xlsx:A3
```
Multiple intersections are detected in the above example, the VPN link is perhaps intensional, whereas the 10.0/16 may not be.

### Prepend to location data
The zoomed-out perspective is often missing in location data, for example a location may be 'Temuco branch office' or 'Main office 2nd floor', but without reference to what country or city. 
However country or preferable even city locations is needed to manage global risks.

The --location-prepend France search each location cell for France (case-insensitive), then prepend the phrase in each cell where it is not found.

Excel before:

![image of france_without_paris before intersection and location fix](/examples/france_without_paris_location.png) 

```
$ python3 intersection.py examples/france_without_paris.xlsx --location-prepend France 
  192.168.1.0/24 -> 192.168.0.0/23  examples/france_without_paris.xlsx:A4 -> examples/france_without_paris.xlsx:A3
```

![image of france_without_paris after intersection and location fix](/examples/france_without_paris_location_fixed.png) 

Above, France has been prepended to all but the first row, which already has the phrase 'France'. 


### Remove overlap comments

Overlap comments can be removed, effectively reverting the spreadsheet to its original state with the --clean flag:

```
$ python3 intersection.py examples/france_without_paris.xlsx --clean
```

## The Intersection --help page
```
python3 intersection.py examples/france_without_paris.xlsx --help
Usage: intersection.py [OPTION]... <EXCEl_SPREADSHEET>...

Verify overlap of each IPv4 network in a spreadsheet column of multiple
spreadsheets. IP-address or full CIDR syntax is needed: x.x.x.x/y, only one
per cell.

Options:
  -h, --help            show this help message and exit
  -n NETWORK, --network=NETWORK
                        Network data column
  -c COMMENT, --comment=COMMENT
                        Uniqueness comment column
  -l LOCATION, --location=LOCATION
                        Location column
  -p LOCATION_PREPEND, --location-prepend=LOCATION_PREPEND
                        Prepend this string to all cells that does not already
                        contain this, for example: --location-prepend France
  -C, --clear           Remove all overlap comment and exit
  -s, --stats           Show stats on how many unique IP addresses found.
  -r, --read-only       Open spreadsheets as read-only

Open Source MIT License. Written by Christian Angerbjorn
```

## Prerequisites

The butcher is written in Python3. The following python3 packages are required:
- openpyxl

## License
Open Source MIT License
