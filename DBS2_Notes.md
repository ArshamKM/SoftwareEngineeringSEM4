# DBS2 - Advanced Database Concepts

---

# NoSQL Databases

These are Databases which are able to handle a large amount of unstructured or semi structured data with a high speed and flexibility. However there are major differences between the NoSQL Databases and other databases:

- Other DBs store the data in fixed tables with rows and columns, while NoSQL DBs use a flexible format which allow them to store in formats such as:
  - JSON, Key Value pairs, graphs or wide columns
  - Useful for modern applications intended to scale across many servers

These DBs are **non SQL, non relational, not only SQL**, where most of these NoSQL Systems are distributed databases or distributed storage systems, following factors such as:

- Focus on semi structured data storage, high performance and availability, data replication + scalability
- Opposed to immediate data consistency, powerful query languages and structured data storage

## NoSQL DBs — Distributed

NoSQL DBs are distributed, which means they contain factors such as:

- **Horizontal scalability** — however issues which may be imposed are replication and eventual consistency

### Replication Models
- Master Slave
- Master Master

### Sharding
- Not practical for storing everything in a single node

### High Performance
- Data is accessed through hashing or range partitioning on object keys

## NoSQL DBs — Performance & Flexibility

A schema for NoSQL DBs is **not required** since it is semi structured and self describing (JSON, XML).

However it is less powerful since it only contains:
- Read and Write through **CRUD Operations**
- Subset of SQL query capabilities — **no JOIN**

**Versioning:**
- Able to store multiple versions of data items
- Timestamp stored for each version

## Issues with Scaling Up

- **Vertical Scaling (Scaling Up)** — making a single machine more powerful
  - Limitation: when datasets are too big or too many requests for one machine
- **Horizontal Scaling (Scaling Out)** — adding smaller/cheaper servers, better long-term choice
  - Approaches: Master Slave, Sharding/Partitioning

## Scaling Out RDBMS — Problems

### Master Slave
- All WRITEs go to the master, all READs from replicated slaves
- **Problem:** Critical READ may be incorrect since WRITE might not yet have propagated down
- Large datasets create duplication burden on master

### Sharding (Partitioning)
- Scales well for both READs and WRITEs
- **Problems:**
  - No longer has relationships across partitions
  - Loss of referential integrity across shards
  - Applications must be partition-aware (not transparent)

## Different Methodologies for Scaling DBMS
- Multi master replication — higher latency
- INSERT only, no UPDATES/DELETES
- No JOINs — reduces query time but requires denormalizing data
- In Memory DBs

---

# CAP Theorem

Three properties of distributed databases — **you can only have 2 of the 3**:

| Property | Meaning |
|---|---|
| **Consistency (C)** | All copies have the same value |
| **Availability (A)** | Every read/write succeeds |
| **Partition Tolerance (P)** | System holds even when network failures prevent machine communication |

**Valid combinations:**
- **CP** = Consistency + Partition Tolerance
- **CA** = Consistency + Availability
- **AP** = Availability + Partition Tolerance

### CAP Misconceptions
> "In large distributed-scale systems, network partitions are a given — therefore consistency and availability cannot be achieved at the same time."

- Perfect availability AND consistency WITH partitions is very rare
- AP systems relax consistency in favour of availability — but are NOT fully inconsistent
- CP systems sacrifice some availability while maintaining consistency + partition tolerance

---

# Consistency Types

## Strong Consistency
After every update completes, any subsequent access will return the **same updated value**.

## Weak Consistency
It is **not guaranteed** that subsequent accesses return the updated value.

## Eventual Consistency
A specific form of weak consistency — if no new updates are made, **eventually** all accesses will return the last updated value.
- Offers reduced load and improved availability

## Variations of Eventual Consistency

| Type | Meaning |
|---|---|
| **Causal Consistency** | If event A caused B, the system will see A before B |
| **Read Your Write** | User always sees their own latest changes |
| **Session Consistency** | Read Your Write only within a session |
| **Monotonic Read** | Once you read a value, you will never see an older version |
| **Monotonic Write** | Sequential writes are executed in order |

## Consistency vs Latency
- High availability → high replication → higher consistency issues
- **Low latency and high consistency are conflicting goals**

---

# PACELC Model

Extends the CAP theorem to also address **normal (non-partition) conditions**.

**PACELC** stands for:
- **(P)** artition tolerance
- **(A)** vailability
- **(C)** onsistency
- **(E)** lse
- **(L)** atency
- **(C)** onsistency

