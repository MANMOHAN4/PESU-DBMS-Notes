# **Redis Hands‑On – Complete Notes (String, List, Hash, Set, Sorted Set)**
### *Based fully on the slides – every point included with explanations*
---

# **1. Introduction – Redis Hands‑On**
This session demonstrates Redis operations using **real-world case studies**.  
Each Redis data type is explained with:
- Scenario
- Tasks to perform
- Corresponding Redis commands

Covered commands: **Strings, Lists, Hashes, Sets, Sorted Sets (Zsets)**.

---

# **2. Redis String Commands – Case Study: User Profile & Session Management**
### **Scenario**
You are building a social media application. Redis is used to:
- Cache user profile information
- Manage temporary user data

Each user has a unique key:
- `user:1`, `user:2` ...

You must store:
- User name
- Extra attributes (like height)

---
## **Tasks & Required Commands**
### **1. Set two user names at once**
```
MSET user:1:name "Vedant" user:2:name "Bob"
```

### **2. Fetch both users at once**
```
MGET user:1:name user:2:name
```

### **3. Add temporary user Alice with 60-second expiry**
```
SET user:3:name "Alice" EX 60
```

### **4. Append last name "Ellison" to Bob**
```
APPEND user:2:name "Ellison"
```
Result → `BobEllison`

### **5. Add height for user:1**
```
SET user:1:height "120"
```

### **6. Retrieve user:1 height**
```
GET user:1:height
```

### **7. Increment user:1 height by 10**
```
INCRBY user:1:height 10
```
---

# **3. Redis List Commands – Case Study: To‑Do List Management**
### **Scenario**
You are building a to‑do list application.  
Key used → `todolist`
- Normal tasks → added to end
- High priority → added to start

---
## **Tasks & Commands**
### **1. Add task “Do laundry”**
```
RPUSH todolist "Do laundry"
```

### **2. View entire list**
```
LRANGE todolist 0 -1
```
`-1` works like Python → last element

### **3. Add urgent task "Call Dad" to top**
```
LPUSH todolist "Call Dad"
```

### **4. Find number of tasks**
```
LLEN todolist
```

### **5. Remove the top (urgent) task**
```
LPOP todolist
```

### **6. Remove the least urgent task**
```
RPOP todolist
```

### **7. Add task "Buy groceries"**
```
RPUSH todolist "Buy groceries"
```

### **8. Keep only the most important task (trim list)**
```
LTRIM todolist 0 0
```
---

# **4. Redis Hash Commands – Case Study: Student Management**
### **Scenario**
You must store student profiles with multiple fields using Redis Hashes.  
Keys → `user:1`, `user:2`.
Fields → name, age.
This is similar to JSON objects.

---
## **Tasks & Commands**
### **1. Create user:1 with name and age**
```
HSET user:1 name "Vedant" age "21"
```

### **2. Fetch user:1 name only**
```
HGET user:1 name
```

### **3. Fetch all details of user:1**
```
HGETALL user:1
```

### **4. Create user:2**
```
HSET user:2 name "Bob" age "23"
```

### **5. Increase Bob's age by 2**
```
HINCRBY user:2 age 2
```

### **6. Count fields stored for user:2**
```
HLEN user:2
```

### **7. Remove Bob's age field**
```
HDEL user:2 age
```

### **8. Check if age field exists for user:2**
```
HEXISTS user:2 age
```
---

# **5. Redis Set Commands – Case Study: Event RSVP Management**
### **Scenario**
You need to manage attendees for an event.  
Redis Set ensures **no duplicate RSVP**.
Key → `rsvp`

---
## **Tasks & Commands**
### **1. Add 3 users at once**
```
SADD rsvp "User A" "User B" "User C"
```

### **2. Fetch all attendees**
```
SMEMBERS rsvp
```

### **3. Add “User A” again and new "User D"**
```
SADD rsvp "User A" "User D"
```
Duplicate automatically ignored.

### **4. Check if User D is present**
```
SISMEMBER rsvp "User D"
```
(1 → present, 0 → not present)

### **5. Find attendee count**
```
SCARD rsvp
```

### **6. Remove User D**
```
SREM rsvp "User D"
```
---

# **6. Redis Sorted Set Commands – Case Study: Gaming Leaderboard**
### **Scenario**
Build a leaderboard sorted by player score.  
Key → `leaderboard`
Sorted Set stores → **member + score**

---
## **Tasks & Commands**
### **1. Add 3 players with scores**
```
ZADD leaderboard 100 "player1" 200 "player2" 300 "player3"
```

### **2. Count total players**
```
ZCARD leaderboard
```

### **3. Display leaderboard (highest → lowest)**
```
ZREVRANGE leaderboard 0 -1
```

### **4. Display leaderboard with scores (descending)**
```
ZREVRANGE leaderboard 0 -1 WITHSCORES
```

### **5. Increase player1 score by 500**
```
ZINCRBY leaderboard 500 "player1"
```

### **6. Find rank of player1 (lowest → highest)**
```
ZRANK leaderboard "player1"
```

### **7. Find rank of player1 (highest → lowest)**
```
ZREVRANK leaderboard "player1"
```

### **8. Remove player1**
```
ZREM leaderboard "player1"
```
---

# **End of Redis Hands‑On Notes – Complete Slide Extraction**

