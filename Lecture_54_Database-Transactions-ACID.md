# Database Transactions and ACID Properties

## Single-User vs Multi-User Systems

- **Single-user system**: At most one user can use the system at a time
- **Multi-user system**: Many users can use the system and access the database concurrently

## What is a Transaction?

A transaction is a unit of program execution in a database system that:
- Accesses (reads) one or more data items
- Possibly updates (writes/changes) one or more data items
- Is the smallest unit of work that is either fully completed or fully rolled back (no partial completion)

Transactions are initiated by user programs written in:
- High-level data manipulation languages (SQL)
- Programming languages with embedded database access (C++, Java with JDBC/ODBC)

## Transaction Concept

From a user's perspective, a transaction is a single operation. From the DBMS perspective, it consists of multiple operations.

### Transaction Boundaries
- Begin transaction: Marks start of execution
- End transaction: Marks end of execution
- All operations between boundaries are part of the same transaction

### Transaction Types
- **Read-only transaction**: Database operations do not update the database
- **Read-write transaction**: Database operations retrieve and update data

### Two Main Concerns
1. Failures: Hardware failures, system crashes during transaction execution
2. Concurrency: Multiple transactions executing simultaneously

## Simple Transactional Model

Transactions access data using two operations:

### read(X)
- Transfers data item X from database to a variable X in main memory buffer
- Steps:
  1. Find address of disk block containing item X
  2. Copy disk block into main memory buffer (if not already there)
  3. Copy item X from buffer to program variable X

### write(X)
- Transfers value in variable X from main memory buffer to data item X in database
- Steps:
  1. Find address of disk block containing item X
  2. Copy disk block into main memory buffer (if not already there)
  3. Copy item X from program variable to correct location in buffer
  4. Write updated disk block back to disk (immediately or later)

## Transaction Data Buffers

- DBMS maintains data buffers in main memory, each holding one database disk block
- Buffer replacement policy decides which buffer to replace when all are full
- **LRU (Least Recently Used)** is a common replacement policy
- Modified buffers are written back to disk before reuse
- Some policies are specific to DBMS characteristics

## Transaction Concurrency

**Reasons for allowing concurrent transactions:**
- Improved throughput and resource utilization
- Reduced waiting time for users

## Transaction Execution States

### State Transitions

1. **Active**: Transaction is executing read/write operations
2. **Partially committed**: All operations executed; system performs final checks
3. **Committed**: Commit point reached; all effects are permanent and survive failures
4. **Failed**: Transaction cannot continue; abort due to checks, errors, or explicit rollback
5. **Terminated**: Transaction has left the system; cleanup completed

### Abort Handling

**Restart**: For failures not due to logical program errors (e.g., temporary system/hardware issues)
**Kill**: For logical errors, bad input, or missing data requiring program correction

## Transaction Primitives

- **BEGIN_TRANSACTION**: Marks beginning of transaction execution
- **READ/WRITE**: Perform operations on database items
- **END_TRANSACTION**: Marks end of read/write phase
- **COMMIT_TRANSACTION**: Successful end; updates made permanent and won't be undone
- **ROLLBACK (ABORT)**: Unsuccessful end; all effects must be undone

## SQL Transaction Support

### Key Points
- No explicit BEGIN TRANSACTION required in standard SQL
- Transactions end with explicit statements:
  - COMMIT: Make changes permanent
  - ROLLBACK: Undo changes

### Access Modes
- **READ WRITE**: Default mode; allows all operations (SELECT, INSERT, UPDATE, DELETE, CREATE)
- **READ ONLY**: Restricts to SELECT statements only

## ACID Properties

To preserve data integrity, database systems must ensure four properties:

### Atomicity (All-or-Nothing)
- Either all operations of the transaction are reflected in the database, or none are
- No partial updates
- Ensures consistency in case of failure

### Consistency (Integrity)
- Transaction execution preserves database integrity constraints and invariants
- Explicit constraints: primary keys, foreign keys, domain checks
- Implicit constraints: business rules
- Database may be temporarily inconsistent during execution but must be consistent after commit

### Isolation (Concurrency)
- Concurrent transactions do not interfere with each other
- Each transaction executes as if alone
- Intermediate transaction results hidden from other executing transactions
- Ensures serial equivalence of concurrent execution

### Durability (Persistence)
- Once a transaction commits, its changes persist despite subsequent failures
- Changes survive hardware crashes and system failures
- Permanent storage guarantee

## Fund Transfer Example

Transaction Ti: Transfer $50 from Account A to Account B

```
1. read(A)
2. A := A - 50
3. write(A)
4. read(B)
5. B := B + 50
6. write(B)
```

### Atomicity Analysis
- Problem: System fails after step 3 but before step 6 → money appears lost
- Solution: Log old values before writes; restore on failure (undo recovery)
- Responsibility: Database recovery system

### Durability Analysis
- After user notification of completion, changes must persist
- Even with software or hardware failures after commit
- Responsibility: Database recovery system

### Consistency Analysis
- Invariant: Sum of A and B remains constant
- During transaction: Database may be temporarily inconsistent
- After commit: Database must return to consistent state
- All integrity constraints must be satisfied

### Isolation Analysis
- Problem: If T2 reads between steps 3 and 6, sees inconsistent state
- Solution: Ensure concurrent execution equivalent to some serial order
- Responsibility: Concurrency-control system
- Benefit: Significant performance improvements from concurrent execution

## Q&A

**Q: Which ACID property ensures that once a transaction completes, changes persist even if the system crashes?**
- A. Atomicity
- B. Consistency
- C. Isolation
- D. Durability

Answer: D

**Q: In a transaction transferring $50 from account A to account B, what happens if system fails after updating A but before updating B?**
- A. System remains consistent
- B. Transaction completes successfully
- C. System enters inconsistent state
- D. Transaction automatically retries

Answer: C (without atomicity enforcement)

**Q: Which ACID property ensures a transaction in isolation appears as if no other transactions execute simultaneously?**
- A. Atomicity
- B. Consistency
- C. Isolation
- D. Durability

Answer: C

**Q: Which best describes a transaction in database systems?**
- A. Set of operations that only reads data
- B. Unit of execution with single operation
- C. Collection of operations that either all occur or none, ensuring data integrity
- D. Always updates database immediately after operation

Answer: C
