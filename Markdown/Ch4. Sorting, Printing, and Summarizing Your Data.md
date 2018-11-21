# Ch4. Sorting, Printing, and Summarizing Your Data (PROC step)

## 1. PROC Procedure

* PROC Statement

  **General form:**

  > PROC proc-name

* BY Statement

  *<u>Example:</u>*

  > BY State

  run a separate analysis for each state (sorted data).

* TITLE Statement

  **General form:**

  > TITLE 'this is title';

* FOOTAGE Statement

  **General form:**

  > FOOTNOTE3  ’This is the third footnote’;

  Can specify up to 10 titles or footnotes by adding numbers to TITLE and FOOTNOTE.

* LABEL Statement

  **General form:**

  > LABEL var = 'description of this var'

## 2. PROC SORT

**General form:**

> ```SAS
> PROC SORT DATA = data OUT = data_out  NODUPKEY  DUPOUT = extraobs SORTSEQ=option;
> 	BY DESCENDING var;
>     /* ASCENDING by default */
> ```
>
> If you don’t specify ```DATA= option```, then SAS will use the most recently created data set. 
>
> If you don’t specify  ```OUT= option```, then SAS will replace the original data with the newly sorted version.
>
> `NODUPKEY` option tells SAS to eliminate any duplicate observations that have the same
> values for the BY variables. 
>
> If you specify the `DUPOUT= option`, then SAS will put the deleted
> observations in that data set. 

*<u>Example:</u>*

<img src = "https://i.loli.net/2018/11/17/5bef59119f9e6.png" style="zoom:60%">

```SAS
DATA marine;
   INFILE 'c:\MyRawData\Lengths.dat';
   INPUT Name $ Family $ Length @@;
RUN;
* Sort the data;
PROC SORT DATA = marine OUT = seasort NODUPKEY;
   BY Family DESCENDING Length;
PROC PRINT DATA = seasort;
   TITLE 'Whales and Sharks';
RUN;
```

<img src = "https://i.loli.net/2018/11/17/5bef591242fa7.png" style="zoom:50%">

- `SORTSEQ` changing the Sort Order For Character Variables

  - `SORTSEQ = ASCII `  vs  `SORTSEQ = EBCDIC `

    | ASCII | <span style="font-weight:normal">Blank</span> | <span style="font-weight:normal">Numerals</span>          | <span style="font-weight:normal">Uppercase letters</span> | <span style="font-weight:normal">Lowercase letters</span> |
    | :-------------------------------------------- | ----- | ----------------- | ----------------- | ----------------- |
    | **EBCDIC**                                        | Blank | Uppercase letters | Lowercase letters | Numerals          |

  - `SORTSEQ=LINGUISTIC`

    - `SORTSEQ=LINGUISTIC (strength = primary)`

      <left><img src = "https://i.loli.net/2018/11/17/5bef6d3209fd2.png" style="zoom:50%"><left/>

    - `SORTSEQ=LINGUISTIC (Numeric_collation = on)`

      <left><img src = "https://i.loli.net/2018/11/17/5bef6d320cc2b.png" style="zoom:50%"><left/>



## 3. PROC PRINT

`PROC PRINT DATA = data-set NOOBS LABEL;`

> `NOOBS`: no observation number
>
> `LABEL`: print the labels instead of the variable names

| PROC PRINT options   | Explanation                                                  |
| -------------------- | ------------------------------------------------------------ |
| `BY variable-list;`  | output a new section for each value of the presorted BY variables. |
| `ID variable-list;`  | the variables in the ID variable list appear on the left-hand side of the page. |
| `SUM variable-list;` | prints sums for the variables in the list.                   |
| `VAR variable-list;` | which variables to print and the order (default as data set). |

*<u>Example:</u>*

```SAS
data selthree;
	set lecture.Sales;
proc sort data=selthree;
	by Name;
proc print data=selthree;
	by Name;
	sum Quantity;
	var Region Quantity UnitCost;
	title 'Sales by EmplID';
run;
```

<p>
    <img src = "https://i.loli.net/2018/11/17/5bef8f3903e01.png" style="zoom:60%">
    <img src = "https://i.loli.net/2018/11/17/5bef8f3907d07.png" style="zoom:60%">
