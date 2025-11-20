# Payroll--management
This project is a complete SQL-based Payroll Management System designed to calculate employee salaries, deductions, overtime, and department-wise payroll costs. It demonstrates skills in joins, CTEs, subqueries, CASE statements, window functions, and relational database design.
LOAD DATA INFILE '/path/departments.csv'
INTO TABLE departments
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;

LOAD DATA INFILE '/path/salary_structure.csv'
INTO TABLE salary_structure
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;

LOAD DATA INFILE '/path/employees.csv'
INTO TABLE employees
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;

LOAD DATA INFILE '/path/attendance.csv'
INTO TABLE attendance
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;

SELECT 
    e.emp_id,
    e.first_name,
    e.last_name,
    d.dept_name,
    e.grade,
    e.basic_salary
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;
