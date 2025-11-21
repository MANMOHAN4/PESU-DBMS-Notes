# Concurrency Control and Isolation

## What is Concurrency Control?

Concurrency control is a procedure for managing simultaneous transactions, ensuring:
- Atomicity of each transaction
- Isolation of concurrent transactions
- Consistency of the database
- Serializability of execution

Restricts concurrent accesses to ensure no erroneous database updates occur.

### Purpose
- Preserve isolation property by controlling interactions among concurrent transactions
- Achieved through concurrency-control schemes
- Most frequently used: two-phase locking and snapshot isolation

## Why Concurrency Control Is Needed?

### Prevention of Data Conflicts
When multiple transactions execute concurrently without control:
- Can access and modify same data items
- Leads to conflicts: incorrect or inconsistent data updates

### Ensuring Data Integrity
- Maintains accuracy and integrity of database
- Prevents race conditions where transaction outcomes depend on timing
- Ensures consistent final state

## Concurrency Control Problems

### Lost Update Problem
- Occurs when two transactions interleave
- One transaction's update is overwritten by another
- Results in incorrect data

**Example**: T1 subtracts N from X, T2 adds M to X; T1's update lost if T2 writes after T1 without proper control

### Temporary Update (Dirty Read) Problem
- Transaction T1 reads uncommitted update from T2
- T2 later fails and rolls back
- T1 has read data that doesn't exist and is incorrect

**Example**: T1 reads temporary value X written by T2, then T2 aborts; T1 now has invalid data

### Incorrect Summary Problem
- Transaction calculates aggregate while others update related data
- Inaccurate result due to partial updates

**Example**: T3 sums A, X, Y while T1 transfers N from X to Y; T3 reads X after subtract and Y before add, off by N

### Unrepeatable Read Problem
- Transaction T reads same item twice
- Another transaction T2 modifies item between reads
- T receives different values for same item on two reads

## Simple Transactional Model

### Read Set
- Set of all items a transaction reads

### Write Set
- Set of all items a transaction writes

## Isolation Levels

### Definition
Isolation levels control how transactions are isolated from one another, ensuring data consistency and preventing conflicts.

### SQL Isolation Level Specification
```sql
SET SESSION TRANSACTION ISOLATION LEVEL <isolation>;
```

Where <isolation> can be:
- READ UNCOMMITTED
- READ COMMITTED
- REPEATABLE READ
- SERIALIZABLE

### Level Details

**READ UNCOMMITTED (Lowest Isolation)**
- Allows reading uncommitted data
- Allows dirty reads, non-repeatable reads, and phantom reads
- Highest concurrency, lowest consistency
- Rarely acceptable for critical applications

**READ COMMITTED**
- Only reads data that has been committed
- Prevents dirty reads
- Allows non-repeatable reads and phantom reads
- Common default in many systems

**REPEATABLE READ**
- Data read once will not change during transaction
- Prevents dirty reads and non-repeatable reads
- Allows phantom reads
- Stronger than READ COMMITTED

**SERIALIZABLE (Highest Isolation)**
- Guarantees complete isolation from other transactions
- Prevents dirty reads, non-repeatable reads, and phantom reads
- Strongest consistency guarantee
- Lower concurrency due to locking

### Default Isolation Level
- Usually SERIALIZABLE, though some systems use READ COMMITTED
- Can be configured per session or transaction

## Possible Violations by Isolation Level

| Violation Type | READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE |
|----------------|------------------|-----------------|-----------------|--------------|
| Dirty Read | Yes | No | No | No |
| Non-repeatable Read | Yes | Yes | No | No |
| Phantom Read | Yes | Yes | Yes | No |

- "Yes" = violation is possible at that level
- "No" = violation is prevented at that level
- READ UNCOMMITTED most forgiving, SERIALIZABLE most restrictive

## Anomalies in Detail

### Dirty Read
- T1 reads uncommitted update from T2
- If T2 fails and aborts, T1 has read invalid data
- Prevented by READ COMMITTED and stronger levels

### Non-repeatable Read
- T1 reads value from table
- T2 updates that value and commits
- T1 reads same value again and gets different result
- Prevented by REPEATABLE READ and SERIALIZABLE

### Phantom Read
- T1 reads set of rows matching WHERE condition
- T2 inserts new row matching same condition
- T1 reads same condition again and sees new row (phantom)
- Critical for range queries and aggregates
- Prevented by SERIALIZABLE only

## SQL Transaction Control

### Basic Pattern
```sql
-- Set isolation level
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- Start transaction
START TRANSACTION;

-- Execute operations
SELECT ...;
INSERT ...;
UPDATE ...;
DELETE ...;

-- End transaction
COMMIT;    -- Make permanent
-- or
ROLLBACK;  -- Undo changes
```

### Practical Guidance
- Disable autocommit to control transaction boundaries
- Choose isolation level balancing correctness and performance
- Higher isolation = less concurrency but stronger guarantees
- Lower isolation = more concurrency but weaker guarantees

## Access Modes

### READ WRITE
- Default mode
- Allows SELECT, INSERT, UPDATE, DELETE, DDL operations
- Use when transaction needs to modify data

### READ ONLY
- Often used with READ UNCOMMITTED
- Restricts to SELECT statements only
- Use for reporting and analytics

## Q&A

**Q: What is the purpose of concurrency control in databases?**
- A. Allow multiple transactions to modify same data simultaneously
- B. Ensure transactions are serialized
- C. Ensure atomicity, isolation, and serializability of transactions
- D. Prevent database access by multiple users

Answer: C

**Q: Which isolation level allows reading only committed data, preventing dirty reads but allowing non-repeatable reads and phantoms?**
- A. Read Uncommitted
- B. Read Committed
- C. Repeatable Read
- D. Serializable

Answer: B

**Q: What is a dirty read?**
- A transaction reads data that has been committed by another transaction
- A transaction reads uncommitted data from another transaction that later aborts
- A transaction reads the same row twice and gets different values
- A transaction in a range query sees newly inserted rows

Answer: A transaction reads uncommitted data from another transaction that later aborts

**Q: Which isolation level prevents all three anomalies (dirty read, non-repeatable read, phantom)?**
- A. Read Uncommitted
- B. Read Committed
- C. Repeatable Read
- D. Serializable

Answer: D