</p>

* #### Change the Appearance of Printed Values

  - **PROC FORMAT**

    **General form:**

    | Character | Numeric   | Date     |
    | --------- | --------- | -------- |
    | $format.  | formatw.d | formatw. |

    **Create format: **

    ```SAS
    PROC FORMAT;
    	VALUE name range-1 = ‘formatted text 1’
    			   range-2= ‘formatted text 2’
    			   /* ........................................ */
    			   range-n = ‘formatted text n’ ;
    ```

    <u>*Example:*</u>

    ```SAS
    DATA carsurvey;
           INPUT Age Sex Income Color $;
           datalines;
           19 1 14000 Y
           45 1 65000 G
           72 2 35000 B
           31 1 44000 Y
           58 2 83000 W
           ;
    PROC FORMAT;
           VALUE gender 1 = 'Male'
           				2 = 'Female';
           VALUE agegroup 13 -< 20 = 'Teen'
                          20 -< 65 = 'Adult'
                          65 - HIGH = 'Senior';
           VALUE $col  'W' = 'Moon White'
                       'B' = 'Sky Blue'
                       'Y' = 'Sunburst Yellow'
                       'G' = 'Rain Cloud Gray';
    * Print data using user-defined and standard (DOLLAR8.) formats;
    PROC PRINT DATA = carsurvey;
           FORMAT Sex gender. Age agegroup. Color $col. Income DOLLAR8.;
           TITLE 'Survey Results Printed with User-Defined Formats';
    RUN;
    ```

    <img src = "https://i.loli.net/2018/11/17/5bef9c8edeacc.png" style="zoom:60%">

  - FORMAT Statement

    > Format A B DOLLAR8.2 C MMDDYY8.

    Variable A and B use DOLLAR8.2 format, and variable C uses MMDDYY8.

  - PUT Statement

    > Put A DOLLAR8.2 B DOLLAR8.2 C MMDDYY8.

    Place a format after each variable name



  ## 4. REPORT

  ### (1) Simple Report

  In data step:  Instead of using an INFILE statement, you use a FILE statement; instead of INPUT statements, you use PUT statements.

  * **FILE Statement**

    `FILE ‘file – specification’ PRINT;`

    Specifies the current output file for PUT statements.

    `PRINT` directs the output to the same file as the output that is produced by SAS procedures.

  * **PUT Statement**

    Writes lines to the SAS log, to the SAS output window, or to an external location that is specified in the most recent FILE statement.

    *Control spacing:*

    | <span style="font-weight:normal">@n</span> | <span style="font-weight:normal">move to column n</span> |
    | ------------------------------------------ | -------------------------------------------------------- |
    | +n                                         | move n columns                                           |
    | /                                          | skip to the next line                                    |
    | #n                                         | skip to line n                                           |
    | @                                          | hold the current line                                    |
    | " " or ' '                                 | enclose a text                                           |

    <u>*Example:*</u>

    ```SAS
    data _NULL_;
    	set Lec9.Sales;
    	file 'J:\CLASSES\STAT46\Customer.txt' PRINT;
    	title;
    	/* The null TITLE statement tells SAS to eliminate all automatic titles. */
    	PUT @5 'Sales report for ' Name 'from region ' Region 'customer of ' customer 			// @5 'your total sales are ' TotalSales;
    	Put _Page_;
    	/* inserts a page break after each student’s report */
    run;
    ```

    the external file *Customers.txt*:

    <left><img src = "https://i.loli.net/2018/11/17/5befb97b4a0aa.png" style="zoom:60%"><left/>


  ### (2) PROC REPORT

  **General form:**

  ```SAS
  PROC REPORT NOWINDOWS;
  COLUMN variable-list;
  ```

  > COLUMN (similar to VAR): variables to include and in what order.
  >
  > NOWINDOWS: If leave it out, SAS will open the interactive Report window.

  *Defaults:*

