# Library_Management_System_SQL_Project
## Project Overview

**Project Title** : Library Management System                                                                                                                                                                       
**Level** : Intermediate                                                                                                                                                                                            
**Database** : `library_system_db`

This project showcases a Library Management System built using SQL. It covers table creation, data management, CRUD operations, and advanced queries, highlighting skills in database design and SQL programming.

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

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
  