**If Partition (P) happens** → system chooses between:
- Availability (A)
- Consistency (C)

**Else (E) — normal operation** → system chooses between:
- Low Latency (L)
- Consistency (C)

---

# NoSQL DB Types Overview

## Column Store / Column Oriented
- Data structured in columns: name, value, timestamp
- Sparse multidimensional distributed persistent sorted map

**Multidimensional keys composed of:**
- Table name
- Row key
- Column (column family + column qualifier)
- Timestamp

## Document Store / Document Oriented DBMS
- Data structured in documents: JSON or XML
- Collection of similar documents — **no schema**
- Documents are self-describing
- Example: **MongoDB**
  - Documents stored in binary BSON format
  - Each document collection has a unique `_id` field (auto-indexed)

## Key Value Store DBMS
- Data structured in associative arrays (hash tables / dictionaries)
- **Key** = unique identifier for the data item
- **Value** = structured (tuples), unstructured (bytes), or semi-structured (JSON)
- Example: **Redis** — values can be strings, lists, sets, or JSON documents
- Supports **In-Memory DB** with persistence via snapshotting

## Graph DBMS
- Data structured in **nodes** (vertices) and **edges**
- Example: **Neo4J**

### Graph DB (Neo4j) vs Relational DB (SQL)

| Neo4J | SQL |
|---|---|
| Nodes | Tables / Rows |
| Node Labels (entity types) | Table Names |
| Edges (relationships) | Foreign Keys / JOINs |
| Graph Traversal | SQL Queries |

---

# Physical Storage & Indexing

> An index is a separate, smaller, sorted lookup structure that points to where actual data lives — so the DB doesn't scan the whole table every time.

## Index Types

### Primary Index
- Data file is **physically sorted by primary key**
- One index entry **per block** (not per record) → called a **sparse index**
  - **Sparse index** = few entries (one per block)
  - **Dense index** = one entry per record

**Insertion problem:** new record must go in sorted position, potentially shifting everything.

**Solutions:**
- **Overflow file** — new records go to a separate unsorted file; system merges periodically
- **Linked overflow chains** — each record in main file points to a chain of overflow records

### Clustering Index
- Same idea as primary: data is physically ordered by this field
- But the field is **NOT unique** — many rows share the same value
- Example: EMPLOYEE table sorted by `department_number`
- One entry per **distinct value**, pointing to first block where that value appears

### Secondary Index
- Field does **NOT determine physical order** of the file
- No shortcut → requires a **dense index** (one entry per record)
- More storage, slower than primary — but enables queries on non-ordering fields

### Quick Reference

| Type | Ordered By | Entries | Density |
|---|---|---|---|
| Primary | Primary key | One per block | Sparse |
| Clustering | Non-key field | One per distinct value | Sparse |
| Secondary | Non-ordering field | One per record | Dense |

## Multi-Level Indexes
- Build an index **of an index** for faster binary search
- Level 1 = original index of data
- Level 2 = primary index of Level 1
- Level 3 = primary index of Level 2
- Forms a tree hierarchy
- Static version has same insertion/deletion headaches → solution: **B-Trees / B+ Trees**

## B-Trees
Self-balancing search tree optimized for large datasets stored on disk.

**Key properties:**
- Always balanced — every leaf at the same depth
- Each node holds multiple keys and multiple children
- CRUD operations run in **logarithmic time O(log n)**
- Insertion/deletion does not require rebuilding the whole tree
- Each node ≈ one disk block read → very few disk reads even with millions of records

## B+ Trees
Key tweak from a plain B-Tree: **only leaf nodes store actual data**. Internal nodes hold signpost keys for navigation only. All leaf nodes are **linked together as a chain**.

```
        [ 30 | 60 ]
       /     |     \
[10, 20] [30, 40, 50] [60, 70, 80, 90]
           ↕               ↕              ↕
        linked  →       linked   →     linked
```

- **Internal nodes (30, 60)** = signposts only, no real data
- **Leaf nodes** = store actual data
- **Linked leaves** = `[10,20] → [30,40,50] → [60,70,80,90]`
- Makes **range queries** very fast — find the start leaf, then walk the chain

---

# Transactions & Concurrency

A transaction is a **logical unit of work** — a sequence of operations that must all succeed together or not at all.

## ACID Properties

| Property | Meaning |
|---|---|
| **Atomicity** | All or nothing — if any step fails, entire TXN rolls back |
| **Consistency** | DB moves from one valid state to another, no broken rules |
| **Isolation** | Concurrent TXNs don't interfere — each runs in its own "bubble" |
| **Durability** | Once committed, changes survive regardless of crashes |

