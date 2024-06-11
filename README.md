# Credit_Card_Dashboard
PowerBI Credit Card DashBoard

Process:
I. SQL Queries

MY SQL...
		
Create a database 
CREATE DATABASE ccdb;


-- 1. Create cc_detail table(containing Credit card data)

CREATE TABLE cc_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);

-- 2. Create Cust_ detail(containing Customers data)

CREATE TABLE cust_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);


3. Import Data from excel to MY SQL Database using Table Data Import Wizard.

Import Cusomter.csv to cust_detail table
and Import Creditcard.csv to cc_detail table

Note: As mySQL accept dateformat as (yyyy-mm-dd) forat make sure to change the format of dates in Creditcard.csv files in the same format.



4. To simulate real time sceanario i have inserted more data in the both table from csv to database table.


I  first imported data from csv file to a new tables (cust_add,cc_add) in the database and then inserted that data to my default tables.
and to import data in these two tables i used same Table data import wizard.



Insert into cust_detail 
select * from cust_add (newtable)

insert into cc_detail
select * from cc_add (new table)



II.  Power BI

1. Import Data from My SQL Database used localhost as (127.0.0.1:3306) and database name as (ccdb).

And Defined the realtionship between the tables. (Client Num)

Dax Query to group Customers Age (calculated columns)
-->
Age Group1 = 
SWITCH(
    TRUE(),
    'ccdb cust_detail'[Customer_Age] <= 30, "less than 30",
    'ccdb cust_detail'[Customer_Age] <= 40, "30-40",
    'ccdb cust_detail'[Customer_Age] <= 50, "40-50",
    'ccdb cust_detail'[Customer_Age] <= 60, "50-60",
    'ccdb cust_detail'[Customer_Age] > 60, "More than 60"
) 

 To sort the data in visuals in proper way like (less than 30,30-40,40-50 and so on) I created another calculated column  

-->
agesort = 
SWITCH(
    TRUE(),
    'ccdb cust_detail'[Age Group1] = "less than 30", 1,
    'ccdb cust_detail'[Age Group1] = "30-40", 2,
    'ccdb cust_detail'[Age Group1] = "40-50", 3,
    'ccdb cust_detail'[Age Group1] = "50-60", 4,
    'ccdb cust_detail'[Age Group1] = "More than 60", 5
)

--->
Income Group 

IncomeGroup = SWITCH(
 TRUE(),
 'public cust_detail'[income] < 35000, "Low",
 'public cust_detail'[income] <70000, "Mid",
 'public cust_detail'[income] >= 70000, "High",
 "unknown"
)

----> 
Week_ year

Week-Year = "Week - "&WEEKNUM('ccdb cc_detail'[Week_Start_Date])

and to sort it in ascending orders we have created another calcualted column

Weeksort = WEEKNUM('ccdb cc_detail'[Week_Start_Date])

---->

Revenue

Revenue = 'ccdb cc_detail'[Total_Trans_Amt] + 'ccdb cc_detail'[Interest_Earned] + 'ccdb cc_detail'[Annual_Fees]

we can calculate new column or create a new measure for this using (it's upto you no difference)

Revenuemeasure = SUM('ccdb cc_detail'[Interest_Earned]) + SUM('ccdb cc_detail'[Annual_Fees]) + SUM('ccdb cc_detail'[Total_Trans_Amt]) 


---->
Currentweek_revenue

Current_week_Reveneue = CALCULATE(
 SUM('public cc_detail'[Revenue]),
 FILTER(
 ALL('public cc_detail'),
 'public cc_detail'[Weeksort] = MAX('public cc_detail'[weeksort])))


Previousweek_reveneue

Previous_week_Reveneue = CALCULATE(
 SUM('public cc_detail'[Revenue]),
 FILTER(
 ALL('public cc_detail'),
 'public cc_detail'[weeksort] = MAX('public cc_detail'[weeksort)-1)) 

----->

Week on Week Growt Revenue

WoW Growth % = DIVIDE([CurrentWeek_Revenue]-[PrevioustWeek_Revenue],[PrevioustWeek_Revenue])

Make sure to format the Wow Growth % as decimalnumber with two digits and convert it to percentage.

...............................

Now After All Neccessary Measures and Calculated Columns are Made. I made all neccessary Visuals and then did formating

III. Powerpoint 

To have a nice background i made a Svg image in powerpoint and used as background in pober BI


**Insights:**

Project Insights - Week 53 (31st Dec)

WoW Change:
Total Revenue increased by 28.8%
Transaction Amount & Count increased by 35.04% & 3.39%
Customer count increased by 12.8%

Revenue YTD:
Overall revenue is $57M 
Total transaction amount is $8M
Total interests are $46M
Gender Contribution:
Male customers contribute $31M to revenue, while female customers contribute $26M.
Credit Card Insights:
Blue & Silver credit cards account for 93% of overall transactions.
Regional Impact:
TX, NY & CA contribute to 68% of overall revenue.
Activation & Deliquient Rate
Activation Rate:57.46%
Overall declination rate is 6.06%.


Thank You! 
