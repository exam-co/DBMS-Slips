Create Database

```sql
CREATE DATABASE student_comp_db;
```

Connect to Database
```sql
\c student_comp_db;
```

Create Tables
```sql
-- Student Table
CREATE TABLE Student (
    Sid INTEGER PRIMARY KEY,
    sname VARCHAR(30),
    class VARCHAR(20)
);

-- Competition Table
CREATE TABLE Competition (
    cno INTEGER PRIMARY KEY,
    cname VARCHAR(30),
    ctype VARCHAR(20)
);

-- Relationship Table
CREATE TABLE Student_Competition (
    Sid INTEGER,
    cno INTEGER,
    rank INTEGER,
    year INTEGER,
    PRIMARY KEY (Sid, cno),
    FOREIGN KEY (Sid) REFERENCES Student(Sid),
    FOREIGN KEY (cno) REFERENCES Competition(cno)
);
```