| Data with all Numeric variable             | Data with one Character variable |
| ------------------------------------------ | -------------------------------- |
| PROC REPORT will sum the variables (1 row) | one row per observation          |

  * DEFINE Statements

    **General form:**

    > DEFINE variable / options ’column-header’;

    | Changing column headers                          | Keeping Missing data             |
    | ------------------------------------------------ | -------------------------------- |
    | `DEFINE Age/ORDER 'Age at/Admission' WIDTH = 9;` | `PROC REPORT NOWINDOWS MISSING;` |

    you specify the variable name followed by a slash and any options for that particular variable. 

    - Usage Options

      | Options  | Explanation                                                  |
      | -------- | ------------------------------------------------------------ |
      | ACROSS   | creates a column for each unique value of the variable       |
      | ANALYSIS | calculates statistics for the variable (default: sum)        |
      | DISPLAY  | creates one row for each variable in the data set.           |
      | GROUP    | creates one row for each unique value of the variable.       |
      | ORDER    | Creates one row for each observation with rows arranged according to the values of the order variable. |

    **<u>Example:</u>**

    ```SAS
    Proc report data = medic NOWIMDOWS;
    	column Clinic VisitDate Weight HR;
    	define Clinic / group;
    	define weight / analysis;
    	title 'Medical data arranged by Clinic group';
    run;
    
    Proc report data = selthree NOWIMDOWS;
    	column Region Quantity TotalSales;
    	define Region / group;
    	define TotalSales / analysis "Total Sales"; /* change header name */
    	title 'Total Sales by Region';
    run;
    ```

    <p>
        <img src = "https://i.loli.net/2018/11/17/5befc0343f58f.png" style="zoom:45%">
        <img src = "https://i.loli.net/2018/11/17/5befc03478a7d.png" style="zoom:40%">
    </p>

    - Create Summary Report

      | Group Variable                                               | Across Variable                                              |
      | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | `DEFINE group-var / GROUP;`                                  | `DEFINE across-var/ ACROSS;`                                 |
      | <img src = "https://i.loli.net/2018/11/17/5befc1fb1fd31.png" style="zoom:55%"> | <img src = "https://i.loli.net/2018/11/17/5befc1fbb9208.png" style="zoom:48%"> |

      **<u>Example:</u>**

      ```SAS
      DATA natparks;
             INFILE 'c:\MyRawData\Parks.dat';
             INPUT Name $ 1-21 Type $ Region $ Museums Camping;
      RUN;
      * Region and Type as GROUP variables;
      PROC REPORT DATA = natparks NOWINDOWS HEADLINE;
      	COLUMN Region Type Museums Camping;
      	DEFINE Region / GROUP;
      	DEFINE Type / GROUP;
      	TITLE 'Summary Report with Two Group Variables';
      RUN;
      * Region as GROUP and Type as ACROSS with sums;
      PROC REPORT DATA = natparks NOWINDOWS HEADLINE;
      	COLUMN Region Type,(Museums Camping);
      	DEFINE Region / GROUP;
      	DEFINE Type / ACROSS;
      	TITLE 'Summary Report with a Group and an Across Variable';
      RUN;
      ```

      <img src = "https://i.loli.net/2018/11/17/5befc38ce5638.png" style="zoom:50%">

    - Adding Summary Breaks

      **General form:**

      `BREAK location variable / options;`

      *BREAK statement*: adds a break for each unique value of the specified variable.

      `RBREAK location / options;`

      *RBREAK statement*: does the same as BREAK for the entire report.

      `location`: two possible values *BEFORE* or *AFTER* depending on whether you want
      the break to precede or follow that particular section of the report.

      `options`: possible kind of break to insert

      | OL            | <span style="font-weight:normal">draws a line over the break</span> |
      | ------------- | ------------------------------------------------------------ |
      | **PAGE**      | starts a new page                                            |
      | **SKIP**      | inserts a blank line                                         |
      | **SUMMARIZE** | inserts sums of numeric variables                            |
      | **UL**        | draws a line under the break                                 |

      *<u>Example:</u>*

      <left><img src = "https://i.loli.net/2018/11/17/5befc6ece7c90.png" style="zoom:55%"><left/>

      ```SAS
      DATA natparks;
             INFILE 'c:\MyRawData\Parks.dat';
             INPUT Name $ 1-21 Type $ Region $ Museums Camping;
      RUN;
          * PROC REPORT with breaks;
          PROC REPORT DATA = natparks NOWINDOWS HEADLINE;
             COLUMN Name Region Museums Camping;
             DEFINE Region / ORDER;
             BREAK AFTER Region / SUMMARIZE OL SKIP;
      	   RBREAK AFTER / SUMMARIZE OL SKIP;
      	   TITLE 'Detail Report with Summary Breaks';
      RUN;
      ```

      <left><img src = "https://i.loli.net/2018/11/17/5befc6ed356e7.png" style="zoom:45%"><left/>



