# intersection
Detect IPv4 network overlaps/intersections in multiple excel spreadsheets

python3 intersection.py examples/*.xlsx
  192.168.0.0/23 -> 192.168.1.0/24  examples/paris_branch_office.xlsx:A2 -> examples/france_without_paris.xlsx:A2
  10.0.1.0/24 -> 10.0.0.0/16  examples/paris_branch_office.xlsx:A3 -> examples/france_without_paris.xlsx:A4
 