## Transaction Lifecycle

```
Start → Active (R/W) → Partially Committed (logging/checks) → Committed ✓
                    ↘ Failed → Rollback → Terminated ✗
```

> **Important:** Even "Partially Committed" is NOT safe — something can still fail there. Only "Committed" is truly final.

## Concurrency Anomalies

| Anomaly | What happens |
|---|---|
| **Dirty Read** | Reading data another TXN wrote but hasn't committed yet |
| **Lost Update** | Two TXNs update same item — one write silently overwrites the other |
| **Incorrect Summary** | Aggregate (SUM/AVG) computed while other TXNs are mid-update |
| **Non-Repeatable Read** | Same row read twice gives different values — another TXN changed it |
| **Phantom Read** | Same query run twice returns different number of rows |

## Correctness of Schedules

- **Serial Schedule** — TXNs run one fully after another. Always correct, but slow (no concurrency).
- **Serializable Schedule** — operations interleaved for performance, but result is equivalent to some serial order. Safe AND efficient.
- **Conflict Equivalence** — two schedules are conflict-equivalent if you can transform one into the other by swapping only non-conflicting operations. Operations **conflict** if they touch the same data item AND at least one is a write.

## Concurrency Control Protocols

### 2PL (Two Phase Locking)

Two phases every transaction must follow:
- **Growing Phase** — acquire locks only, NO releasing
- **Shrinking Phase** — release locks only, NO acquiring new ones

Once a lock is released, no new locks can be acquired.

**Lock types:**
- **Binary** — locked or unlocked
- **Read/Write (Shared/Exclusive)** — shared locks: multiple readers; exclusive locks: one writer only

**Variants:**

| Variant | Rule | Deadlocks? |
|---|---|---|
| **Conservative 2PL** | Grab ALL needed locks before starting | No deadlocks, but impractical |
| **Rigorous 2PL** | Hold ALL locks until commit/abort | Guarantees serializability, but can cause deadlocks |

### Timestamp Ordering
- Every TXN gets a timestamp at start
- If a younger TXN has already read/written an item, the older TXN trying to do the same gets **aborted and restarted** with a fresh timestamp
- Avoids dirty reads and cascading rollbacks

### Multiversion Protocols
- Keep multiple timestamped versions of each data item
- TXN reads the version that was current when it started
- More storage needed, but far fewer conflicts

### Optimistic Concurrency Control
- TXNs run freely with no locking
- Only at commit time: check if conflict happened → if yes, abort and retry
- Great when conflicts are rare; expensive when conflicts are common

### Snapshot Isolation
- Each TXN sees a consistent snapshot of the DB as of when it started
- Changes apply only if no conflict with that snapshot
- Used by **PostgreSQL**
- Can lead to **write skew anomalies** — two TXNs read overlapping data, write to different items; individually fine but combined breaks a constraint

## Granularity of Locking

Smallest → Largest: **Field → Tuple (Row) → Block (Page) → Table → Whole DB**

- **Fine granularity (Row level)** = more concurrency, more overhead (more locks to track)
- **Coarse granularity (Table level)** = less overhead, blocks more TXNs simultaneously

> Exam answer: "Which locking level allows the most concurrency?" → **Row level**

## Performance vs Isolation
- **High isolation** = safer, but slower
- **Low isolation** = faster, but anomaly-prone

---

# Recovery Techniques & Access Control

## Why Recovery is Needed

| Failure Type | Cause |
|---|---|
| User input error | Training/validation issue |
| SQL statement error | DBMS catches and aborts TXN |
| System failure | Crash mid-TXN — anything in progress must be recovered |
| Media failure | Storage fails — DB restored from backups/logs |

## The Two Key Recovery Questions

After a crash, for every TXN ask:

1. **Was it unfinished when the crash happened?** → partial state is lost/unreliable → must **UNDO (rollback)**
2. **Was it committed but updates hadn't fully reached disk yet?** → commit is logged but data not written → must **REDO**

## ACID Responsibilities

| Property | Responsible Subsystem |
|---|---|
| **Atomicity** | Transaction Manager + Recovery Manager |
| **Consistency** | Programmer + Integrity Control Subsystem |
| **Isolation** | Concurrency Control Subsystem |
| **Durability** | Transaction Manager + Recovery Manager |

> Atomicity and Durability share the same owners — recovery is fundamentally about making sure "all or nothing" held and "committed = permanent."

## Deferred Update (NO-UNDO / REDO)

