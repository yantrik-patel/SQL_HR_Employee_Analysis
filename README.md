# HR_Employee_Analysis

![7eK3](https://user-images.githubusercontent.com/116425101/202744716-f474360f-075e-492e-ac7a-85bd8dc553a3.gif)

In this project, we are analyzing the employee data and trying to find trend in attrition using SQL.  
The dataset is downloaded from Kaggle. I have done initial data cleaning and compiled other tables data into one main_data table in Excel.
There are 15000 records or ROWS and 12 COLUMNS or variables or attributes. The size of dataset is the main reason I decided to clean it in Excel.
  
Let's Go!

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

We will go **_TEMPORARY TABLE_** way :)  

Created a TEMPORARY TABLE for extracting the total number of employee left in each salary category then joined it with main table having total employee data to reach the salary category wise attrition percentage.
  
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
## 3. Average Salary amount in each salary category  
  
SELECT salary_category, round(avg(salary_amount)) as avg_salary  
FROM all_data  
GROUP BY salary_category   
ORDER BY avg_salary desc  

![image](https://user-images.githubusercontent.com/116425101/202887360-2b996a2f-e3a5-4f7f-9041-98ce6e854845.png)




---

## 4. All Departments attrition percentage in descending order   
Again we will go **_TEMPORARY TABLE_** way. You might be thinking....  
![1zYm](https://user-images.githubusercontent.com/116425101/202886173-c26cc154-d1ec-45d9-9482-98097dd8958c.gif)


For extracting the total number of employee left in each department then joined it with main table having total employee data to reach the department wise attrition percentage.  

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

## 5. Satsfaction Level and Monthly hours distribution for all departments  
  
SELECT department, ROUND(AVG(satisfaction_level),2) as avg_satisfaction, ROUND(AVG(average_montly_hours),2)as avg_monthly_hour  
FROM all_data  
GROUP BY department  
ORDER BY avg_satisfaction DESC  

![image](https://user-images.githubusercontent.com/116425101/202724740-6f3c3fa5-e63d-47e5-aa8e-fda0fd0199dc.png)
  
  
The average satisfaction is measured between 0 and 1. 0 being not satisfied and 1 being very satisfied.  
From the above table, we can say the average satisfaction level is almost same among the departments but the value 0.6 can be imporved further by the organization.
Average monthly hours are identical for all the departments.  

---

## 6. After How many years from joining date, employees are most likely to leave?  
  
SELECT DISTINCT(time_spend_company), COUNT(emp_Id) as emp_count  
FROM all_data  
WHERE status='Left'  
GROUP BY time_spend_company  
ORDER BY time_spend_company DESC  
  
![image](https://user-images.githubusercontent.com/116425101/202736130-9d212981-3e8b-49a8-bc16-bbbee33ab491.png)
  
  Looks like after 3 years employees leaves the organization.  

---  
  
## 7. Top 3 Employees drawing maximum salary and their status(Left or Present)?  
  
We need to use **_CTE and Windows Function(RANK)_** to get the results  
  
WITH rank_table AS  
(  
SELECT department, emp_id, salary_amount, status,  
RANK() over (PARTITION BY department ORDER BY salary_amount DESC) as ranking  
  
FROM all_data  
)

SELECT *  
FROM rank_table  
WHERE ranking<= 3  
  
![image](https://user-images.githubusercontent.com/116425101/202857696-7016d100-04b6-4e0c-ba5e-17ae7f016e8b.png)
  
From the results we got, we can say that employees drawing highest salary are most likely to stay.
  
---  

## 8. Departments having maximum number of promotion in descending order  
  
SELECT department, count(*) as total_promoted  
FROM all_data  
WHERE promotion_last_5years = 'promoted'  
GROUP BY department  
ORDER BY total_promoted DESC  

![image](https://user-images.githubusercontent.com/116425101/202859553-cc0806cb-bea0-4c72-84e9-528b832d5b41.png)
  
IT Guys be like  

![4HZ](https://user-images.githubusercontent.com/116425101/202860152-98132b9f-69c2-46f2-801c-383aad1c80c5.gif)
  
---  

## 9. Promoted employee Status(Present or Left)?   
We need to use **__Case satement with aggregation function(SUM)__** to find present and left employee count   

SELECT count(*) as total_promoted,  
sum(case when status = 'Left' then 1 end) as promoted_left_count,  
sum(case when status = 'present' then 1 end) as promoted_present_count,  
round((sum(case when status = 'Left' then 1 end) * 100/count( * )),2) as left_promoted_attr_pct  
  
FROM all_data  
WHERE promotion_last_5years = 'Promoted'  
  
![image](https://user-images.githubusercontent.com/116425101/202886845-4a6bdb06-e5c4-4da6-b60f-98a77adb1823.png)

As can be seen, promoted employees have higher chances of staying in organization. They have only 6% attrition.  
  
---  
# Observations  
 The HR, Accounting and Techinical departments have above 25% attrition rate. Attrition is more in lower salary range. Most of the employees are more likely to leave after spending 3 years at the organization. And they are mostly not promoted. Our organization can focus on these factors while designing the solutions to reduce the attrition.  
   
 # I hope you liked the analysis. Thank you!
  




  


