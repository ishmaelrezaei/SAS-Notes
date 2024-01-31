```sas
/* Assign a library reference to the XLSX file */
libname JobLib XLSX "/home/u63764672/DATA.xlsx";

/* Display the contents of the library */
proc contents data=JobLib.sheet1;
run;

/* Let's define a dataset name MyData
it goes to the WORK library*/
data MyData;
	set JobLib.sheet1;
run;

/* shows the content */
proc contents data=MyData;

/* just prints the data */
proc print data=MyData;

/*************************************************************
Note: if you upload a sas file, you do not need to read
it or creat a library for it because it is already existed
*************************************************************/

/* If condition in SAS */
data MyData;
	set JobLib.SHEET1;
	length AgeModi $ 12; 
	if Age >= 25 and Age <= 50 then AgeModi = "Young";
	else if Age >50 then AgeModi = "Old";
	else AgeModi = "Very Young";
run;
	
/* Excluding some observations */
proc means data=MyData;
	where Age not = 34 and name not = 'Kenneth';
	Title 'New table';

/* shows distinct values frequency */
proc freq data=MyData nlevels;


/* To request only one column */
proc freq data=MyData nlevels;
	table  Age / nocum;
run;


proc print data = sashelp.baseball;
proc freq data=sashelp.baseball;
 	table Division*League/norow; /*we can remove norow */


/* shows some stats values of data */
proc means data = MyData;

/* To specify what to show */
proc means data=mydata median q1 maxdec=2 nmiss n mean max mode std var;

/* to show only two variables */
proc means data=mydata median q1 maxdec=2 nmiss n mean max mode std var;
	var Income Experience;
	
/* we can use "output" to save the results */
proc means data=mydata median q1 maxdec=2 nmiss n mean max mode std var;
 output out = ResultsStat;
 run;

proc means data=mydata median q1 maxdec=2 nmiss n mean max mode std var;
	class Age;
	var Experience Income;

/* Performs seperate analysis for each level */
proc means data=sashelp.baseball;
	class League;
	
proc means data=sashelp.baseball;
	class League;
	var nHits nRuns;
	
	
	
/* Same as CLASS except the data must be sorted first by the
variables you list in the by statement */
proc sort data=sashelp.baseball out = sorted_baseball;
	by League;
	
proc means data=sorted_baseball;
	by League;
	var nHits nRuns;
	 
	 
/* to see the number of element for each level we can use tabulate */
proc tabulate data = sashelp.baseball;
	class Division;
	table Division;
run;

/* to classify them based on league */
proc tabulate data = sashelp.baseball;
	class Division League;
	table Division, League;
run;

/* if we add another variable, it shows them like 3D matrix */
proc tabulate data = sashelp.baseball;
	class Division League Team;
	table Division, League, Team;
run;

/* Assume we compare levels of two variables based on another varible */
proc tabulate data = sashelp.baseball;
	class Division League;
 	var nOuts;  /*this is the third variable and must be defined first */
	table Division, Mean*nOuts*(League All);
run;

proc print data=MyData;
proc tabulate data = MyData;
	class Age Experience;
 	var Income;  /*this is the third variable and must be defined first */
	table Age, Mean*Income*(Experience All);
run;

/****************** Report **********************/
proc print data=sashelp.baseball;
/* it sums the numeric vars up, even for data char */
proc report data=sashelp.baseball;
	column CrHome CrHits; 
/* However, for char variables you can get one row per observation */
proc report data=sashelp.baseball;
	column Division CrHits;
run;

/* order by */
proc report data=sashelp.baseball;
	define CrHits / ORDER 'CrHits ordered';
run;

/* Group by */
proc report data=sashelp.baseball;
	column League Salary;
	define League / GROUP; /* it sums up the salary */
run;

proc report data=sashelp.baseball MISSING;
	column League Salary;
	define League / GROUP; /* it sums up the salary */
run;

proc report data=sashelp.baseball MISSING;
	column League Salary;
	define League / GROUP;
	define Salary / Analysis MEAN; /* it group by mean of salary */
run;

/****************** DO / DO UNTIL *********************/
Data DoData;
	Do Bonous_Rate = 0.01 to 0.2 by 0.01;
	Min_Salary = 40000;
	count = 1;
	
	Do until (Bonous_Rate <= 0.2);
	Salary_Bonous = (Bonous_Rate*Min_Salary) + Min_Salary;
	count = count + 1;
	end;
	output;
	end;
run;

/* correlation */
proc corr data=sashelp.baseball;
	var CrHits;
	with CrRuns;
run;

proc corr data=sashelp.baseball plots=(scatter);
	var CrHits nBB;
	with CrRuns;
run;

proc corr data=sashelp.baseball;
run;

/**************** ANOVA ********************/

/* When p-value<0.05, we reject the null hypothesis which means 
there is difference between two groups */
proc anova data=sashelp.bweight;
	class MomEdLevel;
	model Visit = MomEdLevel;
run;

/* From previous result we know that there is difference between at least two groups
  To figure out how which groups/levels are different we can modify the code to.. */
proc anova data=sashelp.bweight;
	class MomEdLevel;
	model Visit = MomEdLevel;
	means MomEdLevel / tukey;
run;
