# DBS2 — Mock Exam (Practice Paper)

**Time:** 90 minutes · **Total:** 100 points · **Pass:** ≥ 55 (mirrors the 5.5/10 rule)
**Format:** Open · MCQ (1-of-4) · Multi-select (x-of-7) · Drag-and-drop
**Weighting honoured:** LG1 ~40 · LG2 ~10 · LG3 ~10 · LG4 ~40. NoSQL/CAP/scaling ≈ half the paper.

> Attempt everything before scrolling to the **ANSWER KEY** at the bottom. For open questions, write the *justification*, not just the verdict — that's where the marks are.

---

# PART A — OPEN QUESTIONS (48 pts)

### A1. Serializability *(8 pts)* — LG1
Given the schedule below, state whether it is **conflict-serializable**. Justify using a precedence graph. If it is serializable, give an equivalent serial order.

| Step | T1 | T2 |
|---|---|---|
| 1 | R(A) | |
| 2 | W(A) | |
| 3 | | R(A) |
| 4 | | W(A) |
| 5 | | R(B) |
| 6 | | W(B) |
| 7 | R(B) | |
| 8 | W(B) | |

### A2. Recovery *(6 pts)* — LG1
Explain the difference between **deferred update** and **immediate update**. For each, state whether UNDO, REDO, or both are needed after a crash, and **why**.

### A3. Indexing *(4 pts)* — LG1
Explain the difference between a **primary index** and a **secondary index**. State which can be sparse and which must be dense, and why.

### A4. Query optimization *(4 pts)* — LG1
Three equivalent relational-algebra expressions compute the same result:
```
(i)   πa (σc=100 (σd=50 (R ⨝b=b S)))
(ii)  πa (σd=50 (R ⨝b=b (σc=100 (S))))
(iii) πa ((σd=50 (R)) ⨝b=b (σc=100 (S)))
```
Which is the most efficient, and why?

### A5. CAP analysis (scenario) *(8 pts)* — LG4
A banking system is configured so that during a network partition it **refuses** transactions rather than risk showing an incorrect balance. Is this a **CP** or **AP** system? Justify your answer, and state what the system is sacrificing.

### A6. NoSQL selection (scenario) *(8 pts)* — LG4
A social platform must answer queries like "friends of friends" and "shortest connection path between two users," where relationships are highly interconnected. **Which type of NoSQL database** would you recommend, and why is it better here than a relational database?

### A7. Scaling (scenario) *(6 pts)* — LG4
Users report that immediately after updating their profile, a page refresh **sometimes shows the old data**. The system uses **master-slave replication**. Explain why this happens, and give **one** mitigation.

### A8. Identify the database *(4 pts)* — LG3
(a) Which type of database stores data as **nodes connected by edges/relationships**? Name an example product.
(b) In what **document format** does MongoDB store data, and what is special about its **`_id`** field?

---

# PART B — MULTIPLE CHOICE (1 of 4) — 18 pts (2 each)

**B1.** During a network partition, a **CP** system sacrifices: — LG4
A) Consistency  B) Availability  C) Partition tolerance  D) Durability

**B2.** Which index **must be dense**? — LG1
A) Primary  B) Clustering  C) Secondary  D) Multi-level

**B3.** "The same query run twice returns a different **number of rows**" is a: — LG1
A) Non-repeatable read  B) Phantom read  C) Dirty read  D) Lost update

**B4.** Timestamp ordering: — LG1
A) Can deadlock  B) Avoids deadlock but can abort/restart transactions  C) Never rolls back  D) Requires locks

**B5.** After a crash, a transaction that **committed but whose changes hadn't reached disk** needs: — LG1
A) UNDO  B) REDO  C) Nothing  D) Shadow paging

**B6.** `WHERE` vs `HAVING`: — LG2
A) Both filter groups
B) WHERE filters rows before grouping; HAVING filters groups after aggregation
C) HAVING runs first
D) WHERE can use aggregate functions

**B7.** Compared to master-slave replication, **sharding** primarily adds the ability to scale: — LG4
A) Reads only  B) Reads and writes  C) Backups  D) Indexes

**B8.** In relational algebra, **σ (selection)** corresponds to which SQL clause? — LG2
A) SELECT clause  B) WHERE  C) JOIN  D) GROUP BY

**B9.** Which NoSQL type best fits **highly connected** data (networks, recommendations)? — LG3
A) Key-value  B) Document  C) Column-family  D) Graph

---

# PART C — MULTIPLE SELECT (x of 7) — 18 pts (6 each)

**C1.** Select ALL characteristics of **NoSQL databases**. — LG3
1. Schema-flexible / self-describing data
2. Built for horizontal scale-out
3. Always provide immediate strong consistency
4. Often trade away rich SQL querying / joins
5. Designed for high availability
6. Require a fixed relational schema
7. Frequently distributed across many nodes

**C2.** Select ALL TRUE statements about **two-phase locking (2PL)**. — LG1
1. Growing phase acquires locks only
2. Shrinking phase releases locks only
3. It guarantees conflict serializability
4. Conservative 2PL avoids deadlock
5. It can never deadlock
6. Strict 2PL holds write locks until commit
7. Only rigorous 2PL is serializable

**C3.** Select ALL TRUE statements about **CAP / PACELC**. — LG4
1. Partition tolerance is effectively mandatory in distributed systems
2. During a partition you choose between Consistency and Availability
3. CA is the ideal choice for distributed databases
4. AP systems are completely inconsistent
5. PACELC's "else" case trades latency vs consistency
6. CP sacrifices availability during a partition
7. You permanently pick exactly 2 of 3

