# HR_Employee_Analysis  
In this project we are analyzing the employee data and trying to find trend from it using SQL.  
The dataset is downloaded from Kaggle. I have done initial data cleaning and compiled other tables data into one main_data table in Excel.
There are 15000 records or ROWS and 12 COLUMNS or variables or attributes. The size of dataset is the main reason I decided to clean it in excel.
As I had already used JOIN function in SQL in my UK_Accidents_2016_Casestudy, I have focused more on Windows Functions and CTE(Common Table Expression) in this analysis.


## 1. Distribution of number of employees on Salary Categories

SELECT sum(case when salary_category='low' then 1 else 0  end) as total_low_salary,  
sum(case when salary_category='medium' then 1 else 0 end) as total_med_salary,  
sum(case when salary_category='high' then 1 else 0 end) as total_high_salary
FROM all_data  
  
![image](https://user-images.githubusercontent.com/116425101/202634988-a86fa1cf-5f7d-4243-b309-b998e0c6491b.png)
  
so out of 14999 employee, 8.25% drew the higher salary, 43% drew medium range salary and 48.75% falls into the lower salary range.  

Now let's find % of employee left in each salary range

## 2. % of Employee left in each Salary Categories

