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
**Task 2: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_count**
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
**Task 3: Find Total Rental Income by Category from Books Issued**:
```sql
SELECT b.category, SUM(b.rental_price) AS total_rental_income
FROM books b
JOIN issued_status ist
ON ist.issued_book_isbn=b.isbn
GROUP BY category;
```
**Task 4: List Members Who Registered in the Last 180 Days**:
```sql
SELECT * FROM members
WHERE reg_date >= (CURRENT_DATE- INTERVAL '180 Days');
```
**Task 5: List Employees with Their Branch Manager's Name and their branch details**:
```sql
SELECT * FROM branch;

SELECT e.*, b.manager_id,e1.emp_name AS manager_name,
		b.branch_address,b.contact_no
FROM employees e
JOIN branch b
ON e.branch_id=b.branch_id
JOIN employees e1
ON e1.emp_id=b.manager_id;
```
**Task 6: Retrieve the List of Books Not Yet Returned**
```sql
SELECT * FROM issued_status ist
LEFT JOIN return_status rs
ON rs.issued_id=ist.issued_id
WHERE return_id IS NULL;
```
## Advanced SQL Operations
**Task 7: Identify Members with Overdue Books**  
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.
```sql
SELECT 
		m.member_id,
		m.member_name,
		b.book_title,
		ist.issued_date,
		(CURRENT_DATE-issued_date) AS over_due
FROM issued_status ist
JOIN members m
ON ist.issued_member_id = m.member_id
JOIN books b
ON ist.issued_book_isbn=b.isbn
LEFT JOIN return_status rs
ON ist.issued_id=rs.issued_id
WHERE return_id IS NULL
AND (CURRENT_DATE-issued_date) > 30;
```
**Task 8: Update Book Status on Return**  
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).
```sql
CREATE OR REPLACE PROCEDURE update_book_status(p_return_id VARCHAR(15),
											   p_issued_id VARCHAR(15),
											   p_book_quality VARCHAR(15))
LANGUAGE plpgsql
AS $$
DECLARE
		v_isbn VARCHAR(30);
		
BEGIN
	INSERT INTO return_status(return_id,issued_id,return_date,book_quality)
	VALUES (p_return_id,p_issued_id,CURRENT_DATE,p_book_quality);

	SELECT issued_book_isbn
			INTO v_isbn
	FROM issued_status
	WHERE issued_id=p_issued_id;

	UPDATE books
	SET status='yes'
	WHERE isbn=v_isbn;

	RAISE NOTICE 'Status is successfully updated for issued_id : %',p_issued_id;

END;
$$
-- TESTING FUNCTION update_books_status

SELECT * FROM issued_status
WHERE issued_book_isbn='978-0-7432-7357-1';

SELECT * FROM books
WHERE isbn='978-0-7432-7357-1';

SELECT * FROM return_status
WHERE issued_id='IS136';

CALL update_book_status('RS165','IS136','Good');
```
**Task 9: Branch Performance Report**  
Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.
```sql
CREATE TABLE branch_performance AS (
		SELECT branch_id, COUNT(ist.issued_id) AS no_of_books_issued, 
				COUNT(rs.return_id) AS no_of_books_returned, 
				SUM(b1.rental_price) AS total_revenue
		FROM branch_ b
		JOIN issued_status ist
		ON b.issued_emp_id=ist.issued_emp_id
		LEFT JOIN return_status rs
		ON ist.issued_id=rs.issued_id
		LEFT JOIN books b1
		ON b1.isbn=ist.issued_book_isbn
		GROUP BY branch_id
		ORDER BY branch_id
);

SELECT * FROM branch_performance;
```
**Task 10: CTAS: Create a Table of Active Members**  
Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.
```sql
CREATE TABLE active_members AS (
SELECT * FROM members
WHERE member_id IN (
			SELECT issued_member_id
			FROM issued_status
			WHERE issued_date >= (CURRENT_DATE - INTERVAL '6 months'))
);

SELECT * FROM active_members;
```
**Task 11: Find Employees with the Most Book Issues Processed**  
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.
```sql
SELECT e.emp_id, e.emp_name, 
		COUNT(ist.issued_id) AS books_processed, e.branch_id
FROM employees e
JOIN issued_status ist
ON e.emp_id=ist.issued_emp_id
GROUP BY 1,2,4
ORDER BY books_processed DESC
LIMIT 3;
```
**Task 12: Stored Procedure**                                                                                                                                                                                    
Objective: Create a stored procedure to manage the status of books in a library system.                                                                                                                           Description: Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows:                                                          The stored procedure should take the book_id as an input parameter.                                                                                                                                              
The procedure should first check if the book is available (status = 'yes').                                                                                                                                      
If the book is available, it should be issued, and the status in the books table should be updated to 'no'.                                                                                                      
If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.                                                                  
```sql
CREATE OR REPLACE PROCEDURE issue_of_book(p_issued_id VARCHAR(10),
											p_issued_member_id VARCHAR(10),
											p_issued_book_isbn VARCHAR(20),
											p_issued_emp_id VARCHAR(10))
LANGUAGE plpgsql
AS $$
DECLARE
		v_status VARCHAR(10);

BEGIN
	SELECT status 
			INTO v_status
	FROM books
	WHERE isbn=p_issued_book_isbn;

	IF v_status='yes' THEN
			INSERT INTO issued_status(issued_id,issued_member_id,issued_book_id,
										issued_date,issued_book_isbn,issued_emp_id)
			VALUES (p_issued_id,p_issued_member_id,NULL,CURRENT_DATE,
					p_issued_book_isbn,p_issued_emp_id);

			UPDATE books
			SET status='no'
			WHERE isbn=p_issued_book_isbn;

			RAISE NOTICE 'Record added Successfully for book isbn :%',p_issued_book_isbn;
	ELSE 
		RAISE NOTICE 'Sorry the book cannot be issued as it is not available';

	END IF;
END;
$$

-- Testing the Function
-- 978-0-553-29698-2 - 'yes'
-- 978-0-307-58837-1 - 'no'

CALL issue_of_book('IS175','C108','978-0-553-29698-2','E105');
CALL issue_of_book('IS156','C106','978-0-307-58837-1','E104');

SELECT * FROM books
WHERE isbn='978-0-553-29698-2';

SELECT * FROM issued_status
WHERE issued_id='IS175';
```
**Task 13: Create Table As Select (CTAS)**                                                                                                                                                                       
Objective: Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines.                                                                                                           

Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include:                                           
    The number of overdue books.
    The total fines, with each day's fine calculated at $0.50.
    The number of books issued by each member.
    The resulting table should show:
    Member ID
    Number of overdue books
    Total fines

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

Thank you !


