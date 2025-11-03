<div align="center">

<img src="hadoop-svgrepo-com.svg" width="120" height="120" alt="Hadoop Logo" />

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

**Cluster** = The ENTIRE system working together (1 NameNode + ALL the DataNode workers)
- Think of it as: The whole team (1 boss + all the workers)
- Example: If you have 1 NameNode and 100 DataNodes → your cluster has 101 computers total
- NOT just the workers! It's the complete system.

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

 <img src="high-priority-svgrepo-com.svg" width="20" height="20" alt="High Priority" style="vertical-align: middle;" /> **CRITICAL:** If NameNode fails → entire cluster fails!

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

**What is MapReduce?**
- MapReduce = Old way of processing big data on Hadoop (before Spark existed)
- Two steps:
  1. **Map** = Break big job into smaller tasks, process in parallel
  2. **Reduce** = Combine results from all tasks

**Example - Count students per city:**
- **Map step:** Each computer counts students in its data chunk
  - Computer 1: NYC=100, LA=50
  - Computer 2: NYC=80, Boston=40
  - Computer 3: LA=30, NYC=20
- **Reduce step:** Combine all counts
  - NYC = 100 + 80 + 20 = 200
  - LA = 50 + 30 = 80
  - Boston = 40

**Why you should know this:** Hive uses MapReduce under the hood, but you just write SQL!

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

-- What is '\t'?
-- '\t' = TAB character (when you press the Tab key on keyboard)
-- It's invisible but creates space between columns
--
-- What are we doing here?
-- We're telling Hive: "In my file, each column is separated by a TAB"
-- Example file content: "Alice[TAB]25[TAB]NYC" → 3 columns (name, age, city)
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

**What does "remembers the structure" mean?**
- Hive stores the table definition (column names, data types) in its metastore (a special database)
- Example: "The students table has 3 columns: id (INT), name (STRING), age (INT)"
- Hive also remembers: "The data files are located at /user/tarik/my_data/students/"

**Why does Hive remember this?**
- So when you run `SELECT * FROM students`, Hive knows:
  1. Where to find the data files (/user/tarik/my_data/students/)
  2. How to read them (what columns exist and their types)
  3. How to organize the results (show id, name, age columns)

**The key point:** Hive remembers the STRUCTURE (table definition), but the actual DATA lives in your specified location on HDFS

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

**"Deleting table doesn't touch data" - What does this mean?**

When you run `DROP TABLE students_ext;` on an external table:

**What Hive deletes:**
- The table definition from its metastore
- The metadata (structure information)
- The table name (you can't query it anymore)

**What Hive DOES NOT delete:**
- Your actual data files in `/user/tarik/my_data/students/`
- All your CSV files stay exactly where they are
- Other applications can still access these files

**Example:**
```sql
DROP TABLE students_ext;  -- Table removed from Hive

-- But your data is STILL THERE:
hdfs dfs -ls /user/tarik/my_data/students/
-- Output: students.csv still exists! (file is safe)

-- You can recreate the table later:
CREATE EXTERNAL TABLE students_ext (id INT, name STRING, age INT)
LOCATION '/user/tarik/my_data/students/';
-- Now you can query the same data again!
```

**Why is this useful?**
- Safe! Accidentally dropping table won't lose your data
- Other tools (Spark, Pig, MapReduce) can still use the same files
- You can share data between multiple applications

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

**Remember:**
- INPATH = move (file disappears from original location)
- LOCAL INPATH = copy (file stays in original location)

**Why the difference?**
- WITHOUT LOCAL: File is already on HDFS → Hive just MOVES it to warehouse (no copying needed, saves time)
- WITH LOCAL: File is on your computer → Hive must COPY it to HDFS first (original stays on your computer)

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
<td colspan="3" style="background: #f0f0f0; padding: 10px;">
<strong>What is ACID? (Simple explanation)</strong><br><br>
<strong>Atomic</strong> = All or nothing. If you update 100 rows and it fails at row 50, ALL changes are cancelled (rollback).<br>
Example: Bank transfer - either BOTH accounts update or NEITHER (no half-completed transfers)<br><br>
<strong>Consistent</strong> = Rules are always followed. If a rule says "age must be positive", database won't accept age = -5.<br>
Example: You can't have invalid data (like a student with negative age)<br><br>
<strong>Isolated</strong> = Transactions don't interfere. If two people update the same row, they wait in line (no conflicts).<br>
Example: Two people booking the last seat - one waits for the other to finish<br><br>
<strong>Durable</strong> = Once saved, it's permanent. Even if power fails, your changes are saved.<br>
Example: After you see "Transaction complete", data is safe even if computer crashes immediately
</td>
</tr>
<tr>
<td><strong>Indexes</strong></td>
<td>✅ Full index support</td>
<td>⚠️ Limited index support</td>
</tr>
<tr>
<td colspan="3" style="background: #f0f0f0; padding: 10px;">
<strong>What is an index?</strong><br>
An index is like a book's index at the back - it helps you find information quickly without reading everything.<br><br>
<strong>Without index:</strong> To find all students named "Alice", database scans ALL 1 million rows (slow!)<br>
<strong>With index:</strong> Database has a list: "Alice → rows 42, 157, 8934" → jumps directly there (fast!)<br><br>
<strong>Example:</strong> Phone book is indexed by name (find people quickly). Without index, you'd read every page to find someone.
</td>
</tr>
<tr>
<td><strong>Speed</strong></td>
<td>⚡ Fast (milliseconds)</td>
<td>🐢 Slower (seconds to minutes)<br><small>Because it's batch processing</small></td>
</tr>
<tr>
<td colspan="3" style="background: #f0f0f0; padding: 10px;">
<strong>What is batch processing?</strong><br>
Process LARGE amounts of data all at once (in a batch), not one item at a time.<br><br>
<strong>Example 1 - Payroll:</strong><br>
• Batch: Process all 10,000 employees' salaries at end of month (one big job, takes 1 hour)<br>
• NOT batch: Calculate each employee's salary individually as they ask (10,000 separate jobs)<br><br>
<strong>Example 2 - Daily reports:</strong><br>
• Batch: Process all of today's sales at midnight (analyze 1 million transactions together)<br>
• NOT batch: Update report after each individual sale (1 million separate updates)<br><br>
<strong>Why Hive uses batch:</strong> It's designed for analyzing huge datasets (terabytes). Better to process everything together than one row at a time.
</td>
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

**What is Streaming?**
- Streaming = Process data continuously as it arrives (real-time), not waiting for all data
- Opposite of batch processing

**Batch vs Streaming examples:**

**Batch processing (Hive style):**
- Wait for all of today's data → Process at midnight → Get results
- Example: Daily sales report (wait until day ends, then analyze all sales)

**Streaming (Spark can do this!):**
- Process data immediately as it comes in
- Example: Twitter sentiment analysis (analyze each tweet as it's posted, see trends NOW)
- Example: Fraud detection (check each credit card transaction immediately, block suspicious ones in real-time)
- Example: Stock market dashboard (update prices every second as trades happen)

**Why streaming is useful:**
- Get insights instantly (no waiting)
- React quickly (detect problems immediately)
- Always up-to-date (not yesterday's data)

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

# CODE PATTERNS YOU MUST KNOW

## Pattern 1: Load CSV → Process → Save Results

**Scenario:** You have student grades in CSV, need to calculate averages and save results

```scala
// ============================================
// Using Spark DataFrame (EASIER)
// ============================================

// Step 1: Read CSV file from HDFS
val df = spark.read
  .option("header", "true")                    // First line = column names
  .option("inferSchema", "true")               // Auto-detect data types (INT, STRING, etc.)
  .csv("/user/tarik/student_project/grades.csv")

// What you have now:
// +----+-------+--------+------+
// | id | name  | course | grade|
// +----+-------+--------+------+
// |  1 | Alice | Math   |   85 |
// |  2 | Bob   | Math   |   92 |
// |  3 | Alice | CS     |   78 |
// +----+-------+--------+------+

// Step 2: Calculate average grade per student
val averages = df.groupBy("name")
  .agg(avg("grade").as("average_grade"))

// Result:
// +-------+--------------+
// | name  | average_grade|
// +-------+--------------+
// | Alice |          81.5|
// | Bob   |          92.0|
// +-------+--------------+

// Step 3: Save to HDFS
averages.write
  .mode("overwrite")                           // Replace if exists
  .csv("/user/tarik/student_project/averages")

// Creates:
// /user/tarik/student_project/averages/part-00000.csv
// /user/tarik/student_project/averages/part-00001.csv
```

## Pattern 2: Filter → Transform → Aggregate

**Scenario:** Find total sales for each product that sold more than 100 units

```scala
// ============================================
// Using Spark DataFrame
// ============================================

// Step 1: Read data
val sales = spark.read
  .option("header", "true")
  .csv("/user/tarik/sales_data.csv")

// Data looks like:
// product,units,price
// Laptop,150,800
// Mouse,50,20
// Keyboard,200,45

// Step 2: Filter (keep only high-selling products)
val highSellers = sales.filter($"units" > 100)

// Now only have:
// Laptop,150,800
// Keyboard,200,45

// Step 3: Transform (calculate total revenue = units × price)
val withRevenue = highSellers
  .withColumn("revenue", $"units" * $"price")

// Result:
// product,units,price,revenue
// Laptop,150,800,120000
// Keyboard,200,45,9000

// Step 4: Aggregate (sum total revenue)
val totalRevenue = withRevenue.agg(sum("revenue"))
totalRevenue.show()

// Output: 129000
```

## Pattern 3: Join Two Datasets

**Scenario:** Match student names with their department information

```scala
// ============================================
// Join Pattern
// ============================================

// Dataset 1: Students
val students = spark.read.csv("/user/tarik/students.csv")
// student_id,name
// 1,Alice
// 2,Bob

// Dataset 2: Departments
val departments = spark.read.csv("/user/tarik/departments.csv")
// student_id,department
// 1,Computer Science
// 2,Mathematics

// Join them by student_id
val joined = students.join(departments, "student_id")

// Result:
// student_id,name,department
// 1,Alice,Computer Science
// 2,Bob,Mathematics

// Alternative: Join with different column names
val students2 = spark.read.csv("/user/tarik/students2.csv")
// id,name
val departments2 = spark.read.csv("/user/tarik/depts2.csv")
// student_number,dept

val joined2 = students2.join(
  departments2,
  students2("id") === departments2("student_number")
)
```

## Pattern 4: Hive Table → Spark Processing → Hive Table

**Scenario:** Read from Hive table, process in Spark, write back to Hive

```scala
// ============================================
// Hive + Spark Integration
// ============================================

// Step 1: Read from existing Hive table
val inputData = spark.sql("SELECT * FROM students WHERE age > 20")

// Step 2: Process in Spark (faster than Hive!)
val processed = inputData
  .groupBy("department")
  .agg(
    count("*").as("student_count"),
    avg("gpa").as("average_gpa")
  )

// Step 3: Write results back to Hive
processed.write
  .mode("overwrite")
  .saveAsTable("department_statistics")

// Now you can query it from Hive:
// SELECT * FROM department_statistics;
```

## Pattern 5: Word Count (Classic RDD Pattern)

**Scenario:** Count word frequencies in a book

```scala
// ============================================
// Word Count (appears in EVERY exam!)
// ============================================

val wordCounts = sc.textFile("/user/tarik/books/dracula.txt")
  .flatMap(line => line.split(" "))            // Split into words
  .map(word => (word, 1))                      // Create (word, 1) pairs
  .reduceByKey(_ + _)                          // Sum counts for each word
  .sortBy(_._2, ascending = false)             // Sort by count

// Save top 100 words
wordCounts.take(100).foreach(println)

// OR save all results to HDFS
wordCounts.saveAsTextFile("/user/tarik/wordcount_output")
```

---

# COMMON EXAM QUESTIONS

## Question 1: Explain HDFS Architecture

**Question:** Describe the architecture of HDFS. What are the roles of NameNode and DataNode?

<div style="background: #e8f5e9; padding: 15px; border-left: 4px solid #4caf50;">

**Good Answer:**

HDFS uses a **master-slave architecture** (master-slave = one boss, many workers):

**NameNode (the master/boss):**
- **Only ONE** in the cluster
- Stores **metadata** (= information ABOUT files, like: "where is file X stored?")
- Does NOT store actual data
- Keeps track of which DataNodes have which blocks
- Example: "students.csv block 1 is on DataNode 3, 7, and 12"

**DataNodes (the workers):**
- **MANY** in the cluster (could be hundreds!)
- Store the **actual data blocks** (the real file content)
- Send heartbeat (= "I'm alive!") to NameNode every 3 seconds
- If DataNode dies, NameNode knows and creates new copies

**How they work together:**

1. You want to read `/user/tarik/report.pdf`
2. You ask NameNode: "Where is report.pdf?"
3. NameNode replies: "Block 1 is on DataNode 5, Block 2 is on DataNode 9"
4. You go directly to DataNode 5 and 9 to get the data
5. NameNode is NOT involved in data transfer (only tells you where things are)

**Critical point:** If NameNode fails → entire cluster fails! (because nobody knows where anything is anymore)

</div>

## Question 2: Managed vs External Tables in Hive

**Question:** What is the difference between managed and external tables in Hive?

<div style="background: #e8f5e9; padding: 15px; border-left: 4px solid #4caf50;">

**Good Answer:**

**Managed Table (internal table):**
```sql
CREATE TABLE students (id INT, name STRING);
LOAD DATA INPATH '/user/tarik/students.txt' INTO TABLE students;
```

- Hive **MOVES** the data from HDFS to Hive's warehouse directory
- Original file `/user/tarik/students.txt` **disappears** (moved!)
- If you `DROP TABLE students`, the **data is DELETED** forever!
- Use when: Only your Hive queries need this data

**External Table:**
```sql
CREATE EXTERNAL TABLE students (id INT, name STRING)
LOCATION '/user/tarik/student_project/';
```

- Data stays in `/user/tarik/student_project/` (NOT moved!)
- If you `DROP TABLE students`, only the table definition is removed
- **Data remains safe** in HDFS
- Use when: Other applications also need this data (Spark, MapReduce, etc.)

**Key difference:**

| Action | Managed Table | External Table |
|--------|--------------|----------------|
| DROP TABLE | Data **deleted** | Data **safe** |
| Data location | Hive warehouse | Your specified location |

**Exam tip:** Always use EXTERNAL if you want to keep your data safe!

</div>

## Question 3: Transformations vs Actions in Spark

**Question:** Explain the difference between transformations and actions in Spark. Give examples.

<div style="background: #e8f5e9; padding: 15px; border-left: 4px solid #4caf50;">

**Good Answer:**

**Transformations (lazy = doesn't execute immediately):**
- Build a **plan** of what to do (like a to-do list)
- **Nothing actually happens** until you call an action
- Return a new RDD/DataFrame
- Examples: `map`, `filter`, `groupBy`, `join`

```scala
// These just build the plan (nothing executes yet!)
val step1 = df.filter($"age" > 20)        // Add to plan: "filter age > 20"
val step2 = step1.select("name")          // Add to plan: "then select name"
val step3 = step2.distinct()              // Add to plan: "then remove duplicates"

// Still nothing has happened! Just a plan exists.
```

**Actions (eager = executes NOW!):**
- **Triggers execution** of the entire plan
- Returns results to your program
- Examples: `count`, `collect`, `show`, `saveAsTextFile`

```scala
// NOW everything executes!
val result = step3.collect()  // ← ACTION! Spark runs all transformations now
```

**Why is this useful?**

Spark can **optimize the entire plan** before executing:

```scala
df.filter($"age" > 20)        // Plan: filter age > 20
  .filter($"city" == "NYC")   // Plan: filter city = NYC
  .count()                    // ACTION!

// Spark optimizes: "I can do both filters at once! More efficient!"
```

**Memory trick:**
- **Transformations** = Lazy (Like writing a shopping list, haven't shopped yet)
- **Actions** = Eager (Actually going to the store and buying things)

</div>

## Question 4: reduceByKey vs groupByKey

**Question:** Why is `reduceByKey` preferred over `groupByKey`? Explain with an example.

<div style="background: #e8f5e9; padding: 15px; border-left: 4px solid #4caf50;">

**Good Answer:**

**Performance:** `reduceByKey` is **10-100 times faster** than `groupByKey`!

**Why?** Let me show you what happens inside:

**Example:** Count how many times each word appears

**BAD: groupByKey** (slow)
```scala
wordPairs.groupByKey().mapValues(_.sum)
```

What happens:
```
Computer 1 has: ("apple", 1), ("apple", 1), ("apple", 1)
Computer 2 has: ("apple", 1), ("apple", 1)

SHUFFLE (send over network):
→ Send ALL 5 individual numbers: 1, 1, 1, 1, 1

Computer 3 receives: ("apple", [1, 1, 1, 1, 1])
Then sums: ("apple", 5)

Problem: Sent 5 numbers over the network (waste of bandwidth!)
```

**GOOD: reduceByKey** (fast)
```scala
wordPairs.reduceByKey(_ + _)
```

What happens:
```
Computer 1 has: ("apple", 1), ("apple", 1), ("apple", 1)
→ Local sum FIRST: ("apple", 3)

Computer 2 has: ("apple", 1), ("apple", 1)
→ Local sum FIRST: ("apple", 2)

SHUFFLE (send over network):
→ Send only 2 numbers: 3 and 2

Computer 3 receives: ("apple", 3) and ("apple", 2)
Final sum: ("apple", 5)

Benefit: Only sent 2 numbers instead of 5! Much less network traffic!
```

**When to use each:**

→ Use `reduceByKey`: When you can combine values (sum, max, min, multiply, etc.)
→ Use `groupByKey`: When you actually need ALL individual values (rare!)

**Exam answer:** Always prefer `reduceByKey` because it reduces data LOCALLY before shuffling, reducing network traffic and making it much faster.

</div>

## Question 5: What happens when you run `df.show()`?

**Question:** Trace what happens when you execute `df.show()` in Spark.

<div style="background: #e8f5e9; padding: 15px; border-left: 4px solid #4caf50;">

**Good Answer:**

```scala
val df = spark.read.csv("/user/tarik/data.csv")
df.filter($"age" > 20).show()  // What happens here?
```

**Step-by-step execution:**

1. **`show()` is an ACTION** → triggers execution

2. **Driver** (your program) creates execution plan:
   - Read CSV file
   - Filter rows where age > 20
   - Take first 20 rows

3. **Driver sends tasks to Executors** (workers):
   - Executor 1: "Read blocks 1-5, filter age > 20"
   - Executor 2: "Read blocks 6-10, filter age > 20"
   - Executor 3: "Read blocks 11-15, filter age > 20"

4. **Executors do the work** in parallel:
   - Each executor reads its blocks
   - Each executor filters its rows
   - Each executor sends results back to Driver

5. **Driver collects results:**
   - Receives filtered rows from all executors
   - Takes first 20 rows
   - Formats as pretty table
   - Prints to your screen

6. **You see output:**
```
+---+-------+-----+
| id|   name|  age|
+---+-------+-----+
|  1|  Alice|   25|
|  2|    Bob|   30|
...
```

**Important points:**
- Data stays on executors (not all sent to driver)
- Only 20 rows sent to driver (safe)
- `collect()` would send ALL rows to driver (dangerous if millions of rows!)

</div>

---

# COMPARISON TABLES

## HDFS Commands: Local vs HDFS Paths

<table style="width: 100%; border-collapse: collapse;">
<tr style="background: #2196F3; color: white;">
<th style="padding: 10px;">Action</th>
<th style="padding: 10px;">Local Computer (Windows/Mac)</th>
<th style="padding: 10px;">HDFS (Hadoop)</th>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>List files</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>dir</code> (Windows)<br><code>ls</code> (Mac/Linux)</td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -ls /user/tarik/</code></td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Create folder</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>mkdir project</code></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -mkdir /user/tarik/project</code></td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>View file</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>type file.txt</code> (Windows)<br><code>cat file.txt</code> (Mac/Linux)</td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -cat /user/tarik/file.txt</code></td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Delete file</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>del file.txt</code> (Windows)<br><code>rm file.txt</code> (Mac/Linux)</td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -rm /user/tarik/file.txt</code></td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Copy file</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>copy old.txt new.txt</code></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -cp /user/tarik/old.txt /user/tarik/new.txt</code></td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Upload to HDFS</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">—</td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -put local_file.csv /user/tarik/</code></td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Download from HDFS</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">—</td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>hdfs dfs -get /user/tarik/file.csv .</code></td>
</tr>
</table>

## Hive: LOAD DATA Variants

<table style="width: 100%; border-collapse: collapse;">
<tr style="background: #4CAF50; color: white;">
<th style="padding: 10px;">Command</th>
<th style="padding: 10px;">What It Does</th>
<th style="padding: 10px;">Original File After Loading</th>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><code>LOAD DATA <strong>LOCAL</strong> INPATH '/home/tarik/data.csv' INTO TABLE students;</code></td>
<td style="padding: 10px; border: 1px solid #ddd;">Uploads from YOUR computer to Hive<br>(LOCAL = your Windows/Mac/Linux machine)</td>
<td style="padding: 10px; border: 1px solid #ddd;">✅ <strong>Stays on your computer</strong><br>(file is COPIED)</td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><code>LOAD DATA INPATH '/user/tarik/data.csv' INTO TABLE students;</code></td>
<td style="padding: 10px; border: 1px solid #ddd;">Moves from HDFS to Hive warehouse<br>(no LOCAL = from HDFS)</td>
<td style="padding: 10px; border: 1px solid #ddd;">❌ <strong>Disappears!</strong><br>(file is MOVED, not copied)</td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><code>LOAD DATA INPATH '/user/tarik/data.csv' <strong>OVERWRITE</strong> INTO TABLE students;</code></td>
<td style="padding: 10px; border: 1px solid #ddd;">Same as above, but <strong>deletes existing data</strong> in table first</td>
<td style="padding: 10px; border: 1px solid #ddd;">❌ Disappears<br>⚠️ Old table data deleted</td>
</tr>
</table>

## Spark: DataFrame vs RDD

<table style="width: 100%; border-collapse: collapse;">
<tr style="background: #FF9800; color: white;">
<th style="padding: 10px;">Feature</th>
<th style="padding: 10px;">DataFrame (Use This!)</th>
<th style="padding: 10px;">RDD (Old Way)</th>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Ease of use</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">✅ Easy (like SQL)<br><code>df.filter($"age" > 20)</code></td>
<td style="padding: 10px; border: 1px solid #ddd;">❌ Complex<br><code>rdd.filter(x => x.age > 20)</code></td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Performance</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">✅ Optimized automatically<br>(Catalyst optimizer)</td>
<td style="padding: 10px; border: 1px solid #ddd;">❌ You must optimize manually</td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Schema</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">✅ Has schema (knows column names and types)</td>
<td style="padding: 10px; border: 1px solid #ddd;">❌ No schema (just raw data)</td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>When to use</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">✅ <strong>Default choice</strong> for 95% of cases</td>
<td style="padding: 10px; border: 1px solid #ddd;">Only when you need very low-level control</td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Example</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>spark.read.csv("data.csv")</code></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>sc.textFile("data.csv")</code></td>
</tr>
</table>

## Spark: Transformations vs Actions

<table style="width: 100%; border-collapse: collapse;">
<tr style="background: #9C27B0; color: white;">
<th style="padding: 10px; width: 33%;">Property</th>
<th style="padding: 10px; width: 33%;">Transformations</th>
<th style="padding: 10px; width: 34%;">Actions</th>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>When executed</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">**Lazy** (not immediately)</td>
<td style="padding: 10px; border: 1px solid #ddd;">**Eager** (immediately)</td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Return type</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">New RDD/DataFrame</td>
<td style="padding: 10px; border: 1px solid #ddd;">Result value (number, array, nothing)</td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Examples</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>map</code>, <code>filter</code>, <code>groupBy</code>, <code>join</code>, <code>distinct</code>, <code>select</code></td>
<td style="padding: 10px; border: 1px solid #ddd;"><code>count</code>, <code>collect</code>, <code>show</code>, <code>take</code>, <code>saveAsTextFile</code></td>
</tr>
<tr style="background: #f5f5f5;">
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Purpose</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">Build a plan of what to do</td>
<td style="padding: 10px; border: 1px solid #ddd;">Execute the plan and get results</td>
</tr>
<tr>
<td style="padding: 10px; border: 1px solid #ddd;"><strong>Analogy</strong></td>
<td style="padding: 10px; border: 1px solid #ddd;">Writing a recipe (planning)</td>
<td style="padding: 10px; border: 1px solid #ddd;">Cooking the food (doing)</td>
</tr>
</table>

---

# COMMON PITFALLS (AVOID THESE!)

## Pitfall 1: Using `collect()` on Large Datasets

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ WRONG:**

```scala
val df = spark.read.csv("/user/tarik/huge_file_10GB.csv")
val all_data = df.collect()  // CRASH! Trying to load 10GB into driver memory
```

**Problem:** `collect()` brings ALL data to your driver program (one computer). If the dataset is huge (gigabytes), your program will crash!

**✅ CORRECT:**

```scala
// Option 1: Use take() to get just a sample
val sample = df.take(100)  // Only 100 rows (safe!)

// Option 2: Save to HDFS instead
df.write.csv("/user/tarik/output/")  // Stays distributed

// Option 3: Use show() for preview
df.show(20)  // Just show 20 rows for checking
```

**Rule:** Never use `collect()` unless you're SURE the result is small (< 1GB).

</div>

## Pitfall 2: Using `groupByKey` Instead of `reduceByKey`

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ WRONG:**

```scala
word_pairs.groupByKey().mapValues(_.sum)  // SLOW! (10-100x slower)
```

**Problem:** Sends ALL individual values over the network before combining them.

**✅ CORRECT:**

```scala
word_pairs.reduceByKey(_ + _)  // FAST! Combines locally first
```

**Rule:** Always use `reduceByKey` when you can (sum, max, min, multiply, etc.).

</div>

## Pitfall 3: Forgetting `LOCAL` Keyword in Hive LOAD DATA

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ WRONG:**

```sql
-- Trying to load from my computer, but forgot LOCAL
LOAD DATA INPATH 'C:\Users\Tarik\data.csv' INTO TABLE students;
```

**Error you get:**
```
File not found: hdfs://C:\Users\Tarik\data.csv
```

**Problem:** Without `LOCAL`, Hive looks for the file in HDFS (not your computer)!

**✅ CORRECT:**

```sql
-- From your computer: use LOCAL
LOAD DATA LOCAL INPATH 'C:\Users\Tarik\data.csv' INTO TABLE students;

-- From HDFS: no LOCAL
LOAD DATA INPATH '/user/tarik/data.csv' INTO TABLE students;
```

**Rule:** Use `LOCAL` when loading from your computer, no `LOCAL` when loading from HDFS.

</div>

## Pitfall 4: Not Understanding Managed vs External Tables

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ DANGEROUS:**

```sql
CREATE TABLE important_data (id INT, value STRING);
LOAD DATA INPATH '/user/tarik/critical_file.csv' INTO TABLE important_data;

-- Later... you DROP the table
DROP TABLE important_data;

-- OOPS! critical_file.csv is DELETED FOREVER!
```

**Problem:** Managed tables DELETE your data when you drop the table!

**✅ SAFE:**

```sql
CREATE EXTERNAL TABLE important_data (id INT, value STRING)
LOCATION '/user/tarik/project/';

-- Later... you DROP the table
DROP TABLE important_data;

-- File is SAFE! Only table definition is removed.
```

**Rule:** Use `EXTERNAL` tables when your data is important or shared with other tools.

</div>

## Pitfall 5: Confusing Transformations with Actions

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ WRONG EXPECTATION:**

```scala
val df = spark.read.csv("/user/tarik/data.csv")
val filtered = df.filter($"age" > 20)

println("Data filtered!")  // WRONG! Nothing has happened yet!
```

**Problem:** `filter()` is a transformation (lazy). Nothing executes until you call an action!

**✅ CORRECT UNDERSTANDING:**

```scala
val df = spark.read.csv("/user/tarik/data.csv")
val filtered = df.filter($"age" > 20)  // Just planning (lazy)

filtered.count()  // NOW it executes! (action triggers execution)
```

**Rule:** Remember that transformations build a plan. Only actions trigger execution.

</div>

## Pitfall 6: Using Wrong Delimiters in Hive

<div style="background: #ffebee; padding: 15px; border-left: 4px solid #f44336;">

**❌ WRONG:**

```sql
-- Your CSV file uses commas: Alice,25,NYC
-- But you create table with tabs as delimiter:
CREATE TABLE students (name STRING, age INT, city STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

LOAD DATA LOCAL INPATH 'students.csv' INTO TABLE students;

SELECT * FROM students;
-- Result: All data in one column! "Alice,25,NYC" instead of 3 columns
```

**Problem:** Hive expects tabs (`\t`) but your file has commas (`,`)!

**✅ CORRECT:**

```sql
-- CSV file (comma-separated)
-- Your file looks like: Alice,25,NYC
CREATE TABLE students (name STRING, age INT, city STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';  -- Use comma!

-- OR for tab-separated file (TSV):
-- Your file looks like: Alice[TAB]25[TAB]NYC
-- (TAB is invisible space created by pressing Tab key)
CREATE TABLE students (name STRING, age INT, city STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';  -- Use tab! (\t = TAB character)
```

**Rule:** Match your FIELDS TERMINATED BY with your actual file format!

**Common delimiters:**
- `','` = Comma (CSV files - most common)
- `'\t'` = Tab character (TSV files)
- `'|'` = Pipe character (sometimes used in databases)
- `' '` = Space (rarely used)

</div>

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