---

# PART D — DRAG AND DROP — 16 pts (4 each)

**D1.** Match each **index** to its properties. — LG1
Indexes: Primary · Clustering · Secondary
Properties: [ordered by key, sparse] · [ordered by non-key, sparse] · [non-ordering field, dense]

**D2.** Build the **UNDO** and **REDO** lists from this timeline. — LG1
- T1 commits **before** the checkpoint
- T3 starts before checkpoint, commits **after** it
- T4 starts before checkpoint, **never commits**
- *CHECKPOINT*
- T2 starts after checkpoint, **commits** before crash
- T5 starts after checkpoint, **never commits**
- *CRASH*
Drag T1–T5 into: **UNDO box** / **REDO box** / **No action**.

**D3.** Match **relational algebra symbol → SQL**. — LG2
Symbols: π · σ · ⨝ · γ
SQL: SELECT (columns) · WHERE · JOIN · GROUP BY/HAVING

**D4.** Match **NoSQL type → use case**. — LG4
Types: Key-value · Document · Graph · Column-family
Use cases: [session cache / fast key lookup] · [flexible evolving records] · [connected data / traversals] · [huge wide sparse tables / time-series]

---
---

# ⛔ STOP — ANSWER KEY BELOW ⛔
*(Mark yourself. For open questions, award partial credit for correct justification.)*

---

## PART A

**A1 (8) — NOT conflict-serializable.**
Conflicts: on **A**, T1.W(A)[2] precedes T2.R(A)[3]/W(A)[4] → edge **T1→T2**. On **B**, T2.W(B)[6] precedes T1.R(B)[7]/W(B)[8] → edge **T2→T1**. The precedence graph has both T1→T2 and T2→T1 → **a cycle** → not conflict-serializable (no equivalent serial order exists).

**A2 (6) — Deferred vs immediate.**
*Deferred update:* nothing is written to the DB until commit (log first). After a crash there is **NO UNDO** (uncommitted transactions changed nothing on disk); only **REDO** is needed for committed transactions whose changes hadn't been flushed.
*Immediate update:* writes can reach the DB during the transaction; the log stores old + new values. Needs **both UNDO** (to roll back uncommitted changes already on disk) **and REDO** (for committed changes not yet flushed).

**A3 (4) — Primary vs secondary.**
A **primary index** is on the **ordering key** field by which the file is physically sorted, so it can be **sparse** (one entry per block). A **secondary index** is on a **non-ordering** field; the file isn't sorted by it, so it must be **dense** (one entry per record).

**A4 (4) — (iii) is most efficient.**
It applies the selections (σ d=50 on R, σ c=100 on S) **before** the join, shrinking both inputs so the expensive ⨝ operates on far fewer rows. (i) joins first then filters — worst; (ii) filters only S before the join — better than (i) but worse than (iii).

**A5 (8) — CP.**
By refusing transactions during a partition to avoid an incorrect balance, the system keeps **Consistency** and tolerates the **Partition**, but gives up **Availability** (it won't answer). That is the **CP** tradeoff. Correctness is prioritised over being always-on — appropriate for banking.

**A6 (8) — Graph database (e.g. Neo4j).**
Relationships are first-class (nodes + edges), so "friends of friends" and shortest-path queries are cheap **traversals**. In a relational DB the same queries need many self-joins across a junction table, which becomes expensive and slow as depth/connectivity grows.

**A7 (6) — Stale read from a slave.**
Writes go only to the **master**; reads come from **slaves**. The profile update hit the master but hadn't **replicated** to the slave yet, so a read from the slave returns the old value. Mitigation (any one): route the user's own critical reads to the master (**read-your-writes**); read from master after a write; or wait for/confirm replication before serving the read.

**A8 (4)**
(a) A **graph database** — example **Neo4j**.
(b) MongoDB stores documents in **BSON** (binary JSON); the **`_id`** field is a unique, **automatically indexed** identifier for each document.

## PART B
B1 **B** (Availability) · B2 **C** (Secondary) · B3 **B** (Phantom) · B4 **B** · B5 **B** (REDO) · B6 **B** · B7 **B** (Reads and writes) · B8 **B** (WHERE) · B9 **D** (Graph)

## PART C
**C1:** 1, 2, 4, 5, 7 (3 and 6 are false).
**C2:** 1, 2, 3, 4, 6 (5 false — strict/rigorous can deadlock; 7 false — all 2PL is conflict-serializable).
**C3:** 1, 2, 5, 6 (3, 4, 7 are the classic misconceptions).

## PART D
**D1:** Primary → ordered by key, sparse · Clustering → ordered by non-key, sparse · Secondary → non-ordering field, dense.
**D2:** **UNDO = {T4, T5}** (active, never committed) · **REDO = {T2, T3}** (committed after the checkpoint, ensure flushed) · **No action = {T1}** (committed before the checkpoint → already durable).
**D3:** π → SELECT (columns) · σ → WHERE · ⨝ → JOIN · γ → GROUP BY/HAVING.
**D4:** Key-value → session cache / fast key lookup · Document → flexible evolving records · Graph → connected data / traversals · Column-family → huge wide sparse tables / time-series.

---

### Scoring guide
- **85–100:** exam-ready across the board.
- **70–84:** solid; revisit any topic where you lost a full question.
- **55–69:** passing but fragile — drill the two confirmed types (A1 serializability, A8 identify-DB) and the CAP cluster.
- **< 55:** focus on LG1 mechanics (index/recovery/serializability) + CAP correctness first; those are the highest-yield recoveries.