## 5. PROC MEANS

**General form:**

> PROC MEANS options;

**Summary Options:**

| MAX        | <span style="font-weight:normal">the maximum value</span> |
| ---------- | --------------------------------------------------------- |
| **MIN**    | the minimum value                                         |
| **MEAN**   | the mean                                                  |
| **MEDIAN** | the median                                                |
| **MODE**   | the mode                                                  |
| **N**      | number of non-missing values                              |
| **NMISS**  | number of missing values                                  |
| **RANGE**  | the range                                                 |
| **STDDEV** | the standard deviation                                    |
| **SUM**    | the sum                                                   |

**Control Options:**

| MAXDEC = n  | <span style="font-weight:normal">specifies the number of decimal places to be displayed</span> |
| ----------- | ------------------------------------------------------------ |
| **MISSING** | treats missing values as valid summary groups                |

**Optional Statements:**

| BY variable-list;        | <span style="font-weight:normal">performs separate analyses for each level of listed variables. (sorted data)</span> |
| ------------------------ | ------------------------------------------------------------ |
| **CLASS variable-list;** | Similar but more compact output than BY statement. (no matter sorted or not) |
| **VAR variable-list;**   | specifies which numeric variables to use in the analysis (default all). |

<u>*Example:*</u>

```SAS
proc means data = Learn.Blood;
/* PROC MEANS with no other statements will get statistics for all observations and all numeric variables in the data set. */
run;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf4eed6c2949.png" style="zoom:60%"><left/>


- OUTPUT Statement

  **General form:**

  > OUTPUT OUT = data-set output-statistic-list;

   `data-set`: name of the SAS data set that will contain the results (either temporary or permanent)

   `output-statistic-list`: defines which statistics you want and the associated variable names. 

  - Multiple OUTPUT statements: 

    ```SAS
    OUTPUT OUT = data-set
    statistics (variable – list) = name-list;
    ```

    `variable – list`: defines which of the variables in the VAR list you want to output.

    <u>*Example:*</u>

    ```SAS
    PROC MEANS DATA = zoo NOPRINT;
    	VAR Lions Tigers Bears;
    	/* no BY or CLASS statement, then the data set will have just one observation.*/
    	OUTPUT OUT = zoosum /* two extra variables _TYPE_ and _FREQ_ */
    	MEAN = LionWeight BearWeight TigerWeight;
        /* MEAN (Lions Tigers Bears) = LionWeight BearWeight TigerWeight; */
    RUN;
    ```

    > BY statement: the data set will have one observation for each level of the BY group. 
    >
    > CLASS statements: one observation for each level of interaction of the class variables.

    <u>*Detailed example:*</u>

    ```SAS
    DATA sales;
           INPUT CustomerID $ @9 SaleDate MMDDYY10. Petunia SnapDragon Marigold;
           DATALINES;
           756-01  05/04/2008 120  80 110
           834-01  05/12/2008  90 160  60
           901-02  05/18/2008  50 100  75
           834-01  06/01/2008  80  60 100
           756-01  06/11/2008 100 160  75
           901-02  06/19/2008  60  60  60
           756-01  06/25/2008  85 110 100
           ;
    PROC SORT DATA = sales;
           BY CustomerID;
    * Calculate means by CustomerID, output sum and mean to new data set;
    PROC MEANS NOPRINT DATA = sales;
           BY CustomerID;
           VAR Petunia SnapDragon Marigold;
           OUTPUT OUT = totals  
           MEAN(Petunia SnapDragon Marigold) = MeanPetunia MeanSnapDragon MeanMarigold
           SUM(Petunia SnapDragon Marigold) = Petunia SnapDragon Marigold;
    PROC PRINT DATA = totals;
           TITLE 'Sum of Flower Data over Customer ID';
           FORMAT MeanPetunia MeanSnapDragon MeanMarigold 3.;
    RUN;
    ```

    <left><img src = "https://i.loli.net/2018/11/21/5bf4eed6c1a6f.png" style="zoom:45%"><left/>



## 6. PROC FREQ

[1] Create tables showing the distribution of categorical data values .

[2] Reveal irregularities in your data. (data entry errors)

**General form:**

```SAS
PROC FREQ;
	TABLES variable-combinations;
