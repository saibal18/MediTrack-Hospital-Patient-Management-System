# MediTrack --- Hospital Patient Management System

A relational database project built with **MySQL** to demonstrate how
hospital data can be organized, connected, queried, and reported using
SQL.

## Table of Contents

-   [Project Overview](#project-overview)
-   [Objectives](#objectives)
-   [Technology Stack](#technology-stack)
-   [Database Design](#database-design)
-   [Relationships](#relationships)
-   [Features and SQL Concepts](#features-and-sql-concepts)
-   [Sample Queries](#sample-queries)
-   [Getting Started](#getting-started)
-   [Suggested Repository Structure](#suggested-repository-structure)
-   [Sample Data](#sample-data)
-   [Limitations](#limitations)
-   [Learning Outcomes](#learning-outcomes)
-   [References](#references)

## Project Overview

**MediTrack** is a sample Hospital Patient Management System database
designed to organize information about patients, doctors, appointments,
prescriptions, and billing. The project uses related tables and
primary/foreign keys to keep data structured and support common
reporting tasks.

This is a **SQL/database project**, not a web or desktop application.
The data included in the project is sample data for learning and
demonstration.

## Objectives

-   Store patient and doctor details in separate tables.
-   Connect appointments to the relevant patient and doctor.
-   Record prescription details associated with appointments.
-   Track bill amounts, payment methods, and payment status.
-   Practise SQL filtering, sorting, joins, grouping, aggregate
    functions, subqueries, indexes, and views.
-   Demonstrate relational database design and referential integrity.

## Technology Stack

-   **Database:** MySQL
-   **SQL environment:** MySQL Shell
-   **Language:** SQL

The uploaded shell session records MySQL Shell 8.0.46. The SQL
statements are intended for a compatible MySQL server.

## Database Design

The database is named `MediTrack` and contains five core tables.

  --------------------------------------------------------------------------
  Table             Purpose            Primary key         Important
                                                           relationships
  ----------------- ------------------ ------------------- -----------------
  `Patients`        Stores patient     `Patient_ID`        Referenced by
                    profile and                            `Appointments`
                    contact details                        

  `Doctors`         Stores doctor      `Doctor_ID`         Referenced by
                    names,                                 `Appointments`
                    specializations,                       
                    fees, and                              
                    experience                             

  `Appointments`    Connects patients  `Appointment_ID`    Foreign keys to
                    and doctors to                         `Patients` and
                    appointment dates                      `Doctors`
                    and statuses                           

  `Prescriptions`   Stores medicines,  `Prescription_ID`   Foreign key to
                    dosage, and                            `Appointments`
                    duration for an                        
                    appointment                            

  `Billing`         Stores bill        `Bill_ID`           Foreign key to
                    amounts and                            `Appointments`
                    payment details                        
  --------------------------------------------------------------------------

### Table columns

**Patients** - `Patient_ID` --- primary key - `Name`, `Age`, `Gender`,
`Contact`, `Email`, `Address`

**Doctors** - `Doctor_ID` --- primary key - `Name`, `Specialization`,
`Fee`, `Experience_Years`

**Appointments** - `Appointment_ID` --- primary key - `Patient_ID` ---
foreign key referencing `Patients.Patient_ID` - `Doctor_ID` --- foreign
key referencing `Doctors.Doctor_ID` - `Appointment_Date`, `Status`

**Prescriptions** - `Prescription_ID` --- primary key - `Appointment_ID`
--- foreign key referencing `Appointments.Appointment_ID` - `Medicine`,
`Dosage`, `Duration_Days`

**Billing** - `Bill_ID` --- primary key - `Appointment_ID` --- foreign
key referencing `Appointments.Appointment_ID` - `Amount`,
`Payment_Method`, `Payment_Status`

### Relationships

-   **Patients → Appointments:** one-to-many. A patient can have
    multiple appointments.
-   **Doctors → Appointments:** one-to-many. A doctor can attend
    multiple appointments.
-   **Appointments → Prescriptions:** one-to-many. An appointment can
    have multiple prescription lines.
-   **Appointments → Billing:** intended as one-to-one in the project
    design. The current schema has a foreign key but does not declare
    `Appointment_ID` as `UNIQUE` in `Billing`, so the database does not
    itself enforce one bill per appointment.

## Features and SQL Concepts

The project scripts and report demonstrate examples of:

-   Database and table creation (`CREATE DATABASE`, `CREATE TABLE`)
-   Inserting records (`INSERT`)
-   Retrieving and filtering data (`SELECT`, `WHERE`)
-   Sorting and limiting results (`ORDER BY`, `LIMIT`)
-   Joining related tables (`JOIN`)
-   Grouping and aggregation (`GROUP BY`, `HAVING`, `COUNT`, `SUM`,
    `AVG`, `MAX`)
-   Subqueries
-   Date filtering and date functions
-   Indexes, including an index on `Appointments.Patient_ID` and a
    unique index on `Patients.Email`
-   Views, including a `HighValueBills` view for bills greater than 700
-   Primary keys and foreign keys

## Sample Queries

The following examples are based on queries recorded in the project. Run
them after creating and populating the tables.

### 1. Find appointments after a given date

``` sql
SELECT *
FROM Appointments
WHERE Appointment_Date > '2024-01-08';
```

### 2. Find payments made by credit card

``` sql
SELECT *
FROM Billing
WHERE Payment_Method = 'Credit Card';
```

### 3. Display the five doctors with the highest consultation fees

``` sql
SELECT *
FROM Doctors
ORDER BY Fee DESC
LIMIT 5;
```

### 4. Retrieve medicines prescribed to a patient

``` sql
SELECT pr.Medicine
FROM Prescriptions AS pr
JOIN Appointments AS a
  ON pr.Appointment_ID = a.Appointment_ID
JOIN Patients AS p
  ON a.Patient_ID = p.Patient_ID
WHERE p.Name = 'Rohit Sharma';
```

### 5. Find bills above the average bill amount

``` sql
SELECT *
FROM Billing
WHERE Amount > (
    SELECT AVG(Amount)
    FROM Billing
);
```

### 6. Count appointments for each patient

``` sql
SELECT Patient_ID, COUNT(*) AS Total_Appointments
FROM Appointments
GROUP BY Patient_ID;
```

### 7. Calculate total billing by payment method

``` sql
SELECT Payment_Method, SUM(Amount) AS Total_Revenue
FROM Billing
GROUP BY Payment_Method;
```

### 8. Find appointments that have more than one prescription line

``` sql
SELECT Appointment_ID
FROM Prescriptions
GROUP BY Appointment_ID
HAVING COUNT(Prescription_ID) > 1;
```

### 9. Create a view for higher-value bills

``` sql
CREATE VIEW HighValueBills AS
SELECT *
FROM Billing
WHERE Amount > 700;
```

Query the view:

``` sql
SELECT * FROM HighValueBills;
```

## Getting Started

### Prerequisites

-   MySQL Server installed and running.
-   MySQL Shell installed.
-   The project SQL files saved locally.

### 1. Connect to MySQL

Open MySQL Shell and connect to your local server:

``` text
\connect root@localhost:3306
```

Switch to SQL mode:

``` text
\sql
```

Enter your MySQL password if prompted. Do not put your password in the
project files or GitHub repository.

### 2. Create and select the database

``` sql
CREATE DATABASE MediTrack;
USE MediTrack;
```

If the database already exists, use `USE MediTrack;` rather than
creating it again.

### 3. Run the SQL scripts in order

After organizing the project into clean SQL files, run them in this
order:

1.  `meditrack_schema.sql` --- creates the tables.
2.  `meditrack_data.sql` --- inserts the sample records.
3.  `meditrack_queries.sql` --- runs example queries, indexes, and views
    as appropriate.

The filenames above are a recommended repository layout. If your files
use different names, substitute those names. Make sure table definitions
run before inserts, and inserts run before queries that expect populated
data.

**Important:** The uploaded `26.10.26MySQL Shell.sql` file is a captured
shell session that includes prompts and a recorded syntax-error attempt,
rather than a clean SQL script. Before using it as an executable GitHub
script, remove the MySQL Shell prompts, status messages, query outputs,
and failed command attempt. Keep only the valid SQL statements, in
execution order.

## Suggested Repository Structure

``` text
MediTrack-Hospital-Patient-Management-System/
├── README.md
├── meditrack_schema.sql
├── meditrack_data.sql
├── meditrack_queries.sql
├── docs/
│   └── project-report.pdf
└── screenshots/
    ├── patients-table.png
    ├── doctors-table.png
    └── query-output.png
```

This is a suggested structure. Use only files that you have actually
created and include screenshots if you want to showcase the output.

## Sample Data

The report and shell transcript show the following sample dataset:

  Table               Sample record count
  ----------------- ---------------------
  `Patients`                           20
  `Doctors`                            15
  `Appointments`                       10
  `Prescriptions`                      13
  `Billing`                            10

The appointment examples use dates in January 2024. The data is
illustrative and should not be treated as real patient information or
medical advice.

## Limitations

-   The project demonstrates database operations through SQL; it does
    not include a graphical user interface or application backend.
-   The included data is sample data.
-   The schema has a foreign key from `Billing` to `Appointments`, but
    it does not enforce the intended one-bill-per-appointment rule with
    a `UNIQUE` constraint.
-   This educational project is not a production-ready hospital
    information system and does not implement authentication, role-based
    access control, audit logging, or production healthcare privacy
    safeguards.

## Learning Outcomes

Through this project, you can demonstrate practical understanding of
relational database design, table relationships, primary and foreign
keys, data retrieval, multi-table joins, aggregate reporting,
subqueries, indexes, and views in MySQL.

## References

-   [MySQL Documentation](https://dev.mysql.com/doc/)
-   [W3Schools SQL Tutorial](https://www.w3schools.com/sql/)
-   [SQLBolt Interactive SQL Lessons](https://sqlbolt.com/)

------------------------------------------------------------------------

**Project:** Hospital Patient Management System\
**Database:** `MediTrack`\
**Tools:** MySQL, MySQL Shell, SQL
