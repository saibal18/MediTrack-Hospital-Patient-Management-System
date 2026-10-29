# MediTrack — Hospital Patient Management System

> **A MySQL relational database project** demonstrating how hospital records can be organized, connected, queried, and reported using SQL.

![Database](https://img.shields.io/badge/Database-MySQL-4479A1?logo=mysql&logoColor=white)
![Language](https://img.shields.io/badge/Language-SQL-0F766E)
![Project Type](https://img.shields.io/badge/Project-Database%20Only-475569)

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Objectives](#2-objectives)
3. [Technology Stack](#3-technology-stack)
4. [Database Design](#4-database-design)
5. [Entity Relationships](#5-entity-relationships)
6. [Features and SQL Concepts](#6-features-and-sql-concepts)
7. [Sample SQL Queries](#7-sample-sql-queries)
8. [Setup and Execution](#8-setup-and-execution)
9. [Recommended Repository Structure](#9-recommended-repository-structure)
10. [Sample Dataset](#10-sample-dataset)
11. [Limitations and Future Improvements](#11-limitations-and-future-improvements)
12. [Learning Outcomes](#12-learning-outcomes)
13. [References](#13-references)

---

## 1. Project Overview

**MediTrack** is an educational Hospital Patient Management System database designed to organize five kinds of healthcare records:

- Patient profiles and contact details
- Doctor details and specializations
- Appointment dates and statuses
- Prescribed medicines and dosage information
- Bills, payment methods, and payment statuses

The database uses related tables, primary keys, and foreign keys to structure information and support common reporting tasks.

> **Project scope:** This is a SQL/database project. It does not currently include a web interface, desktop application, or application backend. All records are sample data for learning and demonstration, not real patient information or medical advice.

## 2. Objectives

- Store patient and doctor information in separate tables.
- Connect appointments to the appropriate patient and doctor.
- Record prescription details associated with appointments.
- Track bill amounts, payment methods, and payment statuses.
- Practise filtering, sorting, joins, grouping, aggregate functions, subqueries, indexes, and views.
- Demonstrate relational database design and referential integrity.

## 3. Technology Stack

| Component | Technology |
|---|---|
| Database | MySQL |
| SQL environment | MySQL Shell |
| Query language | SQL |

The recorded shell session uses **MySQL Shell 8.0.46**. The SQL statements are intended for a compatible MySQL server.

## 4. Database Design

The database is named `MediTrack` and contains five core tables.

| Table | Purpose | Primary key |
|---|---|---|
| `Patients` | Stores patient profiles and contact details | `Patient_ID` |
| `Doctors` | Stores doctor names, specializations, fees, and experience | `Doctor_ID` |
| `Appointments` | Connects patients and doctors to appointment dates and statuses | `Appointment_ID` |
| `Prescriptions` | Stores medicine, dosage, and duration for an appointment | `Prescription_ID` |
| `Billing` | Stores bill amounts and payment details | `Bill_ID` |

### 4.1 Table columns

#### `Patients`

| Column | Description |
|---|---|
| `Patient_ID` | Primary key |
| `Name` | Patient name |
| `Age` | Patient age |
| `Gender` | Patient gender |
| `Contact` | Contact number |
| `Email` | Email address |
| `Address` | Address |

#### `Doctors`

| Column | Description |
|---|---|
| `Doctor_ID` | Primary key |
| `Name` | Doctor name |
| `Specialization` | Medical specialization |
| `Fee` | Consultation fee |
| `Experience_Years` | Years of experience |

#### `Appointments`

| Column | Description |
|---|---|
| `Appointment_ID` | Primary key |
| `Patient_ID` | Foreign key referencing `Patients.Patient_ID` |
| `Doctor_ID` | Foreign key referencing `Doctors.Doctor_ID` |
| `Appointment_Date` | Appointment date |
| `Status` | Appointment status |

#### `Prescriptions`

| Column | Description |
|---|---|
| `Prescription_ID` | Primary key |
| `Appointment_ID` | Foreign key referencing `Appointments.Appointment_ID` |
| `Medicine` | Medicine name |
| `Dosage` | Prescribed dosage instructions |
| `Duration_Days` | Duration in days |

#### `Billing`

| Column | Description |
|---|---|
| `Bill_ID` | Primary key |
| `Appointment_ID` | Foreign key referencing `Appointments.Appointment_ID` |
| `Amount` | Bill amount |
| `Payment_Method` | Payment method |
| `Payment_Status` | Payment status |

## 5. Entity Relationships

```text
Patients (1) ─────── (N) Appointments (N) ─────── (1) Doctors
                              |
                              | 1:N
                              v
                        Prescriptions

Appointments (1) ─────── (1) Billing  [intended]
```

- **Patients → Appointments (1:N):** A patient can have multiple appointments.
- **Doctors → Appointments (1:N):** A doctor can attend multiple appointments.
- **Appointments → Prescriptions (1:N):** An appointment can have multiple prescription lines.
- **Appointments → Billing (intended 1:1):** The design intends one bill per appointment.

> **Important schema note:** The described `Billing.Appointment_ID` column is a foreign key, but it is not declared `UNIQUE` in the source schema. Therefore, the database does not enforce one bill per appointment. To enforce this rule, add a `UNIQUE` constraint after checking that no duplicate appointment IDs exist in `Billing`.

## 6. Features and SQL Concepts

| Concept | SQL feature |
|---|---|
| Database and table creation | `CREATE DATABASE`, `CREATE TABLE` |
| Data insertion | `INSERT` |
| Data retrieval and filtering | `SELECT`, `WHERE` |
| Sorting and limiting | `ORDER BY`, `LIMIT` |
| Combining related tables | `JOIN` |
| Grouping and aggregation | `GROUP BY`, `HAVING`, `COUNT`, `SUM`, `AVG`, `MAX` |
| Nested queries | Subqueries |
| Date-based queries | Date filtering and date functions |
| Performance and uniqueness | Indexes |
| Reusable query results | Views |
| Data integrity | Primary keys and foreign keys |

The project records include an index on `Appointments.Patient_ID`, a unique index on `Patients.Email`, and a `HighValueBills` view for bills greater than 700.

## 7. Sample SQL Queries

Run these examples after creating and populating the tables.

### 7.1 Find appointments after a given date

```sql
SELECT *
FROM Appointments
WHERE Appointment_Date > '2024-01-08';
```

### 7.2 Find payments made by credit card

```sql
SELECT *
FROM Billing
WHERE Payment_Method = 'Credit Card';
```

### 7.3 Display the five doctors with the highest consultation fees

```sql
SELECT *
FROM Doctors
ORDER BY Fee DESC
LIMIT 5;
```

### 7.4 Retrieve medicines prescribed to a patient

```sql
SELECT pr.Medicine
FROM Prescriptions AS pr
JOIN Appointments AS a
    ON pr.Appointment_ID = a.Appointment_ID
JOIN Patients AS p
    ON a.Patient_ID = p.Patient_ID
WHERE p.Name = 'Rohit Sharma';
```

Replace `'Rohit Sharma'` with a patient name that exists in your sample data if needed.

### 7.5 Find bills above the average bill amount

```sql
SELECT *
FROM Billing
WHERE Amount > (
    SELECT AVG(Amount)
    FROM Billing
);
```

### 7.6 Count appointments for each patient

```sql
SELECT Patient_ID, COUNT(*) AS Total_Appointments
FROM Appointments
GROUP BY Patient_ID;
```

### 7.7 Calculate total billing by payment method

```sql
SELECT Payment_Method, SUM(Amount) AS Total_Revenue
FROM Billing
GROUP BY Payment_Method;
```

### 7.8 Find appointments with more than one prescription line

```sql
SELECT Appointment_ID
FROM Prescriptions
GROUP BY Appointment_ID
HAVING COUNT(Prescription_ID) > 1;
```

### 7.9 Create a view for higher-value bills

```sql
CREATE VIEW HighValueBills AS
SELECT *
FROM Billing
WHERE Amount > 700;
```

Query the view:

```sql
SELECT * FROM HighValueBills;
```

## 8. Setup and Execution

### Prerequisites

- MySQL Server installed and running
- MySQL Shell installed
- Project SQL files saved locally

### Step 1 — Connect to MySQL

Open MySQL Shell and connect to your local server:

```text
\\connect root@localhost:3306
```

Switch to SQL mode:

```text
\\sql
```

Enter your MySQL password if prompted. **Never store your password in project files or commit it to GitHub.**

### Step 2 — Create and select the database

```sql
CREATE DATABASE MediTrack;
USE MediTrack;
```

If the database already exists, run `USE MediTrack;` instead of creating it again.

### Step 3 — Run the SQL scripts in order

1. `meditrack_schema.sql` — creates the tables.
2. `meditrack_data.sql` — inserts the sample records.
3. `meditrack_queries.sql` — contains example queries, indexes, and views as appropriate.

Table definitions must run before inserts, and inserts must run before queries that expect populated tables.

> **Important:** The recorded `26.10.26MySQL Shell.sql` file is a captured shell session, not a clean executable SQL script. Before using it as a GitHub script, remove shell prompts, status messages, query outputs, and failed command attempts. Keep only valid SQL statements in execution order.

## 9. Recommended Repository Structure

```text
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

This is a suggested structure. Include only files that you have actually created. Screenshots are optional but useful for demonstrating query results.

## 10. Sample Dataset

The project report and shell transcript describe the following sample record counts:

| Table | Number of records |
|---|---:|
| `Patients` | 20 |
| `Doctors` | 15 |
| `Appointments` | 10 |
| `Prescriptions` | 13 |
| `Billing` | 10 |
| **Total** | **68** |

The appointment examples use dates in January 2024. The dataset is illustrative and should not be treated as real patient information or medical advice.

## 11. Limitations and Future Improvements

### Current limitations

- The project demonstrates database operations through SQL; it does not include a graphical user interface or application backend.
- The included records are sample data.
- The intended one-bill-per-appointment rule is not enforced unless `Billing.Appointment_ID` is made unique.
- The project is educational and is not a production-ready hospital information system.
- Authentication, role-based access control, audit logging, and production healthcare privacy safeguards are not implemented.

### Possible future improvements

- Add stronger validation constraints.
- Enforce the one-to-one billing relationship with a unique constraint.
- Add more indexes where query patterns justify them.
- Build reports for appointments and payments.
- If the project scope expands, add an application interface and appropriate security controls.

## 12. Learning Outcomes

Through this project, you can demonstrate practical understanding of:

- Relational database design
- Tables, columns, and data types
- Primary keys and foreign keys
- One-to-many relationships
- Data retrieval and multi-table joins
- Aggregate reporting and subqueries
- Indexes and views
- Referential integrity in MySQL

## 13. References

- [MySQL Documentation](https://dev.mysql.com/doc/)
- [W3Schools SQL Tutorial](https://www.w3schools.com/sql/)
- [SQLBolt Interactive SQL Lessons](https://sqlbolt.com/)

---

**Project:** Hospital Patient Management System  
**Database:** `MediTrack`  
**Tools:** MySQL, MySQL Shell, SQL  
**Project type:** Educational relational database project
