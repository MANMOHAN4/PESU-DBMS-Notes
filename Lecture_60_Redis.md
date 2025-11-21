# Redis — Key-Value Database

## NoSQL Key-Value Stores

### Concept
Key-value stores are the simplest NoSQL databases storing data as key-value pairs.

**Key**: Unique identifier locating an item (e.g., SRN)
**Value**: Data itself in various formats
**Like**: Giant dictionary/hash map persisted on disk
**Schema**: Usually schema-less (no fixed columns/table structure)
**Benefits**: High performance, scalability, flexibility

### Common Use Cases
- Caching
- Pub/Sub message queues
- Fetching user preferences
- Session storage
- Real-time leaderboards
- Rate limiting
- Feature flags

### Examples
Redis, Amazon DynamoDB, CouchDB, Aerospike

## What is Redis?

Open-source, advanced key-value store used for:
- High-performance, scalable web applications
- In-memory storage with disk persistence options
- Versatile data structures (strings, lists, sets, hashes, sorted sets)
- Lua scripting support
- Replication and clustering for horizontal scale

## Redis Use Cases

### Caching
Cache frequently requested information reducing load and improving performance. Example: cache popular restaurants.

### Message Broker
Asynchronous communication using Pub/Sub architecture. Example: push notifications to users.

### Streaming Data Processing
Real-time analytics: monitor traffic, track user activity, analyze transactions.

### Session Storage
Store login status, shopping cart information avoiding re-login on page refresh.

### Game Leaderboard
Store profiles and scores for real-time ranking.

### Rate Limiting
Track request counts per user/IP for API throttling.

### Feature Flags
Toggle features on/off without deployment.

## Redis Limitations

### RAM Expensive
Large datasets become costly due to memory prices. Limited by available RAM.

### Manual Memory Management
Configure eviction policies when memory is full. Need to monitor memory usage.

### Potential Data Loss
In-memory design risks loss on crashes if not persisted appropriately. Requires RDB/AOF persistence configuration.

### Limited Query Capabilities
Optimized for key-based operations, not complex queries. No joins or complex aggregations like SQL.

## Redis Basics

### Keys and Values
**Keys**: Binary safe strings (can hold arbitrary bytes)
**Values**: Can be different data structures (strings, hashes, lists, sets, sorted sets)

### Unique Keys and Namespacing
Keys must be unique. Use colon (:) to show hierarchy (namespace-like style).

**Example keys**:
- PES
- PES:Department
- user:123:profile
- session:abc-def-ghi

By default, values are strings unless specific structure used.

## Key Management Commands

### EXPIRE key seconds
Set TTL in seconds; key deleted after timeout.
```
EXPIRE user:session:123 3600
```

### TTL key
Return remaining time to live in seconds.
```
TTL user:session:123
```

### PERSIST key
Remove timeout, making key persistent.
```
PERSIST user:session:123
```

### DEL key [key ...]
Delete one or more keys.
```
DEL user:session:123 temp:data:abc
```

**Notes**:
- TTL returns -2 if key doesn't exist, -1 if key exists with no TTL
- Expiring keys essential for session and cache lifecycle management

## Data Structures in Redis

### Strings
Binary-safe sequence of bytes, max ~512 MB. Use cases: CRUD storage, caching, atomic counters.

### Lists
Sequences of strings implemented as linked lists. Use cases: task queues, logs, activity feeds.

### Hashes
Map of field → value pairs (like Python dicts or JSON). Use cases: structured records, user profiles, configurations.

### Sets
Unordered collection of unique strings. Use cases: tags, membership testing, deduplication.

### Sorted Sets (Zsets)
Like sets with score per member; sorted by score. Use cases: leaderboards, rankings, priority queues.

## String Commands

### SET key value [EX seconds]
Set string value with optional expiration.
```
SET user:1:name "Alice" EX 3600
```

### GET key
Get string value.
```
GET user:1:name
```

### MSET key1 val1 key2 val2
Set multiple key-value pairs atomically.
```
MSET user:1:name "Alice" user:2:name "Bob"
```

