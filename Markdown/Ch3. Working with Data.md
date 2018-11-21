# Ch3. Working with Data (Data Step)

## 1. Creating and refining variables.

Create and redefine variables with assignment statements.

**General form:**

> *variable = expression*

| Type of expression | Assignment statement  |
| ------------------ | --------------------- |
| numeric constant   | Qwerty = 10           |
| character constant | Qwerty = 'ten'        |
| a variable         | Qwerty = OldVar       |
| addition           | Qwerty = OldVar + 10  |
| subtraction        | Qwerty = OldVar - 10  |
| multiplication     | Qwerty = OldVar * 10  |
| division           | Qwerty = OldVar / 10  |
| exponentiation     | Qwerty = OldVar ** 10 |

<u>*Example*:</u>

```SAS
DATA homegarden;
       INFILE 'c:\MyRawData\Garden.dat';
       INPUT Name $ 1-7 Tomato Zucchini Peas Grapes;
       Zone = 14;
       type = 'home';
       Zucchini = Zucchini * 10;
       Total = Tomato + Zucchini + Peas + Grapes;
       PerTom = (Tomato / Total) * 100;
RUN;
PROC PRINT DATA = homegtarden;
       TITLE 'Home Gardening Survey';
RUN;
```



## 2. SAS Functions

**General form:**

> *[function-name] ( argument, argument, ....)* 

SAS has hundreds of functions in general areas including:

| Character                 | Macro                |
| ------------------------- | -------------------- |
| Character String Matching | Mathematical         |
| Date and Time             | Probability          |
| Descriptive Statistics    | Random Number        |
| Distance                  | State and ZIP Code   |
| Financial                 | Variable Information |

<u>*Example*:</u>

```SAS
DATA contest;
       INFILE 'c:\MyRawData\Pumpkin.dat';
       INPUT Name $16. Age 3. +1 Type $1. +1 Date MMDDYY10.
             (Scr1 Scr2 Scr3 Scr4 Scr5) (4.1);
       AvgScore = MEAN(Scr1, Scr2, Scr3, Scr4, Scr5);
       DayEntered = DAY(Date);
       Type = UPCASE(Type);
RUN;
PROC PRINT DATA = contest;
       TITLE 'Pumpkin Carving Contest';
RUN;
```

