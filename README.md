# Employee Data Base SQL Exploration

## By: Tahsin Jahin Khalid

1. Retrieve the number of employees born in March.

```sql
SELECT COUNT(emp_no) 
FROM employees 
WHERE birth_date LIKE "%03%";
```

```txt
+---------------+
| COUNT(emp_no) |
+---------------+
|         34757 |
+---------------+
1 row in set (0.10 sec)
```

2. Retrieve the total count of male and female employees.

```sql
SELECT gender,COUNT(emp_no) 
FROM employees 
GROUP BY gender;
```

```txt
+--------+---------------+
| gender | COUNT(emp_no) |
+--------+---------------+
| M      |        179973 |
| F      |        120051 |
+--------+---------------+
2 rows in set (0.44 sec)
```

3. Retrieve the year and total hires for the year with the most hires.

```sql
SELECT COUNT(emp_no),YEAR(hire_date)
FROM employees
GROUP BY YEAR(hire_date) 
ORDER BY YEAR(hire_date)
-- only the one with the most hires
-- which is the top
LIMIT 1;
```

```txt
+---------------+-----------------+
| COUNT(emp_no) | YEAR(hire_date) |
+---------------+-----------------+
|         35316 |            1985 |
+---------------+-----------------+
1 row in set (0.30 sec)
```

4. Retrieve the employment ID, birthdate, first name, and last name of the oldest employee. (There could be several oldest employees with the same birthdate.)

```sql
SELECT emp_no, birth_date,
    last_name, first_name
FROM employees
ORDER BY birth_date
LIMIT 6;
```

```txt
+--------+------------+--------------+------------+
| emp_no | birth_date | last_name    | first_name |
+--------+------------+--------------+------------+
| 237571 | 1952-02-01 | Schaad       | Ronghao    |
|  65308 | 1952-02-01 | Pocchiola    | Jouni      |
|  91374 | 1952-02-01 | Kuzuoka      | Eishiro    |
|  87461 | 1952-02-01 | Decaestecker | Moni       |
| 406121 | 1952-02-01 | Remmele      | Supot      |
| 207658 | 1952-02-01 | Whitcomb     | Kiyokazu   |
+--------+------------+--------------+------------+
6 rows in set (0.36 sec)
```

5. Retrieve the first name, last name, and salary of the highest paid female employee.

```sql
SELECT salaries.emp_no,salaries.salary,
 employees.first_name, employees.last_name,
 employees.gender
FROM salaries
INNER JOIN employees
ON salaries.emp_no = employees.emp_no
WHERE gender = "F"
ORDER BY salaries.salary DESC
-- we want this first entry only
LIMIT 1;
```

```txt
+--------+--------+------------+-----------+--------+
| emp_no | salary | first_name | last_name | gender |
+--------+--------+------------+-----------+--------+
| 266526 | 152710 | Weijing    | Chenoweth | F      |
+--------+--------+------------+-----------+--------+
1 row in set (1.41 sec)
```

6. Retrieve all the employees that were born in 1965 and have had at least 3 titles. Show their first name, last name, birthdate, and the number of titles they have had. Display the results in ascending order of the last name.

```sql
SELECT employees.first_name,employees.last_name,
  YEAR(employees.birth_date) AS birth_year,
  COUNT(titles.title) AS titles_held
FROM employees
INNER JOIN titles
ON titles.emp_no = employees.emp_no
WHERE YEAR(employees.birth_date) = 1965
GROUP BY titles.emp_no
HAVING COUNT(titles.title) >= 3
ORDER BY employees.last_name ASC
LIMIT 50;
```