### MGET key1 key2
Get multiple values.
```
MGET user:1:name user:2:name
```

### INCR key / DECR key
Increment/decrement integer by 1.
```
INCR page:views
DECR items:in_stock
```

### INCRBY key n / DECRBY key n
Increment/decrement by n.
```
DECRBY items:in_stock 5
```

### APPEND key value
Append to string value.
```
APPEND log:file "Logged in"
```

**Notes**:
- INCR/DECR create key with 0 before operation if missing
- For atomic counters across processes, prefer INCR/DECRBY

## List Commands

### LPUSH key element
Push to head (left).
```
LPUSH tasks "Start task"
```

### RPUSH key element
Push to tail (right).
```
RPUSH logs "User A logged in"
```

### LPOP key / RPOP key
Pop from head/tail.
```
LPOP tasks
RPOP logs
```

### LRANGE key start stop
Get elements [start, stop] inclusive, 0-based.
```
LRANGE logs 0 99
```

### LLEN key
Get list length.
```
LLEN tasks
```

### BLPOP key timeout
Blocking pop from head; waits until element available or timeout.
```
BLPOP queue 0
```

### LTRIM key start stop
Keep only range [start, stop].
```
LTRIM logs 0 4
```

## Hash Commands

### HSET key field value
Set one or more hash fields.
```
HSET user:1 name "Alice" age 30
```

### HGET key field
Get specific field.
```
HGET user:1 name
```

### HGETALL key
Get all fields and values.
```
HGETALL user:1
```

### HDEL key field
Delete fields.
```
HDEL user:1 temp_token
```

### HINCRBY key field increment
Increment integer field.
```
HINCRBY user:1 login_count 1
```

### HLEN key
Number of fields in hash.
```
HLEN user:1
```

### HEXISTS key field
Check field existence (1 or 0).
```
HEXISTS user:1 email
```

## Set Commands

### SADD key member
Add members to set.
```
SADD article:123 "redis" "database"
```

### SREM key member
Remove members.
```
SREM article:123 "old_tag"
```

### SMEMBERS key
Get all set members.
```
SMEMBERS article:123
```

### SCARD key
Cardinality (size) of set.
```
SCARD article:123
```

### SISMEMBER key member
Membership test (1 if present, 0 if not).
```
SISMEMBER article:123 "redis"
```

### SINTER key [key ...]
Set intersection.
```
SINTER friends:alice friends:bob
```

### SUNION key [key ...]
Set union.
```
SUNION tech_tags devops_tags
```

### SDIFF key [key ...]
Set difference.
```
SDIFF group:a group:b
```

## Sorted Set Commands

### ZADD key score member
Add/update members with score.
```
ZADD leaderboard 1550 "player1"
```

### ZINCRBY key increment member
Increment member score.
```
ZINCRBY leaderboard 50 "player1"
```

### ZRANGE key start stop [WITHSCORES]
Members by rank ascending; optional scores.
```
ZRANGE leaderboard 0 9 WITHSCORES
```

### ZREVRANGE key start stop
Members by rank descending.
```
ZREVRANGE leaderboard 0 9
```

### ZRANK key member
Rank 0-based, ascending order.
```
ZRANK leaderboard "player1"
```

### ZREM key member
Remove members.
```
ZREM leaderboard "player1"
```

### ZCARD key
Number of members.
```
ZCARD leaderboard
```

**Notes**:
- Use sorted sets for leaderboards and time/score-ordered data
- ZREVRANGE 0 N-1 for top-N queries

## Practical Tips

### Namespacing
Use consistent key prefixes showing hierarchy.
Examples: session:prod:user:123, cart:staging:user:42

### Expiry and Caching
Apply EXPIRE to cache keys; PERSIST for durable data. Consider cache stampede protection.

### Atomicity
Use INCR/DECR for atomic counters. Combine operations with transactions when needed.

### Memory Management
Configure eviction policies (allkeys-lru, volatile-ttl). Monitor memory and TTLs.

### Performance
Use pipelining for batch operations. Keep values small, key names concise but readable.

### Persistence
RDB and AOF persistence reduce data loss risk. Configure per durability/performance needs.
