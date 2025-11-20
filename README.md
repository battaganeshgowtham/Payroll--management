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

SELECT 
    emp_id,
    status,
    COUNT(*) AS days_count
FROM attendance
GROUP BY emp_id, status
ORDER BY emp_id;

SELECT 
    d.dept_name,
    COUNT(e.emp_id) AS total_employees
FROM departments d
LEFT JOIN employees e ON d.dept_id = e.dept_id
GROUP BY d.dept_name;


WITH att AS (
    SELECT 
        emp_id,
        MONTH(work_date) AS month,
        SUM(CASE WHEN status='P' THEN 1 END) AS present_days,
        SUM(overtime_hours) AS total_ot
    FROM attendance
    WHERE work_date BETWEEN '2025-01-01' AND '2025-01-31'
    GROUP BY emp_id
)
SELECT 
    e.emp_id,
    e.first_name,
    e.last_name,
    d.dept_name,
    e.basic_salary,
    (e.basic_salary/30) * a.present_days AS basic_pay,
    (e.basic_salary * s.hra_percent/100) AS hra,
    (e.basic_salary * s.da_percent/100)  AS da,
    (a.total_ot * s.overtime_rate) AS ot_pay,
    ((e.basic_salary/30) * a.present_days) +
    (e.basic_salary * s.hra_percent/100) +
    (e.basic_salary * s.da_percent/100) +
    (a.total_ot * s.overtime_rate) AS gross_salary
FROM employees e
JOIN att a ON e.emp_id = a.emp_id
JOIN salary_structure s ON e.grade = s.grade
JOIN departments d ON e.dept_id = d.dept_id;
WITH gross AS (
    SELECT 
        e.emp_id,
        e.dept_id,
        (e.basic_salary * 1.4) AS approx_gross   
    FROM employees e
)
SELECT *
FROM gross g1
WHERE approx_gross > (
    SELECT AVG(approx_gross)
    FROM gross g2
    WHERE g1.dept_id = g2.dept_id
);