![Screen Shot 2018-11-12 at 2.38.22 AM.png](https://i.loli.net/2018/11/12/5be9308bdb23f.png)



#### Selected SAS Character Functions

![Screen Shot 2018-11-12 at 3.00.13 AM.png](https://i.loli.net/2018/11/12/5be933785280e.png)

![Screen Shot 2018-11-12 at 3.00.42 AM.png](https://i.loli.net/2018/11/12/5be933c7ad9bc.png)



#### Selected SAS Numeric Function

![Screen Shot 2018-11-12 at 3.04.52 AM.png](https://i.loli.net/2018/11/12/5be9345b42f6d.png)

![Screen Shot 2018-11-12 at 3.05.09 AM.png](https://i.loli.net/2018/11/12/5be9345b1f646.png)



## 3. IF-THEN/ELSE Statements

- **General form (one action):**

> IF [condition] THEN [action];

| Symbolic   | Mnemonic | Meaning                                                      |
| ---------- | -------- | ------------------------------------------------------------ |
| =          | EQ       | equals                                                       |
| ¬=,^=,or~= | NE       | not equal                                                    |
| >          | GT       | greater than                                                 |
| <          | LT       | less than                                                    |
| >=         | GE       | greater than or equal                                        |
| <=         | LE       | less than or equal                                           |
|            | IN       | equal to one in a list<br />('A','B','C') or ('A' 'B' 'C')<br />[separated by commas or spaces] |

- **General form (multiple actions):**

  > ``` SAS
  > IF [condition] THEN DO; 
  > 	[action];
  > 	(action); 
  > END;
  > ```

- **General form (multiple conditions):**

  >  IF [condition] AND [condition] THEN [action];

| Symbolic | Mnemonic | Meaning                                              | Examples                                                     |
| -------- | -------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| &        | AND      | all comparisons must be true                         | IF condition AND condition THEN ...                          |
| \| or !  | OR       | only one comparison must be true                     | IF X AND (Y OR Z) THEN <br />IF (X AND Y ) OR Z THEN ...     |
|          | NOT      | highest precedence; <br />performed before AND or OR | IF (X AND NOT Y ) OR Z THEN ...<br />IF (X AND (NOT Y )) OR Z THEN... |

<u>*Example*:</u>

![Screen Shot 2018-11-12 at 3.35.51 AM.png](https://i.loli.net/2018/11/12/5be93b71f2089.png)

```SAS
DATA sportscars;
       INFILE 'c:\MyRawData\UsedCars.dat';
       INPUT Model $ Year Make $ Seats Color $;
       IF Year < 1975 THEN Status = 'classic';
       IF Model = 'Corvette' OR Model = 'Camaro' THEN Make = 'Chevy';
       IF Model = 'Miata' THEN DO;
          Make = 'Mazda';
		  Seats = 2;
	   END;
 RUN;
 PROC PRINT DATA = sportscars;
       TITLE "Eddy’s Excellent Emporium of Used Sports Cars";
RUN;
```

![Screen Shot 2018-11-12 at 3.24.30 AM.png](https://i.loli.net/2018/11/12/5be938cc01bb1.png)



- **General form (multiple actions):**

  > ```SAS
  > IF [condition] THEN [action];
  > 	ELSE IF [condition] THEN [action]; 
  > 	ELSE [action];
  > ```
  
  > Advantages: 
  >
  > 1. it is more efficient, using less computer time; once an observation satisfies a condition, SAS skips the rest of the series. 
  >
  > 2. ELSE logic ensures that your groups are mutually exclusive so you don’t accidentally have an observation fitting into more than one group.

<u>*Example (Grouping Observations)*:</u>

![Screen Shot 2018-11-12 at 3.34.36 AM.png](https://i.loli.net/2018/11/12/5be93b2c8a142.png)

```SAS
    * Group observations by cost;
    DATA homeimprovements;
       INFILE 'c:\MyRawData\Home.dat';
       INPUT Owner $ 1-7 Description $ 9-33 Cost;
       IF Cost = . THEN CostGroup = 'missing';
          ELSE IF Cost < 2000 THEN CostGroup = 'low';
          ELSE IF Cost < 10000 THEN CostGroup = 'medium';
          ELSE CostGroup = 'high';
    RUN;
    PROC PRINT DATA = homeimprovements;
       TITLE 'Home Improvement Cost Groups';
    RUN;
```

![Screen Shot 2018-11-12 at 3.38.24 AM.png](https://i.loli.net/2018/11/12/5be93c160b153.png)



<u>*Example (Subsetting Data)*:</u>

> IF [expression] THEN DELETE;

![Screen Shot 2018-11-12 at 3.41.09 AM.png](https://i.loli.net/2018/11/12/5be93cba184e7.png)

```SAS
* Choose only comedies;
    DATA comedy;
       INFILE 'c:\MyRawData\Shakespeare.dat';
       INPUT Title $ 1-26 Year Type $;
       IF Type = 'comedy';
       * IF Type = 'tragedy' OR Type = 'romance' OR Type = 'history' THEN DELETE;
    RUN;
    PROC PRINT DATA = comedy;
       TITLE 'Shakespearean Comedies';
    RUN;
```

![Screen Shot 2018-11-12 at 3.42.30 AM.png](https://i.loli.net/2018/11/12/5be93d0821683.png)



## 4. SELECT Statement

An alternative to a series of IF and ELSE IF statements

**General form (multiple actions):**

> ```SAS
> Select [select-expression];
> 	when [when-expression];
> 	when [when-expression]; 
> 	Otherwise [otherwise-expression];
> End;
> ```

<u>*Continued example (Grouping Observations)*:</u>

```SAS
* Group observations by cost;
DATA homeimprovements;
	INFILE 'c:\MyRawData\Home.dat';
	INPUT Owner $ 1-7 Description $ 9-33 Cost;
	*use a select statement
	Select;
	When (Cost = .) CostGroup = 'missing';
    When (Cost < 2000) CostGroup = 'low';
    When (Cost < 10000) CostGroup = 'medium';
    Otherwise CostGroup = 'high';
END;
RUN;
PROC PRINT DATA = homeimprovements;
	TITLE 'Home Improvement Cost Groups';
RUN;
```



## 5. Dates

| Date            | SAS date value |
| --------------- | -------------- |
| January 1, 1959 | -365           |
| January 1, 1960 | 0              |
| January 1, 1961 | 366            |
| January 1, 2008 | 17532          |

* Informat:

  >  INPUT BirthDate ANYDTDTE9.;

* Setting the default century for input:

  > OPTIONS YEARCUTOFF = 1950;

* SAS value for a date:

  Creates a variable named EarthDay08, which is equal to the SAS date value for April 22, 2008.

  > EarthDay08 = '22APR2008'D;

* Functions:

  > AGE = INT (YRDIF (BirthDate, TODAY(), 'ACTUAL') );
  >
  > - Returns the integer portion of the value.
  >
  > AGE = ROUND(YRDIF(BirthDate, TODAY(), 'ACTUAL') ); 
  >
  > - Round to the nearest year.
  >
  > If Missing(AGE) then Date=mdy(Month,15,Year)
  >
  > - Substituting a day of the month when the day value is missing.

* Date interval functions:

  > intck ('qtr', '31Dec2002'd, Today())
  >
  > - Difference in quarters between December 31, 2002 and today.
  >
  > intex ('month', today(), 6)
  >
  > - Compute Dates 6 months after today

![Screen Shot 2018-11-12 at 4.14.37 AM.png](https://i.loli.net/2018/11/12/5be944a15cb16.png)

![Screen Shot 2018-11-12 at 4.14.52 AM.png](https://i.loli.net/2018/11/12/5be944a15a246.png)

<u>*Example*:</u>

![Screen Shot 2018-11-12 at 4.17.10 AM.png](https://i.loli.net/2018/11/12/5be9453343c61.png)

```SAS
DATA librarycards;
	INFILE 'c:\MyRawData\Library.dat' TRUNCOVER;
	INPUT Name $11. + 1 BirthDate MMDDYY10. +1 IssueDate ANYDTDTE10.
   		  DueDate DATE11.;
	DaysOverDue = TODAY() - DueDate;
	Age = INT(YRDIF(BirthDate, TODAY(), 'ACTUAL'));
	IF IssueDate > '01JAN2008'D THEN NewCard = 'yes';
RUN;
PROC PRINT DATA = librarycards;
	FORMAT Issuedate MMDDYY8. DueDate WEEKDATE17.;
	TITLE 'SAS Dates without and with Formats';
RUN;
```

![Screen Shot 2018-11-12 at 4.17.27 AM.png](https://i.loli.net/2018/11/12/5be9453345811.png)





## 6. Retain and Sum Statements

### Retain Statement

Preserve avariable’s value from the previous iteration of the DATA step.

**General form:**

> RETAIN [variable-list] (initial-value);

### Sum Statement

Retains values from the previous iteration, and cumulatively add the value of an expression to a variable.

**General form:**

> SUM([variable], [expression]);

* #### Retain + Sum

> **General form:**
>
> ```SAS
> RETAIN [variable] 0;
> [variable] = SUM([variable], [expression]);
> ```
>
> The variable must be numeric and has the initial value of zero. 
>
> *Example:*
>
> ```SAS
> Data MonthSales;
> 	Input month  sales @@;
> 	retain SumSales 0;
> 	SumSales = sum(SumSales, SumSales + sales);
> 	Datalines;
> 	1 4000 2 5000 3 . 4 5500 5 500 6 6000 7 6500 8 4500
> 	9 5100 10 5700 11 6500 12 7500
> ;
> run;
> proc print data=MonthSales;
> run;
> ```

<u>*Example:*</u>

![Screen Shot 2018-11-13 at 2.51.03 AM.png](https://i.loli.net/2018/11/13/5bea8298489e8.png)

```SAS
* Using RETAIN and sum statements to find most runs and total runs;
    DATA gamestats;
       INFILE 'c:\MyRawData\Games.dat';
       INPUT Month 1 Day 3-4 Team $ 6-25 Hits 27-28 Runs 30-31;
       RETAIN MaxRuns;
       MaxRuns = MAX(MaxRuns, Runs);
       RunsToDate + Runs;
    RUN;
    PROC PRINT DATA = gamestats;
       TITLE "Season's Record to Date";
    RUN;
```

![Screen Shot 2018-11-13 at 2.51.17 AM.png](https://i.loli.net/2018/11/13/5bea82a669bff.png)



## 7. Iterative Processing: Looping
#### General form1:

> ```SAS
> DO [index-variable] = [start #] to [stop #] by [increment];
> 	[action];
> END;
> ```
>
> default increment is 1

*Example1*

```SAS
data Compound;
	Interest = .0375;
	Total = 100;
	do Year = 1 to 3;
		Total + Interest*Total;
		output;
	end;
	format Total dollar10.2;
run;
proc print data=Compound noobs;
run;
```

<left><img src="https://i.loli.net/2018/11/15/5becb0a634ffa.png" style="zoom:60%"><left/>

*Example2: graph an equation*

```SAS
data Equation;
	do X = -10 to 10 by .01;
		Y = 2*X**3 - 5*X**2 + 15*X - 8;
		output;
	end;
run;
proc sgplot;
	series x=X y=Y;
run;
```

<left><img src="https://i.loli.net/2018/11/15/5bec9d4522b86.png" style="zoom:50%"><left/>

*Example3: character values*

```SAS
data Easyway;
	do Group = 'Placebo','Active';
		do Subj = 1 to 5;
			input Score @;
			output;
		end;
	end;
datalines;
250 220 230 210 199
166 183 123 129 234
;
proc print data=Easyway noobs;
run;
```

<left><img src="https://i.loli.net/2018/11/15/5becae208dd67.png" style="zoom:70%"><left/>

#### General form2 (DO UNTIL):

> ```SAS
> DO [index-variable] = [start #] to [stop #] by [increment] until ([codition]);
> 	action;
> END;
> ```

<u>*Example:*</u>

```SAS
data Continue_on;
	Interest = .0375;
	Total = 100;
	do Year = 1 to 100 until (Total ge 200);
		Total + Interest*Total;
		if Total le 150 then continue;
		output;
	end;
	format Total dollar10.2;
run;
proc print data=Continue_on noobs;
run;
```

Equivalent to (output Year 1 to 19):

```SAS
* Do loop
do until (Total ge 200);
		Year + 1
		Total + Interest*Total;
		if Total le 150 then continue;
		output;
end;
```

> ```continue ``` halts further statements within the loop from executing and continues iteration in the Loop. 

<left><img src="https://i.loli.net/2018/11/15/5becb06c7e01e.png" style="zoom:70%"><left/>

```SAS
*without until (condition)
data Continue_on;
	Interest = .0375;
	Total = 100;
	do Year = 1 to 100;
		Total + Interest*Total;
		output;
		if Total ge 200 then leave;
	end;
	format Total dollar10.2;
run;
proc print data=Continue_on noobs;
run;
```

> ```LEAVE``` inside a DO Loop shifts control to the statement following the END statement. At the Bottom of the Loop.

#### General form3 (DO WHILE):

> ```SAS
> DO [index-variable] = [start #] to [stop #] by [increment] while ([codition]);
> 	action;
> END;
> ```

<u>*Equivalent example*</u>:

```SAS
* Do loop
do until (Total less 200);
		Year + 1
		Total + Interest*Total;
		if Total le 150 then continue;
		output;
end;
```



## 8. Subsetting a Data Set

### (1) WHERE

**General forms:**. 

> ```SAS
> WHERE condition;
> ```
>
> WHERE statement can be used in a DATA step or in a PROC step.

| Symbolic   | Mnemonic       | Example                                     |
| ---------- | -------------- | ------------------------------------------- |
| =          | EQ             | WHERE Region = 'Spain';                     |
| ¬=, ~=, ^= | NE             | WHERE Region ~= 'Spain';                    |
| >          | GT             | WHERE Rainfall > 20;                        |
| <          | LT             | WHERE Rainfall < AvgRain;                   |
| >=         | GE             | WHERE Rainfall >= AvgRain + 5;              |
| <=         | LE             | WHERE Rainfall <= AvgRain / 1.25;           |
| &          | AND            | WHERE Rainfall > 20 AND Temp < 90;          |
| \|,!       | OR             | WHERE Rainfall > 20 OR Temp < 90;           |
|            | IS NOT MISSING | WHERE Region IS NOT MISSING;                |
|            | BETWEEN AND    | WHERE Region BETWEEN 'Plain' AND 'Spain';   |
|            | CONTAINS       | WHERE Region CONTAINS 'ain';                |
|            | IN ( list )    | WHERE Region IN ('Rain', 'Spain', 'Plain'); |

- #### KEEP/DROP 

**General forms:**

> ```SAS
> KEEP = variable-list
> ```
>
> *tells SAS which variables to keep* 
>
>  ```SAS
>  DROP = variable-list
>  ```
>
> *tells SAS which variables to drop*
>
> KEEP and DROP options can be used in a DATA step or in a PROC step.

<u>*Example:*</u>

```SAS
data Females;
	set Learn.Survey (keep = ID Gender Age Ques1-Ques5);
	where Gender = 'F';
run;
```

### (2) IF

<u>*Example:*</u>

```SAS
data Males Females;
	set Learn.Survey (drop = ID);
	if Gender = 'F' then output Females;
	else if Gender = 'M' then output Males;
run;
```



## 9. Merging Data Sets

### (1) data sets contain the same variables

<u>*Example:*</u>

```SAS
data One_Two;
	set One Two;
run;
```

<p>
    <img src = "https://i.loli.net/2018/11/15/5becc52a38b96.png" style="zoom:50%">
    <img src = "https://i.loli.net/2018/11/15/5becc52a3a9a4.png" style="zoom:50%">
</p>

<left></left><img src = "https://i.loli.net/2018/11/15/5becc52a9f4f9.png" style="zoom:60%"><left/>

### (2) data sets not contain the same variables

- #### Simple Merge

<u>*Example:*</u>

```SAS
proc sort data=Employee;
	by ID;
proc sort data=Hours;
	by ID;
data Combine;
	merge Employee Hours;
	by ID
run;
```

<p>
    <img src = "https://i.loli.net/2018/11/16/5bedb31b90d26.png" style="zoom:50%">
    <img src = "https://i.loli.net/2018/11/16/5bedb31b95cbd.png" style="zoom:50%">
</p>
<left><img src = "https://i.loli.net/2018/11/17/5bef59119f9e6.png" style="zoom:50%"><left/>


- #### Control Obs. in a Merged Data Set

  - IN

  > IN = option

  <u>*illustrate:*</u>

  ```SAS
  data NEW;
  	merge Employee (in = In_Employee) /* new var., if in Employee, 1 else 0 */
  		  Hours (in = In_Hours) /* new variable, if in Hours, 1 else 0 */
  	by ID;
  	file print; 
  	put ID= In_Employee= In_Hours= Name= JobClass= Hours=;
  	/* PUT lists the values of these variables */
  run;
  ```

  ![Screen Shot 2018-11-15 at 12.53.57 PM.png](https://i.loli.net/2018/11/16/5bedb31c51eab.png)

  <u>*Example:*</u>

  ```SAS
  data In_both Missing_Name;
  	Missing_Hours (drop = NAME); /* drop NAME in the date set Missing_Hours */
  	merge Employee (in = In_Employee) /* new var., if in Employee, 1 else 0 */
  		  Hours (in = In_Hours) /* new variable, if in Hours, 1 else 0 */
  	by ID;
  	if In_Employee & In_Hours then output In_both;
  	else if NOT In_Employee & In_Hours then output Missing_Hours;
  run;
  ```

  <p>
      <img src = "https://i.loli.net/2018/11/16/5bedb31c3c788.png" style="zoom:50%">
      <img src = "https://i.loli.net/2018/11/16/5bedba31c4ff8.png" style="zoom:50%">
  </p>

  - RENAME

  Rename = (old = new)

  <u>*Example:*</u>

  ```SAS
  data Combine;
  	merge A
      	  B (rename = (EmpNo = ID));
  	by ID
  run;
  ```

### (3) Different By Variable Data Type

<u>*Example:*</u>

<p>
    <img src = "https://i.loli.net/2018/11/16/5bedbf5beb219.png" style="zoom:50%">
    <img src = "https://i.loli.net/2018/11/16/5bedbf5beb9ab.png" style="zoom:50%">
</p>

```SAS
data Division1C;
	set Division1(rename=(SS = NumSS));
	SS = put(NumSS,ssn11.);
	drop NumSS;
run;
data Combine;
/* sorted */
	merge Division1C Division2;
	by SS;
run;
```

<u>*Alternative:*</u>

```SAS
data Division2N;
	set Division2(rename=(SS = CharSS));
	SS = input(compress(CharSS,'kd'),9.);
	/* Alternative:
	   SS = input(CharSS,comma11.); */
	drop CharSS;
run;
data Combine;
/* sorted */
	merge Division1 Division2N;
	by SS;
run;
```

### (4) Updating a Master File from a Transaction file

```SAS
proc sort data=Price;
	by ItemCode;
run;
proc sort data=New15Dec2017;
	by ItemCode;
run;
data Price_15Dec2017;
	update Price New15Dec2017;
	by ItemCode;
run;
```

<p>
    <img src = "https://i.loli.net/2018/11/16/5bedfd82d99b2.png" style="zoom:30%">
    <img src = "https://i.loli.net/2018/11/16/5bedfd82d9d87.png" style="zoom:30%">
</p>

<left><img src = "https://i.loli.net/2018/11/16/5bedfd82dc3d4.png" style="zoom:25%"><left/>







