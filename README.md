# SQL Window Functions

Data 

EmployeeID	EmployeeName	Department	Salary	HireDate	ManagerID
1	John	IT	60000	3/15/2019	5
2	Anna	HR	75000	5/20/2020	6
3	Mike	IT	70000	1/22/2021	5
4	Sarah	Finance	85000	11/30/2018	7
5	Chris	IT	95000	8/5/2017	NULL
6	Mary	HR	90000	7/19/2019	NULL
7	James	Finance	60000	3/10/2020	NULL

1) Find the cumulative total salary of employees by department. 
Select 
EmployeeId, 
EmployeeName, 
Department, 
Salary, 
SUM(SALARY) OVER(Partition By Department Order By HireDate) as CUMSAL 
From Win 

2) Rank employees within each department based on their salary. 

Select 
EmployeeID, 
EmployeeName, 
Department, 
Salary, 
RANK() OVER(Partition By Department Order By Salary Desc) As Rankdep 
From Win 

3) Find the difference in salary between each employee and the next hired employee in the same department. 

Select  
EMPLOYEEID,EMPLOYEENAME, DEPARTMENT, SALARY, HIREDATE, 
LEAD(SALARY) OVER(PARTITION BY DEPARTMENT ORDER BY HireDate) As NextEmpSalary, 
LEAD(SALARY) OVER(PARTITION BY DEPARTMENT ORDER BY HireDate)- Salary As Differenceinsalary 
From Win 

4) Find the average salary of each department without using GROUP BY. 
Select Department, Salary, 
Avg(salary) Over(Partition By Department) As AVGSALARY 
From Win 

5) List employees and their salary difference from the previous employee hired in the same department. 

Select  
EmployeeID, EmployeeName, Department, Salary,  
COALESCE(LAG(SALARY) Over(Partition By Department Order By Hiredate), 0) As Previousemployeesalary, 
COALESCE(LAG(SALARY) Over(Partition By Department Order By Hiredate), 0)- Salary As SalaryDifference, 
HireDate 
From Win 

6) Rank employees by their hire date within their department, giving the most recently hired employee the highest rank. 

Select 
EmployeeId, EmployeeName, Department, Hiredate, 
RANK() OVER(Partition By Department Order By Hiredate Desc) As RNK 
From Win 

7) Calculate the rolling 3-month salary average for employees based on hire date. 

Select 
EmployeeID, 
EmployeeName, 
Department, 
Salary, 
Hiredate, 
AVG(Salary) OVER (ORDER BY HireDate ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS RollingSalary 
From Win 

8) Find the difference in hire date between an employee and the next hired employee within the same department. 

SELECT EmployeeID,  
EmployeeName,  
Department,  
HireDate, 
LEAD(HireDate) Over(Partition By Department Order By HireDate) as Leadingdate, 
DATEDIFF(DAY, HireDate, LEAD(HireDate) OVER (PARTITION BY Department ORDER BY HireDate)) AS DateDifference 
FROM Win; 

9) Identify the top 2 highest-paid employees in each department. 

  
WITH TOP2 AS 
( 
Select  
EmployeeId, EmployeeName, Department, Salary, 
Dense_RANK() Over(Partition By Department Order By Salary Desc) RNK 
From Win 
) 
SELECT * From TOP2 
Where RNK<=2 

10) Calculate the running total of salaries across all employees ordered by hire date. 

Select 
EmployeeID, 
EmployeeName, 
Department, 
Salary, 
HireDate, 
SUM(SALARY) Over(Order By HireDate) As RunningtotalofSalary 
From WIN 

11) List employees and their percentage contribution to the total salary of their department. 

Select 
EmployeeId, 
EmployeeName, 
Department, 
Salary, 
Sum(Salary) Over(Partition By Department) As TotalDepartmentSalary, 
Round(Cast(Salary As Decimal(10,2))*100/CAST(SUM(Salary) OVER (PARTITION BY Department) AS DECIMAL(10, 2)),2) percentagecontribution 
From WIN 

12) For each employee, find the next employee hired in the same department. 

Select 
EmployeeId, 
EmployeeName, 
Department, 
HireDate, 
Coalesce(LEAD(EmployeeName) OVER(Partition By Department Order By HireDate), 'No Next Employee') As NextHiredEmployee 
From WIN 

13) List the employees and their salary ranks across all departments. 

Select  
EmployeeID, 
EmployeeName,  
Department, 
Salary, 
RANK() OVER(ORDER BY SALARY DESC) As SalaryRank 
From WIN 

14) Find employees who are earning more than the average salary of their department 

WITH MORETHANAVG AS 
( 
Select 
EmployeeID, 
EmployeeName, 
Department, 
Salary, 
AVG(SALARY) OVER(Partition By Department) As AVGSALARY 
From WIN 
) 
Select EmployeeId, 
EmployeeName, 
Salary, 
Department, 
AVGSALARY 
From MORETHANAVG 
Where Salary>AVGSALARY 

15) List employees with their cumulative count of employees hired before them in the same department. 

SELECT  
EmployeeID,  
EmployeeName,  
Department,  
HireDate, 
ROW_NUMBER() OVER (PARTITION BY Department ORDER BY HireDate) - 1 AS CumulativeCount 
FROM WIN; 

 
