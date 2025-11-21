# Transaction Serializability and Locking

## Schedules

### Definition
A schedule is a sequence of instructions that specifies the chronological order in which instructions of concurrent transactions are executed.

### Schedule Requirements
- Must consist of all instructions of participating transactions
- Must preserve the order in which instructions appear in each individual transaction
- A transaction that successfully completes has COMMIT as last statement
- A transaction that fails has ABORT as last statement
- Order of operations is a total ordering (any two operations have a defined order)

### Formal Definition
A schedule S of n transactions T1, T2, ..., Tn is an ordering of the operations of the transactions where:
- Operations from different transactions can be interleaved
- Operations of Ti in S must appear in same order as in Ti

## Conflicting Operations

### Conflict Types
- **Read-Write Conflict**: Read and write operation on same data item from different transactions; changing order alters read value
- **Write-Write Conflict**: Two writes on same data item from different transactions; final value depends on execution order

### Conflicting Operations Definition
Two operations conflict if:
1. They belong to different transactions
2. They access the same item X
3. At least one operation is write_item(X)

### Non-Conflicting Operations
- read(X) and read(X) do not conflict (both reads)
- Operations on different items do not conflict
- Operations from same transaction do not conflict

## Banking Example

### Scenario
T1: Transfer $50 from account A to account B
T2: Transfer 10% of A balance to account B

Initial values: A = $1000, B = $2000

### Serial Schedule (T1 then T2)
- T1 executes: A = 950, B = 2050
- T2 executes: A = 855, B = 2145
- Result: A + B = 3000 (sum preserved)

### Equivalent Non-Serial Schedule (Schedule 3)
Concurrent execution can produce same final state as serial execution if operations are properly interleaved

### Incorrect Schedule (Schedule 4)
Not all concurrent executions preserve consistency:
- Final: A = $950, B = $2100
- Total: $3050 (incorrect, gained $50)
- Sum A + B not preserved

## Serial vs Serializable Schedules

### Serial Schedules
- No interleaving; transactions execute one after another
- Low concurrency
- Limited throughput and resource utilization
- Impractical in practice
- Always consistent

### Serializable Schedules
- Non-serial but equivalent to some serial schedule
- Concurrent execution allowed
- Improved resource utilization and throughput
- Maintains data consistency
- More practical and efficient

### Equivalence Notions

**Result Equivalence**
- Same final database state
- Insufficient alone for defining schedule equivalence
- Can occur accidentally

**Conflict Equivalence**
- Relative order of any two conflicting operations identical in both schedules
- Different orders of conflicting operations may produce different database states

**View Equivalence**
- Schedules produce same final state
- Ensure same read/write behavior for all transactions
- Both schedules have transactions reading from same sources

## Conflict Serializability

### Definition
A schedule is conflict-serializable if it is conflict equivalent to some serial schedule.

### Achievability
If a non-serial schedule of n transactions can be converted into a serial schedule by swapping non-conflicting operations, it is conflict serializable.

### Conflict Relationships
Given instructions li from Ti and lj from Tj:
- li = read(Q), lj = read(Q) → no conflict
- li = read(Q), lj = write(Q) → conflict
- li = write(Q), lj = read(Q) → conflict
- li = write(Q), lj = write(Q) → conflict

### Precedence Graph Method
Algorithm to test conflict serializability:

1. Create node for each transaction Ti in schedule S
2. For each read_item(X) by Tj after write_item(X) by Ti → create edge Ti → Tj
3. For each write_item(X) by Tj after read_item(X) by Ti → create edge Ti → Tj
4. For each write_item(X) by Tj after write_item(X) by Ti → create edge Ti → Tj
5. Schedule S is serializable if and only if precedence graph has no cycles

### Testing Steps
1. List all conflicting operations
2. Draw one node for each transaction
3. Draw edge for each conflict pair (ensure proper transaction order)
4. Check for cycles (no cycle = conflict serializable)

### Practice Examples
- Schedule with R1(A), R2(A), R1(B), R2(B), R3(B), W1(A), W2(B)
  - Conflicts: (R2(A),W1(A)), (R1(B),W2(B)), (R3(B),W2(B))
  - Edges: T2→T1, T1→T2, T3→T2
  - Cycle exists (T2↔T1) → NOT conflict serializable

## Lock-Based Protocols

### Definition
Locks are mechanisms to control concurrent access to data items through two modes.

