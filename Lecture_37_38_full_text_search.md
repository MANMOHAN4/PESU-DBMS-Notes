# **Lecture 37 & 38 – Full Text Search in MySQL**

---

# **1. Introduction to Full Text Search (FTS)**

Full Text Search (FTS) in MySQL allows powerful search operations on large text-based fields such as:

- `CHAR`
- `VARCHAR`
- `TEXT`

It is used to search _words, phrases, or combinations_ inside huge text columns using **Natural Language Processing (NLP)** techniques.

### **Why FTS instead of LIKE/REGEXP?**

- `LIKE` only checks substrings and is slow.
- FTS is **index-based** → much faster.
- FTS uses **relevance ranking** (LIKE does not).
- FTS understands **word boundaries**, **stopwords**, etc.

### **Key Features of FTS**

- 🔹 Uses **FULLTEXT indexes** (much faster)
- 🔹 Supports **Natural Language**, **Boolean**, **Query Expansion** modes
- 🔹 Supports **relevance ranking**
- 🔹 Handles stopwords and specific NLP parsing
- 🔹 Supports phrase search & word proximity

---

# **2. Basic Syntax of FTS**

The core syntax:

```sql
MATCH(column1, column2, ...)
AGAINST('search terms' IN <MODE>);
```

### **Example Scenario**

A `products` table contains:

- product_id
- product_name
- description
- price

### **Query: Search for “smartphone”**

```sql
SELECT * FROM products
WHERE MATCH(product_name, description)
AGAINST('smartphone' IN BOOLEAN MODE);
```

- `MATCH()` → specifies which columns to search
- `AGAINST()` → gives search keywords + mode

---

# **3. Full Text Search vs Normal String Search**

| Feature                 | LIKE / REGEXP       | FULL TEXT SEARCH       |
| ----------------------- | ------------------- | ---------------------- |
| Index Usage             | ❌ Usually no index | ✅ Uses FULLTEXT index |
| Speed                   | Slow on large data  | Fast                   |
| Relevance Ranking       | ❌ None             | ✅ Results ranked      |
| Natural Language        | ❌ No               | ✅ Yes                 |
| Word Boundary Detection | ❌ Poor             | ✅ Accurate            |
| Boolean Logic           | ❌ No               | ✅ Yes                 |
| Phrase Search           | Limited             | Accurate               |

FTS is superior for all text-heavy search situations.

---

# **4. How Full Text Search Works**

### Steps:

1. Use text-based columns: `VARCHAR`, `TEXT`
2. Create FULLTEXT index
3. Run `MATCH() ... AGAINST()` query

### Example Table Setup

```sql
CREATE TABLE posts (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200),
    body TEXT
) ENGINE=InnoDB;

ALTER TABLE posts
ADD FULLTEXT(title, body);
```

### Insert Sample Data

(Complete from slides)

```sql
INSERT INTO posts (title, body) VALUES
('MySQL Tutorial', 'Learn the basics of database systems'),
('Advanced Database', 'We explore indexing and performance'),
('Cooking Tips', 'Always use fresh ingredients'),
('MySQL vs PostgreSQL', 'Comparison of two database systems'),
('Oracle Tips', 'Learn how to use Oracle effectively');
```

### Query: Find rows containing “database”

```sql
SELECT * FROM posts
WHERE MATCH(title, body)
AGAINST('database' IN NATURAL LANGUAGE MODE);
```

---

# **5. Natural Language Mode (Default Mode)**

### Characteristics:

- Uses **NLP** to determine relevance.
- Ignores **stopwords** (the, a, an, etc.).
- Ignores words < 4 letters by default.
- Returns matches with a **relevance score**.

Natural Language Mode is simple and clean — no special operators required.

---

# **6. Relevance (Very Important)**

MySQL assigns each row a **relevance score** → the higher the score, the better the match.

### Relevance Score Depends On:

1. Frequency of search term in row
2. Rarity of term across all rows
3. Document length (shorter docs = higher relevance)

### Example:

```sql
SELECT id, title,
       MATCH(title, body) AGAINST('database') AS relevance
FROM posts
WHERE MATCH(title, body) AGAINST('database')
ORDER BY relevance DESC;
```

### What if you don’t show relevance?

```sql
SELECT id, title
FROM posts
WHERE MATCH(title, body) AGAINST('database');
```

MySQL will **still sort by relevance automatically**, as long as:

- no custom ORDER BY is used
- FULLTEXT index is used
- simple single-table query

### IMPORTANT:

If you add an ORDER BY clause → relevance sorting is overridden.

```sql
ORDER BY id;
```

---

# **7. Boolean Mode in FTS**

Boolean Mode allows complex search logic using operators:

### **Common Boolean Operators**

| Operator | Meaning                           | Example              |
| -------- | --------------------------------- | -------------------- |
| `+`      | Must include term                 | `+mysql`             |
| `-`      | Must NOT include term             | `-oracle`            |
| _(none)_ | Optional term (affects relevance) | `database`           |
| `""`     | Exact phrase                      | `"database systems"` |
| `*`      | Wildcard (suffix only)            | `data*`              |
| `( )`    | Grouping                          | `+(mysql database)`  |

---

# **8. Boolean Mode Examples**

### **1. Must include “mysql”, must exclude “oracle”**

```sql
SELECT * FROM posts
WHERE MATCH(title, body)
AGAINST('+mysql -oracle' IN BOOLEAN MODE);
```

### **2. Exact Phrase Search**

```sql
AGAINST('"database systems"' IN BOOLEAN MODE);
```

### **3. Prefix Search**

```sql
AGAINST('data*' IN BOOLEAN MODE);
```

Matches:

- data
- database
- databases

### **4. Grouped Terms**

```sql
AGAINST('+(mysql database) -oracle' IN BOOLEAN MODE);
```

---

# **9. Boolean Mode – Practical Exercise (Exactly from Slides)**

### Sample Table

```sql
CREATE TABLE articles (
    article_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    content TEXT,
    FULLTEXT(title, content)
);
```

### Insert Data (all entries included)

```sql
INSERT INTO articles (title, content) VALUES
('Introduction to Databases', 'Databases are organized collections of data. MySQL is a popular open-source database.'),
('MySQL Optimization Techniques', 'Learn indexing, query plans, and caching to speed up MySQL queries.'),
('Oracle vs MySQL', 'Comparison of Oracle and MySQL databases based on performance and cost.'),
('PostgreSQL Guide', 'PostgreSQL is an advanced open-source RDBMS.'),
('Data Science and SQL', 'SQL plays a key role in data science workflows.'),
('MySQL Tutorial for Beginners', 'This MySQL tutorial covers basic to intermediate topics for new developers.'),
('Cooking for Beginners', 'This guide is about basic cooking techniques and not related to databases.');
```

### Practice Questions

1. Retrieve articles containing "MySQL" (Natural Language)
2. Display relevance score for "MySQL"
3. Must include “MySQL”, must not include “Oracle”
4. Articles containing “tutorial” OR “guide”
5. Articles containing “data” AND any word starting with “scien\*”
