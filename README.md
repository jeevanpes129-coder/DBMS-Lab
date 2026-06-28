create database company_db;
use company_db;
create table department (
dname varchar(50) not null,
dnumber int primary key,
mgr_ssn int,
mgr_start_date date,
unique (dname)
);
create table employee (
fname varchar(20) not null,
minit char(1),
lname varchar(20) not null,
ssn int primary key,
bdate date,
address varchar(100),
sex char(1),
salary decimal(10,2),
super_ssn int,
dno int,
foreign key (super_ssn) references employee(ssn),
foreign key (dno) references department(dnumber)
);
alter table department
add foreign key (mgr_ssn)
references employee(ssn);
create table dept_locations (
dnumber int not null,
dlocation varchar(50) not null,
foreign key (dnumber) references department(dnumber)
);
create table project (
pname varchar(50) not null,
pnumber int primary key,
plocation varchar(50),
dnum int not null,
unique (pname),
foreign key (dnum) references department(dnumber)
);
create table works_on (
essn int not null,
pno int not null,
hours decimal(4,2),
foreign key (essn) references employee(ssn),
foreign key (pno) references project(pnumber)
);
create table dependent (
essn int not null,
dependent_name varchar(50) primary key,
sex char(1),
bdate date,
relationship varchar(20),
foreign key (essn) references employee(ssn)
);
insert into department (dname, dnumber, mgr_ssn, mgr_start_date)
values
('computer science',1,null,'2024-01-10'),
('information science',2,null,'2024-02-15'),
('electronics',3,null,'2024-03-20');
insert into employee
(fname,minit,lname,ssn,bdate,address,sex,salary,super_ssn,dno)
values
('rahul','k','sharma',101,'1990-05-15','bengaluru','m',75000.00,null,1),
('priya','s','patil',102,'1992-08-20','mysuru','f',70000.00,101,2),
('arjun','r','reddy',103,'1991-11-25','hyderabad','m',72000.00,101,3);
update department
set mgr_ssn = 101
where dnumber = 1;
update department
set mgr_ssn = 102
where dnumber = 2;
update department
set mgr_ssn = 103
where dnumber = 3;
insert into dept_locations
values
(1,'bengaluru'),
(2,'mysuru'),
(3,'hyderabad');
insert into project
(pname,pnumber,plocation,dnum)
values
('college management system',201,'bengaluru',1),
('hospital management system',202,'mysuru',2),
('smart traffic system',203,'hyderabad',3);
insert into works_on
(essn,pno,hours)
values
(101,201,40.00),
(102,202,35.00),
(103,203,38.50);
insert into dependent
(essn,dependent_name,sex,bdate,relationship)
values
(101,'anita','f','1993-02-10','wife'),
(102,'rohan','m','2018-07-21','son'),
(103,'lakshmi','f','1995-11-18','wife');
select e.fname, e.minit, e.lname, e.address
from employee e, department d
where e.dno = d.dnumber
and d.dname = 'computer science';
select p.pnumber,
p.dnum,
e.lname,
e.address,
e.bdate
from project p,
department d,
employee e
where p.dnum = d.dnumber
and d.mgr_ssn = e.ssn
and p.plocation = 'bengaluru';
select e.fname,
e.minit,
e.lname,
s.fname as supervisor_fname,
s.minit as supervisor_minit,
s.lname as supervisor_lname
from employee e,
employee s
where e.super_ssn = s.ssn;
select e.fname,
e.minit,
e.lname
from employee e,
dependent d
where e.ssn = d.essn
and e.fname = d.dependent_name
and e.sex = d.sex;
select distinct e.ssn
from employee e,
works_on w,
project p
where e.ssn = w.essn
and w.pno = p.pnumber
and p.plocation in ('bengaluru', 'mysuru', 'hyderabad');
select distinct essn
from works_on
where pno in
(
select pnumber
from project
where plocation in ('bengaluru', 'mysuru', 'hyderabad')
);
select
sum(salary) as total_salary,
max(salary) as maximum_salary,
min(salary) as minimum_salary,
avg(salary) as average_salary
from employee;