```txt
+------------+-----------+------------+-------------+
| first_name | last_name | birth_year | titles_held |
+------------+-----------+------------+-------------+
| Keiichiro  | Armand    |       1965 |           3 |
| Val        | Barvinok  |       1965 |           3 |
| Theron     | Billawala |       1965 |           3 |
| Chinho     | Bottner   |       1965 |           3 |
| Shaunak    | Brizzi    |       1965 |           3 |
| Shridhar   | Chleq     |       1965 |           3 |
| Yuuichi    | Conde     |       1965 |           3 |
| Nechama    | Gaughan   |       1965 |           3 |
| Nathalie   | Luke      |       1965 |           3 |
| Sailaja    | Negoita   |       1965 |           3 |
| Somnath    | Nitsche   |       1965 |           3 |
| Hideo      | Ratnakar  |       1965 |           3 |
| Shichao    | Thiria    |       1965 |           3 |
| Randi      | Tomescu   |       1965 |           3 |
+------------+-----------+------------+-------------+
14 rows in set (0.34 sec)
```

7. Retrieve the average salary of all the department managers. Round it to 2 decimal places.

```sql
SELECT 
    ROUND(AVG(salaries.salary),2) AS average_salary
FROM dept_manager
INNER JOIN salaries
ON dept_manager.emp_no = salaries.emp_no;
```

```txt
+----------------+
| average_salary |
+----------------+
|       66924.27 |
+----------------+
1 row in set (0.00 sec)
```

8. Retrieve the first name, last name, and department name of all the employees whose first name is ‘Vivian’ and last name begins with ‘A’.

```sql
SELECT employees.first_name,
    employees.last_name,
    departments.dept_name
FROM employees
INNER JOIN dept_emp
ON employees.emp_no = dept_emp.emp_no
INNER JOIN departments
ON dept_emp.dept_no = departments.dept_no
WHERE first_name = "Vivian" AND last_name LIKE "A%"
LIMIT 20;
```

```txt
+------------+-----------+--------------------+
| first_name | last_name | dept_name          |
+------------+-----------+--------------------+
| Vivian     | Alblas    | Development        |
| Vivian     | Adachi    | Production         |
| Vivian     | Adachi    | Quality Management |
| Vivian     | Atchley   | Finance            |
| Vivian     | Alameldin | Production         |
| Vivian     | Angot     | Production         |
| Vivian     | Alencar   | Human Resources    |
+------------+-----------+--------------------+
7 rows in set (0.29 sec)
```

9. Retrieve the current highest salary of each title. Display the results in descending order of the highest salary.

```sql
SELECT 
    titles.title AS Title,
    MAX(salaries.salary) AS HighestCurrentSalary
FROM titles
INNER JOIN salaries
ON titles.emp_no = salaries.emp_no
WHERE titles.to_date = "9999-01-01"
GROUP BY titles.title
ORDER BY HighestCurrentSalary DESC;
```

```txt
+--------------------+----------------------+
| Title              | HighestCurrentSalary |
+--------------------+----------------------+
| Senior Staff       |               158220 |
| Technique Leader   |               144434 |
| Senior Engineer    |               140784 |
| Staff              |               137875 |
| Engineer           |               130939 |
| Assistant Engineer |               117636 |
| Manager            |               106491 |
+--------------------+----------------------+
7 rows in set (7.81 sec)
```

10. Retrieve the former title(s) of the current department manager with the current lowest salary.

```sql
SELECT 
    titles.emp_no,
    titles.title,
    dept_manager.dept_no
FROM titles
INNER JOIN dept_manager
ON titles.emp_no = dept_manager.emp_no 
WHERE titles.emp_no IN (
    -- current managers
    SELECT emp_no FROM dept_manager
    WHERE to_date = "9999-01-01")
AND dept_manager.dept_no = (
    SELECT dept_manager.dept_no
    FROM dept_manager
    INNER JOIN salaries
    ON dept_manager.emp_no = salaries.emp_no
    WHERE dept_manager.to_date = "9999-01-01"
    GROUP BY dept_manager.dept_no
    ORDER BY MAX(salaries.salary)
    LIMIT 1)
-- finally get the manager's previous title
LIMIT 1,1;

```

```txt
+--------+-----------------+---------+
| emp_no | title           | dept_no |
+--------+-----------------+---------+
| 110420 | Senior Engineer | d004    |
+--------+-----------------+---------+
1 row in set (0.00 sec)
```