```

One-way frequency table: `TABLES YearsEducation;`

Cross-tabulation: `TABLES Sex * YearsEducation;` (list the variables separated by an asterisk.)

**PROC FREQ options:** 

options appear after a slash `/` in the TABLES statement.

| LIST                   | <span style="font-weight:normal">Prints cross tabulations in list format rather than grid</span> |
| ---------------------- | ------------------------------------------------------------ |
| **MISSPRINT**          | Includes missing values in frequencies but not in percentages |
| **MISSING**            | Includes missing values in frequencies and in percentages    |
| **NOCOL**              | Suppresses printing of column percentages in cross-tabulations |
| **NOPERCENT**          | Suppresses printing of percentages                           |
| **NOROW**              | Suppresses printing of row percentages in cross-tabulations  |
| **OUT = *data - set*** | Writes a data set containing frequencies                     |

<u>*Example:*</u>

```SAS
title "A Two-way Table of Gender by Blood Type";
proc freq data = Learn.Blood;
	tables Gender * BloodType;
run;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf4f57063d3f.png" style="zoom:60%"><left/>



## 7. PROC TABULATE

Every summary statistic the TABULATE procedure computes can also be produced by other procedures such as PRINT, MEANS, and FREQ, but PROC TABULATE is popular because its reports are pretty.

**General form:**

> ```SAS
> PROC TABULATE;
> 	CLASS classification-variable-list;
> 	TABLE page-dimension, row-dimension, column-dimension;
> ```
>
> `CLASS` tells which variables contain categorical data to be used for dividing observations into groups `TABLE` tells how to organize your table and what numbers to compute. 
>
> *Dimensions:*
> If you specify only one dimension, then that becomes, by default, the column dimension.
> If you specify two dimensions, then you get rows and columns, but no page dimension. 
> If you specify three dimensions, then you get pages, rows, and columns.

***Missing data***: by default, observations are excluded from tables if they have missing values for variables listed in a CLASS statement. If you want, use `PROC TABULATE MISSING;` to include those observations.

*<u>Example:</u>*

```SAS
title "The Effect of Missing Values on Class variables";
proc tabulate data = Learn.Missing format = 4.missing;
	class A B;
	table A ALL B ALL;
run;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf4f949cda91.png" style="zoom:60%"><left/>

#### (1) Add statistics to output

```SAS
PROC TABULATE;
	VAR analysis-variable-list;
	CLASS classification-variable-list;
	TABLE page-dimension, row-dimension, column-dimension;
```

`VAR` tells SAS which variables contain continuous data.

Keywords:

| ALL        | <span style="font-weight:normal">adds a row, column, or page showing the total</span> |
| ---------- | ------------------------------------------------------------ |
| **MAX**    | highest value                                                |
| **MIN**    | lowest value                                                 |
| **MEAN**   | the arithmetic mean                                          |
| **MEDIAN** | the median                                                   |
| **MODE**   | the mode                                                     |
| **N**      | number of non-missing values                                 |
| **NMISS**  | number of missing values                                     |
| **PCTN**   | the percentage of observations for that group                |
| **PCTSUM** | the percentage of a total sum represented by that group      |
| **STDDEV** | the standard deviation                                       |
| **SUM**    | the sum                                                      |

Concatenating, crossing, and grouping:

| Concatenating                             | <span style="font-weight:normal">`TABLE Locomotion Type ALL;`</span> |
| ----------------------------------------- | ------------------------------------------------------------ |
| **Crossing**                              | `TABLE MEAN * Price;`                                        |
| **Crossing, grouping, and concatenating** | `TABLE PCTN *(Locomotion Type);`                             |

*<u>Example:</u>*

```SAS
title "Computing Percentage on a Numerical Value";
proc tabulate data = Learn.Sales;
	class Region;
	var TotalSales;
	table (Region ALL), /* rows */
		  TotalSales * (n*f = 6. sum*f = dollar6. pctum*f = pctfmt7.); 
		  /* columns; f: format */
	keylabel ALL = "All Regions"
			 n = "Number of Sales"
			 sum = "Sum"
			 pctsum = "Percent";
	label TotalSales = "Total Sales";
