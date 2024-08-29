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
![ERD](https://github.com/Bhushan148/Library-Management-System/blob/main/Screenshot%202024-08-28%20115609.png)

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

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1: Create a New Book Record**  
"978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```
**Task 2: Update an Existing Member's Address**

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

**Task 3: Delete a Record from the Issued Status Table**  
Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
DELETE FROM issued_status
WHERE   issued_id =   'IS121';
```

**Task 4: Retrieve All Books Issued by a Specific Employee**  
Select all books issued by the employee with emp_id = 'E101'.
```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101'
```

**Task 5: List Members Who Have Issued More Than One Book**  
Use GROUP BY to find members who have issued more than one book.

```sql
SELECT
    issued_emp_id,
    COUNT(*)
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1
```

### 3. CTAS (Create Table As Select)

**Task 6: Create Summary Tables:**  
Used CTAS to generate new tables based on query results - each book and total book_issued_cnt.

```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status as ist
JOIN books as b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```


### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

**Task 7. Retrieve All Books in a Specific Category**:

```sql
SELECT * FROM books
WHERE category = 'Classic';
```

**Task 8: Find Total Rental Income by Category**:

```sql
SELECT 
    b.category,
    SUM(b.rental_price),
    COUNT(*)
FROM 
issued_status as ist
JOIN
books as b
ON b.isbn = ist.issued_book_isbn
GROUP BY 1
```

**Task 9: List Members Who Registered in the Last 180 Days**:
```sql
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

**Task 10: List Employees with Their Branch Manager's Name and their branch details**:

```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id
```

**Task 11. Create a Table of Books with Rental Price Above a Certain Threshold**:
```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;
```

**Task 12: Retrieve the List of Books Not Yet Returned**
```sql
SELECT * FROM issued_status as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;
```

**Task 13: Identify Members with Overdue Books**  
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```sql
SELECT 
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    -- rs.return_date,
    CURRENT_DATE - ist.issued_date as over_dues_days
FROM issued_status as ist
JOIN 
members as m
    ON m.member_id = ist.issued_member_id
JOIN 
books as bk
ON bk.isbn = ist.issued_book_isbn
LEFT JOIN 
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE 
    rs.return_date IS NULL
    AND
    (CURRENT_DATE - ist.issued_date) > 30
ORDER BY 1
```

**Task 14: Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).

```sql
DELIMITER $$

CREATE PROCEDURE update_book_status_on_return()
BEGIN
    -- Update book status to 'Yes' for all returned books
    UPDATE books b
    JOIN issued_status ist ON b.isbn = ist.issued_book_isbn
    JOIN return_status rs ON ist.issued_id = rs.issued_id
    SET b.status = 'Yes'
    WHERE rs.return_date IS NOT NULL;
    
    -- Optional: Notify about the update
    SELECT 'Book statuses have been updated successfully.' AS message;
END $$

DELIMITER ;

-- 1. Insert Five Issued Records
INSERT INTO issued_status (issued_id, issued_member_id, issued_book_isbn, issued_date) VALUES
('IS201', 'C120', '978-3-16-148410-0', '2024-08-15'),
('IS202', 'C121', '978-0-307-58837-1', '2024-08-14'),
('IS203', 'C122', '978-1-86197-876-9', '2024-08-13'),
('IS204', 'C123', '978-0-7432-7356-5', '2024-08-12'),
('IS205', 'C124', '978-0-553-21311-7', '2024-08-11');

-- 2. Insert Three Return Records
INSERT INTO return_status (return_id, issued_id, return_date, book_quality) VALUES
('RS201', 'IS201', CURRENT_DATE, 'Good'),
('RS202', 'IS202', CURRENT_DATE, 'Fair'),
('RS203', 'IS203', CURRENT_DATE, 'Excellent');

-- 3. Verify Current Book Status
SELECT b.isbn, b.book_title, b.status
FROM books b
JOIN issued_status ist ON b.isbn = ist.issued_book_isbn
JOIN return_status rs ON ist.issued_id = rs.issued_id
WHERE rs.return_date IS NOT NULL;

-- 4. Call the Stored Procedure
CALL update_book_status_on_return();

-- 5. Verify Updated Book Status
SELECT b.isbn, b.book_title, b.status
FROM books b
JOIN issued_status ist ON b.isbn = ist.issued_book_isbn
JOIN return_status rs ON ist.issued_id = rs.issued_id
WHERE rs.return_date IS NOT NULL;
```

**Task 15: Branch Performance Report**  
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
CREATE TABLE branch_reports AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(DISTINCT ist.issued_id) AS number_of_books_issued,
    COUNT(DISTINCT rs.return_id) AS number_of_books_returned,
    SUM(bk.rental_price) AS total_revenue
FROM issued_status ist
JOIN employees e ON e.emp_id = ist.issued_emp_id
JOIN branch b ON e.branch_id = b.branch_id
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
JOIN books bk ON ist.issued_book_isbn = bk.isbn
GROUP BY b.branch_id, b.manager_id;

