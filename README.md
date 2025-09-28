# Library_Management_System_SQL_Project
## Project Overview

**Project Title** : Library Management System                                                                                                                                                                       
**Level** : Intermediate                                                                                                                                                                                            
**Database** : `library_system_db`

## Objectives

I have built a Library Management System using SQL where I solved many SQL problems and performed data analysis on the complete system. In this project, I applied advanced concepts like CTAS, stored procedures, subqueries, joins, and other SQL functions to showcase my skills in database design, data handling, and real-world problem solving. All the SQL codes for the problems I solved are provided below for reference

## Project Structure

### 1. Database Setup

- **Database Creation**: Created a database named `library_system_db`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
CREATE DATABASE library_system_db;

-- Creating Table "Branch" 
DROP TABLE IF EXISTS branch;
CREATE TABLE branch(
		branch_id VARCHAR(10) PRIMARY KEY,
		manager_id VARCHAR(10),
		branch_address VARCHAR(50),
		contact_no VARCHAR(10)
);

-- Creating Table "Employees"
DROP TABLE IF EXISTS employees;
CREATE TABLE employees(
		emp_id VARCHAR(10) PRIMARY KEY,
		emp_name VARCHAR(25),
		position VARCHAR(20),
		salary INT,
		branch_id VARCHAR(10)
);

-- Creating Table "Books"
DROP TABLE IF EXISTS books;
CREATE TABLE books(
		isbn VARCHAR(20) PRIMARY KEY,
		book_title TEXT,
		category VARCHAR(25),
		rental_price NUMERIC,
		status VARCHAR(10),
		author VARCHAR(50),
		publisher VARCHAR(50)
);

-- Creating Table "members"
DROP TABLE IF EXISTS members;
CREATE TABLE members(
		member_id VARCHAR(10) PRIMARY KEY,
		member_name VARCHAR(25),
		member_address VARCHAR(20),
		reg_date DATE
);

-- Creating Table "Issued_Status"
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status(
		issued_id VARCHAR(10) PRIMARY KEY,
		issued_member_id VARCHAR(10),
		issued_book_id VARCHAR(60),
		issued_date DATE,
		issued_book_isbn VARCHAR(20),
		issued_emp_id VARCHAR(10)
);

--Creating Table "Return_Status"
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status(
		return_id VARCHAR(15) PRIMARY KEY,
		issued_id VARCHAR(15),
		return_book_name TEXT,
		return_date DATE,
		return_book_isbn VARCHAR(20)
);
```

**Task 1: List Member_id and Member_Name Who Have Issued More Than One Book**
```sql
SELECT ist.issued_member_id, m.member_name,
		COUNT(ist.issued_id) AS no_of_books_issued
FROM members m
JOIN issued_status ist
ON ist.issued_member_id=m.member_id
GROUP BY 1,2
HAVING COUNT(ist.issued_id) > 2;
```
**Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**
```sql
CREATE TABLE book_issued_count AS (
	SELECT b.isbn,b.book_title,
			COUNT(ist.issued_id) AS book_issued_count
	FROM books b
	JOIN issued_status ist
	ON ist.issued_book_isbn=b.isbn
	GROUP BY 1,2
);

SELECT * FROM book_issued_count;
```


