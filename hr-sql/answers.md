1. Write a SQL query to remove the details of an employee whose first name ends in ‘even’

```[sql]
-- remove constraints from employees.manager_id
update employees
set manager_id = NULL
where manager_id in (
    select employee_id from (
        select employee_id from employees
        where first_name like "%even"
    ) as temp
);

-- remove constraints from departments.manager_id
update departments
set manager_id = NULL
where manager_id in (
    select employee_id from employees
    where first_name like "%even"
);

-- remove the details
delete from employees
where first_name like "%even";
```

---

2. Write a query in SQL to show the three minimum values of the salary from the table

```[sql]
select distinct salary from employees
order by salary asc
limit 3;
```

---

3. Write a SQL query to remove the employees table from the database

```[sql]
-- remove the foreign key constraints from the departments table
alter table departments
drop foreign key departments_ibfk_2;

-- remove the foreign key constraints from the job_history table
alter table job_history
drop foreign key job_history_ibfk_1;

-- drop table employees
drop table employees;
```

---

4. Write a SQL query to copy the details of this table into a new table with table name as Employee table and to delete the records in employees table

```[sql]
-- create a copy
create table Employee as
select * from employees;

-- remove the foreign key constraints from the departments table
alter table departments
drop foreign key departments_ibfk_2;

-- remove the foreign key constraints from the job_history table
alter table job_history
drop foreign key job_history_ibfk_1;

-- truncate employees table
truncate employees;
```

---

5. Write a SQL query to remove the column Age from the table

```[sql]
alter table Employee
drop column age;
```

---

6. Obtain the list of employees (their full name, email, hire_year) where they have joined the firm before 2000

```[sql]
select
    concat_ws(" ", first_name, last_name) as full_name,
    email,
    hire_date
from employees
where year(hire_date) < 2000;
```

---

7. Fetch the employee_id and job_id of those employees whose start year lies in the range of 1990 and 1999

```[sql]
select employee_id, job_id from employees
where year(hire_date) >= 1990 and year(hire_date) <= 1999;
```

---

8. Find the first occurrence of the letter 'A' in each employees Email ID Return the employee_id, email id and the letter position

```[sql]
select employee_id, email, locate("A", email) as a_position from employees
where locate("A", email) > 0;
```

---

9. Fetch the list of employees(employee_id, full name, email) whose full name holds characters less than 12

```[sql]
select
    employee_id,
    concat_ws(" ", first_name, last_name) as full_name,
    email
from employees
where length(concat_ws(" ", first_name, last_name)) < 12;
```

---

10. Create a unique string by hyphenating the first name, last name , and email of the employees to obtain a new field named UNQ_ID Return the employee_id, and their corresponding UNQ_ID

```[sql]
-- crate a new column
alter table employees
add column unq_id varchar(100)

-- populate the column
alter table employees
set unq_id = concat_ws("-", first_name, last_name, email)

-- return the values
select employee_id, unq_id from employees;
```

---

11. Write a SQL query to update the size of email column to 30

```[sql]
alter table employees
modify column email varchar(30);
```

---

12. Write a SQL query to change the location of Diana to London

```[sql]
update locations
set city = "London"
where city = "Diana";
```

---

13. Write a SQL query to find the employee with second and third maximum salary with and without using top/limit keyword

```[sql]
-- with limit and offset
select employee_id, salary from employees
order by salary desc
limit 2 offset 1;

-- without limit and offset
select employee_id, salary
from (
    select
        employee_id,
        salary,
        row_number() over (order by salary desc) as rn
    from employees
) t
where rn in (2, 3);
```

---

14. Fetch all details of top 3 highly paid employees who are in department Shipping and IT

```[sql]
select salary from employees
where department_id in (
    select department_id from departments
    where department_name = "Shipping" or department_name = "IT"
)
order by salary desc
limit 3;
```

---

15. Display employee id and the positions(jobs) held by that employee (including the current position)

```[sql]
select employee_id, group_concat(job_id) from (
    select employee_id, job_id from employees       -- current job position
    union
    select employee_id, job_id from job_history     -- previous job positions
) as combined
group by employee_id;
```

