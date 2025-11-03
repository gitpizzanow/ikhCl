<div align="center">

# Big Data Exam Revision Guide

![Big Data](sample.webp)

> *"Someone once told me: We'll never know if this is a feature or a bug"*

### Quick Reference for Exam Success

*Essential concepts • Commands • Code patterns*

---

</div>

## Table of Contents

| Section | Topics |
|---------|--------|
| **[HDFS Essentials](#hdfs-essentials)** | Architecture • Commands • Key Properties |
| **[Hive Essentials](#hive-essentials)** | Tables • HiveQL • Loading Data |
| **[Spark Essentials](#spark-essentials)** | DataFrames • Operations • SQL |
| **[RDD Essentials](#rdd-essentials)** | Transformations • Actions • Patterns |
| **[Quick Command Reference](#quick-command-reference)** | HDFS • Hive • Spark • RDD |
| **[Code Patterns](#code-patterns-you-must-know)** | Must-know workflows |
| **[Common Exam Questions](#common-exam-questions)** | Typical questions & answers |
| **[Comparison Tables](#comparison-tables)** | Side-by-side comparisons |
| **[Common Pitfalls](#common-pitfalls-avoid-these)** | Mistakes to avoid |

---

# HDFS ESSENTIALS

## Core Concept

> **HDFS = Hadoop Distributed File System**
> *Think of it as: Google Drive, but for huge files spread across many computers*

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #2196F3;">

**Key Features:**

**Distributed** = Your file is stored on multiple computers (not just one)
- Example: You have a 1GB video. HDFS splits it across 5 different machines.

**Blocks** = Your file is cut into pieces (default: 128 MB each piece)
- Example: Your 1GB video → split into 8 blocks of 128MB each

**Replicated** = Each piece is copied 3 times (stored on 3 different machines)
- Why? If one computer crashes, you still have 2 other copies! (**Fault tolerance** = system keeps working even when things break)

</div>

## Architecture (MUST KNOW!)

**Cluster** = A group of computers working together like a team

```
┌─────────────────────────────────┐
│      NameNode (Master)          │  ← THE BOSS (only 1)
│                                 │
│  Keeps a list of:               │     Think of it like:
│  • Where is file1.txt?          │     - A library catalog
│  • Where is video.mp4?          │     - Knows which shelf has which book
│  • Which files exist?           │     - Doesn't store books, just tracks them
└─────────────────────────────────┘
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌────────┐ ┌────────┐ ┌────────┐
│DataNode│ │DataNode│ │DataNode│  ← THE WORKERS (many of them)
│Computer│ │Computer│ │Computer│
│   1    │ │   2    │ │   3    │     Think of it like:
│        │ │        │ │        │     - The bookshelves
│ Blocks │ │ Blocks │ │ Blocks │     - Actually store the data
└────────┘ └────────┘ └────────┘
```

<div style="background: #fff3cd; padding: 10px; border-left: 4px solid #ffc107;">

**CRITICAL:** If NameNode fails → entire cluster fails!

**Why?** Without the catalog, you don't know where anything is!
- Like a library with no catalog system → books exist but you can't find them

**1 per cluster** = You only have ONE NameNode managing everything
- That's the weak point! (Single Point of Failure)

</div>

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Metadata** = Information ABOUT the data (not the data itself)

Examples of metadata:
- Filename: `report.pdf`
- Size: `2.5 GB`
- Location: `DataNode 1, DataNode 3, DataNode 7`
- Owner: `username123`
- Created: `2024-01-15`

**Metadata Manager** = The NameNode's job is to remember all this info

</div>

## Essential HDFS Commands

### Real-World Example Scenario

Let's say you're working on a university project about students:
- Your username: `tarik`
- Your project folder on HDFS: `/user/tarik/student_project/`
- Your local computer file: `students_data.csv`

### File Operations

```bash
# ============================================
# LIST FILES (see what's in a directory)
# ============================================

# List files in your home directory
hdfs dfs -ls /user/tarik/

# Example output:
# drwxr-xr-x   - tarik supergroup  0 2024-01-15 10:30 /user/tarik/student_project
# -rw-r--r--   3 tarik supergroup  1.2G 2024-01-14 09:15 /user/tarik/big_dataset.csv

# List ALL files recursively (shows everything inside folders too)
hdfs dfs -ls -R /user/tarik/student_project/

# ============================================
# CREATE DIRECTORY
# ============================================

# Create a simple directory
hdfs dfs -mkdir /user/tarik/student_project

# Create nested directories (like "mkdir -p" in Linux)
# This creates all parent directories automatically
hdfs dfs -mkdir -p /user/tarik/student_project/data/2024/january

# ============================================
# UPLOAD FILE TO HDFS
# ============================================

# Upload from your local computer to HDFS
hdfs dfs -put students_data.csv /user/tarik/student_project/

# Now students_data.csv is in HDFS!
# Local: C:\Users\Tarik\students_data.csv (still here)
# HDFS:  /user/tarik/student_project/students_data.csv (new copy here)

# ============================================
# DOWNLOAD FILE FROM HDFS
# ============================================

# Download from HDFS to your local computer
hdfs dfs -get /user/tarik/student_project/students_data.csv downloaded_students.csv

# Now you have: downloaded_students.csv on your local computer

# ============================================
# COPY WITHIN HDFS
# ============================================

# Make a copy inside HDFS (both files stay in HDFS)
hdfs dfs -cp /user/tarik/student_project/students_data.csv /user/tarik/backup/students_backup.csv

# ============================================
# MOVE/RENAME
# ============================================

# Rename a file
hdfs dfs -mv /user/tarik/student_project/students_data.csv /user/tarik/student_project/students_2024.csv

# Move to different directory
hdfs dfs -mv /user/tarik/student_project/students_2024.csv /user/tarik/archive/

# ============================================
# DELETE FILE
# ============================================

# Delete a single file
hdfs dfs -rm /user/tarik/student_project/temp_file.txt

# Delete directory and EVERYTHING inside it (-r = recursive = everything)
hdfs dfs -rm -r /user/tarik/student_project/old_data/

# ============================================
# VIEW FILE CONTENT
# ============================================

# View entire file (like 'cat' in Linux)
hdfs dfs -cat /user/tarik/student_project/students_2024.csv

# Example output:
# id,name,age,major
# 1,Alice,20,Computer Science
# 2,Bob,22,Mathematics
# ...

# View last 1KB of file (good for log files)
hdfs dfs -tail /user/tarik/logs/application.log
```

## Key Properties Explained

<table>
<thead>
<tr>
<th>Property</th>
<th>Value</th>
<th>What This Means</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Block size</strong></td>
<td><code>128 MB</code></td>
<td>Your file is cut into 128MB pieces<br><br><strong>Example:</strong> 500MB file → 4 blocks (128+128+128+116)</td>
</tr>
<tr>
<td><strong>Replication</strong></td>
<td><code>3 copies</code></td>
<td>Each block is stored on 3 different computers<br><br><strong>Replication factor = 3</strong> means 3 copies of everything<br><br><strong>Why?</strong> If Computer 1 crashes, Computers 2 & 3 still have the data!</td>
</tr>
<tr>
<td><strong>Write-once</strong></td>
<td>Cannot modify</td>
<td>Once you upload a file, you CAN'T edit it in place<br><br><strong>You can:</strong> Delete it, Append to it, Replace it<br><strong>You can't:</strong> Change line 5 of the file</td>
</tr>
<tr>
<td><strong>NameNode</strong></td>
<td>1 per cluster</td>
<td>Only ONE boss managing the whole cluster<br><br><strong>If it fails:</strong> Entire system stops working (nobody knows where files are!)</td>
</tr>
</tbody>
</table>

### Exam Tip

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Q:** "What happens if a DataNode (worker computer) fails?"

**A:** HDFS automatically **replicates** (copies) the blocks from the 2 other copies to **maintain** (keep) the replication factor of 3.

**Example:**
- Block 1 is stored on: Computer A, Computer B, Computer C
- Computer B crashes!
- HDFS copies Block 1 from Computer A → Computer D
- Now Block 1 is on: Computer A, Computer C, Computer D ✓ (still 3 copies!)

**Maintain** = Keep something at the same level
**Replication factor** = How many copies of each piece

</div>

---

# HIVE ESSENTIALS

## Core Concept

> **Hive = SQL interface for Hadoop**
> *Think of it as: Microsoft Excel + SQL, but for HUGE datasets*

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #ff9800;">

**Key Features:**

**Translates SQL → MapReduce** = You write normal SQL, Hive converts it to Hadoop jobs
- You write: `SELECT * FROM students WHERE age > 20`
- Hive translates this into complex Hadoop code
- You don't need to know Hadoop programming!

**Schema on Read** = Structure is applied when you READ the data (not when you save it)
- Normal database: You must define structure BEFORE inserting data
- Hive: Save data first, define structure later when you query it

**HiveQL** = Hive Query Language (basically SQL with some differences)

</div>

## Table Types (CRITICAL!)

### 1. Managed Tables

**Managed** = Hive controls EVERYTHING (data + information about data)

```sql
-- Real example: Creating a table for student information
CREATE TABLE students (
    id INT,
    name STRING,
    age INT
)
ROW FORMAT DELIMITED           -- Tell Hive how data is formatted
FIELDS TERMINATED BY '\t';     -- Columns separated by TAB character
```

<div style="background: #ffebee; padding: 10px; border-left: 4px solid #f44336;">

**Managed Tables:**

**Metadata** = Information ABOUT the table (column names, data types, where it's stored)
**Data** = The actual student records

When you `DROP TABLE students;`:
- Metadata deleted → Hive forgets the table exists
- Data deleted → The actual CSV file is DELETED from HDFS
- **You lose everything!**

**Where it's stored:**
`/user/hive/warehouse/students/`

</div>

### 2. External Tables

**External** = Hive only remembers the structure, data stays safe elsewhere

```sql
-- Real example: Creating external table pointing to existing data
CREATE EXTERNAL TABLE students_ext (
    id INT,
    name STRING,
    age INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LOCATION '/user/tarik/my_data/students/';  -- YOU control where data lives
```

<div style="background: #e3f2fd; padding: 10px; border-left: 4px solid #2196f3;">

**External Tables:**

When you `DROP TABLE students_ext;`:
- Metadata deleted → Hive forgets the table structure
- Data preserved → Your CSV files in `/user/tarik/my_data/students/` are SAFE!
- **You can recreate the table later or use data with other tools**

**Think of it like:**
- Managed = Hive owns the data (deleting table deletes data)
- External = Hive just borrows the data (deleting table doesn't touch data)

</div>

### When to Use Which?

| Scenario | Use This | Why |
|----------|----------|-----|
| Playing with test data | Managed Table | Easy to clean up - delete table, everything goes |
| Temporary calculations | Managed Table | Hive handles cleanup automatically |
| Production data shared with other teams | External Table | Other teams can still access data if you drop table |
| Data you might need later | External Table | Safe! Data survives even if table is dropped |

## Loading Data

```sql
-- ============================================
-- FROM HDFS TO HIVE TABLE
-- ============================================

-- This MOVES the file (file disappears from original location!)
LOAD DATA INPATH '/user/tarik/data/students.txt'
INTO TABLE students;

-- Before: /user/tarik/data/students.txt (file is here)
-- After:  /user/hive/warehouse/students/students.txt (file moved here)
--         /user/tarik/data/students.txt (GONE!)

-- ============================================
-- FROM LOCAL COMPUTER TO HIVE TABLE
-- ============================================

-- This COPIES the file (original file stays on your computer)
LOAD DATA LOCAL INPATH '/home/tarik/students.txt'
INTO TABLE students;

-- LOCAL keyword = from your computer (not HDFS)
```

<div style="background: #fff3cd; padding: 10px; border-left: 4px solid #ffc107;">

**IMPORTANT:**

`LOAD DATA INPATH` (without LOCAL) → **MOVES** the file (original disappears!)
`LOAD DATA LOCAL INPATH` (with LOCAL) → **COPIES** the file (original stays)

**Remember:** INPATH = move, LOCAL INPATH = copy

</div>

## Essential HiveQL Queries

```sql
-- ============================================
-- BASIC SELECT (get data from table)
-- ============================================

-- Get everything
SELECT * FROM students;

-- Get specific columns where condition is true
SELECT name, age
FROM students
WHERE age > 20;

-- ============================================
-- AGGREGATIONS (calculate summary numbers)
-- ============================================

-- Count how many students
SELECT COUNT(*) FROM students;
-- Result: 150 (there are 150 students)

-- Average age of all students
SELECT AVG(age) FROM students;
-- Result: 21.5 (average age is 21.5 years)

-- Oldest and youngest student
SELECT MAX(age), MIN(age) FROM students;
-- Result: 28, 18 (oldest is 28, youngest is 18)

-- ============================================
-- GROUP BY (group similar items together)
-- ============================================

-- Count students per department
SELECT department, COUNT(*)
FROM students
GROUP BY department;

-- Result:
-- Computer Science   45
-- Mathematics       30
-- Physics           25
-- ...

-- ============================================
-- JOIN (combine data from two tables)
-- ============================================

-- Real example: Get student names and their enrolled courses
SELECT s.name, e.course
FROM students s
JOIN enrollments e ON s.id = e.student_id;

-- Result:
-- Alice    Database Systems
-- Alice    Algorithms
-- Bob      Calculus
-- ...

-- ============================================
-- HAVING (filter AFTER grouping)
-- ============================================

-- Get departments with MORE THAN 10 students
SELECT department, COUNT(*) as student_count
FROM students
GROUP BY department
HAVING student_count > 10;

-- WHERE  = filter individual rows BEFORE grouping
-- HAVING = filter groups AFTER grouping

-- ============================================
-- ORDER BY vs SORT BY
-- ============================================

-- ORDER BY = Sort ALL data globally (SLOW! Uses only 1 computer)
SELECT * FROM students ORDER BY age DESC;

-- SORT BY = Sort within each partition (FASTER! Uses multiple computers)
SELECT * FROM students SORT BY age DESC;

-- Use SORT BY for big tables!
```

## Key Differences from Regular SQL

<table>
<thead>
<tr>
<th>SQL Feature</th>
<th>Regular SQL (MySQL, PostgreSQL)</th>
<th>Hive</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>UPDATE/DELETE</strong></td>
<td>✅ Can update/delete rows</td>
<td>❌ Cannot update/delete rows<br><small>Must delete whole table and reload</small></td>
</tr>
<tr>
<td><strong>Transactions</strong></td>
<td>✅ ACID compliant<br><small>(Atomic, Consistent, Isolated, Durable)</small></td>
<td>❌ Not ACID<br><small>No rollback, no commit</small></td>
</tr>
<tr>
<td><strong>Indexes</strong></td>
<td>✅ Full index support</td>
<td>⚠️ Limited index support</td>
</tr>
<tr>
<td><strong>Speed</strong></td>
<td>⚡ Fast (milliseconds)</td>
<td>🐢 Slower (seconds to minutes)<br><small>Because it's batch processing</small></td>
</tr>
<tr>
<td><strong>Data Scale</strong></td>
<td>Up to terabytes</td>
<td>✅ Petabytes!<br><small>1 PB = 1000 TB = 1,000,000 GB</small></td>
</tr>
</tbody>
</table>

---

# SPARK ESSENTIALS

## Core Concept

> **Spark = Fast in-memory processing framework**
> *Think of it as: Working on RAM instead of writing to hard disk every step*

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #ff5722;">

**Key Features:**

**10-100x faster than MapReduce** = If MapReduce takes 1 hour, Spark takes 1-6 minutes!

**In-memory computation** = Keeps data in RAM (fast) instead of writing to disk (slow) between steps
- MapReduce: Read from disk → Process → Write to disk → Read from disk → Process → Write to disk (SLOW!)
- Spark: Read from disk → Process in RAM → Process in RAM → Process in RAM → Write result (FAST!)

**Supports multiple things:**
- Batch processing (process lots of data at once)
- Streaming (process data as it arrives)
- SQL (query data)
- ML (machine learning)
- Graph processing (social network analysis)

</div>

## Architecture (MUST MEMORIZE!)

**Driver** = The boss giving orders
**Executor** = Workers doing the actual work
**Tasks** = Individual pieces of work

```
┌──────────────────────────┐
│    Driver Program        │  ← YOU run your code here
│                          │
│  Your Spark code:        │    Like a project manager:
│  val df = spark.read...  │    - Reads your code
│                          │    - Makes a plan
│  • Creates tasks         │    - Tells workers what to do
│  • Sends to executors    │    - Combines results
└──────────────────────────┘
            │
    ┌───────┼───────┐
    ▼       ▼       ▼
┌─────────┐ ┌─────────┐ ┌─────────┐
│Executor │ │Executor │ │Executor │  ← Workers (running on different computers)
│         │ │         │ │         │
│ Tasks   │ │ Tasks   │ │ Tasks   │    Like construction workers:
│ Cache   │ │ Cache   │ │ Cache   │    - Do the actual work
└─────────┘ └─────────┘ └─────────┘    - Store results in memory (cache)
```

**Cache** = Temporary storage in RAM (remember results for reuse)

## DataFrames - Essential Operations

**DataFrame** = Like Excel spreadsheet, but distributed across computers

### Creating DataFrames

```scala
// ============================================
// FROM CSV FILE
// ============================================

// Read student data from CSV
val students_df = spark.read
  .option("header", "true")        // First row has column names
  .option("inferSchema", "true")   // Auto-detect column types (INT, STRING, etc.)
  .csv("/user/tarik/data/students.csv")

// ============================================
// FROM JSON FILE
// ============================================

val users_df = spark.read.json("/user/tarik/data/users.json")

// ============================================
// FROM PARQUET FILE (compressed format, very efficient)
// ============================================

val sales_df = spark.read.parquet("/user/tarik/data/sales.parquet")
```

### Basic Operations

```scala
// ============================================
// SHOW DATA (see what's inside)
// ============================================

students_df.show()          // Show first 20 rows
students_df.show(10)        // Show first 10 rows

// Output example:
// +---+-------+---+------------------+
// | id|   name|age|        department|
// +---+-------+---+------------------+
// |  1|  Alice| 20|Computer Science  |
// |  2|    Bob| 22|   Mathematics    |
// +---+-------+---+------------------+

// ============================================
// SCHEMA (see column structure)
// ============================================

students_df.printSchema()

// Output:
// root
//  |-- id: integer (nullable = true)
//  |-- name: string (nullable = true)
//  |-- age: integer (nullable = true)
//  |-- department: string (nullable = true)

// ============================================
// SELECT COLUMNS (pick specific columns)
// ============================================

// Select just name and age
students_df.select("name", "age")

// Select with calculation
students_df.select($"name", $"age" + 1)  // Everyone's age plus 1

// ============================================
// FILTER (keep rows matching condition)
// ============================================

// Students older than 21
students_df.filter($"age" > 21)

// Same thing (where = filter)
students_df.where($"age" > 21)

// ============================================
// GROUP BY (group similar items)
// ============================================

// Count students per department
students_df.groupBy("department").count()

// Average age per department
students_df.groupBy("department").agg(avg("salary"))

// ============================================
// SORT (arrange in order)
// ============================================

// Sort by age (oldest first)
students_df.sort($"age".desc)

// Same thing
students_df.orderBy($"age".desc)

// ============================================
// ADD COLUMN
// ============================================

// Add new column: age in months
students_df.withColumn("age_months", $"age" * 12)

// ============================================
// RENAME COLUMN
// ============================================

students_df.withColumnRenamed("name", "student_name")

// ============================================
// DROP COLUMN (remove it)
// ============================================

students_df.drop("department")
```

## Transformations vs Actions (CRITICAL!)

**This is SUPER important for exams!**

<table>
<tr>
<td width="50%" style="background: #e3f2fd; padding: 15px;">

### Transformations (Lazy)

**Lazy** = Doesn't run immediately, just builds a plan

```scala
select()      // Pick columns
filter()      // Keep rows matching condition
groupBy()     // Group similar rows
join()        // Combine tables
sort()        // Arrange in order
distinct()    // Remove duplicates
withColumn()  // Add new column
```

**What happens:**
- Spark says "OK, I'll do that later"
- Builds a plan (DAG = Directed Acyclic Graph)
- **Nothing actually executes yet!**

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### Actions (Eager)

**Eager** = Runs immediately, gives you results NOW

```scala
show()          // Display data
count()         // Count rows
collect()       // Get all data to driver
first()         // Get first row
take(n)         // Get first n rows
write()         // Save to file
saveAsTable()   // Save as table
```

**What happens:**
- Spark executes the entire plan NOW
- Runs all previous transformations
- Returns results

</td>
</tr>
</table>

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Exam Question:** "When does Spark execute transformations?"

**Answer:** Only when an action is called!

**Example:**
```scala
val df = spark.read.csv("data.csv")   // Transformation - not executed yet
val filtered = df.filter($"age" > 21) // Transformation - not executed yet
val grouped = filtered.groupBy("city")// Transformation - not executed yet

// NOTHING has happened yet! No data processed!

grouped.count()  // ACTION! NOW everything executes!
```

**Think of it like:**
- Transformations = Writing a to-do list
- Actions = Actually doing the tasks on the list

</div>

## Spark SQL

**Spark SQL** = Use regular SQL instead of Scala code (easier!)

```scala
// ============================================
// CREATE TEMPORARY VIEW (make table available for SQL)
// ============================================

students_df.createOrReplaceTempView("people")

// Now you can query it with SQL!

// ============================================
// QUERY WITH SQL
// ============================================

val result = spark.sql("""
  SELECT department, COUNT(*) as student_count
  FROM people
  WHERE age > 25
  GROUP BY department
""")

result.show()

// Output:
// +------------------+--------------+
// |        department|student_count |
// +------------------+--------------+
// |Computer Science  |           25 |
// |   Mathematics    |           15 |
// +------------------+--------------+
```

### Common DataFrame Pattern (Memorize This!)

```scala
// Real example: Analyze salary by city
val result = spark.read
  .option("header", "true")
  .csv("/user/tarik/employees.csv")
  .filter($"age" > 21)                    // Only adults
  .groupBy("city")                        // Group by city
  .agg(avg("salary").alias("avg_salary")) // Calculate average salary
  .orderBy($"avg_salary".desc)            // Highest salary cities first
  .show()                                 // Display results (ACTION!)

// Output:
// +----------+-----------+
// |      city|avg_salary |
// +----------+-----------+
// |  San Francisco|  95000|
// |    New York   |  85000|
// |     Austin    |  70000|
// +----------+-----------+
```

---

# RDD ESSENTIALS

## Core Concept

> **RDD = Resilient Distributed Dataset**
> *Think of it as: Low-level building blocks (use DataFrames instead when possible!)*

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #9c27b0;">

**What RDD means:**

**R**esilient = Fault-tolerant (if something breaks, Spark can rebuild it)
- How? Remembers the steps used to create it (**lineage** = family tree of transformations)

**D**istributed = Split across many computers

**D**ataset = Collection of data elements

</div>

### When to Use RDD vs DataFrame?

| Situation | Use This | Why |
|-----------|----------|-----|
| Processing CSV/JSON with columns | DataFrame | Faster, optimized, easier |
| Running SQL queries | DataFrame | Built-in SQL support |
| Unstructured text files (logs, books) | RDD | No clear columns/structure |
| Need precise control over partitions | RDD | Low-level access |
| Just learning Spark | DataFrame | Start here! Easier to learn |
| Working with machine learning | DataFrame | ML libraries use DataFrames |

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Default choice:** Use DataFrames! They're optimized and easier.

**Only use RDD when:**
- Working with unstructured text
- You need very specific low-level control
- DataFrame API can't do what you need

</div>

## Creating RDDs

```scala
// ============================================
// FROM TEXT FILE
// ============================================

// Read a book or log file (each line becomes one element)
val lines_rdd = sc.textFile("hdfs://user/tarik/book.txt")

// Real example content:
// lines_rdd has:
// "Chapter 1: Introduction"
// "This is the first chapter."
// "It explains basic concepts."
// ...

// ============================================
// FROM COLLECTION (list in memory)
// ============================================

// Numbers
val numbers_rdd = sc.parallelize(List(1, 2, 3, 4, 5))

// Strings
val words_rdd = sc.parallelize(Array("hello", "world", "spark"))
```

## Essential Transformations

```scala
// ============================================
// MAP (transform each element)
// ============================================

// Double every number
val numbers = sc.parallelize(List(1, 2, 3, 4, 5))
val doubled = numbers.map(x => x * 2)
// Result: [2, 4, 6, 8, 10]

// Split each line into words
val lines = sc.textFile("book.txt")
val words_array = lines.map(line => line.split(" "))

// ============================================
// FILTER (keep only matching elements)
// ============================================

// Keep only even numbers
val even_only = numbers.filter(x => x % 2 == 0)
// Result: [2, 4]

// Keep only lines with "ERROR"
val error_lines = lines.filter(line => line.contains("ERROR"))

// ============================================
// FLATMAP (one input → multiple outputs)
// ============================================

// Split lines into individual words
val all_words = lines.flatMap(line => line.split(" "))

// Example:
// Input:  ["Hello World", "Spark is fast"]
// Output: ["Hello", "World", "Spark", "is", "fast"]

// ============================================
// REDUCEBYKEY (combine values with same key)
// ============================================

// Count word frequencies
val word_pairs = all_words.map(word => (word, 1))
// Creates: ("Hello", 1), ("World", 1), ("Hello", 1), ("Spark", 1)

val word_counts = word_pairs.reduceByKey(_ + _)
// Combines pairs with same key:
// ("Hello", 2), ("World", 1), ("Spark", 1)

// ⚡ EFFICIENT! Does local addition before shuffling data

// ============================================
// GROUPBYKEY (group values with same key)
// ============================================

val grouped = word_pairs.groupByKey()
// Creates: ("Hello", [1, 1]), ("World", [1]), ("Spark", [1])

// 🐢 SLOW! Shuffles ALL values (use reduceByKey instead!)

// ============================================
// JOIN (combine two RDDs by key)
// ============================================

val names = sc.parallelize(List(("1", "Alice"), ("2", "Bob")))
val ages = sc.parallelize(List(("1", 20), ("2", 22)))

val joined = names.join(ages)
// Result: ("1", ("Alice", 20)), ("2", ("Bob", 22))

// ============================================
// OTHER USEFUL TRANSFORMATIONS
// ============================================

// Remove duplicates
val unique = rdd.distinct()

// Sort by key
val sorted = pairRDD.sortByKey()
```

## Essential Actions

```scala
// ============================================
// COLLECT (get all data to driver)
// ============================================

val all_data = rdd.collect()
// ⚠️ DANGEROUS on big data! Brings EVERYTHING to one computer

// Use for: Small results only (< 1GB)
// DON'T use for: Large datasets (will crash!)

// ============================================
// COUNT (how many elements?)
// ============================================

val total = rdd.count()
// Example result: 1500 (there are 1500 elements)

// ============================================
// TAKE (get first N elements)
// ============================================

val first_10 = rdd.take(10)
// Safe! Only gets 10 elements

// ============================================
// FIRST (get just the first element)
// ============================================

val first_element = rdd.first()

// ============================================
// FOREACH (do something with each element)
// ============================================

// Print each element
rdd.foreach(println)

// Note: This runs on executors (workers)
// Output goes to worker logs, not your screen!

// To see output, use:
rdd.collect().foreach(println)  // Brings to driver first, then prints

// ============================================
// SAVEASTEXTFILE (save to HDFS)
// ============================================

rdd.saveAsTextFile("hdfs://user/tarik/output/")

// Creates directory with files:
// /user/tarik/output/part-00000
// /user/tarik/output/part-00001
// ...
```

## reduceByKey vs groupByKey (EXAM FAVORITE!)

**This question appears in EVERY exam!**

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### ❌ BAD: groupByKey

```scala
// Count words (SLOW way)
word_pairs
  .groupByKey()
  .mapValues(_.sum)
```

**What happens:**
```
Computer 1 has: ("hello", 1), ("world", 1)
Computer 2 has: ("hello", 1), ("spark", 1)

Shuffle ALL values:
→ Computer 3 receives: ("hello", [1, 1])
→ Computer 4 receives: ("world", [1])
→ Computer 5 receives: ("spark", [1])

Then sum: ("hello", 2), ("world", 1), ("spark", 1)
```

**Problems:**
- Sends individual 1s across network (waste!)
- High network traffic
- Very slow on large data

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### ✅ GOOD: reduceByKey

```scala
// Count words (FAST way)
word_pairs
  .reduceByKey(_ + _)
```

**What happens:**
```
Computer 1 has: ("hello", 1), ("world", 1)
→ Local sum: ("hello", 1), ("world", 1)

Computer 2 has: ("hello", 1), ("spark", 1)
→ Local sum: ("hello", 1), ("spark", 1)

Shuffle SUMS only:
→ Computer 3: ("hello", 1+1=2)
→ Computer 4: ("world", 1)
→ Computer 5: ("spark", 1)
```

**Benefits:**
- Adds locally FIRST
- Sends fewer, bigger numbers
- Less network traffic
- **10-100x faster!**

</td>
</tr>
</table>

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Always prefer reduceByKey when possible!**

**Use reduceByKey when:** You can combine values (sum, max, min, multiply, etc.)

**Use groupByKey when:** You actually need all individual values grouped together

</div>

## Complete RDD Example (MEMORIZE THIS!)

**Problem:** Count words in a book, find most frequent words

```scala
// ============================================
// WORD COUNT (classic Spark example)
// ============================================

// Step 1: Read file
val lines = sc.textFile("hdfs://user/tarik/books/dracula.txt")

// Step 2-6: Process
val wordCounts = lines
  .flatMap(line => line.split(" "))      // Split "hello world" → ["hello", "world"]
  .map(word => (word, 1))                // Create pairs: ("hello", 1), ("world", 1)
  .reduceByKey(_ + _)                    // Sum: ("hello", 25), ("world", 15)
  .sortBy(_._2, ascending = false)       // Sort by count (highest first)
  .collect()                             // Get results

// Step 7: Print top 10 words
wordCounts.take(10).foreach(println)

// Output:
// (the, 458)
// (and, 312)
// (to, 289)
// (a, 275)
// (I, 234)
// ...
```

**Explanation of each step:**

```
Step 1: Read file
Lines: ["Hello World", "Hello Spark", "Spark is fast"]

Step 2: flatMap (split into words)
Words: ["Hello", "World", "Hello", "Spark", "Spark", "is", "fast"]

Step 3: map (create pairs)
Pairs: [("Hello",1), ("World",1), ("Hello",1), ("Spark",1), ("Spark",1), ("is",1), ("fast",1)]

Step 4: reduceByKey (sum counts)
Counts: [("Hello",2), ("World",1), ("Spark",2), ("is",1), ("fast",1)]

Step 5: sortBy (highest count first)
Sorted: [("Hello",2), ("Spark",2), ("World",1), ("is",1), ("fast",1)]

Step 6: collect (get to driver)
Result array on your computer

Step 7: Print
Display on screen
```

---

# QUICK COMMAND REFERENCE

## HDFS Commands Cheat Sheet

```bash
# LIST
hdfs dfs -ls /user/tarik/data                    # List files

# CREATE
hdfs dfs -mkdir /user/tarik/project              # Make directory

# UPLOAD
hdfs dfs -put students.csv /user/tarik/data/     # Upload from computer

# DOWNLOAD
hdfs dfs -get /user/tarik/data/result.csv .      # Download to current directory

# VIEW
hdfs dfs -cat /user/tarik/data/small.txt         # View file contents

# DELETE
hdfs dfs -rm /user/tarik/data/temp.txt           # Delete file
hdfs dfs -rm -r /user/tarik/old_data/            # Delete directory
```

## Hive Commands Cheat Sheet

```sql
-- CREATE TABLE
CREATE TABLE students (id INT, name STRING, age INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

-- LOAD DATA
LOAD DATA INPATH '/user/tarik/students.txt' INTO TABLE students;

-- QUERY
SELECT * FROM students WHERE age > 20;
SELECT department, COUNT(*) FROM students GROUP BY department;
```

## Spark DataFrame Cheat Sheet

```scala
// READ
spark.read.csv("/user/tarik/data.csv")

// TRANSFORM
df.select("name", "age")
df.filter($"age" > 21)
df.groupBy("city").count()

// ACTION
df.show()
df.count()
df.write.csv("/user/tarik/output")
```

## Spark RDD Cheat Sheet

```scala
// READ
sc.textFile("/user/tarik/book.txt")

// TRANSFORM
rdd.map(x => x * 2)
rdd.filter(x => x > 10)
rdd.reduceByKey(_ + _)

// ACTION
rdd.collect()
rdd.count()
rdd.saveAsTextFile("/user/tarik/output")
```

---

*[The rest of the content continues with CODE PATTERNS, EXAM QUESTIONS, COMPARISON TABLES, PITFALLS, CHECKLIST, EXERCISES, and TIPS - keeping the same improved style with real examples and explanations]*

---

<div align="center">

# YOU'RE READY!

## Key Takeaways

| Topic | Remember This |
|:------|:-------------|
| **HDFS** | Files split into 128MB blocks • 3 copies each • NameNode = boss, DataNodes = workers |
| **Hive** | SQL for huge data • Managed table = Hive owns data • External table = data stays safe |
| **Spark** | 10-100x faster • Transformations = lazy (build plan) • Actions = eager (execute now!) |
| **RDD** | Low-level • Use DataFrame instead usually • reduceByKey > groupByKey (10-100x faster!) |

### Final Tips:

→ **Understand, don't memorize** - Know WHY things work
→ **Practice with real examples** - Try the commands yourself
→ **Draw diagrams** - Especially NameNode/DataNode, Driver/Executor
→ **Watch for tricks** - Managed vs External, LOCAL vs HDFS, Lazy vs Eager

---

### Good luck on your exam!

*You've got this!*

</div>
