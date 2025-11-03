# Traitement des Big Data - Course Notes
**Instructor:** Amin Mesmoudi (amin.mesmoudi@univ-poitiers.fr)
**Institution:** Université de Poitiers - IUT Poitiers Nord Châtellerault

---

## Table of Contents
1. [Big Data History](#big-data-history)
2. [Understanding Data Scale](#understanding-data-scale)
3. [Real-World Big Data Projects](#real-world-big-data-projects)
4. [Big Data Challenges](#big-data-challenges)
5. [Data Scientist Skills](#data-scientist-skills)
6. [Hadoop Ecosystem](#hadoop-ecosystem)
7. [HDFS - Hadoop Distributed File System](#hdfs---hadoop-distributed-file-system)
8. [HDFS Commands Reference](#hdfs-commands-reference)
9. [Practical Examples](#practical-examples)

---

## Big Data History

### Timeline Evolution

#### 1970s - Traditional Databases
- **Tools:** Relational DBMS (Système R, DB2, Oracle)
- **Characteristics:** ACID properties
- **Use Cases:** Commercial data (insurance, banks)
- **Example:** Weather information from Barbados island

#### 2000s - Web Era
- **Tools:** OLAP, NoSQL, Column storage (C-Store)
- **Focus:** Decision support, web analytics
- **New Paradigm:** Data warehouses

#### 2017 - Modern Big Data
- **Tools:** Hadoop, Spark, Flink
- **Architecture:** MPP (Massively Parallel Processing)
- **Scale Examples:**
  - Google
  - Facebook: 300 PB of data (2014)
  - Airbus A350: 2.5 TB/day
  - CERN: 500 TB/day, 140 PB storage

### Key Insight
> **"The term 'Big Data' reflects our inability to process data efficiently"**
>
> The use of "Big Data" essentially means we've reached the limits of traditional data processing methods.

---

## Understanding Data Scale

### Storage Units Progression

| Unit | Size | Real-World Equivalent |
|------|------|----------------------|
| **1 Mega-octet (MB)** | 1 million characters | Book "Un président ne devrait pas dire ça !" |
| **1 Giga-octet (GB)** | 1,000 MB | Information in human genome |
| **1 Tera-octet (TB)** | 1,000 GB | Annual literary production worldwide |
| **1 Peta-octet (PB)** | 1,000 TB | All American academic libraries |
| **1 Exa-octet (EB)** | 1,000 PB | 2/3 of annual information production |

### Modern Examples
- **Airbus A350:** 2.5 TB per day
- **Facebook (2014):** 300 PB total data storage
- **CERN:** 500 TB/day production, 140 PB storage capacity

---

## Real-World Big Data Projects

### LSST Project (Large Synoptic Survey Telescope)
**Location:** SLAC (Stanford, USA)
**Website:** http://www.lsst.org

#### Project Specifications
- **Started:** 2012
- **Duration:** 10 years
- **Total Data:** 140 Petabytes
- **Operations:**
  - Image capture every 15 seconds
  - 1 visit every 3 days
  - Located at Cerro Pachón (future site)

#### LSST Database Tables

| Table Name | Size | # Records | # Attributes |
|------------|------|-----------|--------------|
| Object | 109 TB | 38 Billion | 470 |
| Moving Object | 5 GB | 6 Million | 100 |
| Source | 3.6 PB | 5 Trillion | 125 |
| Forced Source | 1.1 PB | 32 Trillion | 7 |
| Difference Image Source | 71 TB | 200 Billion | 65 |
| CCD Exposure | 0.6 TB | 17 Billion | 45 |

#### Data Access Requirements
- **Query Volume:** 500,000 queries per day
- **Query Language:** SQL (declarative queries)
- **Custom Functions:** UDF (User Defined Functions)
  - Example: `areaspec_box`, `angSep < dist`

#### Example LSST Query
```sql
SELECT objectId, taiMidPoint, fluxToAbMag(psfMag)
FROM Source
JOIN Object USING(objectId)
JOIN Filter USING(filterId)
WHERE areaSpec_box(:raMin, :declMin, :raMax, :declMax)
  AND filterName = 'u'
  AND variability BETWEEN :varMin AND :varMax
ORDER BY objectId, taiMidPoint ASC
```

---

## Big Data Challenges

### The Four V's of Big Data

#### 1. **Volume** (Très volumineuses)
- Massive amounts of data
- Traditional databases cannot handle

#### 2. **Velocity** (Vélocité)
- High-frequency data production
- Real-time or near-real-time processing needed

#### 3. **Variety** (Variabilité)
- Highly heterogeneous data
- Structured, semi-structured, unstructured

#### 4. **Veracity** (Véracité)
- Uncertain data
- Data quality issues

### Data Management vs Analysis Complexity

```
Passage à l'échelle (Scalability) ←→ Auto. apprentissage (Machine Learning)
         ↑                                        ↑
    Gestion                                  Analyse
         ↓                                        ↓
    Performances          Big Data        Nouveaux algorithmes
```

#### Data Management Side
- **Tools:** SQL/XQuery, MapReduce
- **Techniques:** Reporting, Ad-hoc queries, ETL/ELT, Aggregation/Selection

#### Analysis Side
- **Tools:** SAS, Matlab, R
- **Techniques:** Data Mining, Predictive/Prescriptive analytics

---

## Data Scientist Skills

### The Three Pillars

#### 1. **Data Analysis & Machine Learning**
- Statistics
- Mathematical programming
- Machine learning algorithms
- NLP (Natural Language Processing)
- Signal processing
- Image/Audio-video analysis
- Information extraction & integration
- Predictive analytics
- Stochastic gradient descent
- Estimation of error
- Monte Carlo methods
- Iterative algorithms
- Sampling
- Convergence analysis

#### 2. **Large-Scale Data Management**
- Relational algebra / SQL
- Data warehouse / OLAP
- Resource management
- Fault tolerance
- Memory management
- Parallelization
- Scalability
- Query optimization
- Data analysis languages
- Indexation
- Communication protocols
- Efficient algorithms

#### 3. **Domain Knowledge**
- Medicine
- Physics
- Energy
- Logistics
- Industry-specific expertise

### The Skills Gap Problem

**R/Matlab Users:** 2.6 million users
- Know: Data analysis, statistics, optimization, machine learning
- Need: System programming skills

**Hadoop Users:** 250,000 users
- Know: Parallelization, fault tolerance, scalability
- Need: Advanced analytics skills

**The Challenge:** Finding people with BOTH skill sets!

> **Average Salary for Hadoop Developer: $139,000 per year**

### Simple vs Advanced Analysis

```
                Advanced Analysis
                       ↑
    MATLAB    ?        |        ?
      R       ?        |        ?
    ---------------Big Data---------------
    Excel             |     Hadoop
                      |     Spark
                      ↓
                Simple Analysis
```

---

## Hadoop Ecosystem

### What is Hadoop?

**Hadoop** is a system for managing data and parallel processing. It contains many components, including:

1. **HDFS** - A file system that distributes data across many machines
2. **YARN** - A scheduling mechanism for MapReduce-type programs

### Why Do We Need Hadoop?

#### The Problem
Processing massive amounts of data requires special methods. A classic DBMS, even high-end ones, cannot handle such volumes.

#### The Solution
**Distribute data across multiple machines** (up to millions of computers)

### Key Components

#### 1. Storage Layer
- **HDFS:** Special file system for gigantic and/or numerous files
- **HBase:** Specific databases for Big Data

#### 2. Processing Layer
- **MapReduce:** Processing paradigm
  - Easy to write algorithms
  - Easy to parallelize execution

---

## HDFS - Hadoop Distributed File System

### What is HDFS?

HDFS is a **distributed file system**, meaning:

1. **Storage and access device for files**
2. **Files stored across many machines** - Making the exact position invisible
3. **Transparent access** - Regardless of which machines contain the files

### Main Features

**HDFS allows you to see all folders and files from thousands of machines as a single tree, containing Petabytes of data, as if they were on your local hard drive.**

### File Organization

HDFS uses a hierarchical structure similar to Unix/Linux:

```
/
├── hbase/
├── tmp/
├── var/
├── user/
│   ├── ubuntu/
│   ├── hive/
│   └── spark/
└── share/
```

#### Path Examples
- Hive directory: `/user/hive`
- Spark directory: `/user/spark`

Each file and folder is identified by its access path.

---

## HDFS Commands Reference

### General Syntax
```bash
hdfs dfs [option] [arguments]
```

### Basic Commands

#### 1. Help
```bash
hdfs dfs -help
```
Display help information

#### 2. List Directory Contents
```bash
hdfs dfs -ls [path...]
```
Display the contents of a folder

#### 3. Display File Contents
```bash
hdfs dfs -cat filename
```
Display the contents of a file

#### 4. Move Files/Folders
```bash
hdfs dfs -mv old_name new_name
```
Move or rename a file or folder

#### 5. Copy Files/Folders
```bash
hdfs dfs -cp old_name new_name
```
Copy a file or folder

#### 6. Create Directory
```bash
hdfs dfs -mkdir folder_name
```
Create a new folder

#### 7. Remove Directory
```bash
hdfs dfs -rm -r folder_name
```
Delete a folder (recursively)

#### 8. Remove File
```bash
hdfs dfs -rm filename
```
Delete a file

### Data Exchange Commands

#### Upload to HDFS (2 methods)

**Method 1:**
```bash
hdfs dfs -copyFromLocal source_file destination_file
```

**Method 2:**
```bash
hdfs dfs -put source_file [destination_file]
```

#### Download from HDFS (2 methods)

**Method 1:**
```bash
hdfs dfs -copyToLocal source_file destination
```

**Method 2:**
```bash
hdfs dfs -get source_file [destination_file]
```

---

## How HDFS Works: Distributed Storage

### Storage Process

#### Step 1: File Splitting
A large file is divided into blocks (e.g., File1 → A, B, C, D, E)

#### Step 2: Block Distribution
Blocks are distributed across multiple machines:
- Machine 1: Block A
- Machine 2: Block B, E
- Machine 3: Block C

#### Step 3: Replication (Fault Tolerance)
Each block is replicated across multiple machines:
- Machine 1: A, D, C
- Machine 2: B, E, D
- Machine 3: C, A, B, E

### Benefits
1. **Fault Tolerance:** If one machine fails, data is not lost
2. **Parallel Access:** Multiple machines can read different parts simultaneously
3. **Load Balancing:** Work is distributed across the cluster

### Visualization
```
Original File
     ↓
[A][B][C][D][E]  ← Split into blocks
     ↓
Distribution across machines
     ↓
Machine 1: [A][D]
Machine 2: [B][E]
Machine 3: [C]
     ↓
Replication for redundancy
     ↓
Machine 1: [A][D][C]
Machine 2: [B][E][D]
Machine 3: [C][A][B][E]
```

---

## Infrastructure: Data Centers

### Cluster Architecture

A **cluster** consists of approximately **5,000 interconnected computers**

### Blade Server / Rack Server Specifications

**Typical Configuration:**
- **CPU:** 4 multi-core processors
- **RAM:** 128 GB
- **Storage:** 24 TB fast disks
- **Cost:** ~5,000€ per unit

### Google's Approach
Google uses relatively basic, inexpensive computers but in extremely large quantities (10^6 = 1 million machines)

### Physical Layout
Data centers contain rows upon rows of rack-mounted servers with:
- Complex cooling systems
- Redundant power supplies
- High-speed networking infrastructure
- Organized cable management

---

## Practical Examples

### Example Exercise: Working with Dracula Text File

#### Objective
Practice basic HDFS operations using a classic literature file

#### Steps

1. **Download the file**
   ```bash
   wget http://www.textfiles.com/etext/FICTION/dracula
   ```

2. **Create a directory in HDFS**
   ```bash
   hdfs dfs -mkdir livre
   ```
   Creates a "livre" (book) folder in the root user's main directory

3. **Transfer file to HDFS**
   ```bash
   hdfs dfs -put dracula livre/
   ```
   Uploads the dracula file to the livre folder

4. **Display directory contents**
   ```bash
   hdfs dfs -ls livre
   ```
   Shows contents of the livre folder

5. **Retrieve file from HDFS with new name**
   ```bash
   hdfs dfs -get livre/dracula dracula_hdfs
   ```
   Downloads the file with a different name

---

## Key Takeaways

### Why Big Data Matters
1. **Volume:** Traditional systems cannot handle Petabyte-scale data
2. **Velocity:** Data generation is faster than ever
3. **Variety:** Data comes in many formats
4. **Veracity:** Data quality and uncertainty must be managed

### HDFS Advantages
1. **Scalability:** Add more machines to increase capacity
2. **Fault Tolerance:** Data replication prevents loss
3. **Cost-Effective:** Uses commodity hardware
4. **Transparent Access:** Appears as single file system

### Skills Required
1. **Data Analysis:** Statistics, ML, algorithms
2. **System Programming:** Distributed systems, parallelization
3. **Domain Expertise:** Understanding the business context

### Next Steps in Course
- Learn YARN and MapReduce programming
- Understand parallel processing paradigms
- Practice writing distributed algorithms

---

## Additional Resources

- **LSST Project:** http://www.lsst.org
- **Dracula Text:** http://www.textfiles.com/etext/FICTION/dracula
- **Instructor Email:** amin.mesmoudi@univ-poitiers.fr

---

## Notes Section

### Important Concepts to Review
- [ ] Understand the difference between HDFS and traditional file systems
- [ ] Practice all HDFS commands
- [ ] Review MapReduce concepts (coming in next session)
- [ ] Study the LSST example queries

### Questions to Ask
1. How does HDFS decide which machines to replicate blocks to?
2. What happens when a machine fails during a read operation?
3. How does MapReduce work with HDFS?

---

**Last Updated:** Course Material 1 & 2 - HDFS and Hive
**Status:** Ready for hands-on practice with HDFS and Hive

---
---

# COURSE 2: Apache Hive

---

## Table of Contents - Hive
1. [Hadoop Ecosystem Architecture](#hadoop-ecosystem-architecture)
2. [What is Apache Hive?](#what-is-apache-hive)
3. [Getting Started with Hive](#getting-started-with-hive)
4. [Creating Tables in Hive](#creating-tables-in-hive)
5. [Loading Data into Hive](#loading-data-into-hive)
6. [Verifying Operations](#verifying-operations)
7. [Understanding Data Storage in Hive](#understanding-data-storage-in-hive)
8. [External Tables](#external-tables)
9. [Hive Queries - HiveQL](#hive-queries---hiveql)
10. [Hive Command Reference](#hive-command-reference)

---

## Hadoop Ecosystem Architecture

### The Complete Stack

The Hadoop ecosystem is organized in layers:

```
┌─────────────────────────────────────────────────────────┐
│  Top Level Interfaces                                   │
│  ┌──────────┐  ┌──────────────┐  ┌──────────┐          │
│  │   ETL    │  │ BI Reporting │  │  RDBMS   │          │
│  │  Tools   │  │              │  │          │          │
│  └──────────┘  └──────────────┘  └──────────┘          │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Top Level Abstractions                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │   PIG    │  │   Hive   │  │  Sqoop   │              │
│  │          │  │          │  │          │              │
│  └──────────┘  └──────────┘  └──────────┘              │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Distributed Data Processing                            │
│  ┌──────────────────────────┐  ┌──────────────────┐    │
│  │                          │  │     HBASE        │    │
│  │      MapReduce           │  │  Database with   │    │
│  │                          │  │  Real-time       │    │
│  │                          │  │  access          │    │
│  └──────────────────────────┘  └──────────────────┘    │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  At the base: Self-healing clustered storage system    │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Hadoop Distributed File System (HDFS)          │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

### Layer Breakdown

#### Layer 1: Storage (Bottom)
- **HDFS** - The foundation that stores all data

#### Layer 2: Processing
- **MapReduce** - Distributed data processing framework
- **HBase** - NoSQL database for real-time random read/write access

#### Layer 3: Abstraction (High-Level Tools)
- **PIG** - Scripting language for data flow
- **Hive** - SQL-like interface (our focus)
- **Sqoop** - Data transfer between Hadoop and RDBMS

#### Layer 4: User Interfaces
- **ETL Tools** - Extract, Transform, Load utilities
- **BI Reporting** - Business Intelligence tools
- **RDBMS Connectors** - Traditional database interfaces

---

## What is Apache Hive?

### Definition

**Apache Hive** translates queries written in **HiveQL** (a SQL dialect influenced by MySQL) into a workflow of MapReduce jobs, then submits these jobs to the Hadoop cluster.

### Key Characteristics

> **Important:** Hive is NOT a database! It is an abstraction layer on top of the MapReduce framework.

#### What Hive Does:
1. **Accepts SQL-like queries** (HiveQL)
2. **Translates them** into MapReduce jobs
3. **Submits jobs** to the Hadoop cluster
4. **Returns results** like a traditional database would

#### Why Use Hive?
- **Familiar Interface:** SQL knowledge is transferable
- **No MapReduce Programming:** Don't need to write complex MapReduce code
- **Large-Scale Queries:** Process petabytes of data with SQL
- **Integration:** Works seamlessly with existing Hadoop infrastructure

### Course Focus

This course covers three main operations:

1. **Table Creation** - Defining schema and structure
2. **Data Loading** - Importing data into tables
3. **Query Execution** - Running HiveQL queries

---

## Getting Started with Hive

### Launching Hive

To start working with Hive, connect using the Beeline client:

```bash
beeline -u jdbc:hive2://master:10000
```

**Explanation:**
- `beeline` - The Hive client interface
- `-u` - Connection URL parameter
- `jdbc:hive2://` - JDBC protocol for Hive 2
- `master:10000` - Master node hostname and port

### Creating a Database

```sql
CREATE DATABASE setudiant;
```

Creates a new database named "setudiant" (student in French)

### Selecting a Database

```sql
USE setudiant;
```

Switch to the specified database for subsequent operations

---

## Creating Tables in Hive

### Basic Table Syntax

```sql
CREATE TABLE table_name(
    column1 datatype,
    column2 datatype,
    ...
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY 'delimiter';
```

### Example 1: Student Table (Etudiant)

```sql
CREATE TABLE etudiant(
    id INT,
    nom STRING,
    prenom STRING,
    AnneeN INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

**Table Structure:**
- `id` - Student ID (integer)
- `nom` - Last name (string)
- `prenom` - First name (string)
- `AnneeN` - Birth year (integer)
- Fields separated by tabs (`\t`)

### Example 2: Grades Table (Note)

```sql
CREATE TABLE Note(
    idE INT,
    UE STRING,
    Note Float
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

**Table Structure:**
- `idE` - Student ID (foreign key)
- `UE` - Course/Module name (string)
- `Note` - Grade (float)
- Fields separated by tabs

### Supported Data Types

| Hive Type | Description | Example |
|-----------|-------------|---------|
| INT | Integer numbers | 42, -10, 0 |
| BIGINT | Large integers | 9223372036854775807 |
| FLOAT | Floating point | 3.14, -0.5 |
| DOUBLE | Double precision | 3.141592653589793 |
| STRING | Text data | "Hello", "Student Name" |
| BOOLEAN | True/False | TRUE, FALSE |
| TIMESTAMP | Date and time | 2023-01-15 14:30:00 |

---

## Loading Data into Hive

### LOAD DATA INPATH Command

#### Syntax
```sql
LOAD DATA INPATH 'hdfs_path'
INTO TABLE table_name;
```

#### Example: Loading Student Data

```sql
LOAD DATA INPATH '/user/votreLogin/data/etudiant.txt'
INTO TABLE etudiant;
```

**What happens:**
1. Hive reads the file from HDFS path `/user/votreLogin/data/etudiant.txt`
2. **Moves** (not copies) the file to Hive's warehouse
3. Associates the data with the `etudiant` table

#### Example: Loading Grades Data

```sql
LOAD DATA INPATH '/user/votreLogin/data/notes.txt'
INTO TABLE note;
```

### Important Notes

**File Movement:**
- `LOAD DATA INPATH` **moves** the file from its original location
- The original file no longer exists at the source path
- File is now managed by Hive

**Data Format Requirements:**
- File must match the delimiter specified in table creation
- Number of columns must match table schema
- Data types should be compatible

---

## Verifying Operations

### Basic Verification Commands

#### 1. Show All Tables
```sql
SHOW TABLES;
```
Lists all tables in the current database

#### 2. Describe Table Structure
```sql
DESCRIBE etudiant;
```
Shows column names, types, and comments for the table

Output example:
```
id          int
nom         string
prenom      string
anneen      int
```

#### 3. View Table Data
```sql
SELECT * FROM etudiant;
```
Displays all records from the table

#### 4. Verify Distinct Values
```sql
SELECT DISTINCT anneeN FROM etudiant;
```
Shows unique birth years in the student table

**Purpose:** Check that data was loaded correctly and attribute-value mappings are correct

---

## Understanding Data Storage in Hive

### Where Does Hive Store Data?

#### Default Storage Location
```
/user/hive/warehouse/
```

#### Viewing Data Files

To see where a table's data is actually stored in HDFS:

```bash
hdfs dfs -cat /user/hive/warehouse/etudiant/etudiant.txt
```

### How Hive Manages Tables

1. **File Copy Process:**
   - Hive copies files containing table records
   - Files are stored in a directory within Hive's HDFS space
   - Directory name matches the table name

2. **Example Structure:**
   ```
   /user/hive/warehouse/
   ├── etudiant/
   │   └── etudiant.txt
   └── note/
       └── notes.txt
   ```

3. **Independence:**
   - This allows Hive to manage tables independently of original data files
   - **Trade-off:** Requires duplicating the original file

### Deleting Tables

```sql
DROP TABLE etudiant;
```

**What happens:**
- Table definition is removed from Hive metastore
- Data files are deleted from HDFS warehouse
- **Warning:** This is permanent!

---

## External Tables

### What are External Tables?

External tables allow Hive to query data WITHOUT moving it to the Hive warehouse. The data remains in its original HDFS location.

### Creating External Tables

#### Syntax
```sql
CREATE EXTERNAL TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY 'delimiter'
LOCATION 'hdfs_path';
```

#### Example: External Note Table

```sql
CREATE EXTERNAL TABLE note (
    idE INT,
    nomUE INT,
    note FLOAT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LOCATION '/user/votreLogin/data/note';
```

**Key Difference:** The `LOCATION` clause specifies where data already exists

### Loading Data into External Tables

```sql
LOAD DATA LOCAL INPATH 'data/notes.txt'
OVERWRITE INTO TABLE note;
```

**Key Points:**
- `LOCAL INPATH` - Load from local file system (not HDFS)
- `OVERWRITE` - Replace existing data in the table

### Verifying External Tables

```sql
DESCRIBE FORMATTED records;
```

Shows detailed information including:
- Table type (EXTERNAL_TABLE)
- Data location
- File format details
- Partitioning information

### External vs Managed Tables

| Aspect | Managed Table | External Table |
|--------|---------------|----------------|
| **Data Location** | /user/hive/warehouse/ | User-specified |
| **DROP TABLE** | Deletes data | Keeps data, removes metadata |
| **Use Case** | Hive-only data | Shared data sources |
| **File Ownership** | Hive manages | User manages |

---

## Hive Queries - HiveQL

### SPJ Queries (Select-Project-Join)

#### Basic SELECT Query

```sql
SELECT nom, premon
FROM etudiant
WHERE anneeN > 1998;
```

**Returns:** Names of students born after 1998

**SPJ stands for:**
- **S**elect - Choose specific rows (WHERE clause)
- **P**roject - Choose specific columns (SELECT clause)
- **J**oin - Combine tables (JOIN clause)

### Aggregate Queries

#### Average Grade per Student

```sql
SELECT IdE, AVG(note)
FROM note
GROUP BY IdE;
```

**What it does:**
- Groups records by student ID
- Calculates average grade for each student
- Returns: Student ID and their average

#### Supported Aggregate Functions

| Function | Description | Example |
|----------|-------------|---------|
| `COUNT()` | Count rows | `COUNT(*)` |
| `SUM()` | Add values | `SUM(note)` |
| `AVG()` | Calculate average | `AVG(note)` |
| `MIN()` | Find minimum | `MIN(note)` |
| `MAX()` | Find maximum | `MAX(note)` |

### Join Queries

#### Basic Join with Aggregation

```sql
SELECT nom, premon, AVG(note)
FROM etudiant JOIN note
ON (etudiant.id = note.idE)
GROUP BY IdE, nom, premon;
```

**What it does:**
1. Joins student and grade tables
2. Matches records where `etudiant.id = note.idE`
3. Groups by student
4. Calculates average grade per student
5. Returns: Name and average grade

#### Join with Sorting

```sql
SELECT nom, premon, AVG(note) AS moy
FROM etudiant JOIN note
ON (etudiant.id = note.idE)
GROUP BY IdE, nom, premon
ORDER BY moy;
```

**Enhancement:**
- Uses `AS moy` to create an alias for the average
- `ORDER BY moy` sorts results by average grade (ascending by default)
- For descending: `ORDER BY moy DESC`

### Complex Query Example

```sql
SELECT nom, premon, MAX(note) AS meilleure_note
FROM etudiant JOIN note
ON (etudiant.id = note.idE)
WHERE anneeN > 1995
GROUP BY IdE, nom, premon
HAVING AVG(note) > 12
ORDER BY meilleure_note DESC
LIMIT 10;
```

**This query:**
1. Joins students and grades
2. Filters students born after 1995
3. Groups by student
4. Filters groups with average > 12
5. Shows student's best grade
6. Sorts by best grade (descending)
7. Shows only top 10 results

---

## Hive Command Reference

### Database Operations

```sql
-- Create database
CREATE DATABASE database_name;

-- Use database
USE database_name;

-- Show all databases
SHOW DATABASES;

-- Drop database
DROP DATABASE database_name;
```

### Table Operations

```sql
-- Show all tables
SHOW TABLES;

-- Describe table
DESCRIBE table_name;
DESCRIBE FORMATTED table_name;  -- Detailed info

-- Drop table
DROP TABLE table_name;
```

### Data Operations

```sql
-- Load from HDFS
LOAD DATA INPATH 'hdfs_path' INTO TABLE table_name;

-- Load from local file
LOAD DATA LOCAL INPATH 'local_path' INTO TABLE table_name;

-- Overwrite existing data
LOAD DATA LOCAL INPATH 'path' OVERWRITE INTO TABLE table_name;
```

### Query Operations

```sql
-- Basic select
SELECT * FROM table_name;

-- With conditions
SELECT columns FROM table WHERE condition;

-- With grouping
SELECT col1, AGG_FUNC(col2) FROM table GROUP BY col1;

-- With join
SELECT * FROM table1 JOIN table2 ON table1.id = table2.id;

-- With ordering
SELECT * FROM table ORDER BY column [ASC|DESC];

-- With limit
SELECT * FROM table LIMIT n;
```

---

## Key Differences: Hive vs Traditional SQL

### Similarities
- SQL-like syntax (HiveQL)
- Support for SELECT, WHERE, JOIN, GROUP BY, ORDER BY
- Aggregate functions (COUNT, SUM, AVG, MIN, MAX)
- Subqueries and complex queries

### Differences

| Aspect | Traditional RDBMS | Hive |
|--------|-------------------|------|
| **Purpose** | OLTP - Transactions | OLAP - Analytics |
| **Data Updates** | UPDATE, DELETE supported | Append-only (mostly) |
| **Speed** | Fast for small data | Optimized for huge data |
| **Latency** | Milliseconds | Seconds to minutes |
| **Schema** | Schema on write | Schema on read |
| **ACID** | Full ACID support | Limited ACID |
| **Indexes** | Full index support | Limited indexing |
| **Backend** | Traditional storage | MapReduce/Tez/Spark |

### When to Use Hive

✅ **Good for:**
- Batch processing of large datasets
- Data warehousing and analytics
- ETL operations
- Historical data analysis
- Log analysis

❌ **Not good for:**
- Real-time queries
- Row-level updates
- Transaction processing
- Small data queries
- Low-latency requirements

---

## Practical Exercise: Student Database

### Complete Workflow Example

#### Step 1: Prepare Data Files

**etudiant.txt** (tab-separated):
```
1	Dupont	Jean	1998
2	Martin	Marie	1999
3	Bernard	Pierre	1997
4	Dubois	Sophie	2000
```

**notes.txt** (tab-separated):
```
1	Math	15.5
1	Physics	14.0
2	Math	17.5
2	Physics	16.0
3	Math	12.0
4	Math	18.5
```

#### Step 2: Upload to HDFS

```bash
# Create directory
hdfs dfs -mkdir -p /user/yourLogin/data

# Upload files
hdfs dfs -put etudiant.txt /user/yourLogin/data/
hdfs dfs -put notes.txt /user/yourLogin/data/
```

#### Step 3: Launch Hive

```bash
beeline -u jdbc:hive2://master:10000
```

#### Step 4: Create Database and Tables

```sql
-- Create and use database
CREATE DATABASE student_db;
USE student_db;

-- Create student table
CREATE TABLE etudiant(
    id INT,
    nom STRING,
    prenom STRING,
    AnneeN INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';

-- Create grades table
CREATE TABLE note(
    idE INT,
    UE STRING,
    note FLOAT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

#### Step 5: Load Data

```sql
LOAD DATA INPATH '/user/yourLogin/data/etudiant.txt'
INTO TABLE etudiant;

LOAD DATA INPATH '/user/yourLogin/data/notes.txt'
INTO TABLE note;
```

#### Step 6: Verify

```sql
-- Check tables exist
SHOW TABLES;

-- View data
SELECT * FROM etudiant;
SELECT * FROM note;

-- Count records
SELECT COUNT(*) FROM etudiant;
SELECT COUNT(*) FROM note;
```

#### Step 7: Run Queries

```sql
-- Students born after 1998
SELECT nom, prenom FROM etudiant WHERE anneeN > 1998;

-- Average grade per student
SELECT e.nom, e.premon, AVG(n.note) as moyenne
FROM etudiant e JOIN note n ON e.id = n.idE
GROUP BY e.id, e.nom, e.premon
ORDER BY moyenne DESC;

-- Top performer in Math
SELECT e.nom, e.premon, n.note
FROM etudiant e JOIN note n ON e.id = n.idE
WHERE n.UE = 'Math'
ORDER BY n.note DESC
LIMIT 1;
```

---

## Best Practices for Hive

### 1. Table Design
- Use appropriate data types
- Consider partitioning for large tables
- Use external tables for shared data
- Choose delimiters that don't appear in data

### 2. Data Loading
- Verify data format before loading
- Use OVERWRITE carefully
- Check file permissions in HDFS
- Validate data after loading

### 3. Query Optimization
- Use WHERE clauses to filter early
- Avoid SELECT * in production
- Use LIMIT for testing queries
- Consider partitioning and bucketing

### 4. Resource Management
- Be aware that queries become MapReduce jobs
- Monitor cluster resources
- Use EXPLAIN to understand query plans
- Optimize joins (put largest table last)

---

## Common Issues and Solutions

### Issue 1: File Not Found

**Error:**
```
File does not exist: /user/yourLogin/data/file.txt
```

**Solution:**
```bash
# Verify file exists in HDFS
hdfs dfs -ls /user/yourLogin/data/

# Check file path is correct
# Make sure you're using INPATH (not LOCAL INPATH) for HDFS files
```

### Issue 2: Data Mismatch

**Problem:** Wrong number of columns or data types

**Solution:**
- Verify delimiter matches between file and CREATE TABLE
- Check for extra/missing tabs or spaces
- Use `DESCRIBE table_name` to verify schema
- Examine actual file: `hdfs dfs -cat filepath | head`

### Issue 3: Permission Denied

**Error:**
```
Permission denied: user=..., access=WRITE
```

**Solution:**
```bash
# Check HDFS permissions
hdfs dfs -ls -R /user/hive/warehouse/

# Fix permissions if needed
hdfs dfs -chmod -R 755 /user/yourLogin/
```

### Issue 4: Query Takes Forever

**Problem:** Query runs for very long time

**Solution:**
- Check if MapReduce jobs are stuck
- Verify cluster has available resources
- Use LIMIT for testing
- Check for Cartesian joins (missing ON clause)

---

## Hive Key Takeaways

### What We Learned

1. **Hive is NOT a database** - It's a SQL abstraction over MapReduce
2. **HiveQL is SQL-like** - Familiar syntax, but different backend
3. **Two table types:**
   - Managed: Hive controls data
   - External: Data stays in place
4. **Schema on Read** - Structure applied when querying, not when loading
5. **MapReduce Backend** - Queries become MapReduce jobs

### Critical Concepts

✅ **Understand:**
- How Hive translates SQL to MapReduce
- Difference between managed and external tables
- Where Hive stores data (/user/hive/warehouse/)
- Why queries are slower than traditional databases

✅ **Practice:**
- Creating databases and tables
- Loading data with different methods
- Writing SELECT, JOIN, and aggregate queries
- Using DESCRIBE and SHOW commands

---

## Hive vs Other Tools

### When to Use What?

| Tool | Best For | Not Good For |
|------|----------|--------------|
| **Hive** | SQL queries on massive data | Real-time queries |
| **Pig** | Data transformation pipelines | Interactive analysis |
| **HBase** | Random read/write access | Batch analytics |
| **Spark SQL** | Faster SQL queries | Simple batch jobs |
| **Traditional DB** | OLTP, transactions | Petabyte-scale analytics |

---

## Review Checklist - Hive

- [ ] Understand Hadoop ecosystem architecture
- [ ] Know what Hive is and isn't (not a database!)
- [ ] Can launch Hive using beeline
- [ ] Can create databases and tables
- [ ] Understand ROW FORMAT DELIMITED
- [ ] Can load data using LOAD DATA INPATH
- [ ] Know difference between INPATH and LOCAL INPATH
- [ ] Understand managed vs external tables
- [ ] Can write SELECT queries with WHERE
- [ ] Can write aggregate queries with GROUP BY
- [ ] Can write JOIN queries
- [ ] Can sort results with ORDER BY
- [ ] Know where Hive stores data in HDFS
- [ ] Understand that Hive queries become MapReduce jobs

---

## Questions to Explore

1. How does Hive translate a JOIN query into MapReduce jobs?
2. What happens to the original file after LOAD DATA INPATH?
3. When should you use an external table vs managed table?
4. Why is Hive slower than traditional databases for small queries?
5. How can you optimize Hive queries for better performance?
6. What is the metastore in Hive?
7. How does partitioning improve Hive query performance?

---

**Course Material 2 Complete:** Apache Hive
**Next Topics:** MapReduce programming, Spark, advanced query optimization

---
---

# COURSE 3: Apache Spark

---

## Table of Contents - Spark
1. [Spark in the Hadoop Ecosystem](#spark-in-the-hadoop-ecosystem)
2. [MapReduce: The Predecessor](#mapreduce-the-predecessor)
3. [Hadoop YARN Architecture](#hadoop-yarn-architecture)
4. [Why Spark is Faster than MapReduce](#why-spark-is-faster-than-mapreduce)
5. [What is Apache Spark?](#what-is-apache-spark)
6. [Spark DataFrames](#spark-dataframes)
7. [Creating DataFrames](#creating-dataframes)
8. [DataFrame Operations](#dataframe-operations)
9. [Transformations vs Actions](#transformations-vs-actions)
10. [Spark SQL Integration](#spark-sql-integration)
11. [Spark Architecture](#spark-architecture)
12. [Complete Examples](#complete-examples)

---

## Spark in the Hadoop Ecosystem

### Updated Hadoop Stack with Spark

```
┌─────────────────────────────────────────────────────────────────┐
│  Top Level Applications                                         │
│  ┌────────┐ ┌────────┐ ┌────────┐ ┌─────┐ ┌──────┐ ┌────────┐ │
│  │ Spark  │ │GraphX  │ │ MLlib  │ │Spark│ │ Pig  │ │  Hive  │ │
│  │Streaming│ │        │ │        │ │ SQL │ │      │ │        │ │
│  └────────┘ └────────┘ └────────┘ └─────┘ └──────┘ └────────┘ │
│                                                    ┌──────────┐ │
│                                                    │ Search/  │ │
│                                                    │ Impala   │ │
│                                                    └──────────┘ │
└─────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────┐
│  Processing Frameworks                                          │
│  ┌──────────────────────────┐  ┌────────────────────────────┐  │
│  │         Spark            │  │    Spark or MapReduce      │  │
│  └──────────────────────────┘  └────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────┐
│  Resource Management                                            │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                        YARN                               │ │
│  └───────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────┐
│  Storage                                                        │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │              HDFS, HBase                                  │ │
│  └───────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘

Legend:
■ Core Hadoop (light blue)
■ Spark Components (dark blue)
```

### Key Points

- **Spark runs ON TOP of YARN** - It's not a replacement for Hadoop, but works with it
- **Multiple Spark components:**
  - **Spark Streaming** - Real-time data processing
  - **GraphX** - Graph processing
  - **MLlib** - Machine learning library
  - **Spark SQL** - SQL queries on big data
- **Coexists with traditional tools** like Pig, Hive, and MapReduce

---

## MapReduce: The Predecessor

### History

**Proposed by Google in 2004**

MapReduce was the original paradigm for distributed data processing that made Hadoop famous.

**Abandoned by Google in 2014**

Even Google moved away from MapReduce to more efficient processing models.

### What is MapReduce?

**Purpose:** Extract synthetic information from large volumes of data

**Key Characteristics:**

1. **Data is distributed with HDFS**
2. **Every processing problem can be written as composition of two functions:**
   - **map:** Extract/calculate information on each tuple (record)
   - **reduce:** Group/aggregate this information

### MapReduce Workflow Example

**Problem:** Count words in a massive text file

```
Input Data (distributed across nodes):
"hello world"
"hello spark"
"world of data"

MAP Phase (runs on each partition):
hello → (hello, 1)
world → (world, 1)
hello → (hello, 1)
spark → (spark, 1)
world → (world, 1)
of → (of, 1)
data → (data, 1)

Shuffle & Sort:
(data, 1)
(hello, 1), (hello, 1)
(of, 1)
(spark, 1)
(world, 1), (world, 1)

REDUCE Phase (aggregate by key):
data → (data, 1)
hello → (hello, 2)
of → (of, 1)
spark → (spark, 1)
world → (world, 2)
```

### Why Google Abandoned MapReduce

**Problems with MapReduce:**
- Writes intermediate results to disk (slow)
- Not suitable for iterative algorithms (machine learning)
- High latency
- Complex to program
- Only works for map-reduce patterns

**Solution:** Move to in-memory processing (like Spark!)

---

## Hadoop YARN Architecture

### What is YARN?

**YARN** = Yet Another Resource Negotiator

YARN is the resource management layer that schedules and allocates resources for applications running on a Hadoop cluster.

### YARN Architecture Components

```
                    ┌──────────────────┐
                    │     Client       │
                    │     Client       │
                    └─────────┬────────┘
                              │
                    ┌─────────▼────────────────┐
                    │       Master             │
                    │  ┌─────────────────┐    │
                    │  │   Resource      │    │
                    │  │   Manager       │    │
                    │  └─────────────────┘    │
                    └────┬─────┬─────┬────────┘
                         │     │     │
           ┌─────────────┘     │     └─────────────┐
           │                   │                   │
    ┌──────▼─────────┐  ┌─────▼──────────┐ ┌─────▼──────────┐
    │   Worker 1     │  │   Worker 2     │ │   Worker 3     │
    │ ┌────────────┐ │  │ ┌────────────┐ │ │ ┌────────────┐ │
    │ │    Node    │ │  │ │    Node    │ │ │ │    Node    │ │
    │ │  Manager   │ │  │ │  Manager   │ │ │ │  Manager   │ │
    │ └────────────┘ │  │ └────────────┘ │ │ └────────────┘ │
    │ ┌──────┐┌────┐ │  │ ┌────┐┌──────┐ │ │ ┌──────┐┌────┐ │
    │ │Cont- ││App │ │  │ │App ││Cont- │ │ │ │Cont- ││Cont│ │
    │ │ainer ││Mstr│ │  │ │Mstr││ainer │ │ │ │ainer ││ainer│ │
    │ └──────┘└────┘ │  │ └────┘└──────┘ │ │ └──────┘└────┘ │
    └────────────────┘  └────────────────┘ └────────────────┘

              Cluster Manager
```

### YARN Components

#### 1. Resource Manager (Master)
- Runs on the master node
- Allocates resources across all applications
- Schedules jobs
- Tracks cluster resources

#### 2. Node Manager (Workers)
- Runs on each worker node
- Manages resources on that specific node
- Reports resource usage to Resource Manager
- Launches and monitors containers

#### 3. Application Master
- One per application
- Negotiates resources with Resource Manager
- Works with Node Managers to execute tasks

#### 4. Container
- Allocation of resources (CPU, memory, disk)
- Where actual computation happens
- Isolated execution environment

### Communication Types

| Type | Description |
|------|-------------|
| **MapReduce Status** | Progress updates (solid line) |
| **Job Submission** | Submit new jobs (dashed line) |
| **Node Status** | Node health reports (dash-dot line) |
| **Resource Request** | Request for resources (dotted line) |

### Educational Cluster Access

**Your cluster web interface:**
```
http://10.16.14.134:8088/cluster
```

This allows you to:
- Monitor running applications
- View cluster resources
- Check job history
- Debug failed jobs

---

## Why Spark is Faster than MapReduce

### The 2014 Sorting Benchmark

**Task:** Sort 100 TB of data

#### MapReduce Performance
- **Time:** 72 minutes
- **Cluster:** 2,100 nodes (50,400 cores)
- **Resources:** Massive hardware requirements

#### Spark Performance
- **Time:** 23 minutes ⚡
- **Cluster:** 206 nodes (6,592 cores)
- **Result:** **3x faster with 10x fewer nodes!**

### Why is Spark So Much Faster?

| Aspect | MapReduce | Spark |
|--------|-----------|-------|
| **Data Storage** | Writes to disk between stages | Keeps data in memory (RAM) |
| **Iterations** | Reloads from disk each time | Reuses in-memory data |
| **Overhead** | High I/O overhead | Minimal I/O |
| **Optimization** | Limited | Advanced query optimization |
| **API** | Complex Java/Python | High-level, easy APIs |

### Real-World Impact

**For iterative algorithms (ML, graph processing):**
- MapReduce: Read from disk → Process → Write to disk → Repeat
- Spark: Load once → Process multiple times in memory

**Speed improvement:** 10x - 100x faster for iterative workloads!

---

## What is Apache Spark?

### Definition

**Apache Spark is a parallel computation engine and a set of libraries for processing massive datasets. It allows you to leverage cluster infrastructure.**

### Spark Architecture Layers

```
┌─────────────────────────────────────────────────────────┐
│          High-Level Components                          │
│  ┌──────────────┐ ┌──────────────┐ ┌────────────────┐  │
│  │  Structured  │ │   Advanced   │ │  Libraries &   │  │
│  │  Streaming   │ │  Analytics   │ │  Ecosystem     │  │
│  └──────────────┘ └──────────────┘ └────────────────┘  │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│          Structured APIs                                │
│  ┌──────────────┐ ┌──────────────┐ ┌────────────────┐  │
│  │   Datasets   │ │  DataFrames  │ │      SQL       │  │
│  └──────────────┘ └──────────────┘ └────────────────┘  │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│          Low-Level APIs                                 │
│  ┌──────────────┐ ┌──────────────────────────────────┐  │
│  │     RDDs     │ │   Distributed Variables          │  │
│  └──────────────┘ └──────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Key Concepts

#### RDD - Resilient Distributed Dataset
- **Low-level API**
- Distributed collection of objects
- Fault-tolerant
- Can be cached in memory
- Foundation of Spark (but now less commonly used directly)

#### DataFrames
- **High-level API** (what we'll focus on)
- Like a table in a relational database
- Organized into named columns
- Optimized execution
- Similar to pandas DataFrames or R data frames

#### Datasets
- Type-safe version of DataFrames (Scala/Java)
- Not available in Python

### Spark Libraries

| Library | Purpose |
|---------|---------|
| **Spark SQL** | Query structured data with SQL |
| **Spark Streaming** | Process real-time data streams |
| **MLlib** | Machine learning algorithms |
| **GraphX** | Graph processing and analysis |

---

## Spark DataFrames

### What is a DataFrame?

**A DataFrame is a collection of data organized into named columns. It is conceptually equivalent to a table in a relational database.**

### DataFrame Characteristics

1. **Named Columns:** Each column has a name and data type
2. **Schema:** Structure is defined (like a table schema)
3. **Distributed:** Data is partitioned across cluster nodes
4. **Immutable:** Operations create new DataFrames
5. **Optimized:** Spark optimizes execution automatically

### DataFrame vs Spreadsheet

```
Spreadsheet on single machine    →    DataFrame partitioned across cluster
┌─────────────────────────────┐       ┌──────────────────────────────────┐
│      Single Computer        │       │         Data Center              │
│  ┌─────────────────────┐   │       │  ┌────────┐ ┌────────┐ ┌──────┐ │
│  │  Name  │ Age │ City │   │       │  │ Part 1 │ │ Part 2 │ │ ...  │ │
│  ├────────┼─────┼──────┤   │       │  │ ┌────┐ │ │ ┌────┐ │ │      │ │
│  │ Alice  │ 25  │ NYC  │   │       │  │ │~~~~│ │ │ │~~~~│ │ │      │ │
│  │ Bob    │ 30  │ LA   │   │   →   │  │ │~~~~│ │ │ │~~~~│ │ │      │ │
│  │ Carol  │ 35  │ SF   │   │       │  │ │~~~~│ │ │ │~~~~│ │ │      │ │
│  └────────┴─────┴──────┘   │       │  │ └────┘ │ │ └────┘ │ │      │ │
└─────────────────────────────┘       │  │ Server │ │ Server │ │Server│ │
                                      │  └────────┘ └────────┘ └──────┘ │
                                      └──────────────────────────────────┘
```

### DataFrame Schema

Every DataFrame has an associated **schema** that specifies:
- Column names
- Data types
- Whether columns can be null

---

## Creating DataFrames

### Data Sources

DataFrames can be created from:
- **Structured data files** (CSV, JSON, Parquet, ORC)
- **Hive tables**
- **External databases** (JDBC)
- **Existing RDDs**
- **Programmatically** (from lists, tuples)

### Example: Reading CSV from HDFS

```scala
val flightData2015 = spark
  .read
  .option("inferSchema", "true")
  .option("header", "true")
  .csv("/user/test/flightdata/")
```

**Breaking it down:**

| Component | Purpose |
|-----------|---------|
| `spark.read` | Start reading data |
| `.option("inferSchema", "true")` | Automatically detect column types |
| `.option("header", "true")` | First row contains column names |
| `.csv("path")` | Read CSV file from HDFS path |

### Other Read Formats

```scala
// JSON
val df = spark.read.json("/path/to/file.json")

// Parquet (columnar format, very efficient)
val df = spark.read.parquet("/path/to/file.parquet")

// Hive table
val df = spark.read.table("database.tablename")

// JDBC (external database)
val df = spark.read
  .format("jdbc")
  .option("url", "jdbc:postgresql://host/database")
  .option("dbtable", "schema.tablename")
  .option("user", "username")
  .option("password", "password")
  .load()
```

---

## DataFrame Operations

### 1. Displaying Data - show()

```scala
flightData2015.show()
```

**Output:**
```
+-----------------+-------------------+-----+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|    United States|            Romania|   15|
|    United States|            Croatia|    1|
|    United States|            Ireland|  344|
|            Egypt|      United States|   15|
|    United States|              India|   62|
|    United States|          Singapore|    1|
|    United States|            Grenada|   62|
|       Costa Rica|      United States|  588|
|          Senegal|      United States|   40|
|          Moldova|      United States|    1|
|    United States|       Sint Maarten|  325|
|    United States|     Marshall Islands|   39|
|           Guyana|      United States|   64|
|            Malta|      United States|    1|
|         Anguilla|      United States|   41|
|          Bolivia|      United States|   30|
|    United States|           Paraguay|    6|
|          Algeria|      United States|    4|
|Turks and Caicos |      United States|  230|
|    United States|          Gibraltar|    1|
+-----------------+-------------------+-----+
only showing top 20 rows
```

### 2. Display Schema - printSchema()

```scala
flightData2015.printSchema()
```

**Output:**
```
root
 |-- DEST_COUNTRY_NAME: string (nullable = true)
 |-- ORIGIN_COUNTRY_NAME: string (nullable = true)
 |-- count: integer (nullable = true)
```

**Information shown:**
- Column names
- Data types (string, integer, etc.)
- Nullable status (can contain NULL values)

### 3. Select Columns - select()

```scala
flightData2015.select("DEST_COUNTRY_NAME").show()
```

**Output:**
```
+-----------------+
|DEST_COUNTRY_NAME|
+-----------------+
|    United States|
|    United States|
|    United States|
|            Egypt|
|    United States|
|    United States|
|    United States|
|       Costa Rica|
|          Senegal|
|          Moldova|
|    United States|
|    United States|
|           Guyana|
|            Malta|
|         Anguilla|
|          Bolivia|
|    United States|
|          Algeria|
|Turks and Caicos |
|    United States|
+-----------------+
only showing top 20 rows
```

**Select multiple columns:**
```scala
flightData2015.select("DEST_COUNTRY_NAME", "count").show()
```

### 4. Filter Rows - filter()

```scala
flightData2015.filter(col("count") > 100).show()
```

**Output:**
```
+-----------------+-------------------+-----+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME|count|
+-----------------+-------------------+-----+
|    United States|            Ireland|  344|
|       Costa Rica|      United States|  588|
|    United States|       Sint Maarten|  325|
|Turks and Caicos |      United States|  230|
|            Italy|      United States|  382|
|    United States|            Russia|  161|
|    United States|        Netherlands|  660|
|          Iceland|      United States|  181|
|       Luxembourg|      United States|  155|
|         Honduras|      United States|  362|
|      The Bahamas|      United States|  955|
|      El Salvador|      United States|  561|
|      Switzerland|      United States|  294|
|     Sint Maarten|      United States|  325|
|        Hong Kong|      United States|  332|
|Trinidad and Tobago|    United States|  211|
|    United States|            Ecuador|  300|
|           Mexico|      United States| 7140|
|          Ecuador|      United States|  268|
|    United States|           Portugal|  134|
+-----------------+-------------------+-----+
only showing top 20 rows
```

**Other filter examples:**
```scala
// Multiple conditions
flightData2015.filter(col("count") > 100 && col("count") < 500).show()

// String matching
flightData2015.filter(col("DEST_COUNTRY_NAME") === "United States").show()

// Using SQL-like syntax
flightData2015.filter("count > 100").show()
```

### 5. Sort Data - sort()

```scala
flightData2015.sort(col("count").desc).show()
```

**Output:**
```
+-----------------+-------------------+------+
|DEST_COUNTRY_NAME|ORIGIN_COUNTRY_NAME| count|
+-----------------+-------------------+------+
|    United States|      United States|370002|
|    United States|             Canada|  8483|
|           Canada|      United States|  8399|
|    United States|             Mexico|  7187|
|           Mexico|      United States|  7140|
|   United Kingdom|      United States|  2025|
|    United States|     United Kingdom|  1970|
|            Japan|      United States|  1548|
|    United States|              Japan|  1496|
|          Germany|      United States|  1468|
|    United States| Dominican Republic|  1420|
|Dominican Republic|      United States|  1353|
|    United States|            Germany|  1336|
|      South Korea|      United States|  1048|
|    United States|        The Bahamas|  986|
|      The Bahamas|      United States|  955|
|    United States|             France|  952|
|           France|      United States|  935|
|    United States|              China|  920|
|         Colombia|      United States|  873|
+-----------------+-------------------+------+
only showing top 20 rows
```

**Sort options:**
```scala
// Ascending (default)
df.sort(col("count"))
df.sort(col("count").asc)

// Descending
df.sort(col("count").desc)

// Multiple columns
df.sort(col("DEST_COUNTRY_NAME"), col("count").desc)
```

### 6. Group and Aggregate - groupBy()

```scala
flightData2015
  .groupBy("DEST_COUNTRY_NAME")
  .sum("count")
  .sort(col("sum(count)").desc)
  .show()
```

**Output:**
```
+-----------------+-----------+
|DEST_COUNTRY_NAME|sum(count) |
+-----------------+-----------+
|    United States|     411352|
|           Canada|       8399|
|           Mexico|       7140|
|   United Kingdom|       2025|
|            Japan|       1548|
|          Germany|       1468|
|Dominican Republic|       1353|
|      South Korea|       1048|
|      The Bahamas|        955|
|           France|        935|
|         Colombia|        873|
|           Brazil|        853|
|      Netherlands|        776|
|            China|        772|
|          Jamaica|        666|
|       Costa Rica|        588|
|      El Salvador|        561|
|           Panama|        510|
|             Cuba|        466|
|            Spain|        420|
+-----------------+-----------+
only showing top 20 rows
```

**Other aggregation functions:**
```scala
// Count
df.groupBy("column").count()

// Average
df.groupBy("column").avg("numeric_column")

// Maximum
df.groupBy("column").max("numeric_column")

// Minimum
df.groupBy("column").min("numeric_column")

// Multiple aggregations
df.groupBy("column").agg(
  sum("col1"),
  avg("col2"),
  max("col3")
)
```

---

## Transformations vs Actions

### The Key Concept

Spark operations are divided into two categories:

```
┌─────────────────────────────────────────────────────────┐
│                  TRANSFORMATIONS                        │
│  (Lazy - build execution plan, don't execute yet)       │
│                                                         │
│  • select()    • filter()    • groupBy()               │
│  • sort()      • join()      • distinct()              │
│  • map()       • flatMap()   • union()                 │
│                                                         │
│  → Return new DataFrame                                 │
│  → Not executed immediately                             │
│  → Build logical plan                                   │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│                     ACTIONS                             │
│  (Eager - trigger actual execution)                     │
│                                                         │
│  • show()      • count()     • collect()               │
│  • take()      • save()      • write()                 │
│  • foreach()   • reduce()    • first()                 │
│                                                         │
│  → Trigger computation                                  │
│  → Return results or write to storage                   │
│  → Execute the entire plan                              │
└─────────────────────────────────────────────────────────┘
```

### Example: Understanding Lazy Evaluation

```scala
// These are all TRANSFORMATIONS (lazy)
val result = flightData2015
  .groupBy("DEST_COUNTRY_NAME")     // Not executed yet
  .sum("count")                      // Not executed yet
  .sort(col("sum(count)").desc)      // Not executed yet

// Nothing has actually run yet!

// This is an ACTION (eager)
result.show()   // NOW everything executes!
```

### Execution Plans

#### Logical Plan

Shows **what** needs to be done (high-level):
```
flightData2015 → Aggregate → Sort → Show
```

#### Physical Plan

Shows **how** it will be executed (low-level):
```scala
flightData2015
  .groupBy("DEST_COUNTRY_NAME")
  .sum("count")
  .sort(col("sum(count)").desc)
  .explain()
```

**Output:**
```
== Physical Plan ==
AdaptiveSparkPlan isFinalPlan=false
+- Sort [sum(count)#26L DESC NULLS LAST], true, 0
   +- Exchange rangepartitioning(sum(count)#26L DESC NULLS LAST, 200)
      +- HashAggregate(keys=[DEST_COUNTRY_NAME#16], functions=[sum(count#18)])
         +- Exchange hashpartitioning(DEST_COUNTRY_NAME#16, 200)
            +- HashAggregate(keys=[DEST_COUNTRY_NAME#16], functions=[partial_sum(count#18)])
               +- FileScan csv [DEST_COUNTRY_NAME#16,count#18]
                  Format: CSV, Location: InMemoryFileIndex
```

**What this shows:**
- How data will be read (FileScan)
- How aggregation will happen (HashAggregate)
- How data will be shuffled (Exchange)
- How sorting will occur (Sort)

### Why Lazy Evaluation?

**Benefits:**

1. **Optimization:** Spark can optimize the entire workflow before executing
2. **Efficiency:** Combine multiple operations into fewer stages
3. **Fault Tolerance:** Can recompute only failed partitions
4. **Resource Management:** Better scheduling of cluster resources

**Example optimization:**
```scala
// You write:
df.filter(col("age") > 20).filter(col("city") === "NYC")

// Spark optimizes to:
df.filter(col("age") > 20 && col("city") === "NYC")
```

---

## Spark SQL Integration

### Creating Views from DataFrames

You can register any DataFrame as a SQL table/view:

```scala
flightData2015.createOrReplaceTempView("flight_data_2015")
```

**Now you can query it with SQL!**

### Using spark.sql()

```scala
val sqlWay = spark.sql("""
  SELECT DEST_COUNTRY_NAME, sum(count)
  FROM flight_data_2015
  GROUP BY DEST_COUNTRY_NAME
  ORDER BY sum(count) DESC
  """)
```

**Result:** `sqlWay` is a DataFrame!

### DataFrame API vs SQL - Same Performance!

```scala
// DataFrame API
val dataFrameWay = flightData2015
  .groupBy("DEST_COUNTRY_NAME")
  .sum("count")
  .sort(col("sum(count)").desc)

// SQL
val sqlWay = spark.sql("""
  SELECT DEST_COUNTRY_NAME, sum(count)
  FROM flight_data_2015
  GROUP BY DEST_COUNTRY_NAME
  ORDER BY sum(count) DESC
  """)

// BOTH PRODUCE IDENTICAL EXECUTION PLANS!
dataFrameWay.explain()
sqlWay.explain()
```

### Key Points About Spark SQL

1. **Same transformations, same performance** - Whether you use DataFrame API or SQL
2. **SQL queries return DataFrames** - Can be used in further Spark operations
3. **No performance difference** - Both compile to same optimized plan
4. **Choose based on preference:**
   - SQL: Familiar to database users
   - DataFrame API: More programmatic, type-safe

### Complete Example with SQL

```scala
// Create view
flightData2015.createOrReplaceTempView("flights")

// Run SQL query
val topDestinations = spark.sql("""
  SELECT
    DEST_COUNTRY_NAME,
    sum(count) as total_flights
  FROM flights
  WHERE count > 100
  GROUP BY DEST_COUNTRY_NAME
  HAVING sum(count) > 1000
  ORDER BY total_flights DESC
  LIMIT 10
  """)

// Result is a DataFrame - can use DataFrame operations
topDestinations.show()
topDestinations.write.parquet("/output/top_destinations")
```

---

## Spark Architecture

### Application Structure

**Spark Application = Driver Process + Executor Processes**

```
┌───────────────────────────────────────────────────────────────┐
│                        Driver                                 │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                  Spark Session                          │ │
│  │                        ▲                                │ │
│  │                        │                                │ │
│  │                        ▼                                │ │
│  │               Code utilisateur                          │ │
│  │           (Your Spark Program)                          │ │
│  └─────────────────────────────────────────────────────────┘ │
│                          │                                    │
│                          │ Task Assignment                    │
│                          ▼                                    │
└──────────────────────────┼────────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
    ┌────▼─────┐      ┌───▼──────┐     ┌───▼──────┐
    │ Executor │      │ Executor │     │ Executor │
    │ ┌──────┐ │      │ ┌──────┐ │     │ ┌──────┐ │
    │ │ Task │ │      │ │ Task │ │     │ │ Task │ │
    │ └──────┘ │      │ └──────┘ │     │ └──────┘ │
    │ ┌──────┐ │      │ ┌──────┐ │     │ ┌──────┐ │
    │ │ Task │ │      │ │ Task │ │     │ │ Task │ │
    │ └──────┘ │      │ └──────┘ │     │ └──────┘ │
    └────▲─────┘      └────▲─────┘     └────▲─────┘
         │                 │                 │
         │ Results         │ Results         │ Results
         └─────────────────┴─────────────────┘
                           │
                           ▼
              ┌────────────────────────────┐
              │     Cluster Manager        │
              │    (YARN, Mesos, K8s)      │
              └────────────────────────────┘
```

### Components Explained

#### 1. Driver Process

**Responsibilities:**
- Runs the `main()` function
- Creates SparkSession
- Analyzes, distributes, and schedules work across executors
- Maintains information about Spark application
- Responds to user program

**Location:** Can run on cluster or your local machine

#### 2. Executors

**Responsibilities:**
- Execute code assigned by driver
- Report state of computation back to driver
- Store data for caching (if requested)

**Characteristics:**
- Multiple executors run in parallel
- Each executor runs tasks on its data partition
- Isolated from each other

#### 3. Cluster Manager

**Options:**
- **YARN** (Hadoop's resource manager) - Most common
- **Mesos** (Apache cluster manager)
- **Kubernetes** (Container orchestration)
- **Standalone** (Spark's built-in manager)

**Role:**
- Allocates resources to Spark application
- Manages worker nodes
- Monitors node health

### Execution Flow

1. **Submit Application**
   - Driver program is started
   - SparkSession is created

2. **Request Resources**
   - Driver requests executors from cluster manager
   - Cluster manager allocates executor nodes

3. **Distribute Code**
   - Driver sends code to executors
   - Data is partitioned across executors

4. **Execute Tasks**
   - Driver converts user program into tasks
   - Tasks are scheduled on executors
   - Executors run tasks in parallel

5. **Return Results**
   - Results are sent back to driver
   - Driver combines results

6. **Cleanup**
   - Application completes
   - Resources are released

---

## Complete Examples

### Example 1: Flight Data Analysis

**Objective:** Analyze 2015 flight data to find top international routes

```scala
// 1. Create SparkSession
val spark = SparkSession.builder()
  .appName("Flight Analysis")
  .master("local[*]")
  .getOrCreate()

// 2. Read data
val flightData = spark.read
  .option("inferSchema", "true")
  .option("header", "true")
  .csv("/user/test/flightdata/2015-summary.csv")

// 3. Explore data
flightData.printSchema()
flightData.show(5)

// 4. Find top destinations
val topDestinations = flightData
  .groupBy("DEST_COUNTRY_NAME")
  .sum("count")
  .withColumnRenamed("sum(count)", "total_flights")
  .sort(col("total_flights").desc)
  .limit(10)

topDestinations.show()

// 5. Find routes with most flights
val topRoutes = flightData
  .filter(col("ORIGIN_COUNTRY_NAME") =!= col("DEST_COUNTRY_NAME"))
  .sort(col("count").desc)
  .limit(10)

topRoutes.show()

// 6. Save results
topDestinations.write
  .mode("overwrite")
  .parquet("/output/top_destinations")
```

### Example 2: Using SQL

```scala
// Register as table
flightData.createOrReplaceTempView("flights")

// Complex SQL query
val internationalFlights = spark.sql("""
  SELECT
    ORIGIN_COUNTRY_NAME as origin,
    DEST_COUNTRY_NAME as destination,
    count as num_flights,
    CASE
      WHEN count > 1000 THEN 'High Traffic'
      WHEN count > 100 THEN 'Medium Traffic'
      ELSE 'Low Traffic'
    END as traffic_category
  FROM flights
  WHERE ORIGIN_COUNTRY_NAME != DEST_COUNTRY_NAME
  ORDER BY count DESC
  """)

internationalFlights.show(20)

// Group by traffic category
val trafficSummary = spark.sql("""
  WITH categorized AS (
    SELECT
      CASE
        WHEN count > 1000 THEN 'High'
        WHEN count > 100 THEN 'Medium'
        ELSE 'Low'
      END as category,
      count
    FROM flights
    WHERE ORIGIN_COUNTRY_NAME != DEST_COUNTRY_NAME
  )
  SELECT
    category,
    COUNT(*) as num_routes,
    SUM(count) as total_flights,
    AVG(count) as avg_flights
  FROM categorized
  GROUP BY category
  ORDER BY total_flights DESC
  """)

trafficSummary.show()
```

### Example 3: Data Cleaning and Transformation

```scala
// Read potentially messy data
val rawData = spark.read
  .option("header", "true")
  .csv("/data/raw/flights.csv")

// Clean and transform
val cleanData = rawData
  // Remove nulls
  .filter(col("count").isNotNull)
  .filter(col("ORIGIN_COUNTRY_NAME").isNotNull)

  // Filter out invalid data
  .filter(col("count") > 0)

  // Add calculated columns
  .withColumn("is_domestic",
    col("ORIGIN_COUNTRY_NAME") === col("DEST_COUNTRY_NAME"))
  .withColumn("flight_category",
    when(col("count") > 1000, "High Volume")
    .when(col("count") > 100, "Medium Volume")
    .otherwise("Low Volume"))

  // Rename columns for clarity
  .withColumnRenamed("count", "num_flights")
  .withColumnRenamed("ORIGIN_COUNTRY_NAME", "origin")
  .withColumnRenamed("DEST_COUNTRY_NAME", "destination")

// Save cleaned data
cleanData.write
  .mode("overwrite")
  .partitionBy("flight_category")
  .parquet("/data/clean/flights")
```

---

## Spark Command Reference

### Starting Spark

```bash
# Spark Shell (Scala)
spark-shell

# PySpark (Python)
pyspark

# Submit application
spark-submit --class MainClass --master yarn myapp.jar
```

### Creating SparkSession

```scala
import org.apache.spark.sql.SparkSession

val spark = SparkSession.builder()
  .appName("MyApp")
  .master("local[*]")  // or "yarn" for cluster
  .getOrCreate()

// Access context
val sc = spark.sparkContext
```

### Reading Data

```scala
// CSV
spark.read.option("header", "true").csv("path")

// JSON
spark.read.json("path")

// Parquet
spark.read.parquet("path")

// Table
spark.read.table("tablename")
```

### DataFrame Operations

```scala
// Select
df.select("col1", "col2")
df.select(col("col1"), col("col2") + 1)

// Filter
df.filter(col("age") > 21)
df.where("age > 21")

// GroupBy
df.groupBy("category").count()
df.groupBy("category").agg(sum("amount"), avg("price"))

// Sort
df.sort(col("age").desc)
df.orderBy("age")

// Limit
df.limit(10)

// Distinct
df.select("country").distinct()

// Drop duplicates
df.dropDuplicates("id")

// Rename column
df.withColumnRenamed("old_name", "new_name")

// Add column
df.withColumn("new_col", col("old_col") * 2)

// Drop column
df.drop("unwanted_col")
```

### Actions

```scala
// Display
df.show()
df.show(50)  // show 50 rows

// Count
df.count()

// Collect (bring to driver - be careful!)
val rows = df.collect()

// Take first n rows
val first10 = df.take(10)

// First row
val firstRow = df.first()

// Write
df.write.parquet("path")
df.write.mode("overwrite").csv("path")
```

---

## Best Practices for Spark

### 1. Data Loading

- Use Parquet for best performance (columnar, compressed)
- Infer schema on small sample, then apply to full dataset
- Partition large datasets appropriately
- Use appropriate file format for your use case

### 2. Transformations

- Chain operations together for optimization
- Use built-in functions instead of UDFs when possible
- Filter early to reduce data size
- Avoid collect() on large datasets

### 3. Performance

- Cache/persist DataFrames you'll reuse
- Use broadcast joins for small tables
- Monitor Spark UI for bottlenecks
- Tune partition size (too many or too few is bad)

### 4. Memory Management

```scala
// Cache in memory
df.cache()
df.persist()

// Uncache when done
df.unpersist()

// Check what's cached
spark.catalog.cacheTable("tablename")
spark.catalog.uncacheTable("tablename")
```

### 5. Debugging

```scala
// Explain execution plan
df.explain()
df.explain(true)  // Extended explanation

// Count partitions
df.rdd.getNumPartitions

// Sample data for testing
df.sample(0.1)  // 10% sample
```

---

## Common Operations Cheat Sheet

| Task | DataFrame API | SQL |
|------|---------------|-----|
| **Select columns** | `df.select("col1", "col2")` | `SELECT col1, col2 FROM table` |
| **Filter rows** | `df.filter(col("age") > 21)` | `SELECT * FROM table WHERE age > 21` |
| **Group and count** | `df.groupBy("city").count()` | `SELECT city, COUNT(*) FROM table GROUP BY city` |
| **Order by** | `df.orderBy(col("age").desc)` | `SELECT * FROM table ORDER BY age DESC` |
| **Join** | `df1.join(df2, "id")` | `SELECT * FROM t1 JOIN t2 ON t1.id = t2.id` |
| **Distinct** | `df.select("country").distinct()` | `SELECT DISTINCT country FROM table` |
| **Aggregate** | `df.groupBy("cat").agg(sum("amount"))` | `SELECT cat, SUM(amount) FROM table GROUP BY cat` |

---

## Spark vs Hive vs MapReduce

### Quick Comparison

| Feature | MapReduce | Hive | Spark |
|---------|-----------|------|-------|
| **Speed** | Slow (disk-based) | Slow (uses MR) | Fast (in-memory) |
| **API** | Complex Java | SQL | SQL + DataFrames + RDDs |
| **Latency** | High | High | Low |
| **Use Case** | Batch processing | SQL on Hadoop | General-purpose |
| **Learning Curve** | Steep | Easy (SQL) | Moderate |
| **Iterative Algorithms** | Poor | Poor | Excellent |
| **Real-time** | No | No | Yes (Streaming) |
| **In-Memory** | No | No | Yes |

### When to Use What?

| Tool | Best For |
|------|----------|
| **Spark** | Most big data workloads, ML, iterative algorithms, streaming |
| **Hive** | Simple SQL queries, legacy Hadoop workflows |
| **MapReduce** | Legacy applications (generally avoid for new projects) |

---

## Key Takeaways - Spark

### Critical Concepts

✅ **Spark is 10-100x faster than MapReduce** because of in-memory processing

✅ **DataFrames are like distributed tables** - think SQL tables spread across cluster

✅ **Transformations are lazy** (filter, select, groupBy) - Actions are eager (show, count, write)

✅ **Spark SQL and DataFrame API produce identical execution plans** - use what you prefer

✅ **Architecture: Driver + Executors** - Driver coordinates, Executors do the work

✅ **Runs on YARN** - Spark works WITH Hadoop, not against it

### Review Checklist

- [ ] Understand Spark's place in Hadoop ecosystem
- [ ] Know why Spark is faster than MapReduce (in-memory vs disk)
- [ ] Can create DataFrames from CSV/JSON/Parquet
- [ ] Know basic DataFrame operations (select, filter, groupBy, sort)
- [ ] Understand transformations vs actions
- [ ] Can create temp views and use Spark SQL
- [ ] Understand Driver vs Executor architecture
- [ ] Can explain logical vs physical plans
- [ ] Know when to use cache/persist
- [ ] Can monitor jobs via Spark UI

---

## Questions to Explore

1. Why does Spark keep data in memory while MapReduce writes to disk?
2. What happens when data doesn't fit in memory?
3. How does Spark handle node failures?
4. When should you use DataFrame API vs SQL?
5. What is the difference between cache() and persist()?
6. How does Spark optimize query execution?
7. What are partitions and why do they matter?
8. How does broadcast join work?

---

**Course Material 3 Complete:** Apache Spark
**Next Topics:** Advanced Spark (RDDs, Spark Streaming, MLlib), Performance Tuning

---
---

# COURSE 4: Spark RDDs (Resilient Distributed Datasets)

## Table of Contents - Course 4

1. [What is an RDD?](#what-is-an-rdd)
2. [RDD Core Characteristics](#rdd-core-characteristics)
3. [RDD Data Sources](#rdd-data-sources)
4. [RDD Operations Overview](#rdd-operations-overview)
5. [Transformations (Lazy)](#transformations-lazy)
6. [Actions (Eager)](#actions-eager)
7. [Complete RDD Example - Flight Data](#complete-rdd-example---flight-data)
8. [Transformation Examples with Diagrams](#transformation-examples-with-diagrams)
9. [Action Examples with Code](#action-examples-with-code)
10. [RDD vs DataFrame Comparison](#rdd-vs-dataframe-comparison)
11. [Best Practices](#best-practices-rdd)
12. [Review Checklist](#review-checklist-rdd)

---

## What is an RDD?

### Definition

**RDD = Resilient Distributed Dataset**

An RDD is Spark's **fundamental data abstraction** - a collection of elements that can be processed in parallel across a cluster.

```
┌────────────────────────────────────────────────┐
│              RDD Abstraction                   │
│                                                │
│  Logical View:    [data1, data2, data3, ...]   │
│                                                │
│  Physical Reality:                             │
│    Node 1: [data1, data2]                     │
│    Node 2: [data3, data4]                     │
│    Node 3: [data5, data6]                     │
│                                                │
│  → Distributed collection processed in parallel│
└────────────────────────────────────────────────┘
```

### RDD Conceptual Model

```
┌─────────┐                              ┌──────────┐
│   RDD   │ ──Transformation──> │   RDD   │ ──Transformation──> │   RDD   │ ──Action──> │ Résultat │
└─────────┘                     └─────────┘                     └─────────┘             └──────────┘
     │                               │                               │
     └───────────────────────────────┴───────────────────────────────┘
                        Lazy Evaluation
                (Nothing happens until action!)
```

---

## RDD Core Characteristics

### The "R" - Resilient (Tolérant à la panne)

**RDDs are fault-tolerant:**
- If a node fails, Spark can rebuild the lost partition
- Uses **lineage graph** (remembers how it was created)
- No need to replicate data - just recompute if needed

```
RDD Lineage Example:
─────────────────────
File.txt → map() → filter() → reduceByKey()
                                    ↑
                         If this fails, Spark can:
                         1. Re-read File.txt
                         2. Re-apply map()
                         3. Re-apply filter()
                         4. Re-compute reduceByKey()
```

### The "D" - Distributed

**Data is automatically split across cluster nodes:**

```
HDFS File (1GB):
┌────────────────────────────────────────┐
│ Block1 │ Block2 │ Block3 │ Block4      │
└────────────────────────────────────────┘
        ↓
RDD Partitions (distributed):
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│ Node 1   │  │ Node 2   │  │ Node 3   │  │ Node 4   │
│ Part 1   │  │ Part 2   │  │ Part 3   │  │ Part 4   │
└──────────┘  └──────────┘  └──────────┘  └──────────┘
```

### The "D" - Dataset

**Collection of elements:**
- Can be any type: integers, strings, tuples, custom objects
- **Most common: Key-Value pairs (tuples)**

```scala
// Examples of RDD data types:
RDD[Int]              // Simple integers: 1, 2, 3, 4
RDD[String]           // Strings: "hello", "world"
RDD[(String, Int)]    // Key-Value pairs: ("USA", 100), ("France", 50)
RDD[Array[String]]    // Arrays of strings
```

### Key Property: Tuple Independence

**Each tuple/element is independent:**
- Can be processed separately
- No dependencies between elements
- Enables parallel processing

```
Element 1: ("USA", 100)     → Process on Node 1
Element 2: ("France", 50)   → Process on Node 2  ← No dependency!
Element 3: ("Germany", 75)  → Process on Node 3
```

---

## RDD Data Sources

RDDs can be created from multiple sources:

### 1. HDFS Files

```scala
val rdd = sc.textFile("hdfs://path/to/file.txt")
```

### 2. Local Files

```scala
val rdd = sc.textFile("file:///local/path/data.csv")
```

### 3. In-Memory Collections

```scala
val rdd = sc.parallelize(List(1, 2, 3, 4, 5))
val rddStrings = sc.parallelize(List("A", "B", "C"))
```

### 4. Amazon S3

```scala
val rdd = sc.textFile("s3a://bucket-name/path/to/file")
```

### 5. NoSQL Databases

```scala
// Example with Cassandra
import com.datastax.spark.connector._
val rdd = sc.cassandraTable("keyspace", "table")
```

### 6. Custom Implementation

```scala
// You can implement your own RDD source
class CustomRDD extends RDD[T] {
  // Custom logic here
}
```

### Data Sources Overview Diagram

```
           ┌──────────────────────────────┐
           │     RDD Data Sources         │
           └──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
    ┌───▼───┐     ┌───▼───┐     ┌───▼────┐
    │ HDFS  │     │ Local │     │ Memory │
    │ Files │     │ Files │     │ (List) │
    └───────┘     └───────┘     └────────┘
        │              │              │
    ┌───▼───┐     ┌───▼────┐    ┌───▼────┐
    │   S3  │     │ NoSQL  │    │ Custom │
    │       │     │   DB   │    │  Impl  │
    └───────┘     └────────┘    └────────┘
```

---

## RDD Operations Overview

There are **TWO types** of RDD operations:

### 1. Transformations (Lazy)

**Manipulate an RDD, return another RDD**

```
RDD → Transformation → NEW RDD
```

- **Lazy execution:** Not computed immediately
- Build a computation DAG (Directed Acyclic Graph)
- Executed only when an action is called

**Examples:**
- `map()`, `filter()`, `groupByKey()`, `reduceByKey()`, `join()`

### 2. Actions (Eager)

**Do NOT return an RDD**

```
RDD → Action → Result (value, file, side effect)
```

- **Eager execution:** Trigger immediate computation
- Execute all previous transformations in the DAG
- Return results to driver or write to storage

**Examples:**
- `count()`, `collect()`, `saveAsHadoopFile()`, `foreach()`

### Lazy vs Eager Visualization

```
CODE:
────
val rdd1 = sc.textFile("data.txt")        // Transformation (lazy)
val rdd2 = rdd1.map(_.split(","))         // Transformation (lazy)
val rdd3 = rdd2.filter(_(2) == "USA")     // Transformation (lazy)
val rdd4 = rdd3.map(x => (x(0), x(1)))    // Transformation (lazy)
val count = rdd4.count()                  // ACTION! (triggers execution)
                                              ↑
                                    ALL transformations execute NOW!

EXECUTION TIMELINE:
──────────────────
Time 0: Nothing happens
Time 1: Nothing happens
Time 2: Nothing happens
Time 3: Nothing happens
Time 4: count() called → ENTIRE DAG executes at once!
```

---

## Transformations (Lazy)

Transformations create a new RDD from an existing one **without immediate execution**.

### Common Transformations

| Transformation | Input → Output | Description |
|---------------|----------------|-------------|
| `map(func)` | RDD[T] → RDD[U] | Apply function to each element |
| `filter(func)` | RDD[T] → RDD[T] | Keep only elements matching predicate |
| `flatMap(func)` | RDD[T] → RDD[U] | Map each element to 0+ outputs |
| `groupByKey()` | RDD[(K,V)] → RDD[(K, Iterable[V])] | Group values by key |
| `reduceByKey(func)` | RDD[(K,V)] → RDD[(K,V)] | Aggregate values by key |
| `sortByKey()` | RDD[(K,V)] → RDD[(K,V)] | Sort by key |
| `join(otherRDD)` | RDD[(K,V)] + RDD[(K,W)] → RDD[(K,(V,W))] | Inner join by key |
| `union(otherRDD)` | RDD[T] + RDD[T] → RDD[T] | Combine two RDDs |
| `intersection(otherRDD)` | RDD[T] + RDD[T] → RDD[T] | Common elements |
| `distinct()` | RDD[T] → RDD[T] | Remove duplicates |
| `cartesian(otherRDD)` | RDD[T] + RDD[U] → RDD[(T,U)] | Cartesian product |

### Transformation Details

#### map() - One-to-One Transformation

**Signature:**
```scala
def map[U](f: T => U): RDD[U]
```

**Purpose:** Transform each element individually

**Example:**
```scala
val rdd = sc.parallelize(List(1, 4, 6, 8))
val result = rdd.map(x => x + 1)
// Result: [2, 5, 7, 9]
```

#### filter() - Selective Keep

**Signature:**
```scala
def filter(f: T => Boolean): RDD[T]
```

**Purpose:** Keep only elements that satisfy a condition

**Example:**
```scala
val rdd = sc.parallelize(List(1, 4, 6, 8))
val result = rdd.filter(x => x % 2 == 0)
// Result: [4, 6, 8]
```

#### groupByKey() - Group Values by Key

**Signature:**
```scala
def groupByKey(): RDD[(K, Iterable[V])]
```

**Purpose:** Group all values with the same key together

**Example:**
```scala
val pairRDD = sc.parallelize(List(
  ("even", 2), ("odd", 5), ("odd", 7), ("even", 8)
))
val result = pairRDD.groupByKey()
// Result:
// ("even", [2, 8])
// ("odd", [5, 7])
```

**⚠️ Warning:** `groupByKey()` shuffles all data - can be expensive!

#### reduceByKey() - Aggregate Values by Key

**Signature:**
```scala
def reduceByKey(func: (V, V) => V): RDD[(K, V)]
```

**Purpose:** Combine values with the same key using a function

**Example:**
```scala
val pairRDD = sc.parallelize(List(
  ("even", 2), ("odd", 5), ("odd", 7), ("even", 8)
))
val result = pairRDD.reduceByKey((v1, v2) => v1 + v2)
// Result:
// ("even", 10)  // 2 + 8
// ("odd", 12)   // 5 + 7
```

**✅ Best Practice:** Prefer `reduceByKey()` over `groupByKey()` when possible
- `reduceByKey()` does local aggregation before shuffling
- Much more efficient!

#### join() - Combine Two RDDs by Key

**Signature:**
```scala
def join[W](other: RDD[(K, W)]): RDD[(K, (V, W))]
```

**Purpose:** Inner join two RDDs on their keys

**Example:**
```scala
val rdd1 = sc.parallelize(List(("B", "A"), ("C", "D"), ("D", "A"), ("A", "B")))
val rdd2 = sc.parallelize(List(("B", 2), ("C", 5), ("D", 7), ("A", 8)))
val result = rdd1.join(rdd2)
// Result:
// ("B", ("A", 2))
// ("C", ("D", 5))
// ("D", ("A", 7))
// ("A", ("B", 8))
```

#### union() - Combine Two RDDs

**Example:**
```scala
val rdd1 = sc.parallelize(List(1, 2, 4))
val rdd2 = sc.parallelize(List(1, 4, 5))
val result = rdd1.union(rdd2)
// Result: [1, 2, 4, 1, 4, 5]  (duplicates kept!)
```

#### intersection() - Common Elements

**Example:**
```scala
val rdd1 = sc.parallelize(List(1, 2, 4))
val rdd2 = sc.parallelize(List(1, 4, 5))
val result = rdd1.intersection(rdd2)
// Result: [1, 4]
```

#### distinct() - Remove Duplicates

**Example:**
```scala
val rdd = sc.parallelize(Array(1, 1, 2, 4, 5, 6, 8, 8, 9, 10, 11, 11))
val result = rdd.distinct()
// Result: [1, 2, 4, 5, 6, 8, 9, 10, 11]
```

#### cartesian() - Cartesian Product

**Example:**
```scala
val rddStrings = sc.parallelize(List("A", "B", "C"))
val rddIntegers = sc.parallelize(List(1, 4, 5))
val result = rddStrings.cartesian(rddIntegers)
// Result:
// ("A", 1), ("A", 4), ("A", 5),
// ("B", 1), ("B", 4), ("B", 5),
// ("C", 1), ("C", 4), ("C", 5)
```

#### sortByKey() - Sort Pair RDD by Keys

**Example:**
```scala
val unsortedRDD = sc.parallelize(List(
  ("B", 2), ("C", 5), ("D", 7), ("A", 8)
))
val result = unsortedRDD.sortByKey()
// Result:
// ("A", 8), ("B", 2), ("C", 5), ("D", 7)
```

---

## Actions (Eager)

Actions trigger the execution of transformations and return results.

### Common Actions

| Action | Return Type | Description |
|--------|-------------|-------------|
| `count()` | Long | Count number of elements |
| `collect()` | Array[T] | Return all elements to driver |
| `first()` | T | Return first element |
| `take(n)` | Array[T] | Return first n elements |
| `foreach(func)` | Unit | Execute function on each element |
| `saveAsTextFile(path)` | Unit | Save to HDFS as text |
| `saveAsHadoopFile(path)` | Unit | Save in Hadoop format |
| `isEmpty()` | Boolean | Check if RDD is empty |
| `reduce(func)` | T | Aggregate all elements |

### Action Details

#### count() - Count Elements

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val countVal = rddIntegers.count()
// Result: 3
```

#### collect() - Retrieve All Elements

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val collectedList = rddIntegers.collect()
// Result: Array(1, 4, 5)
```

**⚠️ Warning:** Only use `collect()` on small datasets!
- Brings ALL data to driver node
- Can cause out-of-memory errors on large datasets

#### first() - Get First Element

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val first = rddIntegers.first()
// Result: 1
```

#### take(n) - Get First N Elements

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val takeTwo = rddIntegers.take(2)
// Result: Array(1, 4)
```

#### isEmpty() - Check if Empty

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val isRDDEmpty = rddIntegers.filter(a => a.equals(5)).isEmpty()
// Result: false (because filter found element 5)
```

#### foreach() - Execute Function on Each Element

**Example:**
```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
rddIntegers.collect().foreach(x =>
  println("The element values of the RDD are ::" + x)
)
// Output:
// The element values of the RDD are ::1
// The element values of the RDD are ::4
// The element values of the RDD are ::5
```

**Note:** `foreach()` runs on executors, output goes to executor logs (not driver console)

#### saveAsHadoopFile() - Save to HDFS

**Example:**
```scala
val result = rdd.reduceByKey(_ + _)
result.saveAsHadoopFile(
  "hdfs://output/path",
  classOf[String],
  classOf[Int],
  classOf[TextOutputFormat[String, Int]]
)
```

---

## Complete RDD Example - Flight Data

Let's walk through the complete flight data example from the course.

### Problem Statement

**Goal:** Calculate the total number of flights per destination country from a CSV file.

**Input File:** `2015-summary.csv`

```
DEST_COUNTRY_NAME,ORIGIN_COUNTRY_NAME,count
United States,Romania,15
United States,Croatia,1
United States,Ireland,344
...
```

**Desired Output:**

```
(United States,411293)
(Canada,8399)
(Mexico,7140)
(United Kingdom,2025)
...
```

### Complete Spark Program

```scala
// Read file and create RDD
val inputFile = "2015-summary.csv"
val rddFromFile = sc.textFile(inputFile)  // RDD[String]

// Transform and process
val result = rddFromFile
  .filter(f => f(2) != "count")           // Remove header
  .map(f => f.split(","))                 // Split by comma → RDD[Array[String]]
  .map(f => (f(0), f(2).toInt))          // Extract (destination, count) → RDD[(String, Int)]
  .reduceByKey(_ + _)                     // Sum counts by destination → RDD[(String, Int)]
  .sortBy(_._2, false)                    // Sort by count descending
  .collect()                              // Collect to driver
  .foreach(f => println(f))               // Print results
```

### Step-by-Step Breakdown

#### Step 1: Read File

```scala
val rddFromFile = sc.textFile("2015-summary.csv")
```

**Type:** `RDD[String]`

**Content:**
```
"United States,Romania,15"
"United States,Croatia,1"
"United States,Ireland,344"
...
```

#### Step 2: Split Lines into Arrays

```scala
val rdd1 = rddFromFile.map(f => f.split(","))
```

**Type:** `RDD[Array[String]]`

**Content:**
```
Array("United States", "Romania", "15")
Array("United States", "Croatia", "1")
Array("United States", "Ireland", "344")
...
```

#### Step 3: Filter Header

```scala
val rdd2 = rdd1.filter(f => f(2) != "count")
```

**Type:** `RDD[Array[String]]`

**Removes:** `Array("DEST_COUNTRY_NAME", "ORIGIN_COUNTRY_NAME", "count")`

#### Step 4: Extract Key-Value Pairs

```scala
val rdd3 = rdd2.map(f => (f(0), f(2).toInt))
```

**Type:** `RDD[(String, Int)]`

**Content:**
```
("United States", 15)
("United States", 1)
("United States", 344)
("Canada", 100)
("Canada", 50)
...
```

#### Step 5: Aggregate by Key

```scala
val rdd4 = rdd3.reduceByKey(_ + _)
```

**Type:** `RDD[(String, Int)]`

**Content:**
```
("United States", 411293)  // Sum of all US flights
("Canada", 8399)           // Sum of all Canada flights
("Mexico", 7140)
...
```

**How reduceByKey works:**
```
Input pairs with same key:
("United States", 15)
("United States", 1)
("United States", 344)
...

Reduction:
15 + 1 = 16
16 + 344 = 360
... (continues for all US entries)
Final: 411293
```

#### Step 6: Sort by Count

```scala
val rdd5 = rdd4.sortBy(_._2, false)
```

**Type:** `RDD[(String, Int)]`

**Sorted descending by count:**
```
("United States", 411293)  ← Highest
("Canada", 8399)
("Mexico", 7140)
...
("Angola", 13)             ← Lowest
```

#### Step 7: Collect and Print

```scala
rdd5.collect().foreach(f => println(f))
```

**Action triggers execution of entire DAG!**

**Output:**
```
(United States,411293)
(Canada,8399)
(Mexico,7140)
(United Kingdom,2025)
(Japan,1548)
...
```

### RDD Transformation Pipeline Diagram

```
2015-summary.csv
      │
      ▼
┌─────────────┐
│ rddFromFile │ RDD[String]
└─────────────┘
      │ map(_.split(","))
      ▼
┌─────────────┐
│    rdd1     │ RDD[Array[String]]
└─────────────┘
      │ filter(_(2) != "count")
      ▼
┌─────────────┐
│    rdd2     │ RDD[Array[String]]
└─────────────┘
      │ map(f => (f(0), f(2).toInt))
      ▼
┌─────────────┐
│    rdd3     │ RDD[(String, Int)]
└─────────────┘
      │ reduceByKey(_ + _)
      ▼
┌─────────────┐
│    rdd4     │ RDD[(String, Int)]
└─────────────┘
      │ sortBy(_._2, false)
      ▼
┌─────────────┐
│    rdd5     │ RDD[(String, Int)]
└─────────────┘
      │ collect() ← ACTION!
      ▼
┌─────────────┐
│   println   │
└─────────────┘
```

---

## Transformation Examples with Diagrams

### Map Transformation

**Visual:**

```
┌───┐     map(x => x+1)     ┌───┐
│ 1 │  ────────────────>    │ 2 │
└───┘                       └───┘

┌───┐                       ┌───┐
│ 4 │  ────────────────>    │ 5 │
└───┘                       └───┘

┌───┐                       ┌───┐
│ 6 │  ────────────────>    │ 7 │
└───┘                       └───┘

┌───┐                       ┌───┐
│ 8 │  ────────────────>    │ 9 │
└───┘                       └───┘
```

**Code:**
```scala
rdd.map(x => x + 1)
```

### Filter Transformation

**Visual:**

```
┌───┐
│ 1 │  ──────X (rejected)
└───┘

┌───┐     filter(even)      ┌───┐
│ 4 │  ────────────────>    │ 4 │
└───┘                       └───┘

┌───┐                       ┌───┐
│ 6 │  ────────────────>    │ 6 │
└───┘                       └───┘

┌───┐                       ┌───┐
│ 8 │  ────────────────>    │ 8 │
└───┘                       └───┘
```

**Code:**
```scala
rdd.filter(x => x % 2 == 0)
```

### Union Transformation

**Visual:**

```
RDD1:           RDD2:           Result:
┌───┐           ┌───┐           ┌───┐
│ 1 │           │ 1 │           │ 1 │
└───┘           └───┘           └───┘
┌───┐           ┌───┐           ┌───┐
│ 2 │           │ 4 │           │ 2 │
└───┘           └───┘           └───┘
┌───┐           ┌───┐           ┌───┐
│ 4 │           │ 5 │           │ 4 │
└───┘           └───┘           └───┘
                                ┌───┐
                union           │ 1 │ ← From RDD2
                                └───┘
                                ┌───┐
                                │ 4 │
                                └───┘
                                ┌───┐
                                │ 5 │
                                └───┘
```

**Code:**
```scala
rdd1.union(rdd2)
```

### Intersection Transformation

**Visual:**

```
RDD1:           RDD2:           Result:
┌───┐           ┌───┐           ┌───┐
│ 1 │ ─────┬───>│ 1 │ ────>     │ 1 │ ← Common
└───┘      │    └───┘           └───┘
┌───┐      │    ┌───┐           ┌───┐
│ 2 │ ─────X    │ 4 │ ────>     │ 4 │ ← Common
└───┘           └───┘           └───┘
┌───┐           ┌───┐
│ 4 │ ─────┬───>│ 5 │
└───┘      │    └───┘
           │
     intersection
```

**Code:**
```scala
rdd1.intersection(rdd2)
```

### Distinct Transformation

**Visual:**

```
Input:                      Output:
┌───┐                       ┌───┐
│ 1 │ ─┐                    │ 1 │
└───┘  │                    └───┘
┌───┐  │ merge duplicates   ┌───┐
│ 1 │ ─┘                    │ 2 │
└───┘                       └───┘
┌───┐                       ┌───┐
│ 2 │ ────────────>         │ 4 │
└───┘                       └───┘
┌───┐                       ┌───┐
│ 4 │ ─┐                    │ 5 │
└───┘  │                    └───┘
┌───┐  │
│ 4 │ ─┘
└───┘
┌───┐
│ 5 │ ────────────>
└───┘
```

**Code:**
```scala
val rdd = sc.parallelize(Array(1, 1, 2, 4, 5, 6, 8, 8, 9, 10, 11, 11))
rdd.distinct()
// Result: [1, 2, 4, 5, 6, 8, 9, 10, 11]
```

### Cartesian Product Transformation

**Visual:**

```
RDD1 (Strings):        RDD2 (Integers):       Result (Pairs):
┌───┐                  ┌───┐                  ┌─────────┐
│ A │ ────┬───────────>│ 1 │                  │ ("A",1) │
└───┘     │            └───┘                  └─────────┘
          │            ┌───┐                  ┌─────────┐
          ├───────────>│ 4 │                  │ ("A",4) │
          │            └───┘                  └─────────┘
          │            ┌───┐                  ┌─────────┐
          └───────────>│ 5 │                  │ ("A",5) │
┌───┐                  └───┘                  └─────────┘
│ B │ ────┬───────────> 1                     ┌─────────┐
└───┘     │                                   │ ("B",1) │
          ├───────────> 4                     └─────────┘
          │                                   ┌─────────┐
          └───────────> 5                     │ ("B",4) │
┌───┐                                         └─────────┘
│ C │ ────┬───────────> 1                     ┌─────────┐
└───┘     │                                   │ ("B",5) │
          ├───────────> 4                     └─────────┘
          │                                   ┌─────────┐
          └───────────> 5                     │ ("C",1) │
                                              └─────────┘
                                              ┌─────────┐
                                              │ ("C",4) │
                                              └─────────┘
                                              ┌─────────┐
                                              │ ("C",5) │
                                              └─────────┘
```

**Code:**
```scala
val rddStrings = sc.parallelize(List("A", "B", "C"))
val rddIntegers = sc.parallelize(List(1, 4, 5))
rddStrings.cartesian(rddIntegers)
```

### GroupByKey Transformation

**Visual:**

```
Input (Pair RDD):                Output (Grouped):

┌──────────────┐                 ┌──────────────────┐
│ ("even", 2)  │ ─┐              │ ("even", [2, 8]) │
└──────────────┘  │              └──────────────────┘
                  │  Group by
┌──────────────┐  │  same key    ┌──────────────────┐
│ ("odd", 5)   │ ─┼─────────>    │ ("odd", [5, 7])  │
└──────────────┘  │              └──────────────────┘
                  │
┌──────────────┐  │
│ ("odd", 7)   │ ─┘
└──────────────┘

┌──────────────┐
│ ("even", 8)  │ ─┘
└──────────────┘
```

**Code:**
```scala
pairRDD.groupByKey()
```

### ReduceByKey Transformation

**Visual:**

```
Input (Pair RDD):                Output (Reduced):

┌──────────────┐                 ┌──────────────┐
│ ("even", 2)  │ ─┐              │ ("even", 10) │ ← 2+8=10
└──────────────┘  │              └──────────────┘
                  │  Reduce
┌──────────────┐  │  by key      ┌──────────────┐
│ ("odd", 5)   │ ─┼─(sum)───>    │ ("odd", 12)  │ ← 5+7=12
└──────────────┘  │              └──────────────┘
                  │
┌──────────────┐  │
│ ("odd", 7)   │ ─┘
└──────────────┘

┌──────────────┐
│ ("even", 8)  │ ─┘
└──────────────┘
```

**Code:**
```scala
pairRDD.reduceByKey((v1, v2) => v1 + v2)
```

**Key Difference: groupByKey vs reduceByKey**

```
groupByKey():
─────────────
Node 1: ("A", 1), ("A", 2)  ──> Shuffle ──> ("A", [1,2,3,4])
Node 2: ("A", 3), ("A", 4)  ──> Shuffle ──┘
        ↑
    Shuffles ALL data!

reduceByKey(_ + _):
───────────────────
Node 1: ("A", 1), ("A", 2)  ──> Local reduce: ("A", 3)  ──> Shuffle ──> ("A", 10)
Node 2: ("A", 3), ("A", 4)  ──> Local reduce: ("A", 7)  ──> Shuffle ──┘
        ↑
    Reduces BEFORE shuffling! (More efficient)
```

### SortByKey Transformation

**Visual:**

```
Input (Unsorted):              Output (Sorted):

┌─────────┐                    ┌─────────┐
│ ("B",2) │                    │ ("A",8) │ ← A first
└─────────┘                    └─────────┘
┌─────────┐                    ┌─────────┐
│ ("C",5) │    sortByKey()     │ ("B",2) │ ← B second
└─────────┘  ────────────>     └─────────┘
┌─────────┐                    ┌─────────┐
│ ("D",7) │                    │ ("C",5) │ ← C third
└─────────┘                    └─────────┘
┌─────────┐                    ┌─────────┐
│ ("A",8) │                    │ ("D",7) │ ← D fourth
└─────────┘                    └─────────┘
```

**Code:**
```scala
unsortedPairRDD.sortByKey()
```

### Join Transformation

**Visual:**

```
RDD1 (Pair):         RDD2 (Pair):           Result (Joined):

┌─────────┐          ┌─────────┐            ┌─────────────┐
│ ("B","A")│ ───┬───>│ ("B",2) │ ────>      │ ("B",(A,2)) │
└─────────┘    │    └─────────┘            └─────────────┘
┌─────────┐    │    ┌─────────┐            ┌─────────────┐
│ ("C","D")│ ───┼───>│ ("C",5) │ ────>      │ ("C",(D,5)) │
└─────────┘    │    └─────────┘            └─────────────┘
┌─────────┐    │    ┌─────────┐            ┌─────────────┐
│ ("D","A")│ ───┼───>│ ("D",7) │ ────>      │ ("D",(A,7)) │
└─────────┘    │    └─────────┘            └─────────────┘
┌─────────┐    │    ┌─────────┐            ┌─────────────┐
│ ("A","B")│ ───┴───>│ ("A",8) │ ────>      │ ("A",(B,8)) │
└─────────┘         └─────────┘            └─────────────┘

                    Join matches on KEY
                    Combines VALUES into tuple
```

**Code:**
```scala
val joinedRDD = pairRDD1.join(pairRDD2)
```

---

## Action Examples with Code

### isEmpty() Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))

// Check if any element equals 5
val isRDDEmpty = rddIntegers
  .filter(a => a.equals(5))
  .isEmpty()

// Result: false (because filter found element 5)
```

### collect() Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val collectedList = rddIntegers.collect()

// Result: Array(1, 4, 5)
// Type: Array[Int]
```

**When to use collect():**
- ✅ Small result sets (< 1GB)
- ✅ Final results for display
- ❌ Large datasets (will crash driver!)

### count() Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val countVal = rddIntegers.count()

// Result: 3
// Type: Long
```

### first() Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val first = rddIntegers.first()

// Result: 1
// Type: Int
```

### take(n) Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))
val takeTwo = rddIntegers.take(2)

// Result: Array(1, 4)
// Type: Array[Int]
```

**take() vs collect():**
- `take(n)` - Only retrieves n elements (safer)
- `collect()` - Retrieves ALL elements (can be dangerous)

### foreach() Example

```scala
val rddIntegers = sc.parallelize(List(1, 4, 5))

rddIntegers.collect().foreach(x =>
  println("The element values of the RDD are ::" + x)
)

// Output:
// The element values of the RDD are ::1
// The element values of the RDD are ::4
// The element values of the RDD are ::5
```

**Important:** `foreach()` executes on executors, not driver!

```
// This runs on EXECUTORS (output goes to executor logs):
rdd.foreach(x => println(x))

// This runs on DRIVER (output goes to driver console):
rdd.collect().foreach(x => println(x))
```

---

## RDD vs DataFrame Comparison

Both RDDs and DataFrames are distributed collections, but they have different characteristics.

### Key Differences

| Aspect | RDD | DataFrame |
|--------|-----|-----------|
| **Type Safety** | ✅ Compile-time type checking | ⚠️ Runtime type errors possible |
| **Optimization** | ❌ No automatic optimization | ✅ Catalyst optimizer |
| **Performance** | ⚠️ Manual optimization needed | ✅ Automatically optimized |
| **API** | Functional (map, filter, reduce) | SQL-like (select, where, groupBy) |
| **Schema** | ❌ No schema | ✅ Has schema |
| **Ease of Use** | Requires programming skills | Easier for SQL users |
| **When to Use** | Complex custom logic | Standard analytics |

### Performance Comparison

```
Same Query:
───────────

RDD Approach:
─────────────
rdd.filter(x => x._2 > 100)
   .map(x => (x._1, x._2 * 2))
   .groupByKey()
   .mapValues(_.sum)

Execution: No optimization, exactly as written


DataFrame Approach:
───────────────────
df.filter($"value" > 100)
  .select($"key", $"value" * 2)
  .groupBy($"key")
  .sum()

Execution: Catalyst optimizer reorganizes for efficiency!
            (May push filters, combine operations, etc.)
```

### When to Use RDDs

✅ **Use RDDs when:**
- You need low-level control over data transformations
- Working with unstructured data (text files, custom binary formats)
- Implementing custom partitioning logic
- Your logic doesn't fit SQL/DataFrame paradigm
- Migrating legacy Spark 1.x code

❌ **Avoid RDDs when:**
- Standard analytics (grouping, aggregating, filtering)
- SQL-like operations
- Working with structured data (CSV, JSON, Parquet)
- You want automatic optimization

### Migration: RDD ↔ DataFrame

**RDD to DataFrame:**
```scala
// From RDD of tuples
val rdd = sc.parallelize(List(("Alice", 25), ("Bob", 30)))
val df = rdd.toDF("name", "age")

// From RDD with case class
case class Person(name: String, age: Int)
val rdd = sc.parallelize(List(Person("Alice", 25), Person("Bob", 30)))
val df = rdd.toDF()
```

**DataFrame to RDD:**
```scala
val df = spark.read.csv("people.csv")
val rdd = df.rdd  // Returns RDD[Row]

// Access columns:
rdd.map(row => row.getString(0))  // First column as String
rdd.map(row => row.getInt(1))     // Second column as Int
```

---

## Best Practices (RDD)

### 1. Prefer reduceByKey() over groupByKey()

**❌ Bad (Inefficient):**
```scala
pairs.groupByKey()
     .mapValues(_.sum)
```

**✅ Good (Efficient):**
```scala
pairs.reduceByKey(_ + _)
```

**Why?**
- `reduceByKey()` does local aggregation before shuffling
- `groupByKey()` shuffles ALL data before aggregating
- `reduceByKey()` can be 10-100x faster!

### 2. Avoid collect() on Large Datasets

**❌ Bad:**
```scala
val allData = hugeRDD.collect()  // Crashes driver!
```

**✅ Good:**
```scala
// Option 1: Use take() for sampling
val sample = hugeRDD.take(100)

// Option 2: Save to file
hugeRDD.saveAsTextFile("hdfs://output/path")

// Option 3: Use actions that don't return all data
val count = hugeRDD.count()
val firstTen = hugeRDD.take(10)
```

### 3. Use Transformations for Lazy Evaluation

**Build your computation graph first, trigger execution once:**

```scala
// ✅ Good: Build pipeline, execute once
val result = rdd
  .filter(...)      // Lazy
  .map(...)         // Lazy
  .reduceByKey(...) // Lazy
  .count()          // ACTION - executes entire pipeline once!

// ❌ Bad: Multiple actions trigger multiple executions
rdd.filter(...).count()  // Executes everything
rdd.filter(...).first()  // Executes everything AGAIN!
```

### 4. Persist RDDs That Are Reused

**❌ Bad:**
```scala
val filtered = rdd.filter(...)
filtered.count()  // Computes filter
filtered.take(10) // Computes filter AGAIN!
```

**✅ Good:**
```scala
val filtered = rdd.filter(...).persist()
filtered.count()  // Computes filter, stores in memory
filtered.take(10) // Uses cached data!
```

**Persistence levels:**
```scala
import org.apache.spark.storage.StorageLevel

rdd.persist(StorageLevel.MEMORY_ONLY)     // Default
rdd.persist(StorageLevel.MEMORY_AND_DISK) // Spill to disk if needed
rdd.persist(StorageLevel.DISK_ONLY)       // Store on disk only
```

### 5. Choose Appropriate Partitioning

**Control parallelism:**

```scala
// Too few partitions: Underutilizes cluster
val rdd = sc.textFile("file.txt", 2)  // Only 2 tasks!

// Good: Match cluster size
val rdd = sc.textFile("file.txt", 100)  // 100 parallel tasks

// Repartition if needed
rdd.repartition(200)  // Increase partitions
rdd.coalesce(10)      // Decrease partitions (no shuffle)
```

**Rule of thumb:**
- 2-4 partitions per CPU core in cluster
- For 50-core cluster: 100-200 partitions

### 6. Minimize Shuffles

**Shuffles are expensive!** Operations that cause shuffles:
- `groupByKey()`, `reduceByKey()`, `join()`, `sortByKey()`
- `repartition()`, `coalesce(withShuffle=true)`

**Strategies:**
```scala
// ❌ Bad: Multiple shuffles
rdd.groupByKey()           // Shuffle 1
   .mapValues(_.sum)
   .join(other)            // Shuffle 2
   .sortByKey()            // Shuffle 3

// ✅ Better: Combine operations
rdd.reduceByKey(_ + _)     // Shuffle 1 (more efficient than groupByKey)
   .join(other)            // Shuffle 2
   .sortByKey()            // Shuffle 3

// ✅ Best: Co-partition RDDs
val rdd1 = data1.partitionBy(new HashPartitioner(100))
val rdd2 = data2.partitionBy(new HashPartitioner(100))
// Now join() won't shuffle if both have same partitioning!
```

### 7. Use Appropriate Data Types

**Prefer primitive types over objects:**

```scala
// ❌ Bad: Boxing overhead
val rdd = sc.parallelize(List(
  new java.lang.Integer(1),
  new java.lang.Integer(2)
))

// ✅ Good: Primitive types
val rdd = sc.parallelize(List(1, 2, 3, 4))
```

### 8. Avoid Cartesian Products on Large RDDs

```scala
// ❌ Dangerous: Creates M×N elements!
val huge1 = sc.parallelize(1 to 1000000)
val huge2 = sc.parallelize(1 to 1000000)
val result = huge1.cartesian(huge2)  // 1 trillion elements!
```

**Use cartesian() only on small RDDs or with filters:**

```scala
// ✅ OK: Small RDDs
val small1 = sc.parallelize(List("A", "B", "C"))
val small2 = sc.parallelize(List(1, 2, 3))
val result = small1.cartesian(small2)  // Only 9 elements
```

---

## Review Checklist (RDD)

### Core Concepts

- [ ] Understand what RDD stands for: **R**esilient **D**istributed **D**ataset
- [ ] Know the three key characteristics:
  - [ ] **Resilient:** Fault-tolerant via lineage
  - [ ] **Distributed:** Partitioned across cluster
  - [ ] **Dataset:** Collection of elements
- [ ] Can explain how fault tolerance works (lineage graph)
- [ ] Understand that tuples/elements are independent

### Data Sources

- [ ] Know how to create RDD from HDFS file
- [ ] Know how to create RDD from local file
- [ ] Know how to create RDD from in-memory collection
- [ ] Familiar with other sources (S3, NoSQL)

### Operations

- [ ] Understand difference between **Transformations** and **Actions**
- [ ] Know that transformations are **lazy** (not executed immediately)
- [ ] Know that actions are **eager** (trigger execution)
- [ ] Can list common transformations:
  - [ ] `map()`, `filter()`, `flatMap()`
  - [ ] `groupByKey()`, `reduceByKey()`
  - [ ] `join()`, `union()`, `intersection()`
  - [ ] `sortByKey()`, `distinct()`, `cartesian()`
- [ ] Can list common actions:
  - [ ] `count()`, `collect()`, `first()`, `take()`
  - [ ] `foreach()`, `saveAsTextFile()`, `isEmpty()`

### Transformations Details

- [ ] Can explain what `map()` does (one-to-one)
- [ ] Can explain what `filter()` does (selective keep)
- [ ] Know the difference between `groupByKey()` and `reduceByKey()`
- [ ] Understand why `reduceByKey()` is more efficient
- [ ] Can explain how `join()` works (inner join by key)
- [ ] Can explain `sortByKey()` behavior

### Actions Details

- [ ] Know when to use `collect()` (small datasets only!)
- [ ] Understand `collect()` danger (OOM on large data)
- [ ] Can use `take(n)` for sampling
- [ ] Know difference between `foreach()` on RDD vs on collected array

### Complete Example

- [ ] Can walk through the flight data example step by step
- [ ] Understand each RDD type in the pipeline:
  - [ ] `RDD[String]` → `RDD[Array[String]]` → `RDD[(String, Int)]`
- [ ] Can explain when execution actually happens (at `collect()`)

### Best Practices

- [ ] Prefer `reduceByKey()` over `groupByKey()`
- [ ] Avoid `collect()` on large datasets
- [ ] Use `persist()` for RDDs accessed multiple times
- [ ] Choose appropriate number of partitions
- [ ] Minimize shuffles
- [ ] Understand when to use RDD vs DataFrame

### Performance

- [ ] Know which operations cause shuffles
- [ ] Understand local reduction before shuffling
- [ ] Can explain partitioning impact on performance
- [ ] Know how to monitor RDD lineage in Spark UI

---

## Advanced Topics

### RDD Lineage and DAG

Every RDD remembers how it was created:

```scala
val rdd1 = sc.textFile("file.txt")
val rdd2 = rdd1.map(_.split(","))
val rdd3 = rdd2.filter(_(0) == "USA")
val count = rdd3.count()

// Lineage:
// rdd3 -> filter -> rdd2 -> map -> rdd1 -> textFile -> file.txt
```

**View lineage:**
```scala
println(rdd3.toDebugString)
```

**Output:**
```
(100) MapPartitionsRDD[2] at filter at <console>:25 []
 |  MapPartitionsRDD[1] at map at <console>:23 []
 |  file.txt MapPartitionsRDD[0] at textFile at <console>:21 []
 |  file.txt HadoopRDD[0] at textFile at <console>:21 []
```

### Partitioning

**Check number of partitions:**
```scala
println(rdd.getNumPartitions)
```

**Repartition:**
```scala
// Increase partitions (causes shuffle)
val rdd2 = rdd.repartition(200)

// Decrease partitions (no shuffle if reducing)
val rdd3 = rdd.coalesce(10)
```

**Custom partitioning:**
```scala
import org.apache.spark.HashPartitioner

val partitioned = rdd.partitionBy(new HashPartitioner(100))
```

### Narrow vs Wide Transformations

**Narrow Transformations** (no shuffle needed):
- `map()`, `filter()`, `mapPartitions()`, `union()`
- Each input partition contributes to only one output partition

```
Input Partitions:    Output Partitions:
┌────────┐          ┌────────┐
│ Part 1 │ ──map──> │ Part 1 │
└────────┘          └────────┘
┌────────┐          ┌────────┐
│ Part 2 │ ──map──> │ Part 2 │
└────────┘          └────────┘
```

**Wide Transformations** (shuffle required):
- `groupByKey()`, `reduceByKey()`, `join()`, `sortByKey()`
- Each input partition contributes to multiple output partitions

```
Input Partitions:    Output Partitions:
┌────────┐     ┌──> ┌────────┐
│ Part 1 │ ────┤    │ Part 1 │
└────────┘     └──> └────────┘
┌────────┐     ┌──> ┌────────┐
│ Part 2 │ ────┤    │ Part 2 │
└────────┘     └──> └────────┘
     ↑
  SHUFFLE!
```

---

## Common Pitfalls and Solutions

### Pitfall 1: Using collect() on Large RDD

**Problem:**
```scala
val bigData = sc.textFile("100GB-file.txt")
val result = bigData.collect()  // CRASH! Out of memory
```

**Solution:**
```scala
// Use take() for sampling
val sample = bigData.take(100)

// Or save to distributed storage
bigData.saveAsTextFile("hdfs://output")

// Or use aggregations
val count = bigData.count()
val stats = bigData.map(...).reduce(...)
```

### Pitfall 2: Creating RDD Inside RDD

**Problem:**
```scala
rdd.map(x => {
  val innerRDD = sc.parallelize(List(1,2,3))  // ERROR!
  innerRDD.sum()
})
```

**Solution:**
```scala
// Use broadcast variables or joins instead
val lookupData = sc.broadcast(Map("key" -> "value"))
rdd.map(x => lookupData.value.get(x))
```

### Pitfall 3: Not Persisting Reused RDDs

**Problem:**
```scala
val filtered = rdd.filter(...)
filtered.count()     // Computes filter
filtered.take(10)    // Computes filter AGAIN
filtered.saveAsTextFile(...)  // Computes filter AGAIN
```

**Solution:**
```scala
val filtered = rdd.filter(...).persist()
filtered.count()     // Computes filter, caches result
filtered.take(10)    // Uses cached data
filtered.saveAsTextFile(...)  // Uses cached data

// Don't forget to unpersist when done!
filtered.unpersist()
```

### Pitfall 4: Using groupByKey() Instead of reduceByKey()

**Problem:**
```scala
// Shuffles ALL values, then sums
pairRDD.groupByKey().mapValues(_.sum)
```

**Solution:**
```scala
// Sums locally first, then shuffles aggregated values
pairRDD.reduceByKey(_ + _)
```

**Performance difference:**
```
Dataset: 1 billion records, 1 million unique keys

groupByKey().mapValues(_.sum):
- Shuffles 1 billion records
- Time: 45 minutes

reduceByKey(_ + _):
- Local aggregation to 1 million records
- Shuffles 1 million records
- Time: 3 minutes

15x faster!
```

---

## Summary: RDD Key Concepts

### What You Should Remember

1. **RDD = Fundamental abstraction** in Spark
   - Resilient (fault-tolerant)
   - Distributed (partitioned across cluster)
   - Dataset (collection of elements)

2. **Two types of operations:**
   - **Transformations (lazy):** Build computation DAG
   - **Actions (eager):** Trigger execution

3. **Prefer high-level APIs when possible:**
   - Use DataFrames for structured data
   - Use RDDs when you need low-level control

4. **Performance matters:**
   - `reduceByKey()` > `groupByKey()`
   - Minimize shuffles
   - Partition appropriately
   - Persist reused RDDs

5. **Safety first:**
   - Never `collect()` large datasets
   - Be careful with cartesian products
   - Monitor memory usage

---

## RDD Command Reference

### Creating RDDs

```scala
// From file
sc.textFile("path/to/file")
sc.textFile("hdfs://path", numPartitions)

// From collection
sc.parallelize(List(1, 2, 3))
sc.parallelize(Array("a", "b"), numPartitions)
```

### Transformations

```scala
// Basic
rdd.map(f)
rdd.filter(f)
rdd.flatMap(f)

// Pair RDD operations
rdd.groupByKey()
rdd.reduceByKey(f)
rdd.mapValues(f)
rdd.sortByKey()
rdd.keys
rdd.values

// Multi-RDD
rdd1.union(rdd2)
rdd1.intersection(rdd2)
rdd1.join(rdd2)
rdd1.cartesian(rdd2)

// Other
rdd.distinct()
rdd.repartition(n)
rdd.coalesce(n)
```

### Actions

```scala
// Retrieve data
rdd.collect()
rdd.take(n)
rdd.first()
rdd.top(n)

// Aggregate
rdd.count()
rdd.reduce(f)
rdd.fold(zeroValue)(f)

// Side effects
rdd.foreach(f)
rdd.foreachPartition(f)

// Save
rdd.saveAsTextFile(path)
rdd.saveAsObjectFile(path)

// Info
rdd.isEmpty()
rdd.getNumPartitions
```

### Persistence

```scala
rdd.persist()
rdd.cache()  // Same as persist(MEMORY_ONLY)
rdd.unpersist()

// With storage level
import org.apache.spark.storage.StorageLevel
rdd.persist(StorageLevel.MEMORY_AND_DISK)
```

---

## Questions to Explore

1. **Why does Spark use lineage instead of data replication for fault tolerance?**
2. **What happens to RDD operations when a partition fails?**
3. **When would you choose RDD over DataFrame?**
4. **How does partitioning affect shuffle performance?**
5. **What is the difference between `repartition()` and `coalesce()`?**
6. **Why is `reduceByKey()` faster than `groupByKey()`?**
7. **What happens if you call `collect()` on a 100GB RDD?**
8. **How does `persist()` work with different storage levels?**
9. **What are narrow vs wide transformations?**
10. **How can you view the RDD lineage graph?**

---

**Course Material 4 Complete:** Spark RDDs
**Next Topics:** Spark Streaming, Spark MLlib, Performance Tuning, Advanced Optimizations
