# CREATE TABLES

```sql id="t4pq5o"
-- =========================
-- EMPLOYEE TABLE
-- =========================

CREATE TABLE Employee (
    emp_id INT PRIMARY KEY,
    emp_name VARCHAR(30),
    address VARCHAR(50),
    bdate DATE
);

-- =========================
-- INVESTOR TABLE
-- =========================

CREATE TABLE Investor (
    inv_no INT PRIMARY KEY,
    inv_name VARCHAR(30),
    inv_date DATE,
    inv_amt DECIMAL(10,2)
);
```

---

# INSERT RECORDS

```sql id="0tb0zy"
-- =========================
-- INSERT INTO EMPLOYEE
-- =========================

INSERT INTO Employee VALUES
(1,'Rohan','Pune','1999-05-10'),

(2,'Mahesh','Mumbai','1998-08-15'),

(3,'Sneha','Nashik','2000-02-20'),

(4,'Minal','Satara','1997-11-11');

-- =========================
-- INSERT INTO INVESTOR
-- =========================

INSERT INTO Investor VALUES
(101,'Mohan','2024-01-10',25000),

(102,'Riya','2024-02-15',18000),

(103,'Manoj','2024-03-20',30000),

(104,'Amit','2024-04-01',22000);
```

---

# Q1 SOLUTION

# 1) List the names of employees

```sql id="x04kmg"
SELECT emp_name
FROM Employee;
```

---

# 2) List information of investors whose name starts with 'M'

```sql id="bg64ur"
SELECT *
FROM Investor
WHERE inv_name LIKE 'M%';
```

---

# 3) Increase amount of investors by 10%

```sql id="0a9ujq"
UPDATE Investor
SET inv_amt = inv_amt + (inv_amt * 0.10);
```

---

# 4) Delete investor information whose amount is 20,000

```sql id="1v5g9q"
DELETE FROM Investor
WHERE inv_amt = 20000;
```

---

# Q2 SOLUTION

# 1) Trigger to validate investment amount

```sql id="ptfr9s"
-- =========================
-- TRIGGER FUNCTION
-- =========================

CREATE OR REPLACE FUNCTION check_investment()
RETURNS TRIGGER AS
$$
BEGIN

    IF NEW.inv_amt <= 2000 THEN
        RAISE EXCEPTION 'Investment amount must be greater than 2000';
    END IF;

    RETURN NEW;

END;
$$
LANGUAGE plpgsql;
```

---

# CREATE TRIGGER

```sql id="m9yqfa"
CREATE TRIGGER trg_check_investment
BEFORE INSERT
ON Investor
FOR EACH ROW
EXECUTE FUNCTION check_investment();
```

---

# TEST TRIGGER

```sql id="5xqz9j"
INSERT INTO Investor VALUES
(105,'Test','2024-05-10',1000);
```

---

# 2) Stored Function to count number of investors

```sql id="6y8e5i"
CREATE OR REPLACE FUNCTION count_investor(i_name VARCHAR)
RETURNS INT AS
$$
DECLARE

    total INT;

BEGIN

    SELECT COUNT(*)
    INTO total
    FROM Investor
    WHERE inv_name = i_name;

    IF total = 0 THEN
        RAISE NOTICE 'Invalid investor name';
    END IF;

    RETURN total;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql id="z8v4cv"
SELECT count_investor('Mohan');
```
