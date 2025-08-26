# Lesson 1.3: Raw SQL in Python

## Course Summary
Execute raw SQL queries in Python using database cursors and connection objects. Learn the fundamental patterns for running SQL commands, fetching results, and handling data types between SQL and Python. Build CRUD functions and OOP patterns for database operations.

## Learning Objectives
- Execute SQL queries using cursor objects
- Understand connection vs cursor object differences
- Fetch results with fetchone(), fetchall(), and fetchmany()
- Handle SQL parameters safely to prevent injection
- Build CRUD functions and OOP classes
- Convert between SQL and Python data types

## Key Topics
- **Connection vs Cursor Objects**:
  - Connection: Database session, manages transactions, creates cursors
  - Cursor: Executes queries, fetches results, maintains query state
- Cursor objects and query execution
- Result fetching strategies and memory management
- Parameterized queries and SQL injection prevention
- **CRUD Function Patterns**:
  - Functional approach: separate functions for create, read, update, delete
  - Parameter handling and error management
- **OOP CRUD Patterns**:
  - Database class with connection management
  - CRUD methods within class structure
  - Context managers for resource cleanup
- Data type mapping between SQL and Python