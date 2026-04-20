Create Database
```sql
CREATE DATABASE transport_db2;
```

Connect to Database
```sql
\c transport_db2;
```

Create Tables
```sql
-- Bus
CREATE TABLE Bus (
    Bus_no INTEGER PRIMARY KEY,
    capacity INTEGER NOT NULL,
    depot_name VARCHAR(20)
);

-- Route
CREATE TABLE Route (
    Route_no INTEGER PRIMARY KEY,
    source VARCHAR(20),
    destination VARCHAR(20),
    no_of_stations INTEGER
);

-- Driver
CREATE TABLE Driver (
    Driver_no INTEGER PRIMARY KEY,
    driver_name VARCHAR(20),
    license_no VARCHAR(20) UNIQUE,
    address VARCHAR(20),
    age INTEGER,
    salary FLOAT
);

-- Relationship Table
CREATE TABLE Bus_Route_Driver (
    Bus_no INTEGER,
    Route_no INTEGER,
    Driver_no INTEGER,
    shift INTEGER CHECK (shift IN (1,2)),
    date_of_duty DATE,
    PRIMARY KEY (Bus_no, Route_no, Driver_no),
    FOREIGN KEY (Bus_no) REFERENCES Bus(Bus_no),
    FOREIGN KEY (Route_no) REFERENCES Route(Route_no),
    FOREIGN KEY (Driver_no) REFERENCES Driver(Driver_no)
);
```

Insert Data
```sql
-- Bus
INSERT INTO Bus VALUES
(10, 40, 'DepotA'),
(11, 50, 'DepotB'),
(12, 40, 'DepotC');

-- Route
INSERT INTO Route VALUES
(1, 'Pune', 'Mumbai', 10),
(2, 'Pune', 'Nashik', 8),
(4, 'Pune', 'Satara', 6);

-- Driver
INSERT INTO Driver VALUES
(101, 'Amit', 'LIC101', 'Addr1', 45, 35000),
(102, 'Riya', 'LIC102', 'Addr2', 30, 28000),
(103, 'Ajay', 'LIC103', 'Addr3', 50, 40000);

-- Relationship
INSERT INTO Bus_Route_Driver VALUES
(10, 1, 101, 1, '2013-09-26'),
(11, 2, 102, 2, '2013-09-27'),
(12, 4, 103, 1, '2013-09-26');
```

1. Display driver names
```sql
SELECT driver_name FROM Driver;
```

2. Route details on which buses of capacity 40 runs
```sql
SELECT DISTINCT r.*
FROM Route r
JOIN Bus_Route_Driver brd ON r.Route_no = brd.Route_no
JOIN Bus b ON brd.Bus_no = b.Bus_no
WHERE b.capacity = 40;
```

3. Insert 2 records into Route table
```sql
INSERT INTO Route VALUES
(5, 'Mumbai', 'Goa', 12),
(6, 'Pune', 'Kolhapur', 9);
```

4. Display bus detail of Route no 4
```sql
SELECT b.*
FROM Bus b
JOIN Bus_Route_Driver brd ON b.Bus_no = brd.Bus_no
WHERE brd.Route_no = 4;
```

1. Create VIEW (drivers whose duty allotted on 26-9-2013)
```sql
CREATE VIEW Driver_Duty_26Sep2013 AS
SELECT d.*
FROM Driver d
JOIN Bus_Route_Driver brd ON d.Driver_no = brd.Driver_no
WHERE brd.date_of_duty = '2013-09-26';
```

View Usage
```sql
SELECT * FROM Driver_Duty_26Sep2013;
```

2. Function using CURSOR (dates driver drove bus)
```sql
CREATE OR REPLACE FUNCTION get_driver_dates(d_name VARCHAR)
RETURNS TABLE (
    driver_name VARCHAR,
    duty_date DATE
) AS $$
DECLARE
    rec RECORD;
    cur CURSOR FOR
        SELECT d.driver_name, brd.date_of_duty
        FROM Driver d
        JOIN Bus_Route_Driver brd ON d.Driver_no = brd.Driver_no
        WHERE d.driver_name = d_name;
BEGIN
    OPEN cur;
    LOOP
        FETCH cur INTO rec;
        EXIT WHEN NOT FOUND;
        driver_name := rec.driver_name;
        duty_date := rec.date_of_duty;
        RETURN NEXT;
    END LOOP;
    CLOSE cur;
END;
$$ LANGUAGE plpgsql;
```

Call Function
```sql
SELECT * FROM get_driver_dates('Amit');
```
