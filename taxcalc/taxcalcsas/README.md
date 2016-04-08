Comparison of Tax-Calculator and NBER taxcalc.sas results
=========================================================

This directory includes tools used to compare the income tax results
generated by Tax-Calculator with results generated by a [SAS
program](http://www.nber.org/taxcalc) developed by Dan Feenberg and
Ina Shapiro of NBER.

The tools included in this directory support the following
cross-model-comparison work flow:

  1. Generate a sample of tax filing units (INPUT).
  2. Generate OUTPUT from INPUT using the inctax.py script.
  3. Generate OUTPUT from INPUT using the NBER taxcalc.sas program.
  4. Generate tax differences by comparing the two OUTPUT files.


First comparison: methods and results
-------------------------------------

(1) The INPUT file used in the first comparison is generated as
follows:

```
$ cd taxcalc/taxcalcsas
```

```
$ tclsh ../validation/make-in.tcl 2013 c > c13.in
```

The ```c13.in``` file is in Internet-TAXSIM format, so we
convert that file to CSV format as follows:

```
$ python ../../simtax.py --taxsim2441 --records c13.in
```

We rename the file generated by the ```--records``` option, as follows:

 ```
$ mv c13.in.records c13.csv
```

We process the renamed INPUT file with the ```inctax.py``` command-line
interface to Tax-Calculator as follows:

```
$ python ../../inctax.py c13.csv 2013
```

The resulting OUTPUT file is identical to that produced by the
```simtax.py``` command-line interface to Tax-Calculator because
the following command produces no difference output:

```
$ diff c13-13.out-inctax c13.in.out-simtax
```

So, the contents of the ```c13.in``` and ```c13.csv``` files are identical
in the sense that they generate the exact same Tax-Calculator OUTPUT files:
```c13.in.out-simtax```, which is generated by the ```simtax.py```
command-line interface to the Tax-Calculator using ```c13.in``` as
INPUT, and ```c13-13.out-inctax```, which is generated by the
```inctax.py``` command-line interface to the Tax-Calculator using
```c13.csv``` as INPUT.

(2) The Tax-Calculator OUTPUT file is the ```c13-13.out-inctax``` file.

(3) The taxcalc.sas OUTPUT file, which is called ```c13.out-sas```, has
been generated by taxcalc.sas using the ```c13.csv``` file as input.

(4) The comparison of the two OUTPUT files is accomplished as follows:

```
$ cp c13-13.out-inctax py-out
```

After shortening the Tax-Calculator OUTPUT file name, we convert the
taxcalc.sas OUTPUT file from CSV format to Internet-TAXSIM output format
as follows:

```
$ python sas2out.py c13.out-sas sas-out
```

Then the ```py-out``` and ```sas-out``` files are compared using the
```taxcalc/validation/taxdiffs.tcl``` tool:

```
$ tclsh ../validation/taxdiffs.tcl py-out sas-out > c13-13.taxdiffs
```

The content of the ```c13-13.taxdiffs``` file generated on 04-Apr-2016
for which we have outputs from both tax models is as follows:

```
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 17   2857      0 -45350.00 [14914]
      #big_vardiffs_with_big_inctax_diff=             1252
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 18  27964      0  27150.00 [99899]
      #big_vardiffs_with_big_inctax_diff=            26178
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 19  28000     36   7602.00 [99899]
      #big_vardiffs_with_big_inctax_diff=            26178
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 22   1234      0  -1755.00 [27667]
      #big_vardiffs_with_big_inctax_diff=             1228
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 23    521      0   1755.00 [27667]
      #big_vardiffs_with_big_inctax_diff=              521
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 24   8802      0  -2360.00 [30114]
      #big_vardiffs_with_big_inctax_diff=             8478
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 27  16285      0  16822.00 [41511]
      #big_vardiffs_with_big_inctax_diff=            16285
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]= 28  26570     49   7602.00 [99899]
      #big_vardiffs_with_big_inctax_diff=            26156
TAXDIFF:ovar,#diffs,#1cdiffs,maxdiff[id]=  4  32613     54  14853.42 [4005]
                       #big_inctax_diffs=            32559
```

The above difference results show that almost one-third (32,559 out of
100,000) of the filing units in the c13 sample have federal income tax
liabilities that differ by more than one cent.  The largest of these
differences is $14,853.42, which is for the filing unit with id 4005,
with Tax-Calculator producing the larger tax liability.  The input
variables (in Internet-TAXSIM format) for filing unit 4005 are as
follows:

```
$ awk '$1==4005' c13.in
4005 2013 0 3 1 0 270600 0 9000 0 0 0 0 0 975 38025 11000 5000 1 6000 0 1000
```

The causes of these differences in computed tax results is being
investigated.  But it is important to remember that this c13 sample
produces no differences in federal income tax liabilities when
processed by the Tax-Calculator and Internet-TAXSIM.  This means that
the tax differences reported above reflect differences in output from
Internet-TAXSIM and the taxcalc.sas program.


Second comparison: methods and results
--------------------------------------

...