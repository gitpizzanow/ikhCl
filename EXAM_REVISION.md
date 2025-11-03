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

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #2196F3;">

**Key Features:**
- Stores files across multiple machines (distributed)
- Each file split into **blocks** (default: 128 MB)
- Each block **replicated 3 times** (fault tolerance)

</div>

## Architecture (MUST KNOW!)

```
┌─────────────────────────────────┐
│      NameNode (Master)          │
│  • Stores metadata              │
│  • Manages file system          │
│  • Single Point of Failure      │
└─────────────────────────────────┘
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌────────┐ ┌────────┐ ┌────────┐
│DataNode│ │DataNode│ │DataNode│
│        │ │        │ │        │
│ Blocks │ │ Blocks │ │ Blocks │
└────────┘ └────────┘ └────────┘
```

<div style="background: #fff3cd; padding: 10px; border-left: 4px solid #ffc107;">

**CRITICAL:** If NameNode fails → entire cluster fails!

</div>

## Essential HDFS Commands

### File Operations

```bash
# List files
hdfs dfs -ls /path
hdfs dfs -ls -R /path          # Recursive

# Create directory
hdfs dfs -mkdir /mydir
hdfs dfs -mkdir -p /a/b/c      # Create parent dirs

# Upload file to HDFS
hdfs dfs -put localfile.txt /hdfs/path/

# Download file from HDFS
hdfs dfs -get /hdfs/file.txt localfile.txt

# Copy within HDFS
hdfs dfs -cp /source /destination

# Move/Rename
hdfs dfs -mv /old/path /new/path

# Delete file
hdfs dfs -rm /path/file.txt

# Delete directory (recursive)
hdfs dfs -rm -r /path/directory

# View file content
hdfs dfs -cat /path/file.txt
hdfs dfs -tail /path/file.txt   # Last 1KB
```

## Key Properties

<table>
<thead>
<tr>
<th>Property</th>
<th>Value</th>
<th>Why Important</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Block size</strong></td>
<td><code>128 MB</code></td>
<td>Files split into these chunks</td>
</tr>
<tr>
<td><strong>Replication</strong></td>
<td><code>3 copies</code></td>
<td>Fault tolerance</td>
</tr>
<tr>
<td><strong>Write-once</strong></td>
<td>Cannot modify</td>
<td>Can only append or delete</td>
</tr>
<tr>
<td><strong>NameNode</strong></td>
<td>1 per cluster</td>
<td>Metadata manager</td>
</tr>
</tbody>
</table>

### Exam Tip

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Q:** "What happens if a DataNode fails?"

**A:** HDFS automatically replicates the blocks from other copies to maintain replication factor of 3.

</div>

---

# HIVE ESSENTIALS

## Core Concept

> **Hive = SQL interface for Hadoop**

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #ff9800;">

**Key Features:**
- Translates SQL queries → MapReduce jobs
- **Schema on Read** (defines schema when querying)
- Uses **HiveQL** (SQL-like language)

</div>

## Table Types (CRITICAL!)

### 1. Managed Tables