---

16. Display Employee first name and date joined as WeekDay, Month Day, Year

```[sql]
select
    employee_id,
    first_name,
    concat_ws(", ",
        dayname(hire_date),
        concat_ws(" ", monthname(hire_date), day(hire_date)),
        year(hire_date)
    ) as date_joined
from employees;
```

---

17. Find the average salary of all the employees who got hired after 8th January 1996 but before 1st January 2000 and round the result to 3 decimals

```[sql]
select round(avg(salary), 3) as avg_salary from employees
where (
    hire_date > "1996-01-08" and
    hire_date < "2000-01-01"
);
```

---

18. Write a SQL query to find the total salaries of employees in Tokyo, excluding those whose first name is Nancy

```[sql]
select sum(e.salary) as total_salary from employees as e
join departments as d on e.department_id = d.department_id
join locations as l on d.location_id = l.location_id
where l.city = "Tokyo" and e.first_name != "Nancy";
```

---

19. Fetch all details of employees who has salary more than the avg salary by each department.

```[sql]
select * from employees e
where salary > (
    select avg(salary) from employees
    where e.department_id = department_id
);
```

---

20. Write a SQL query to find the number of employees and its location whose salary is greater than or equal to 70000 and less than 100000

```[sql]
select l.city, count(*) as employee_count from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
where e.salary >= 70000 and e.salary < 100000
group by l.city;
```

---

21. Fetch max salary, min salary and avg salary by job and department. Info: grouped by department id and job id ordered by department id and max salary

```[sql]
select
    department_id,
    job_id,
    min(salary) as min_salary,
    max(salary) as max_salary,
    round(avg(salary), 3) as avg_salary
 from employees
group by department_id, job_id
order by department_id, max_salary;
```

---

22. Write a SQL query to find the total salary of employees whose country_id is ‘US’ excluding whose first name is Nancy

```[sql]
select sum(salary) as total_salary from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = d.location_id
where l.country_id = "US" and e.first_name != "Nancy";
```

---

23. Fetch max salary, min salary and avg salary by job id and department id but only for folks who worked in more than one role(job) in a department.

```[sql]
select
    e.department_id,
    e.job_id,
    max(e.salary) as max_salary,
    min(e.salary) as min_salary,
    round(avg(e.salary), 3) as avg_salary
from employees e
where (e.employee_id, e.department_id) in (
    select employee_id, department_id
    from (
        select employee_id, job_id, department_id from employees
        union all
        select employee_id, job_id, department_id from job_history
    ) as combined
    group by employee_id, department_id
    having count(distinct job_id) > 1
)
group by e.department_id, e.job_id;
```

---

24. Display the employee count in each department and also in the same result.

```[sql]
select
    department_id,
    case
        when department_id is NULL then "-"
        else count(*)
    end as total_employees
from employees
group by department_id;
```

---

25. Display the jobs held and the employee count.

```[sql]
select jobs_held, count(*) as employee_count from (
    select employee_id, count(distinct job_id) as jobs_held from (
        select employee_id, job_id from employees
        union
        select employee_id, job_id from job_history
    ) as combined
    group by employee_id
) as per_employee
group by jobs_held
order by jobs_held;
```

---

26. Display average salary by department and country.

```[sql]
select
    d.department_id,
    l.country_id,
    round(avg(salary), 3) as avg_salary
from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
group by d.department_id, l.country_id;
```

---

27. Display manager names and the number of employees reporting to them by countries (each employee works for only one department, and each department belongs to a country)

```[sql]
select
    e.manager_id as manager_id,
    concat_ws(" ", m.first_name, m.last_name) as manager_full_name,
    count(*) as employee_count_under_manager from employees e
join employees m on e.manager_id = m.employee_id
where e.manager_id is not null
group by e.manager_id;
```

---

28. Group salaries of employees in 4 buckets eg: 0-10000, 10000-20000,.. (Like the previous question) but now group by department and categorize it like below.

