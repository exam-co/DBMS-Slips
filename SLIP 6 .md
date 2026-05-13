```sql
-- =========================
-- Q1) CREATE DATABASE TABLES
-- =========================

CREATE TABLE Student (
    sid INT PRIMARY KEY,
    sname VARCHAR(50),
    class VARCHAR(20)
);

CREATE TABLE Competition (
    cno INT PRIMARY KEY,
    cname VARCHAR(50),
    ctype VARCHAR(50)
);

CREATE TABLE Student_Competition (
    sid INT,
    cno INT,
    rank INT,
    year INT,
    PRIMARY KEY (sid, cno),
    FOREIGN KEY (sid) REFERENCES Student(sid),
    FOREIGN KEY (cno) REFERENCES Competition(cno)
);
```

---

# INSERT RECORDS

```sql
-- =========================
-- INSERT INTO STUDENT
-- =========================

INSERT INTO Student VALUES
(1,'Pooja','SYBCA'),
(2,'Rahul','SYBCA'),
(3,'Sneha','TYBCA'),
(4,'Amit','FYBCA'),
(5,'Neha','SYBCA');

-- =========================
-- INSERT INTO COMPETITION
-- =========================

INSERT INTO Competition VALUES
(101,'Football','Sports'),
(102,'Chess','Indoor'),
(103,'Cricket','Sports'),
(104,'Debate','Academic');

-- =========================
-- INSERT INTO STUDENT_COMPETITION
-- =========================

INSERT INTO Student_Competition VALUES
(1,101,1,2018),
(2,101,2,2018),
(3,102,1,2019),
(4,103,3,2018),
(5,104,1,2020),
(1,102,2,2019),
(2,103,1,2020);
```

---

# Q1 SOLUTION QUERIES

## 1) Display students name

```sql
SELECT sname
FROM Student;
```

---

## 2) Display name of student who took part in 'football'

```sql
SELECT s.sname
FROM Student s
JOIN Student_Competition sc
ON s.sid = sc.sid
JOIN Competition c
ON sc.cno = c.cno
WHERE c.cname = 'Football';
```

---

## 3) List the student detail who got 1st rank

```sql
SELECT s.*
FROM Student s
JOIN Student_Competition sc
ON s.sid = sc.sid
WHERE sc.rank = 1;
```

---

## 4) List competition wise student count

```sql
SELECT c.cname, COUNT(sc.sid) AS student_count
FROM Competition c
JOIN Student_Competition sc
ON c.cno = sc.cno
GROUP BY c.cname;
```

---

# Q2 SOLUTION

## 1) Create View to list name of student belongs to year 2018

```sql
CREATE VIEW student_2018 AS
SELECT s.sname
FROM Student s
JOIN Student_Competition sc
ON s.sid = sc.sid
WHERE sc.year = 2018;
```

### To Display View

```sql
SELECT * FROM student_2018;
```

---

# 2) Stored Function to display details of competition of 'Pooja'

```sql
CREATE OR REPLACE FUNCTION get_pooja_competition()
RETURNS TABLE (
    student_name VARCHAR,
    competition_name VARCHAR,
    competition_type VARCHAR,
    rank INT,
    year INT
)
AS
$$
BEGIN
    RETURN QUERY
    SELECT
        s.sname,
        c.cname,
        c.ctype,
        sc.rank,
        sc.year
    FROM Student s
    JOIN Student_Competition sc
    ON s.sid = sc.sid
    JOIN Competition c
    ON sc.cno = c.cno
    WHERE s.sname = 'Pooja';
END;
$$
LANGUAGE plpgsql;
```

## Execute Function

```sql
SELECT * FROM get_pooja_competition();
```
