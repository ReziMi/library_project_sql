# 📚 Library Management System — SQL Project

This project builds a complete relational database system for managing library operations including branches, employees, members, books, and book issue/return activities.

---

## 🧱 Database Schema

### Tables:

- **branch**: Stores library branches and their manager info.
- **employees**: Stores employee details, linked to branches.
- **members**: Library members with address and registration date.
- **books**: Book catalog with rental info and availability.
- **issued_status**: Tracks book issues (who issued what and when).
- **return_status**: Tracks book returns.

Each table includes primary keys and foreign key relationships to enforce data integrity.

---

## ⚙️ CRUD Operations

- **Insert a new book**  
  ```sql
  INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
  VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
  ```

- **Update member address**  
  ```sql
  UPDATE members
  SET member_address = '125 Oak St'
  WHERE member_id = 'C103';
  ```

- **Delete issued book record**  
  ```sql
  DELETE FROM issued_status
  WHERE issued_id = 'IS121';
  ```

---

## 📊 Data Analysis Tasks

### 1. Retrieve All Books Issued by a Specific Employee
```sql
SELECT * FROM issued_status
WHERE issued_emp_id = 'E101';
```

### 2. List Members Who Have Issued More Than One Book
```sql
SELECT issued_emp_id, COUNT(*)
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1;
```

### 3. Book Issue Summary Table (CTAS)
```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status ist
JOIN books b ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```

### 4. Retrieve Books by Category
```sql
SELECT * FROM books
WHERE category = 'Classic';
```

### 5. Total Rental Income by Category
```sql
SELECT 
    b.category,
    SUM(b.rental_price),
    COUNT(*)
FROM issued_status ist
JOIN books b ON b.isbn = ist.issued_book_isbn
GROUP BY 1;
```

### 6. Members Registered in the Last 180 Days
```sql
SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

### 7. Employees with Branch & Manager Info
```sql
SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees e1
JOIN branch b ON e1.branch_id = b.branch_id    
JOIN employees e2 ON e2.emp_id = b.manager_id;
```

### 8. Expensive Books Table (CTAS)
```sql
CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 7.00;
```

### 9. Books Not Yet Returned
```sql
SELECT * FROM issued_status ist
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;
```

### 10. Overdue Books (More Than 30 Days)
```sql
SELECT
    ist.issued_member_id,
    m.member_name,
    bk.book_title,
    ist.issued_date,
    rs.return_date,
    CURRENT_DATE - ist.issued_date AS over_due_days
FROM issued_status ist
JOIN members m ON m.member_id = ist.issued_member_id
JOIN books bk ON bk.isbn = ist.issued_book_isbn
LEFT JOIN return_status rs ON rs.issued_id = ist.issued_id
WHERE rs.return_date IS NULL
AND (CURRENT_DATE - ist.issued_date) > 30
ORDER BY 1;
```

### 11. Active Members Table (CTAS)
```sql
CREATE TABLE active_members AS
SELECT * FROM members
WHERE member_id IN (
    SELECT DISTINCT issued_member_id
    FROM issued_status
    WHERE issued_date >= CURRENT_DATE - INTERVAL '2 month'
);
```

### 12. Top 3 Employees by Book Issues
```sql
SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) AS no_book_issued
FROM issued_status ist
JOIN employees e ON e.emp_id = ist.issued_emp_id
JOIN branch b ON e.branch_id = b.branch_id
GROUP BY 1, 2
ORDER BY no_book_issued DESC
LIMIT 3;
```

---

## 🛠️ Tech Stack

- SQL (PostgreSQL-compatible syntax)
- Relational Database Design
- CTAS (Create Table As Select)
- Aggregate Functions and Joins
- Date & Time Intervals

---

## 📁 File Structure

```
📦 library_management.sql
├── DROP & CREATE TABLE statements
├── INSERT sample data
├── UPDATE and DELETE examples
├── ANALYTICAL queries
├── CTAS (summary tables)
```

---

## 📌 Use Cases

- Educational project for learning SQL and relational design  
- Case study for data analysis using SQL  
- Backend schema for a real-world library system  

---

## 🏷️ Tags

`#SQL` `#PostgreSQL` `#DataAnalysis` `#LibrarySystem` `#CTAS` `#DatabaseDesign`