**Rule:** Do NOT touch the actual DB while TXN is running. Write everything to the **recovery log** first.

- Every write → logged (DB not updated yet)
- On **Commit** → logged values now written to DB, indexes updated
- On **Rollback** → discard log entries, nothing in DB to undo

### Commit Point Sequence
1. Commit is logged
2. Updates written to DB and indexes updated (marked with `*`)
3. Locks released
4. Cursors/views/positioning discarded
5. Commit logged as finished, TXN terminated

> **Why can failure happen between steps 1-5?** Log records go to a log *buffer* in memory first. If the system crashes before flushing the buffer to disk, you get an **implicit abort** — even if step 1 said "committed." This violates Atomicity and Durability.

### Recovery for Single TXN (Deferred)
- Crash **before** commit logged → discard log entries, nothing to undo (DB was never touched)
- Crash **after** commit logged but before steps 2-4 → **REDO**: write updates to DB, complete steps 3-4, mark commit as finished

## Immediate Update (UNDO / REDO)

**Rule:** Write to the DB **as you go** during the TXN — not just at commit.

Log format for every write:
```
X, transaction-id, old-value, new-value
```

- On **Commit** → logged in recovery log
- On **Rollback** → use `old-value` from log to undo changes already made to DB

Needs **both UNDO and REDO** because:
- Some changes may already be in the DB before crash → need UNDO
- Some committed changes may not have reached disk yet → need REDO

## Shadow Paging

No old/new value logging. Instead:
- DB organized into fixed-size blocks
- At TXN start, two directories set up (both pointing to same blocks):
  - **Current directory** — used by running TXN
  - **Shadow directory** — backup, untouched
- When modifying block Y: make a **copy**, modify the copy, update current directory to point to copy. Shadow still points to original.

**Outcomes:**
- **TXN fails** → discard current directory → shadow directory is still valid, instant rollback
- **TXN commits** → discard old blocks and shadow directory → current directory becomes the new official state

## Worked Example — System Failure with Deferred Update

**Scenario timeline:**

```
Timeline ──────────────────────────────────────────────►

T1  |====commit|
T3             |==========commit|
T4             |============================X (never commits)
               ↑
           CHECKPOINT t1
                         T2  |====commit|
                         T5       |===========X (never commits)
                                              ↑
                                           CRASH t2
```

**Recovery process:**

Start with everyone active at checkpoint t1 → initial UNDO list = `{T3, T4}`

Scan log forward from t1 to t2:
- **T3** → commit found → move to REDO (committed after checkpoint, ensure durable)
- **T4** → no commit found → stays UNDO (rollback)
- **T2** → started after checkpoint, commit found → add to REDO
- **T5** → started after checkpoint, no commit → add to UNDO

**Final lists:**
```
UNDO = {T4, T5}   ← never committed, roll back
REDO = {T2, T3}   ← committed after checkpoint, ensure written to disk
```

> **Key rule:** Committed before crash → REDO. Not committed before crash → UNDO. Checkpoint = where to start scanning, not where everything is already safe.

---

# Advanced SQL

## GROUP BY & HAVING

`GROUP BY` collapses multiple rows sharing the same value(s) into one summary row, enabling aggregate functions per group.

**Aggregate functions:** `SUM`, `AVG`, `COUNT`, `MAX`, `MIN`

```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department;
```

**Golden rule:** Every column in SELECT must either be:
- Listed in `GROUP BY`, OR
- Wrapped in an aggregate function

```sql
-- WRONG: employee_name is neither grouped nor aggregated
SELECT employee_name, department, AVG(salary)
FROM employees
GROUP BY department;
```

### HAVING vs WHERE

| Clause | When it runs | What it filters |
|---|---|---|
| `WHERE` | Before grouping | Individual rows |
| `HAVING` | After aggregation | Groups |

```sql
SELECT project_id, COUNT(*) AS people
FROM assignments
GROUP BY project_id
HAVING COUNT(*) > 2;
-- Only keep projects with more than 2 people
-- Cannot use WHERE here — COUNT doesn't exist yet at that stage
```

---

## OLAP (Online Analytical Processing)

Analyzing data from multiple angles/dimensions — typically for business reporting. Think of data as a cube you can slice by region, product, time, etc.

### Roll Up
Aggregates data **up** a hierarchy — from detailed to summarized, producing subtotals and a grand total.

```sql
SELECT type, store, SUM(number) AS total
FROM Pets
GROUP BY ROLLUP(type, store);
```

