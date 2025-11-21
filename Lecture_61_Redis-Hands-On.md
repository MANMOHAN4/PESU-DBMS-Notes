# Redis Hands-On Tutorial

## Case Study 1: User Profile & Session Management

Build a Redis-backed feature for a social media application caching user profiles and managing temporary data. Each user has unique ID (user:1, user:2, etc.) with name and optional attributes like height.

### Scenarios and Commands

1) Set two user names at once:
```
MSET user:1:name "Vedant" user:2:name "Bob"
```
Create multiple user names simultaneously using MSET.

2) Fetch multiple users:
```
MGET user:1:name user:2:name
```
Retrieve both user names in single command.

3) Create temporary user with 60-second expiration:
```
SET user:3:name "Alice" EX 60
```
Alice's profile auto-deleted after 60 seconds (ideal for temporary sessions).

4) Append last name to existing user:
```
APPEND user:2:name "Ellison"
```
Append "Ellison" to "Bob" resulting in "BobEllison".

5) Add height attribute:
```
SET user:1:height "120"
```
Store user:1's height as 120 cm.

6) Retrieve height:
```
GET user:1:height
```
Fetch stored height value.

7) Increment height by 10:
```
INCRBY user:1:height 10
```
Update height from 120 to 130 without delete/recreate.

---

## Case Study 2: To-Do List Management

Develop a fast to-do list application using Redis Lists. Tasks with normal priority added to end; high-priority tasks added to beginning.

### Scenarios and Commands

1) Add task to end:
```
RPUSH todolist "Do laundry"
```
Add normal-priority task to right (end) of list.

2) View entire to-do list:
```
LRANGE todolist 0 -1
```
Fetch all tasks from index 0 (start) to -1 (last, inclusive). Works like Python indexing.

3) Add urgent task to top:
```
LPUSH todolist "Call Dad"
```
Add high-priority task to left (beginning) of list.

4) Count tasks:
```
LLEN todolist
```
Return total number of tasks in list.

5) Remove most urgent task:
```
LPOP todolist
```
Remove and return first element from left (top).

6) Remove least urgent task:
```
RPOP todolist
```
Remove and return first element from right (bottom).

7) Add another task to end:
```
RPUSH todolist "Buy groceries"
```
Add normal-priority task to end.

8) Keep only most important task:
```
LTRIM todolist 0 0
```
Trim list to contain only elements in range [0, 0], keeping one element.

---

## Case Study 3: Student Management with Hashes

Store detailed student profiles with fields like name and age. Redis Hashes perfect for multiple field-value pairs under single key (similar to JSON).

### Scenarios and Commands

1) Create user with name and age:
```
HSET user:1 name "Vedant" age "21"
```
Add user:1 with name and age fields.

2) Fetch only name:
```
HGET user:1 name
```
Retrieve specific field value.

3) Fetch all user details:
```
HGETALL user:1
```
Retrieve all fields and values as flat list.

4) Create another user:
```
HSET user:2 name "Bob" age "23"
```
Add user:2 with name and age.

5) Increment age by 2:
```
HINCRBY user:2 age 2
```
Update age from 23 to 25 automatically.

6) Count fields in hash:
```
HLEN user:2
```
Returns 2 (name and age fields).

7) Delete age field:
```
HDEL user:2 age
```
Remove age field for privacy; name field remains.

8) Check if field exists:
```
HEXISTS user:2 age
```
Returns 1 if exists, 0 if not. Used before displaying on profile page.

---

## Case Study 4: Event RSVP Management with Sets

Manage event attendees using Redis Sets for automatic uniqueness. Prevents duplicate RSVPs and efficiently tracks attendees.

### Scenarios and Commands

1) Add 3 attendees at once:
```
SADD rsvp "User A" "User B" "User C"
```
Add multiple users to set (automatically handles uniqueness).

2) List all attendees:
```
SMEMBERS rsvp
```
Return all members in set (unordered collection).

3) Add existing and new attendees:
```
SADD rsvp "User A" "User D"
```
Add User A (already in set, ignored) and User D (new). Only 4 unique users total.

4) Check if attendee registered:
```
SISMEMBER rsvp "User D"
```
Returns 1 if present, 0 if not. Used for final confirmation check.

5) Get headcount:
```
SCARD rsvp
```
Return size of set (4 attendees).

6) Remove cancellation:
```
SREM rsvp "User D"
```
Remove User D from set due to cancellation.

---

## Case Study 5: Gaming Leaderboard with Sorted Sets

Build real-time leaderboard for online game. Redis Sorted Sets maintain unique members ordered by score.

### Scenarios and Commands

1) Add 3 players with scores:
```
ZADD leaderboard 100 "player1" 200 "player2" 300 "player3"
```
Add players with scores (score first, then name). Automatically sorted by score.

2) Count total players:
```
ZCARD leaderboard
```
Returns 3 players.

3) Display leaderboard (highest to lowest):
```
ZREVRANGE leaderboard 0 -1
```
Return player names in descending score order (ZREVRANGE for highest first).

4) Display with scores (highest to lowest):
```
ZREVRANGE leaderboard 0 -1 WITHSCORES
```
Return players and scores in descending order.

5) Update player1's score by 500:
```
ZINCRBY leaderboard 500 "player1"
```
Increase player1's score from 100 to 600.

6) Get rank from lowest (0-indexed):
```
ZRANK leaderboard "player1"
```
Returns rank from lowest score (0 = lowest). After increment, player1 at index 2.

7) Get rank from highest (0-indexed):
```
ZREVRANK leaderboard "player1"
```
Returns rank from highest score (0 = highest). After increment, player1 at index 0.

8) Remove cheating player:
```
ZREM leaderboard "player1"
```
Remove player1 from leaderboard.

---

## Command Summary by Data Type

### String Commands
- MSET key1 val1 key2 val2 — Set multiple keys
- MGET key1 key2 — Get multiple values
- APPEND key value — Append to string
- INCRBY key n — Increment by n
- SET key value EX seconds — Set with expiration

### List Commands
- RPUSH key element — Push to right (end)
- LPUSH key element — Push to left (beginning)
- LRANGE key start stop — Get range
- LPOP key / RPOP key — Pop from left/right
- LLEN key — Get list length
- LTRIM key start stop — Keep only range

### Hash Commands
- HSET key field value — Set field value
- HGET key field — Get field value
- HGETALL key — Get all fields and values
- HINCRBY key field n — Increment field
- HDEL key field — Delete field
- HLEN key — Count fields
- HEXISTS key field — Check field existence

### Set Commands
- SADD key member — Add member
- SMEMBERS key — Get all members
- SISMEMBER key member — Check membership
- SCARD key — Get set size
- SREM key member — Remove member

### Sorted Set Commands
- ZADD key score member — Add with score
- ZRANGE key start stop — Get by rank ascending
- ZREVRANGE key start stop — Get by rank descending
- ZINCRBY key increment member — Increment score
- ZRANK key member — Rank ascending
- ZREVRANK key member — Rank descending
- ZCARD key — Get count
- ZREM key member — Remove member
- WITHSCORES — Include scores in output
