### Steps for pre-processing Hudson County, NJ 2016 general election data

#### 1. PDF to raw CSV
Use TabulaPDF to convert PDF to CVS file. This conversion is imperfect and sensitive
to things like the size of the capture region. I captured all pages with the same large
region, but didn't record the details. The output of that scan is the file tabulaHudsonCountyNJ.csv.

```
"",President and Vice President,,,,,,,,,
"",De La Fuente/Steinberg,,,,,,,,,
"",Castle/Bradley,,,,,,,,,
"",Kennedy/Hart,,,,,,,,,
"",Johnson/Weld,,,,,,,,,
Bayonne W1 D1,Total 800 528 66.00 286,224,,3,8,,1,1,,2
Bayonne W1 D2,Stein/BToatraalka 778 499 64.14 313,175,2,6,1,,,,,1
Bayonne W1 D3,Total 647 396 61.21 227,152,,6,6,,1,,,2
```

#### 2. Raw CSV to regular-shaped contest CSVs
Run script [1.processTabulaCSV.jsl](2016_general_nj_hudson/1.processTabulaCSV.jsl)
Script for cleaning up this particular elections results CSV file produces by TabulaPDF
1. Cleans up CSV issues such as extra commas and missing commas, plus some had-picked exceptions.
1. Produces a separate CSV file for each contest.
1. Records any discarded duplicate lines in a separate "dup" csv file.
1. Records any other discarded text in a separate "extra" text file.

```
source line,contest,precinct,total,registered,ballots,turnout,Clinton/Kaine,Trump/Pence,La Riva/Puryear,Stein/Baraka,Johnson/Weld,Kennedy/Hart,Castle/Bradley,De La Fuente/Steinberg,Moorehead/Lily,Personal Choice
6,President and Vice President,Bayonne W1 D1,Total,800,528,66.00,286,224,,3,8,,1,1,,2
7,President and Vice President,Bayonne W1 D2,Total,778,499,64.14,313,175,2,6,1,,,,,1
8,President and Vice President,Bayonne W1 D3,Total,647,396,61.21,227,152,,6,6,,1,,,2
9,President and Vice President,Bayonne W1 D4,Total,759,482,63.50,261,196,1,7,9,,1,,1,2
```

#### 3. Clean and combine CSVs
Run script [2.combineAndClean.jsl](2016_general_nj_hudson/2.combineAndClean.jsl)
 Clean each contest CSV file, recoding known garbled values.
 1. Compute a Ward and Town field for each precinct to help with subtotal validation.
 1. Compute a "summary" flag to indicate the count is a subtotal of other counts.
 1. Stack the choice columns into a pair of columns, one row per choice.
 1. Combine all the stacked tables into a single table.
 1. Add computed columns for running sums, which can be validated against the
provided summary columns.
 1. Create a summary table for validation with computed diff and compare columns.
 1. Add a graph script to that table for a visual quality check.

```
source line,contest,precinct,precinct 2,Ward,Town,Calculated,total,turnout,choice,count,Ward Count,Town Count,Contest Count
6,President and Vice President,Bayonne W1 D1,Bayonne W1 D1,Bayonne Ward 1,Bayonne,0,Total,66,registered,800,800,800,800
6,President and Vice President,Bayonne W1 D1,Bayonne W1 D1,Bayonne Ward 1,Bayonne,0,Total,66,ballots,528,528,528,528
6,President and Vice President,Bayonne W1 D1,Bayonne W1 D1,Bayonne Ward 1,Bayonne,0,Total,66,Clinton/Kaine,286,286,286,286
6,President and Vice President,Bayonne W1 D1,Bayonne W1 D1,Bayonne Ward 1,Bayonne,0,Total,66,Trump/Pence,224,224,224,224
6,President and Vice President,Bayonne W1 D1,Bayonne W1 D1,Bayonne Ward 1,Bayonne,0,Total,66,La Riva/Puryear,0,0,0,0
```

#### 4. Validation
Run the "Quality" graph script found within the created summary table as a validation check.
It should have all squares in the "Equal" color. If not, click a bad cell and look at the
selected row in the table. Find the corresponding rows in the source table and look for
errors compared to the original PDF. Use the Town and Ward subtotals to narrow down mismatches.


#### 5. Run script [3.makeFinal.jsl](2016_general_nj_hudson/3.makeFinal.jsl)
 1. Once the results have passed validation, put them into a shareable form:
  	```county,precinct,office,district,party,candidate,votes```
 1. Add party affiliation from http://www.hudsoncountyclerk.org/2016-general-results/
 1. Remove summary rows (totals and subtotals).

 ```
county,precinct,office,district,party,candidate,votes
Hudson,Bayonne W1 D1,President and Vice President,,,Registered Voters,800
Hudson,Bayonne W1 D1,President and Vice President,,,Ballots Cast,528
Hudson,Bayonne W1 D1,President and Vice President,,Democratic,Clinton/Kaine,286
Hudson,Bayonne W1 D1,President and Vice President,,Republican,Trump/Pence,224
Hudson,Bayonne W1 D1,President and Vice President,,Socialist Workers,La Riva/Puryear,0
Hudson,Bayonne W1 D1,President and Vice President,,Green,Stein/Baraka,3
 ````
