# Library Management System using SQL Project

## Project Overview

**Project Title**: Library Management System  
**Database**: `Library_Management_System`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

![Library_project](https://github.com/najirh/Library-System-Management---P2/blob/main/library.jpg)

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Data Analysis & Findings**: Develop complex queries to analyze and retrieve specific data.

# <span style="color:green; font-weight:bold;">Project Structure</span>

## 1. Database Setup
![ERD](https://github.com/najirh/Library-System-Management---P2/blob/main/libranry_erd.png)

- **Database Creation:** Created a database named `Library_Management_System`.
- **Table Creation:** Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

## Library Management System Database Setup

### Objective
- **Ensure Data Integrity:** Correct data types and constraints maintain accurate data and relationships.
- **Optimize Performance:** Efficient data types and indexes improve query speed and system performance.
- **Align Schema:** Adjustments ensure the schema meets application requirements and business rules.
- **Handle Migration Issues:** Resolve inconsistencies from importing data from different systems or devices.

### Importing Tables
When importing tables from external sources or devices, data types and constraints may not fully align with MySQL standards. Adjusting the schema ensures compatibility and efficiency.

### Steps

#### 1. Create and Select Database
```sql
create database library_management_system;
use library_management_system;
```

#### 2. Update Branch Table
```sql
alter table branch
modify branch_id varchar(10),
modify manager_id varchar(10),
modify branch_address varchar(30),
modify contact_no varchar(15);

alter table branch
add constraint primary key (branch_id);

```

#### 3. Update Employees Table
```sql
alter table employees
modify emp_id varchar(10),
modify emp_name varchar(30),
modify position varchar(30),
modify salary decimal(10,2),
modify branch_id varchar(10);

alter table employees
add constraint foreign key (branch_id) references branch(branch_id);

```

#### 4. Update Members Table
```sql
alter table members
modify member_id varchar(10),
modify member_name varchar(30),
modify member_address varchar(30),
modify reg_date date;

alter table members
add constraint primary key (member_id);
```

#### 5. Update Books Table
```sql
alter table books
modify isbn varchar(50),
modify book_title varchar(80),
modify category varchar(30),
modify rental_price decimal(10,2),
modify status varchar(10),
modify author varchar(30),
modify publisher varchar(30);

alter table books
add constraint primary key (isbn);

```

#### 6. Update Issued Status Table
```sql
alter table issued_status
modify issued_id varchar(10),
modify issued_member_id varchar(30),
modify issued_book_name varchar(80),
modify issued_date date,
modify issued_book_isbn varchar(50),
modify issued_emp_id varchar(10);

alter table issued_status
add constraint primary key (issued_id);

alter table issued_status
add constraint fk_issued_member foreign key (issued_member_id) references members(member_id),
add constraint fk_issued_emp foreign key (issued_emp_id) references employees(emp_id),
add constraint fk_issued_book foreign key (issued_book_isbn) references books(isbn);
```

#### 7. Update Return Status Table
```sql
alter table return_status
modify return_id varchar(10),
modify issued_id varchar(30),
modify return_book_name varchar(80),
modify return_date date,
modify return_book_isbn varchar(50);

set foreign_key_checks = 0;

alter table return_status
add constraint fk_return_book foreign key (return_book_isbn) references books(isbn),
add constraint fk_return_issued foreign key (issued_id) references issued_status(issued_id);

set foreign_key_checks = 1;

```

#### 1. Create and Select Database
```sql
CREATE DATABASE library_management_system;
USE library_management_system;
```