### Lock Modes

**Exclusive Mode (X)**
- Requested via lock-X instruction
- Allows both read and write operations
- No other transaction can hold lock

**Shared Mode (S)**
- Requested via lock-S instruction
- Allows only read operations
- Multiple transactions can hold shared lock simultaneously

### Lock Compatibility

| Mode | S | X |
|------|---|---|
| S | Yes | No |
| X | No | No |

- Shared is compatible with Shared
- Exclusive is incompatible with both S and X
- Multiple S locks can coexist

### Basic Locking Policy
- Transaction can access data only if it holds compatible lock
- Other transactions must wait if lock incompatible
- Provides correctness but poor concurrency

### Lock Manager
Implemented as a process receiving lock/unlock requests:
- Grants locks when compatible
- Rolls back or delays requests when incompatible
- Maintains lock table with granted locks and waiting requests
- Transactions wait until request answered

## Deadlocks

### Definition
Two or more transactions waiting for each other in a circular dependency.

**Example**:
- T3 holds lock on A, waits for lock on B
- T4 holds lock on B, waits for lock on A
- Neither can proceed → deadlock

### Detection
MySQL automatically detects deadlocks and rolls back one conflicting transaction.

### Prevention
- Consistent ordering of row updates in transactions
- Avoid accessing same resources in different order

## Starvation

### Definition
A transaction cannot acquire requested lock while other transactions repeatedly acquire compatible locks.

**Example**:
- T1 requests exclusive lock on X
- T2, T3, T4 repeatedly request shared locks
- T1 never gets exclusive lock despite repeated requests

## Two-Phase Locking (2PL)

### Definition
Locking protocol ensuring conflict-serializable schedules through two phases.

### Growing Phase
- Transaction may acquire locks (S or X)
- Cannot release locks
- Transaction accumulates locks as needed

### Shrinking Phase
- Transaction may release locks
- Cannot acquire new locks
- Once first lock released, enters shrinking phase

### Serializability Guarantee
Transactions serialize in order of their lock points (when transaction acquired final lock - end of growing phase).

### Limitation
Does not prevent deadlocks (extensions needed).

## Strict and Rigorous 2PL

### Strict 2PL
- Hold all exclusive (X) locks until commit/abort
- Ensures recoverability
- Avoids cascading rollbacks
- Earlier shared locks can be released

### Rigorous 2PL
- Hold all locks (S and X) until commit/abort
- Transactions serialize by commit order
- Reduces starvation likelihood
- Provides stronger consistency guarantee

### Comparison

| Aspect | Strict 2PL | Rigorous 2PL |
|--------|-----------|--------------|
| Lock Release | S earlier, X at end | All locks at end |
| Deadlock Prevention | Potential issues | Reduced deadlock chance |
| Resource Utilization | May underutilize | Resources tied up longer |
| Starvation | Possible | Less likely |

## Lock Conversions

### Upgrade (Shared to Exclusive)
During growing phase: Convert S lock to X lock
Allowed when transaction needs write access to previously read item.

### Downgrade (Exclusive to Shared)
During shrinking phase: Convert X lock to S lock
Allows releasing write access while retaining read access.

## Implementation Details

### Lock Table Structure
- In-memory data structure recording granted locks and pending requests
- Per data item: linked list of requests
- Each record: transaction id, lock mode, grant status
- Per transaction: list of all locks held

### Request Processing
1. New request added to end of queue for data item
2. Granted if compatible with earlier locks
3. Unlock removes request and checks later requests for grant eligibility
4. Transaction abort removes all its waiting/granted requests
5. FIFO queuing guarantees freedom from starvation

## Q&A

**Q: What does it mean for two operations to conflict in a schedule?**
- A. They belong to the same transaction
- B. They access different data items
- C. At least one of them is a read operation
- D. They access the same item, and at least one is a write operation

Answer: D

**Q: What defines a serializable schedule in database transactions?**
- A. A schedule that allows only one transaction at a time
- B. A non-serial schedule that is equivalent to some serial schedule
- C. A schedule that does not preserve consistency
- D. A schedule that leads to data inconsistency

Answer: B

**Q: In conflict serializability, what does it mean if the precedence graph of a schedule has a cycle?**
- A. The schedule is conflict serializable
- B. The schedule is serial
- C. The schedule is not conflict serializable
- D. The schedule is view serializable

Answer: C