run;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf4fd3fee9a5.png" style="zoom:60%"><left/>

#### (2) Enhancing the Appearance of output

- `FORMAT = option`

  - to be used in the PROC statement
  - It changes the format of all the data cells in the table

     General form: `PROC TABULATE FORMAT = Comma10.0;`

- `BOX = option`

  - To be used in the TABLE statements 
  -  It allows to write a brief phrase in the upper left corner box of every TABULATE report 

     General form: `TABLE Region, MEAN*sales / BOX = ‘Mean sales by Region’; `

- `MISSTEXT = option`

  - To be used in the TABLE statement. 

  - It specifies a value for SAS to print in empty data cells 

    General form: `TABLE region, MEAN*sales / MISSTEXT = ‘No Sales’; `

<u>*Example:*</u>

```SAS
DATA boats;
	INPUT Name $ 1-12 Port $ 14-20 Locomotion $ 22-26 Type $ 28-30 Price 32-36;
	DATALINES;
	Silent Lady  Maalea  sail  sch 75.00
    America II   Maalea  sail  yac 32.95
    Aloha Anai   Lahaina sail  cat 62.00
    Ocean Spirit Maalea  power cat 22.00
    Anuenue	     Maalea  sail  sch 47.50
	Hana Lei	 Maalea  power cat 28.99
	Leilani		 Maalea  power yac 19.99
	Kalakaua	 Maalea  power cat 29.50
	Reef Runner  Lahaina power yac 29.95
	Blue Dolphin Maalea  sail  cat 42.95
	;
RUN;
* PROC TABULATE report with options;
PROC TABULATE DATA = boats FORMAT=DOLLAR9.2;
	CLASS Locomotion Type;
	VAR Price;
	TABLE Locomotion ALL, MEAN*Price*(Type ALL)/BOX='Full Day Excursions' MISSTEXT='none';
	TITLE;
RUN;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf5005217b3f.png" style="zoom:45%"><left/>

#### (3) Changing Headers

| CLASS variable values  | <span style="font-weight:normal">1. use `PROC FORMAT` to create a user-defined format <br />2. assign
the format to the variable in FORMAT statement.</span> |
| ---------------------- | ------------------------------------------------------------ |
| **BY variable values** | `TABLE Region='', MEAN=''*Sales='Mean Sales by Region';`     |

Multiple Formats for Data Cells: `Variable- name * FORMAT = format.d`

> ```SAS
> TABLE Region, MEAN * (SALES*FORMAT = COMMA8.0 Profit*FORMAT=DOLLAR1010.2);
> ```

*<u>Example (same data):</u>* 

```SAS
PROC FORMAT;
	VALUE $typ  'cat' = 'catamaran'
                'sch' = 'schooner'
                'yac' = 'yacht';
RUN;
PROC TABULATE DATA = boats FORMAT=DOLLAR9.2;
	CLASS Locomotion Type;
	VAR Price;
	FORMAT Type $typ.;
	TABLE Locomotion='' ALL,
		  MEAN=''*Price='Mean Price by Type of Boat'*(Type='' ALL)
		  /BOX ='Full Day Excursions' MISSTEXT='none';
	TITLE;
RUN;
```

<left><img src = "https://i.loli.net/2018/11/21/5bf503e0d63a5.png" style="zoom:45%"><left/>