-- Retrieve data from branch_reports table to display the results
SELECT * FROM branch_reports;
```

**Task 16: CTAS: Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 6 months.

```sql
-- Drop the table if it exists
DROP TABLE IF EXISTS active_members;

-- Create the 'active_members' table with members who have issued at least one book in the last 2 months
CREATE TABLE active_members AS
SELECT *
FROM members
WHERE member_id IN (
    SELECT DISTINCT issued_member_id
    FROM issued_status
    WHERE issued_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
);

-- Retrieve data from the 'active_members' table to verify the results
SELECT * FROM active_members;
```


**Task 17: Find Employees with the Most Book Issues Processed**  
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

```sql
SELECT 
    e.emp_name,
    COUNT(ist.issued_id) AS no_book_issued
FROM issued_status AS ist
JOIN employees AS e
    ON e.emp_id = ist.issued_emp_id
JOIN branch AS b
    ON e.branch_id = b.branch_id
GROUP BY e.emp_name
ORDER BY no_book_issued DESC
LIMIT 3;
```

**Task 18: Identify Members Issuing High-Risk Books**  
Write a query to identify members who have issued books more than twice with the status "damaged" in the books table. Display the member name, book title, and the number of times they've issued damaged books.    
```sql
SELECT
    m.member_name,
    bk.book_title,
    COUNT(ist.issued_id) AS times_issued
FROM issued_status AS ist
JOIN members AS m
    ON m.member_id = ist.issued_member_id
JOIN books AS bk
    ON bk.isbn = ist.issued_book_isbn
WHERE bk.status = 'damaged'
GROUP BY m.member_name, bk.book_title
HAVING COUNT(ist.issued_id) > 2
ORDER BY m.member_name, times_issued DESC;
```


**Task 19: Stored Procedure**
Objective:
Create a stored procedure to manage the status of books in a library system.
Description:
Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows:
The stored procedure should take the book_id as an input parameter.
The procedure should first check if the book is available (status = 'yes').
If the book is available, it should be issued, and the status in the books table should be updated to 'no'.
If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.

```sql

-- Drop the existing procedure if it exists
DROP PROCEDURE IF EXISTS issue_book;

DELIMITER $$

CREATE PROCEDURE issue_book(
    IN p_issued_id VARCHAR(10),
    IN p_issued_member_id VARCHAR(30),
    IN p_issued_book_isbn VARCHAR(30),
    IN p_issued_emp_id VARCHAR(10)
)
BEGIN
    DECLARE v_status VARCHAR(10);
    DECLARE v_message VARCHAR(255);

    -- Check book availability
    SELECT status INTO v_status
    FROM books
    WHERE isbn = p_issued_book_isbn;

    IF v_status = 'yes' THEN
        -- Issue the book and update its status
        INSERT INTO issued_status (issued_id, issued_member_id, issued_date, issued_book_isbn, issued_emp_id)
        VALUES (p_issued_id, p_issued_member_id, CURRENT_DATE, p_issued_book_isbn, p_issued_emp_id);

        UPDATE books
        SET status = 'no'
        WHERE isbn = p_issued_book_isbn;

        -- Set success message
        SET v_message = CONCAT('Success: Book issued. ISBN: ', p_issued_book_isbn);
    ELSE
        -- Set error message
        SET v_message = CONCAT('Error: Book unavailable. ISBN: ', p_issued_book_isbn);
    END IF;

    -- Output the message
    SELECT v_message AS message;
END $$

DELIMITER ;

-- Test Cases
CALL issue_book('IS158', 'C108', '978-0-09-957807-9', 'E104'); -- Should return success message
CALL issue_book('IS156', 'C108', '978-0-375-41398-8', 'E104'); -- Should return error message

-- View Results
SELECT * FROM books WHERE isbn = '978-0-553-29698-2';
SELECT * FROM books WHERE isbn = '978-0-375-41398-8';
SELECT * FROM issued_status;
SELECT * FROM books;

```



**Task 20: Create Table As Select (CTAS)**
Objective: Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines.

Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include:
    The number of overdue books.
    The total fines, with each day's fine calculated at $0.50.
    The number of books issued by each member.
    The resulting table should show:
    Member ID
    Number of overdue books
    Total fines

```sql
-- Create the new table for overdue books and fines
CREATE TABLE overdue_books_summary AS
SELECT
    ist.issued_member_id AS member_id,
    COUNT(*) AS number_of_overdue_books,
    SUM(DATEDIFF(CURDATE(), ist.issued_date) * 0.50) AS total_fines
FROM issued_status ist
LEFT JOIN return_status rs
    ON ist.issued_id = rs.issued_id
WHERE rs.return_date IS NULL
  AND DATEDIFF(CURDATE(), ist.issued_date) > 30
GROUP BY ist.issued_member_id;

-- Retrieve data from the new table to verify the results
SELECT * FROM overdue_books_summary;
```

## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