```sql
-- Creating a managed table
CREATE TABLE students (
    id INT,
    name STRING,
    age INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

<div style="background: #ffebee; padding: 10px; border-left: 4px solid #f44336;">

**Managed Tables:**
- Hive **manages data AND metadata**
- `DROP TABLE` → deletes **both** data AND metadata
- Data stored in: `/user/hive/warehouse/table_name/`

</div>

### 2. External Tables

```sql
-- Creating an external table
CREATE EXTERNAL TABLE students_ext (
    id INT,
    name STRING,
    age INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LOCATION '/user/mydata/students/';
```

<div style="background: #e3f2fd; padding: 10px; border-left: 4px solid #2196f3;">

**External Tables:**
- Hive **manages only metadata**
- `DROP TABLE` → deletes only metadata, **data preserved**
- You specify where data is stored

</div>

### When to Use Which?

| Use Case | Table Type |
|----------|------------|
| Temporary data | Managed |
| Hive controls everything | Managed |
| Shared data with other tools | External |
| Production data | External |

## Loading Data

```sql
-- From HDFS to Hive table
LOAD DATA INPATH '/user/data/file.txt'
INTO TABLE students;

-- From local filesystem
LOAD DATA LOCAL INPATH '/home/user/file.txt'
INTO TABLE students;
```

<div style="background: #fff3cd; padding: 10px; border-left: 4px solid #ffc107;">

**IMPORTANT:** `LOAD DATA INPATH` **moves** the file (not copy!)

</div>

## Essential HiveQL Queries

```sql
-- Basic SELECT
SELECT * FROM students;
SELECT name, age FROM students WHERE age > 20;

-- Aggregations
SELECT COUNT(*) FROM students;
SELECT AVG(age) FROM students;
SELECT MAX(age), MIN(age) FROM students;

-- GROUP BY
SELECT department, COUNT(*)
FROM students
GROUP BY department;

-- JOIN
SELECT s.name, e.course
FROM students s
JOIN enrollments e ON s.id = e.student_id;

-- HAVING (filter after GROUP BY)
SELECT department, COUNT(*) as cnt
FROM students
GROUP BY department
HAVING cnt > 10;

-- ORDER BY (global sort - expensive!)
SELECT * FROM students ORDER BY age DESC;

-- SORT BY (per reducer - faster)
SELECT * FROM students SORT BY age DESC;
```

## Key Differences from SQL

<table>
<thead>
<tr>
<th>SQL Feature</th>
<th>Hive Support</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>UPDATE/DELETE</strong></td>
<td>❌ Not supported</td>
</tr>
<tr>
<td><strong>Transactions</strong></td>
<td>❌ Not ACID</td>
</tr>
<tr>
<td><strong>Indexes</strong></td>
<td>⚠️ Limited</td>
</tr>
<tr>
<td><strong>Speed</strong></td>
<td>Slower (batch)</td>
</tr>
<tr>
<td><strong>Scale</strong></td>
<td>✅ Petabytes</td>
</tr>
</tbody>
</table>

---

# SPARK ESSENTIALS

## Core Concept

> **Spark = Fast in-memory processing framework**

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #ff5722;">

**Key Features:**
- **10-100x faster** than MapReduce
- **In-memory** computation (vs MapReduce disk writes)
- Supports: Batch, Streaming, SQL, ML, Graph processing

</div>

## Architecture (MUST MEMORIZE!)

```
┌──────────────────────────┐
│    Driver Program        │
│  • Creates SparkContext  │
│  • Builds execution plan │
│  • Coordinates tasks     │
└──────────────────────────┘
            │
    ┌───────┼───────┐
    ▼       ▼       ▼
┌─────────┐ ┌─────────┐ ┌─────────┐
│Executor │ │Executor │ │Executor │
│         │ │         │ │         │
│ Tasks   │ │ Tasks   │ │ Tasks   │
│ Cache   │ │ Cache   │ │ Cache   │
└─────────┘ └─────────┘ └─────────┘
```

## DataFrames - Essential Operations

### Creating DataFrames

```scala
// From CSV
val df = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("/path/to/file.csv")

// From JSON
val df = spark.read.json("/path/to/file.json")

// From Parquet
val df = spark.read.parquet("/path/to/file.parquet")
```

### Basic Operations

```scala
// Show data
df.show()
df.show(10)        // First 10 rows

// Schema
df.printSchema()

// Select columns
df.select("name", "age")
df.select($"name", $"age" + 1)

// Filter
df.filter($"age" > 21)
df.where($"age" > 21)    // Same as filter

// Group By
df.groupBy("department").count()
df.groupBy("department").agg(avg("salary"))

// Sort
df.sort($"age".desc)
df.orderBy($"age".desc)  // Same as sort

// Add column
df.withColumn("age_plus_one", $"age" + 1)

// Rename column
df.withColumnRenamed("old_name", "new_name")

// Drop column
df.drop("column_name")
```

## Transformations vs Actions (CRITICAL!)

<table>
<tr>
<td width="50%" style="background: #e3f2fd; padding: 15px;">

### Transformations (Lazy)

```scala
select()
filter()
groupBy()
join()
sort()
distinct()
withColumn()
```

**Not executed immediately!**
Builds execution plan

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### Actions (Eager)

```scala
show()
count()
collect()
first()
take(n)
write()
saveAsTable()
```

**Triggers execution NOW!**
Returns results

</td>
</tr>
</table>

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Exam Question:** "When does Spark execute transformations?"

**Answer:** Only when an action is called! Transformations build a plan, actions execute it.

</div>

## Spark SQL

```scala
// Create temp view
df.createOrReplaceTempView("people")

// Query with SQL
val result = spark.sql("""
  SELECT department, COUNT(*) as count
  FROM people
  WHERE age > 25
  GROUP BY department
""")

result.show()
```

### Common DataFrame Pattern

```scala
val result = spark.read
  .option("header", "true")
  .csv("data.csv")
  .filter($"age" > 21)                    // Filter
  .groupBy("city")                        // Group
  .agg(avg("salary").alias("avg_salary")) // Aggregate
  .orderBy($"avg_salary".desc)            // Sort
  .show()                                 // Display
```

---

# RDD ESSENTIALS

## Core Concept

> **RDD = Resilient Distributed Dataset**

<div style="background: #f5f5f5; padding: 15px; border-left: 4px solid #9c27b0;">

**Components:**
- **R**esilient: Fault-tolerant (via lineage)
- **D**istributed: Partitioned across cluster
- **D**ataset: Collection of elements

</div>

### When to Use RDD?

| Use RDD | Use DataFrame |
|---------|---------------|
| Unstructured data | Structured data |
| Low-level control | Standard analytics |
| Text processing | SQL-like operations |
| Legacy code | New projects |

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Default choice:** Use DataFrames! (they're optimized)

</div>

## Creating RDDs

```scala
// From file
val rdd = sc.textFile("hdfs://path/to/file.txt")

// From collection
val rdd = sc.parallelize(List(1, 2, 3, 4, 5))
val rdd = sc.parallelize(Array("a", "b", "c"))
```

## Essential Transformations

```scala
// map - transform each element
rdd.map(x => x * 2)
rdd.map(x => x.split(","))

// filter - keep matching elements
rdd.filter(x => x > 10)
rdd.filter(x => x.contains("error"))

// flatMap - map to 0 or more elements
rdd.flatMap(line => line.split(" "))

// reduceByKey - aggregate by key (EFFICIENT!)
pairRDD.reduceByKey(_ + _)
pairRDD.reduceByKey((a, b) => a + b)

// groupByKey - group by key (SLOW!)
pairRDD.groupByKey()

// join - inner join on keys
rdd1.join(rdd2)

// distinct - remove duplicates
rdd.distinct()

// sortByKey - sort by key
pairRDD.sortByKey()
```

## Essential Actions

```scala
// collect - bring all data to driver (DANGEROUS!)
val data = rdd.collect()

// count - count elements
val cnt = rdd.count()

// take - get first n elements
val first10 = rdd.take(10)

// first - get first element
val first = rdd.first()

// foreach - execute function on each
rdd.foreach(println)

// saveAsTextFile - save to HDFS
rdd.saveAsTextFile("hdfs://output/path")
```

## reduceByKey vs groupByKey (EXAM FAVORITE!)

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### ❌ BAD (groupByKey)

```scala
pairRDD
  .groupByKey()
  .mapValues(_.sum)
```

**Problems:**
- Shuffles ALL data
- Very slow on large datasets
- High network traffic

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### ✅ GOOD (reduceByKey)

```scala
pairRDD
  .reduceByKey(_ + _)
```

**Benefits:**
- Local aggregation first
- 10-100x faster!
- Less network traffic

</td>
</tr>
</table>

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

**Always prefer reduceByKey when possible!**

</div>

## Complete RDD Example (MEMORIZE THIS!)

```scala
// Problem: Count words in a file
val lines = sc.textFile("input.txt")

val wordCounts = lines
  .flatMap(line => line.split(" "))      // Split into words
  .map(word => (word, 1))                // Create (word, 1) pairs
  .reduceByKey(_ + _)                    // Sum counts per word
  .sortBy(_._2, ascending = false)       // Sort by count
  .collect()                             // Get results

// Print results
wordCounts.foreach(println)
```

---

# QUICK COMMAND REFERENCE

## HDFS Commands Cheat Sheet

```bash
hdfs dfs -ls /path              # List files
hdfs dfs -mkdir /dir            # Create directory
hdfs dfs -put local.txt /hdfs/  # Upload
hdfs dfs -get /hdfs/file local  # Download
hdfs dfs -cat /file             # View content
hdfs dfs -rm /file              # Delete file
hdfs dfs -rm -r /dir            # Delete directory
```

## Hive Commands Cheat Sheet

```sql
-- Create table
CREATE TABLE t (id INT, name STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

-- Load data
LOAD DATA INPATH '/path/file' INTO TABLE t;

-- Query
SELECT * FROM t WHERE id > 10;
SELECT dept, COUNT(*) FROM t GROUP BY dept;
```

## Spark DataFrame Cheat Sheet

```scala
// Read
spark.read.csv("file.csv")

// Transform
df.select("col1", "col2")
df.filter($"age" > 21)
df.groupBy("dept").count()

// Action
df.show()
df.count()
df.write.csv("output")
```

## Spark RDD Cheat Sheet

```scala
// Read
sc.textFile("file.txt")

// Transform
rdd.map(x => x * 2)
rdd.filter(x => x > 10)
rdd.reduceByKey(_ + _)

// Action
rdd.collect()
rdd.count()
rdd.saveAsTextFile("output")
```

---

# CODE PATTERNS YOU MUST KNOW

## Pattern 1: HDFS File Operations

```bash
# Complete workflow
hdfs dfs -mkdir /data                    # Create directory
hdfs dfs -put localfile.txt /data/       # Upload file
hdfs dfs -ls /data                       # List contents
hdfs dfs -cat /data/localfile.txt        # View content
hdfs dfs -get /data/localfile.txt down.txt  # Download
```

## Pattern 2: Hive Table Creation & Query

```sql
-- Create table
CREATE TABLE students (
    id INT,
    name STRING,
    age INT,
    department STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';

-- Load data
LOAD DATA INPATH '/user/data/students.txt'
INTO TABLE students;

-- Query
SELECT name, age
FROM students
WHERE age > 20
ORDER BY age DESC;
```

## Pattern 3: Spark DataFrame Processing

```scala
// Complete pipeline
val df = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("data.csv")

val result = df
  .filter($"age" > 21)                   // Filter
  .groupBy("city")                       // Group
  .agg(
    count("*").alias("count"),           // Count
    avg("salary").alias("avg_salary")    // Average
  )
  .orderBy($"avg_salary".desc)           // Sort

result.show()                            // Display
```

## Pattern 4: RDD Word Count

```scala
val lines = sc.textFile("input.txt")

val wordCounts = lines
  .flatMap(line => line.split("\\s+"))   // Split
  .map(word => (word, 1))                // Pair
  .reduceByKey(_ + _)                    // Sum
  .sortBy(_._2, ascending = false)       // Sort

wordCounts.take(10).foreach(println)     // Top 10
```

## Pattern 5: Spark SQL

```scala
// Load data
val df = spark.read.json("people.json")

// Create view
df.createOrReplaceTempView("people")

// Query with SQL
val result = spark.sql("""
  SELECT department, AVG(salary) as avg_sal
  FROM people
  WHERE age > 30
  GROUP BY department
  HAVING avg_sal > 50000
  ORDER BY avg_sal DESC
""")

result.show()
```

---

# COMMON EXAM QUESTIONS

## Q1: Architecture

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** Explain HDFS architecture. What happens if NameNode fails?

**A:**
- **NameNode:** Stores metadata (file locations, permissions)
- **DataNodes:** Store actual data blocks
- **Replication:** Each block replicated 3 times
- **If NameNode fails:** Entire cluster becomes unavailable (Single Point of Failure)
- **Solution:** Use Secondary NameNode or HDFS HA (High Availability)

</div>

## Q2: Code Output

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** What is the output of this code?

```scala
val rdd = sc.parallelize(List(1, 2, 3, 4, 5))
val result = rdd.map(x => x * 2).filter(x => x > 5).collect()
```

**A:** `Array(6, 8, 10)`

**Step by step:**
1. `map(x => x * 2)` → `[2, 4, 6, 8, 10]`
2. `filter(x => x > 5)` → `[6, 8, 10]`
3. `collect()` → brings to driver as `Array`

</div>

## Q3: Choose Best Approach

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** Which is better for summing values by key?

**A)** `rdd.groupByKey().mapValues(_.sum)`
**B)** `rdd.reduceByKey(_ + _)`

**A: B is better!**
- `reduceByKey()` does local aggregation before shuffle
- `groupByKey()` shuffles all data (slow!)
- Performance difference: **10-100x faster**

</div>

## Q4: Managed vs External Tables

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** What happens when you drop these tables?

```sql
CREATE TABLE managed (id INT, name STRING);
CREATE EXTERNAL TABLE external (id INT, name STRING)
LOCATION '/data/ext/';

DROP TABLE managed;
DROP TABLE external;
```

**A:**
- **managed:** Both metadata AND data deleted
- **external:** Only metadata deleted, data in `/data/ext/` preserved

</div>

## Q5: Fix the Code

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** This code crashes. Why and how to fix?

```scala
val bigData = sc.textFile("100GB-file.txt")
val result = bigData.collect()  // CRASH!
```

**A: Problem:** `collect()` brings ALL 100GB to driver → Out of Memory

**Fix:**
```scala
// Option 1: Use aggregation
val count = bigData.count()

// Option 2: Take sample
val sample = bigData.take(100)

// Option 3: Save to HDFS
bigData.saveAsTextFile("hdfs://output")
```

</div>

## Q6: Transformations vs Actions

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** Which are transformations and which are actions?

`map()`, `filter()`, `collect()`, `count()`, `groupBy()`, `show()`, `reduceByKey()`, `take()`

**A:**
- **Transformations (lazy):** `map`, `filter`, `groupBy`, `reduceByKey`
- **Actions (eager):** `collect`, `count`, `show`, `take`

</div>

## Q7: Spark vs MapReduce

<div style="background: #e3f2fd; padding: 15px; border-left: 4px solid #2196f3;">

**Q:** Why is Spark faster than MapReduce?

**A:**
1. **In-memory processing:** Spark keeps data in RAM between operations
2. **MapReduce writes to disk:** After each map/reduce step
3. **Lazy evaluation:** Spark optimizes entire pipeline
4. **Result:** Spark is **10-100x faster** for iterative algorithms

</div>

---

# COMPARISON TABLES

## HDFS vs Traditional File System

| Feature | HDFS | Traditional FS |
|:--------|:----:|:--------------:|
| **File size** | Optimized for huge files (GB/TB) | Small to medium |
| **Replication** | 3 copies default | No automatic |
| **Write** | Write-once, append-only | Random read/write |
| **Access** | Batch processing | Random access |
| **Failure** | Automatic recovery | Manual intervention |

## Managed vs External Tables (Hive)

| Aspect | Managed | External |
|:-------|:-------:|:--------:|
| **Data location** | Hive warehouse | User-specified |
| **DROP TABLE** | Deletes data + metadata | Deletes only metadata |
| **Use case** | Temporary/intermediate | Shared/production |
| **Control** | Hive manages | User manages |

## DataFrame vs RDD

| Feature | DataFrame | RDD |
|:--------|:---------:|:---:|
| **Optimization** | Automatic (Catalyst) | Manual |
| **Type safety** | Runtime errors | Compile-time |
| **Ease of use** | SQL-like | Functional |
| **Performance** | Faster | Slower |
| **When to use** | Structured data | Unstructured |

## Transformations vs Actions (Spark)

| Type | Execution | Returns | Examples |
|:-----|:---------:|:-------:|:---------|
| **Transformation** | Lazy | New RDD/DF | `map`, `filter`, `groupBy`, `join` |
| **Action** | Eager | Value/Unit | `count`, `collect`, `show`, `save` |

## groupByKey vs reduceByKey

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### groupByKey (SLOW)

**Shuffle:** ALL data
**Performance:** Slow
**Use when:** Need all values grouped

```scala
pairRDD
  .groupByKey()
  .mapValues(_.sum)
```

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### reduceByKey (FAST)

**Shuffle:** Reduced data
**Performance:** Fast
**Use when:** Can aggregate (sum, max)

```scala
pairRDD
  .reduceByKey(_ + _)
```

**10-100x faster!**

</td>
</tr>
</table>

---

# COMMON PITFALLS (AVOID THESE!)

## Pitfall 1: Using collect() on Large Data

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### BAD

```scala
val bigData = sc.textFile("100GB.txt")
val all = bigData.collect()
// Out of Memory!
```

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### GOOD

```scala
// Option 1: Aggregation
val count = bigData.count()

// Option 2: Sampling
val sample = bigData.take(100)

// Option 3: Save to HDFS
bigData.saveAsTextFile("output")
```

</td>
</tr>
</table>

## Pitfall 2: groupByKey instead of reduceByKey

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### SLOW

```scala
pairs
  .groupByKey()
  .mapValues(_.sum)
```

Shuffles **ALL** data

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### FAST

```scala
pairs
  .reduceByKey(_ + _)
```

Reduces **before** shuffle
**10-100x faster!**

</td>
</tr>
</table>

## Pitfall 3: Forgetting Schema in Hive

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### BAD

```sql
CREATE TABLE students
(id INT, name STRING);

LOAD DATA INPATH '/data/file.txt'
INTO TABLE students;
```

Will fail to load properly!

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### GOOD

```sql
CREATE TABLE students
(id INT, name STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';

LOAD DATA INPATH '/data/file.txt'
INTO TABLE students;
```

Properly specifies delimiters!

</td>
</tr>
</table>

## Pitfall 4: Not Persisting Reused RDDs

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### RECOMPUTES 3 TIMES

```scala
val filtered = rdd.filter(x => x > 10)
filtered.count()     // Computes
filtered.take(10)    // Computes AGAIN
filtered.collect()   // Computes AGAIN
```

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### CACHES ONCE

```scala
val filtered = rdd
  .filter(x => x > 10)
  .persist()

filtered.count()     // Computes, caches
filtered.take(10)    // Uses cache
filtered.collect()   // Uses cache
```

</td>
</tr>
</table>

## Pitfall 5: Wrong ORDER BY in Hive

<table>
<tr>
<td width="50%" style="background: #ffebee; padding: 15px;">

### SLOW

```sql
SELECT * FROM huge_table
ORDER BY age;
```

Global sort (one reducer)

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### FASTER

```sql
SELECT * FROM huge_table
SORT BY age;
```

Sort per reducer

```sql
SELECT * FROM huge_table
DISTRIBUTE BY department
SORT BY age;
```

</td>
</tr>
</table>

## Pitfall 6: Mixing Local and HDFS Paths

<div style="background: #fff3cd; padding: 10px; border-left: 4px solid #ffc107;">

**Watch the `LOCAL` keyword!**

```sql
-- This loads from LOCAL filesystem
LOAD DATA LOCAL INPATH '/home/user/file.txt'
INTO TABLE t;

-- This loads from HDFS
LOAD DATA INPATH '/user/hadoop/file.txt'
INTO TABLE t;
```

</div>

## Pitfall 7: Forgetting Lazy Evaluation

<table>
<tr>
<td width="50%" style="background: #fff3e0; padding: 15px;">

### DOES NOTHING

```scala
val df = spark.read.csv("data.csv")
df.filter($"age" > 21)      // Just builds plan
df.select("name", "age")    // Just builds plan

// No action = No execution!
```

</td>
<td width="50%" style="background: #e8f5e9; padding: 15px;">

### EXECUTES

```scala
val df = spark.read.csv("data.csv")
df.filter($"age" > 21)
  .select("name", "age")
  .show()              // NOW it executes!
```

</td>
</tr>
</table>

---

# LAST-MINUTE CHECKLIST

## Must Know Commands

- [ ] **HDFS:** `ls`, `mkdir`, `put`, `get`, `cat`, `rm`
- [ ] **Hive:** `CREATE TABLE`, `LOAD DATA`, `SELECT`, `GROUP BY`
- [ ] **Spark:** `read.csv()`, `filter()`, `groupBy()`, `show()`
- [ ] **RDD:** `map()`, `filter()`, `reduceByKey()`, `collect()`

## Must Know Concepts

- [ ] HDFS block size (`128 MB`) and replication (`3`)
- [ ] Managed vs External tables in Hive
- [ ] Transformations (lazy) vs Actions (eager)
- [ ] `reduceByKey` vs `groupByKey` (prefer reduceByKey!)
- [ ] DataFrame vs RDD (prefer DataFrame!)

## Must Know Code Patterns

- [ ] HDFS file upload/download
- [ ] Hive table creation with delimiters
- [ ] Spark DataFrame filtering and grouping
- [ ] RDD word count pattern
- [ ] Spark SQL with temp views

## Must Avoid

- [ ] `collect()` on large datasets
- [ ] `groupByKey()` when `reduceByKey()` works
- [ ] Forgetting `ROW FORMAT` in Hive
- [ ] Not caching reused RDDs/DataFrames
- [ ] Mixing up `LOCAL` vs HDFS paths

---

# PRACTICE EXERCISES

## Exercise 1: HDFS Operations

**Task:** Upload a file, create directory, move file, download

```bash
# Your turn - write the commands:
# 1. Create directory /exam/data
# 2. Upload local file test.txt to /exam/data/
# 3. List contents of /exam/data/
# 4. Download the file as downloaded.txt
```

<details>
<summary>Click for Solution</summary>

```bash
hdfs dfs -mkdir -p /exam/data
hdfs dfs -put test.txt /exam/data/
hdfs dfs -ls /exam/data/
hdfs dfs -get /exam/data/test.txt downloaded.txt
```
</details>

## Exercise 2: Hive Query

**Task:** Create table, load data, find average age by department

```sql
-- Given file: students.txt (tab-separated)
-- Format: id, name, age, department

-- Your turn:
-- 1. Create table 'students'
-- 2. Load data from /data/students.txt
-- 3. Find average age per department
```

<details>
<summary>Click for Solution</summary>

```sql
CREATE TABLE students (
    id INT,
    name STRING,
    age INT,
    department STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';

LOAD DATA INPATH '/data/students.txt'
INTO TABLE students;

SELECT department, AVG(age) as avg_age
FROM students
GROUP BY department;
```
</details>

## Exercise 3: Spark DataFrame

**Task:** Read CSV, filter age > 25, group by city, count

```scala
// Given: people.csv with columns: name, age, city, salary

// Your turn - complete the code:
val df = spark.read
  .option("header", "true")
  .csv("people.csv")

// Filter age > 25
// Group by city
// Count per city
// Sort by count descending
// Show results
```

<details>
<summary>Click for Solution</summary>

```scala
val df = spark.read
  .option("header", "true")
  .option("inferSchema", "true")
  .csv("people.csv")

val result = df
  .filter($"age" > 25)
  .groupBy("city")
  .count()
  .orderBy($"count".desc)

result.show()
```
</details>

## Exercise 4: RDD Word Count

**Task:** Count words in file, find top 10 most frequent

```scala
// Your turn - complete the word count:
val lines = sc.textFile("input.txt")

// Split into words
// Create (word, 1) pairs
// Count per word
// Sort by count descending
// Take top 10
```

<details>
<summary>Click for Solution</summary>

```scala
val lines = sc.textFile("input.txt")

val top10 = lines
  .flatMap(line => line.split("\\s+"))
  .map(word => (word, 1))
  .reduceByKey(_ + _)
  .sortBy(_._2, ascending = false)
  .take(10)

top10.foreach(println)
```
</details>

---

# FINAL TIPS FOR EXAM SUCCESS

## 1. Syntax Precision

<div style="background: #e8f5e9; padding: 10px; border-left: 4px solid #4caf50;">

Pay attention to exact syntax!

- **Hive:** `FIELDS TERMINATED BY '\t'` (not `FIELD`)
- **Spark:** `$"column"` or `col("column")` (don't mix!)
- **RDD:** `reduceByKey(_ + _)` (underscore for parameters)

</div>

## 2. Common Mistakes

- Forgetting `.show()` or `.collect()` in Spark (nothing happens!)
- Using `ORDER BY` in Hive on huge tables (very slow!)
- Calling `collect()` on big data (driver crash!)

## 3. Speed Tips

| Slower | Faster |
|:------:|:------:|
| RDD | DataFrame |
| groupByKey | reduceByKey |
| Recompute | Persist/cache |

## 4. Exam Strategy

1. **Read carefully:** Managed vs External, Local vs HDFS
2. **Draw diagrams:** NameNode/DataNode, Driver/Executor
3. **Show your work:** Partial credit for logic
4. **Check output:** Does the answer make sense?

## 5. Time Management

- **Quick wins first:** HDFS commands, simple Hive queries
- **Save hard ones:** Complex joins, optimization questions
- **Double-check:** Syntax errors, missing semicolons

---

<div align="center">

# YOU'RE READY!

## Key Takeaways

| Topic | Essential Points |
|:------|:----------------|
| **HDFS** | Distributed storage • 128MB blocks • 3x replication |
| **Hive** | SQL for Hadoop • Managed vs External tables |
| **Spark** | In-memory • Transformations (lazy) vs Actions (eager) |
| **RDD** | Low-level API • Use reduceByKey not groupByKey |

### Remember:

→ Practice the code patterns
→ Understand WHY, not just WHAT
→ Draw diagrams for architecture questions
→ Check for common pitfalls

---

### Good luck on your exam!

*You've got this!*

</div>
