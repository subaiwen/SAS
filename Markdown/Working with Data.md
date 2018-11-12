# Working with Data I (Data Step)

## Creating and refining variables.

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



## SAS Functions

**General form:**

> *function-name ( argument, argument, ....)* 

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



## Using IF-THEN/ELSE Statements

- **General form (one action):**

> IF condition THEN action;

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
  > IF condition THEN DO; 
  > 	action;
  > 	action; 
  > END;
  > ```

- **General form (multiple conditions):**

  >  IF condition AND condition THEN action;

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
  > IF condition THEN action;
  > 	ELSE IF condition THEN action; 
  > 	ELSE action;
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

> IF expression THEN DELETE;

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



## SELECT Statement

An alternative to a series of IF and ELSE IF statements

**General form (multiple actions):**

> ```SAS
> Select (select-expression);
> 	when (when-expression) ;
> 	when (when-expression); 
> 	Otherwise (otherwise-expression);
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



## Working with SAS Dates

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

* Expression:

  Creates a variable named EarthDay08, which is equal to the SAS date value for April 22, 2008.

  > EarthDay08 = '22APR2008'D;

* Functions:

  > AGE = INT (YRDIF (BirthDate, TODAY(), 'ACTUAL') );

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