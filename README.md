CREATE DATABASE company_db;
USE company_db;

CREATE TABLE department (
    dname VARCHAR(50) NOT NULL,
    dnumber INT PRIMARY KEY,
    mgr_ssn INT,
    mgr_start_date DATE,
    UNIQUE (dname)
);

CREATE TABLE employee (
    fname VARCHAR(20) NOT NULL,
    minit CHAR(1),
    lname VARCHAR(20) NOT NULL,
    ssn INT PRIMARY KEY,
    bdate DATE,
    address VARCHAR(100),
    sex CHAR(1),
    salary DECIMAL(10,2),
    super_ssn INT,
    dno INT,
    FOREIGN KEY (super_ssn) REFERENCES employee(ssn),
    FOREIGN KEY (dno) REFERENCES department(dnumber)
);

ALTER TABLE department
ADD FOREIGN KEY (mgr_ssn)
REFERENCES employee(ssn);

CREATE TABLE dept_locations (
    dnumber INT NOT NULL,
    dlocation VARCHAR(50) NOT NULL,
    FOREIGN KEY (dnumber) REFERENCES department(dnumber)
);

CREATE TABLE project (
    pname VARCHAR(50) NOT NULL,
    pnumber INT PRIMARY KEY,
    plocation VARCHAR(50),
    dnum INT NOT NULL,
    UNIQUE (pname),
    FOREIGN KEY (dnum) REFERENCES department(dnumber)
);

CREATE TABLE works_on (
    essn INT NOT NULL,
    pno INT NOT NULL,
    hours DECIMAL(4,2),
    FOREIGN KEY (essn) REFERENCES employee(ssn),
    FOREIGN KEY (pno) REFERENCES project(pnumber)
);

CREATE TABLE dependent (
    essn INT NOT NULL,
    dependent_name VARCHAR(50) PRIMARY KEY,
    sex CHAR(1),
    bdate DATE,
    relationship VARCHAR(20),
    FOREIGN KEY (essn) REFERENCES employee(ssn)
);

INSERT INTO department (dname, dnumber, mgr_ssn, mgr_start_date)
VALUES
('computer science',1,NULL,'2024-01-10'),
('information science',2,NULL,'2024-02-15'),
('electronics',3,NULL,'2024-03-20');

INSERT INTO employee
(fname,minit,lname,ssn,bdate,address,sex,salary,super_ssn,dno)
VALUES
('rahul','k','sharma',101,'1990-05-15','bengaluru','m',75000.00,NULL,1),
('priya','s','patil',102,'1992-08-20','mysuru','f',70000.00,101,2),
('arjun','r','reddy',103,'1991-11-25','hyderabad','m',72000.00,101,3);

UPDATE department
SET mgr_ssn = 101
WHERE dnumber = 1;

UPDATE department
SET mgr_ssn = 102
WHERE dnumber = 2;

UPDATE department
SET mgr_ssn = 103
WHERE dnumber = 3;

INSERT INTO dept_locations
VALUES
(1,'bengaluru'),
(2,'mysuru'),
(3,'hyderabad');

INSERT INTO project
(pname,pnumber,plocation,dnum)
VALUES
('college management system',201,'bengaluru',1),
('hospital management system',202,'mysuru',2),
('smart traffic system',203,'hyderabad',3);

INSERT INTO works_on
(essn,pno,hours)
VALUES
(101,201,40.00),
(102,202,35.00),
(103,203,38.50);

INSERT INTO dependent
(essn,dependent_name,sex,bdate,relationship)
VALUES
(101,'anita','f','1993-02-10','wife'),
(102,'rohan','m','2018-07-21','son'),
(103,'lakshmi','f','1995-11-18','wife');

SELECT e.fname, e.minit, e.lname, e.address
FROM employee e, department d
WHERE e.dno = d.dnumber
AND d.dname = 'computer science';

SELECT p.pnumber,
       p.dnum,
       e.lname,
       e.address,
       e.bdate
FROM project p,
     department d,
     employee e
WHERE p.dnum = d.dnumber
AND d.mgr_ssn = e.ssn
AND p.plocation = 'bengaluru';

SELECT e.fname,
       e.minit,
       e.lname,
       s.fname AS supervisor_fname,
       s.minit AS supervisor_minit,
       s.lname AS supervisor_lname
FROM employee e,
     employee s
WHERE e.super_ssn = s.ssn;

SELECT e.fname,
       e.minit,
       e.lname
FROM employee e,
     dependent d
WHERE e.ssn = d.essn
AND e.fname = d.dependent_name
AND e.sex = d.sex;

SELECT DISTINCT e.ssn
FROM employee e,
     works_on w,
     project p
WHERE e.ssn = w.essn
AND w.pno = p.pnumber
AND p.plocation IN ('bengaluru', 'mysuru', 'hyderabad');

SELECT DISTINCT essn
FROM works_on
WHERE pno IN
(
    SELECT pnumber
    FROM project
    WHERE plocation IN ('bengaluru', 'mysuru', 'hyderabad')
);

SELECT
    SUM(salary) AS total_salary,
    MAX(salary) AS maximum_salary,
    MIN(salary) AS minimum_salary,
    AVG(salary) AS average_salary
FROM employee;
