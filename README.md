# HR_Employee_Analysis

![cytonn-photography-n95VMLxqM2I-unsplash](https://user-images.githubusercontent.com/116425101/202741328-667f1a9d-ad17-45e2-9cb9-9678a94b4950.jpg)

In this project we are analyzing the employee data and trying to find trend from it using SQL.  
The dataset is downloaded from Kaggle. I have done initial data cleaning and compiled other tables data into one main_data table in Excel.
There are 15000 records or ROWS and 12 COLUMNS or variables or attributes. The size of dataset is the main reason I decided to clean it in excel.
As I had already used JOIN function in SQL in my UK_Accidents_2016_Casestudy, I have focused more on Windows Functions and CTE(Common Table Expression) in this analysis.

---
---

## 1. Distribution of number of employees on Salary Categories

SELECT sum(case when salary_category='low' then 1 else 0  end) as total_low_salary,  
sum(case when salary_category='medium' then 1 else 0 end) as total_med_salary,  
sum(case when salary_category='high' then 1 else 0 end) as total_high_salary
FROM all_data  
  
![image](https://user-images.githubusercontent.com/116425101/202634988-a86fa1cf-5f7d-4243-b309-b998e0c6491b.png)
  
so out of 14999 employee, 8.25% drew the higher salary, 43% drew medium range salary and 48.75% falls into the lower salary range.  

---

## 2. % of employee left in each salary range  
  
CREATE TEMPORARY TABLE employee_left1  
SELECT salary_category, COUNT(DISTINCT emp_id) as emp_left  
FROM all_data  
WHERE status = 'Left'  
GROUP BY salary_category;  
  
SELECT all_data.salary_category, COUNT(DISTINCT all_data.emp_Id) as total_employee, employee_left1.emp_left, ROUND((employee_left1.emp_left * 100/ COUNT(DISTINCT all_data.emp_Id)),2) as attrition_pct  
FROM all_data  
JOIN employee_left1 ON all_data.salary_category = employee_left1.salary_category  
GROUP BY all_data.salary_category  
ORDER BY attrition_pct DESC  

![image](https://user-images.githubusercontent.com/116425101/202738839-57c3f47f-7b93-4612-ba1d-c2502a1bdee9.png)
  
  
Employees from Lower salary range have the highest attrition rate. Quite obvious, isn't it?



---

## 3. All Departments attrition percentage in descending order  
For this I have created a TEMPORARY TABLE for extracting the total number of employee left in each department then joined it with main table having total employee data to reach the department wise attrition percentage.  

CREATE TEMPORARY TABLE employee_left  
SELECT	department, COUNT(DISTINCT emp_id) as emp_left  
FROM all_data  
WHERE status = 'Left'  
GROUP BY department;


SELECT all_data.department, COUNT(DISTINCT all_data.emp_Id) as total_employee,	employee_left.emp_left, ROUND((employee_left.emp_left * 100/ COUNT(DISTINCT all_data.emp_Id)),2) as attrition_pct  
FROM all_data  
JOIN employee_left ON all_data.department = employee_left.department  
GROUP BY all_data.department  
ORDER BY attrition_pct DESC  
  
![image](https://user-images.githubusercontent.com/116425101/202711807-ff2b69d9-b26d-456d-b6f7-0068bf4268bf.png)

---

## 4. Satsfaction Level and Monthly hours distribution for all departments  
  
SELECT department, ROUND(AVG(satisfaction_level),2) as avg_satisfaction, ROUND(AVG(average_montly_hours),2)as avg_monthly_hour  
FROM all_data  
GROUP BY department  
ORDER BY avg_satisfaction DESC  

![image](https://user-images.githubusercontent.com/116425101/202724740-6f3c3fa5-e63d-47e5-aa8e-fda0fd0199dc.png)
  
  
The average satisfaction is measured between 0 and 1. 0 being not satisfied and 1 being very satisfied.  
From the above table, we can say the average satisfaction level is almost same among the departments but the value 0.6 can be imporved further by the organization.
Average monthly hours are identical for all the departments.  

---

## 5. After How many years from joining date, eployees are most likely to leave?  
  
SELECT DISTINCT(time_spend_company), COUNT(emp_Id) as emp_count  
FROM all_data  
WHERE status='Left'  
GROUP BY time_spend_company  
ORDER BY time_spend_company DESC  
  
![image](https://user-images.githubusercontent.com/116425101/202736130-9d212981-3e8b-49a8-bc16-bbbee33ab491.png)
  
  Looks like after 3 years employees leaves the organization.
  
  
![7eK3](https://user-images.githubusercontent.com/116425101/202744716-f474360f-075e-492e-ac7a-85bd8dc553a3.gif)