```[sql]
select
    department_id,
    sum(salary between 0 and 10000) as '0-10000',
    sum(salary > 10000 and salary <= 20000) as '10000-20000',
    sum(salary > 20000 and salary <= 30000) as '20000-30000',
    sum(salary > 30000 and salary <= 40000) as '30000-40000'
from employees
where department_id is not null
group by department_id;
```

---

29. Display employee count by country and the avg salary

```[sql]
select
    count(*) as employee_count,
    country_id,
    round(avg(salary), 3)
from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
group by country_id;
```

---

30. Display region and the number off employees by department

```[sql]
select
    d.department_id,
    coalesce(sum(case
        when r.region_name = 'americas' then 1
    end), '-') as americas,
    coalesce(sum(case
        when r.region_name = 'europe' then 1
    end), '-') as europe,
    coalesce(sum(case
        when r.region_name = 'asia' then 1
    end), '-') as asia
from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
join countries c on l.country_id = c.country_id
join regions r on c.region_id = r.region_id
group by d.department_id;
```

---

31. Select the list of all employees who work either for one or more departments or have not yet joined / allocated to any department

```[sql]
select * from employees;
```

---

32. write a SQL query to find the employees and their respective managers. Return the first name, last name of the employees and their managers

```[sql]
select
    e.first_name as employee_first_name,
    e.last_name as employee_last_name,
    m.first_name as manager_first_name,
    m.last_name as manager_last_name
from employees e
join employees m on e.manager_id = m.employee_id;
```

---

33. .write a SQL query to display the department name, city, and state
    province for each department.

```[sql]
select d.department_name, l.city, l.state_province from departments d
join locations l on d.location_id = l.location_id;
```

---

34. write a SQL query to list the employees (first_name , last_name, department_name) who belong to a department or don't

```[sql]
select e.first_name, e.last_name, d.department_name from employees e
left join departments d on e.department_id = d.department_id;
```

---

35. The HR decides to make an analysis of the employees working in every department. Help him to determine the salary given in average per department and the total number of employees working in a department. List the above along with the department id, department name

```[sql]
select
    e.department_id,
    d.department_name,
    round(avg(salary), 3) as avg_salary,
    count(*) as total_employees
from employees e
join departments d on e.department_id = d.department_id
group by d.department_id;
```

---

36..Write a SQL query to combine each row of the employees with each row of the jobs to obtain a consolidated results. (i.e.) Obtain every possible combination of rows from the employees and the jobs relation

```[sql]
select * from employees
cross join jobs;
```

---

37. Write a query to display first_name, last_name, and email of employees who are from Europe and Asia

```[sql]
select e.first_name, e.last_name, e.email from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
join countries c on l.country_id = c.country_id
join regions r on c.region_id = r.region_id
where r.region_name = "Asia" or r.region_name = "Europe";
```

---

38. Write a query to display full name with alias as FULL_NAME who are from oxford city and their second last character of their last name is 'e' and are not from finance and shipping department.

```[sql]
select concat_ws(" ", e.first_name, e.last_name) as full_name from employees e
join departments d on e.department_id = d.department_id
join locations l on d.location_id = l.location_id
where (
    l.city = "Oxford" and
    substring(e.last_name, -2, 1) = "e" and
    d.department_name != "Finance" and
    d.department_name != "Shipping"
);
```

---

39. Display the first name and phone number of employees who have less than 50 months of experience

```[sql]
select first_name, phone_number from employees
where timestampdiff(month, hire_date, curdate()) < 50;
```

---

40. Display Employee id, first name, last name, hire \_ \_ date and salary for employees who has the highest salary for each hiring year. (For eg: John and Deepika joined on year 2023, and john has a salary of 5000, and Deepika has a salary of 6500. Output should show Deepika’s details only).

```[sql]
select
    e.employee_id,
    e.first_name,
    e.last_name,
    e.hire_date,
    e.salary
from employees e
where e.salary = (
    select max(salary) from employees
    where year(hire_date) = year(e.hire_date)
);
```

---
