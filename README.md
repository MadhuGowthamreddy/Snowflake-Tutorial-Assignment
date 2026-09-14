# Snowflake-Tutorial-Assignment
Overview
This repository contains my Snowflake Tutorial Assignment covering SnowSQL connectivity, Snowflake object creation, CRUD operations, CSV data loading, Time Travel, and data recovery.
Student Information
Student Name: Madhu Gowtham Reddy
Subject: Snowflake / Data Warehousing
Assignment: Snowflake Tutorial Assignment
Objectives
Install and configure SnowSQL.
Connect to a Snowflake account.
Verify user, role, warehouse, database, and schema.
Create Snowflake database objects.
Perform INSERT, SELECT, UPDATE, and DELETE operations.
Load CSV data using SnowSQL.
Demonstrate Snowflake Time Travel.
Recover accidentally deleted records.
Technologies Used
Snowflake
SnowSQL
SQL
CSV
GitHub
1. SnowSQL Login and Connection
Check installation:
snowsql --version
Connect:
snowsql -a <account_identifier> -u <username>
Verify the connection:
SELECT CURRENT_USER(),
       CURRENT_ROLE(),
       CURRENT_WAREHOUSE(),
       CURRENT_DATABASE(),
       CURRENT_SCHEMA();
2. Creation of Snowflake Objects
Database
CREATE DATABASE IF NOT EXISTS SNOWFLAKE_TUTORIAL_DB;
Schema
CREATE SCHEMA IF NOT EXISTS SNOWFLAKE_TUTORIAL_DB.STUDENT_SCHEMA;
Warehouse
CREATE WAREHOUSE IF NOT EXISTS SNOWFLAKE_TUTORIAL_WH
WITH
WAREHOUSE_SIZE = 'X-SMALL'
AUTO_SUSPEND = 60
AUTO_RESUME = TRUE;
Select Objects
USE WAREHOUSE SNOWFLAKE_TUTORIAL_WH;
USE DATABASE SNOWFLAKE_TUTORIAL_DB;
USE SCHEMA STUDENT_SCHEMA;
Table
CREATE TABLE STUDENTS (
    STUDENT_ID INTEGER,
    NAME VARCHAR(100),
    DEPARTMENT VARCHAR(100),
    AGE INTEGER,
    MARKS FLOAT
);
Stage
CREATE STAGE STUDENT_STAGE;
CRUD Operations
INSERT
INSERT INTO STUDENTS VALUES
(101, 'Rahul', 'Computer Science', 20, 85),
(102, 'Priya', 'Data Analytics', 21, 91),
(103, 'Arjun', 'Information Technology', 20, 78),
(104, 'Sneha', 'Computer Science', 22, 88),
(105, 'Kiran', 'Data Analytics', 21, 95);
SELECT
SELECT * FROM STUDENTS;
UPDATE
UPDATE STUDENTS
SET MARKS = 89
WHERE STUDENT_ID = 106;
DELETE
DELETE FROM STUDENTS
WHERE STUDENT_ID = 106;
3. Data Loading Using SnowSQL
The repository includes students.csv.
Loading Table
CREATE TABLE STUDENT_LOAD (
    STUDENT_ID INTEGER,
    NAME VARCHAR(100),
    DEPARTMENT VARCHAR(100),
    AGE INTEGER,
    MARKS FLOAT
);
File Format
CREATE FILE FORMAT STUDENT_CSV_FORMAT
TYPE = 'CSV'
FIELD_DELIMITER = ','
SKIP_HEADER = 1
FIELD_OPTIONALLY_ENCLOSED_BY = '"';
Upload CSV
Replace the example path with the actual location of students.csv:
PUT file:///C:/path/to/students.csv
@STUDENT_STAGE
AUTO_COMPRESS = TRUE;
Check Stage
LIST @STUDENT_STAGE;
Load Data
COPY INTO STUDENT_LOAD
FROM @STUDENT_STAGE
FILE_FORMAT = (FORMAT_NAME = 'STUDENT_CSV_FORMAT');
Verify
SELECT * FROM STUDENT_LOAD;
SELECT COUNT(*) AS ROW_COUNT FROM STUDENT_LOAD;
Expected row count: 5
4. Snowflake Time Travel
Create Table
CREATE OR REPLACE TABLE TIME_TRAVEL_STUDENTS (
    STUDENT_ID INTEGER,
    NAME VARCHAR(100),
    DEPARTMENT VARCHAR(100),
    MARKS INTEGER
);
Insert Records
INSERT INTO TIME_TRAVEL_STUDENTS VALUES
(301, 'Ramesh', 'CSE', 80),
(302, 'Suresh', 'ECE', 75),
(303, 'Lakshmi', 'CSE', 90),
(304, 'Pooja', 'Data Analytics', 95);
Update
UPDATE TIME_TRAVEL_STUDENTS
SET MARKS = 88
WHERE STUDENT_ID = 301;
Delete
DELETE FROM TIME_TRAVEL_STUDENTS
WHERE STUDENT_ID = 302;
Find DELETE Query ID
SELECT QUERY_ID, QUERY_TEXT, START_TIME
FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY())
WHERE QUERY_TEXT ILIKE 'DELETE FROM TIME_TRAVEL_STUDENTS%'
ORDER BY START_TIME DESC
LIMIT 5;
Query Historical Data
Replace YOUR_DELETE_QUERY_ID with the actual query ID:
SELECT *
FROM TIME_TRAVEL_STUDENTS
BEFORE (STATEMENT => 'YOUR_DELETE_QUERY_ID');
5. Data Recovery Using Time Travel
Identify the deleted record:
SELECT *
FROM TIME_TRAVEL_STUDENTS
BEFORE (STATEMENT => 'YOUR_DELETE_QUERY_ID')
WHERE STUDENT_ID = 302;
Expected historical record:
302 | Suresh | ECE | 75
Recover it:
INSERT INTO TIME_TRAVEL_STUDENTS
SELECT *
FROM TIME_TRAVEL_STUDENTS
BEFORE (STATEMENT => 'YOUR_DELETE_QUERY_ID')
WHERE STUDENT_ID = 302;
Verify:
SELECT *
FROM TIME_TRAVEL_STUDENTS
WHERE STUDENT_ID = 302;
Project Structure
Snowflake-Tutorial-Assignment/
├── README.md
├── snowflake_tutorial_assignment.docx
├── snowflake_tutorial.sql
├── students.csv
└── screenshots/
Files
File
Description
README.md
Assignment documentation
snowflake_tutorial_assignment.docx
Complete assignment report
snowflake_tutorial.sql
SQL commands
students.csv
Sample dataset
screenshots/
Snowflake execution screenshots
Screenshot Checklist
SnowSQL login and connection
Connection verification
Database creation
Schema and warehouse creation
Table creation
Stage creation
INSERT and SELECT
UPDATE
DELETE
CSV data loading
PUT/LIST output
COPY INTO output
Loaded data
Time Travel query
Deleted record
Recovery query
Final recovered table
Conclusion
This assignment demonstrates SnowSQL connectivity, Snowflake object creation, CRUD operations, CSV data loading, Time Travel, and recovery of accidentally deleted records.
Security
Never upload Snowflake passwords, MFA codes, access tokens, private keys, API credentials, or other confidential information to GitHub