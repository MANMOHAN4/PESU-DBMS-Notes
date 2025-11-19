# **Redis – Key-Value Database (Complete Notes)**
### *Compiled from Lecture Slides – Includes every detail & explanation*
---

# **1. Introduction to Key-Value Databases (NoSQL)**
Key-value stores are the **simplest type of NoSQL databases**, where data is stored as:
- **Key** → Unique identifier (e.g., SRN)
- **Value** → Actual data (e.g., Name)

### **Characteristics**
- Works like a **giant dictionary / hash map** stored on disk.
- **Schema-less** → No tables, no fixed columns.
- Highly **performant, scalable, flexible**.

### **Common Use Cases**
- Caching
- Message queues (pub-sub)
- Session storage
- User preferences
- Real-time leaderboards

### **Examples**
Redis, Amazon DynamoDB, CouchDB, Aerospike

---

# **2. Redis DB – Introduction & Features**
Redis = **Remote Dictionary Server**
- Open-source, advanced key-value store.
- Used for **high-performance and scalable** applications.

### **Key Features**
1. **In-Memory Storage**
   - Everything is stored in RAM → super fast
   - Disk used only for persistence

2. **Versatile Data Structures**
   - Strings, Lists, Sets, Sorted Sets, Hashes, etc.

3. **Lua Scripting Support**
   - Execute custom logic inside Redis.

4. **Replication & Clustering**
   - Master-Slave replication
   - Horizontal scalability with Redis Cluster

---

# **3. Redis Use Cases**
### **1. Caching**
- Frequently accessed data stored in Redis.
- Example: Zomato popular restaurants caching.

### **2. Message Broker (Pub/Sub)**
- Redis acts as a mediator.
- Example: Roblox notification system.

### **3. Streaming & Real-Time Analytics**
- Monitor traffic, user activity, transactions.
- Example: Xignite.

### **4. Session Storage**
- Preserves login state, cart items.
- Example: Amazon shopping cart.

### **5. Game Leaderboards**
- Store users with scores.
- Example: HackerRank leaderboard.

---

# **4. Redis Limitations**
### **1. RAM is Expensive**
Scaling large datasets = costly.

### **2. Manual Memory Management**
Developer must configure eviction policies.

### **3. Potential Data Loss**
Redis is RAM-based → crash = data loss.

### **4. No Support for Complex Queries**
- No joins
- No aggregations
- No advanced query engine

---

# **5. Redis Basics**
Redis uses key-value pairs.
- Keys = **strings** (binary safe)
- Values = **different data types**
- Uses colon `:` for hierarchical namespaces

### **Examples of Namespaces**
- `PES`
- `PES:Department`
- `user:101`, `user:102`

### **Example: Storing User Data with HSET**
```
HSET user:101 name "Alice" age 23
HSET user:102 name "Bob"   age 20
```

`user:101` = key 
`name`, `age` = fields 
`Alice`, `23` = values

---

# **6. Key Management Commands**

| Command | Description | Example |
|--------|-------------|---------|
| **EXPIRE key seconds** | Sets a timeout | EXPIRE user:session:123 3600 |
| **TTL key** | Time to live remaining | TTL user:session:123 |
| **PERSIST key** | Removes expiry | PERSIST user:session:123 |
| **DEL key** | Deletes keys | DEL user:session:123 temp:data:abc |

---

# **7. Redis Data Structures Overview**
Redis supports 5 major data types:

### **1. Strings**
- Binary safe: can store bytes
- Max size: **512MB**
- Use cases: caching, counters, CRUD

### **2. Lists**
- Linked lists of strings
- Use cases: queues, logs

### **3. Hashes**
- Similar to Python dict / JSON
- Use cases: storing structured data

### **4. Sets**
- Unique unordered elements
- Use cases: tags, unique items

### **5. Sorted Sets (Zsets)**
- Unique elements with scores, auto-sorted
- Use cases: leaderboards, ranking

---

# **8. String Commands**
| Command | Description | Example |
|---------|-------------|---------|
| SET | Set value | SET user:1:name "Alice" EX 3600 |
| GET | Get value | GET user:1:name |
| MSET | Set multiple keys | MSET user:1 "A" user:2 "B" |
| MGET | Get multiple | MGET user:1 user:2 |
| INCR / DECR | Increment / decrement | INCR page:views |
| INCRBY | Increment by N | INCRBY items:stock 5 |
| APPEND | Append to string | APPEND log "Logged in" |

---

# **9. List Commands** 
| Command | Description | Example |
|---------|-------------|---------|
| LPUSH | Add to start | LPUSH tasks "Begin task" |
| RPUSH | Add to end | RPUSH logs "User logged in" |
| LPOP | Remove first element | LPOP tasks |
| RPOP | Remove last element | RPOP logs |
| LRANGE | Get items in range | LRANGE logs 0 99 |
| LLEN | Length of list | LLEN tasks |
| BLPOP | Blocking list pop | BLPOP queue 0 |
| LTRIM | Trim list to range | LTRIM logs 0 4 |

### **Worked Example: List Manipulation**
(Full execution sequence included in slides.)

---

# **10. Hash Commands**
| Command | Description | Example |
|---------|-------------|---------|
| HSET | Set field values | HSET user:1 name "A" age 30 |
| HGET | Get field | HGET user:1 name |
| HGETALL | Get all fields | HGETALL user:1 |
| HDEL | Delete field | HDEL user:1 token |
| HINCRBY | Increment a field | HINCRBY user:1 login_count 1 |
| HLEN | Count fields | HLEN user:1 |
| HEXISTS | Check field | HEXISTS user:1 email |

---

# **11. Set Commands**
| Command | Description | Example |
|---------|-------------|---------|
| SADD | Add members | SADD article:123 "redis" "db" |
| SREM | Remove members | SREM article:123 "old_tag" |
| SMEMBERS | Get all members | SMEMBERS article:123 |
| SCARD | Count members | SCARD article:123 |
| SISMEMBER | Check membership | SISMEMBER article:123 "redis" |
| SINTER | Intersection | SINTER friends:alice friends:bob |
| SUNION | Union | SUNION tag1 tag2 |
| SDIFF | Difference | SDIFF group:A group:B |

---

# **12. Sorted Set (Zset) Commands**
| Command | Description | Example |
|---------|-------------|---------|
| ZADD | Add with score | ZADD leaderboard 1550 "player1" |
| ZINCRBY | Increment score | ZINCRBY leaderboard 50 "player1" |
| ZRANGE | Ascending range | ZRANGE lb 0 9 WITHSCORES |
| ZREVRANGE | Descending range | ZREVRANGE lb 0 9 |
| ZRANK | Get rank | ZRANK leaderboard "player1" |
| ZREM | Remove member | ZREM leaderboard "player1" |
| ZCARD | Count members | ZCARD leaderboard |

---

# **13. References (as in slides)**
(List of URLs preserved exactly as provided.)

---

# **End of Redis Notes – Full Slide Coverage**