`ROLLUP(type, store)` produces all levels:
- `GROUP BY (type, store)` — most detailed
- `GROUP BY (type)` — subtotal per type across all stores
- Grand total — one row, everything summed

### Drill Down
The **opposite** of Roll Up — breaks aggregated numbers back down into detailed components. "Zooming in."

---

## Window Functions

Normal aggregates with `GROUP BY` **collapse** rows (100 rows → 5 group rows).

Window functions compute across related rows but **keep every original row** — still 100 rows out, each annotated with a calculated value (rank, running total, etc.).

```sql
SELECT brand, model, segment,
RANK() OVER (PARTITION BY segment ORDER BY acceleration_0_100 ASC) AS rank_in_acceleration
FROM cars;
```

**Breaking down `OVER(...)`:**

| Part | What it does |
|---|---|
| `RANK()` | Assigns position numbers based on ordering |
| `PARTITION BY segment` | Splits table into independent groups by segment (like a "soft" GROUP BY — rows NOT collapsed) |
| `ORDER BY acceleration_0_100 ASC` | Within each partition, determines rank order |

> Result: "Within each car segment, rank cars from fastest to slowest — but still show every car as its own row."

> `OVER(...)` is processed **after** `FROM/WHERE/GROUP BY` — it works on the result set as-is, adding computed columns without merging rows.

---

## CTEs (Common Table Expressions)

A **named, temporary result set** that exists only for the duration of one query — like a mini virtual table you define before using it.

```sql
WITH expression_name (column_name1, column_name2) AS (
    SELECT ...
)
SELECT * FROM expression_name;
```

- Defined with `WITH`, referenced like any table in the query that follows
- Exists only for that single statement — not saved anywhere
- Breaks complex/nested queries into readable, reusable, sequential steps

---

## Recursive CTEs

A CTE that **references itself** — essential for hierarchical data like org charts, family trees, category trees.

**Structure (two parts joined by UNION):**
1. **Base case (non-recursive term)** — the anchor/starting point
2. **Recursive term** — references the CTE's own output, repeats until condition is false

```sql
WITH RECURSIVE t(n) AS (
    SELECT 1                        -- base case: start at n=1
    UNION
    SELECT n+1 FROM t WHERE n < 5   -- recursive: add 1, stop when n >= 5
)
SELECT * FROM t;
-- Result: 1, 2, 3, 4, 5
```

**General template:**
```sql
WITH RECURSIVE cte_name(column) AS (
    SELECT ...          -- base case (anchor)
    UNION
    SELECT ...          -- recursive term (references cte_name)
    FROM cte_name
    WHERE condition     -- stopping condition
)
SELECT * FROM cte_name;
```

---

# Exercises — Relational Algebra

## Symbol Reference

| Symbol | SQL Equivalent |
|---|---|
| **π (pi)** | SELECT (projection) |
| **σ (sigma)** | WHERE (selection/filter) |
| **⨝ (bowtie)** | JOIN |

## Example

```sql
SELECT e.First_Name
FROM employee e
WHERE e.First_Name = 'Arsham';
```

Relational algebra: `π First_Name (σ First_Name='Arsham' (employee))`

## Optimisation Examples

```
(i)  πa (σc=100 (σd=50 (R ⨝b=b S)))
     → Join R and S, then filter d=50, then filter c=100, then select a

(ii) πa (σd=50 (R ⨝b=b (σc=100 (S))))
     → Filter S where c=100 first, then join with R, then filter d=50, then select a

(iii) πa ((σd=50 (R)) ⨝b=b (σc=100 (S)))
      → Filter R where d=50, filter S where c=100, then join, then select a
```

> Version (iii) is the most optimised — filtering before joining reduces the size of both tables before the expensive JOIN operation.

## Index Type Quick Reference (Exam Reminder)

| Index | Ordering | Density |
|---|---|---|
| **Primary** | Organized by primary key | Sparse |
| **Clustering** | Non-key field | Sparse |
| **Secondary** | No organized key | Dense |

## Locking Granularity — Exam Answer
> "Which level of locking allows the most concurrency?"
> **→ Row level** — most specific, disturbs the fewest other transactions

## RANK() Window Function Example

```sql
SELECT brand, model, segment,
RANK() OVER (PARTITION BY segment ORDER BY acceleration_0_100 ASC) AS rank_in_acceleration
FROM cars;
```

- `RANK()` → assigns position number
- `OVER()` → tells RANK how to work
- `PARTITION BY` → separates data into independent groups (restarts rank per group)
- `ORDER BY DESC` → orders from most to least within each group
