### Locks

- We need locks to ensure consistency; cons: performance.
- Exclusive Lock:
  - when I access a row, I want to be the only one who can access it.
  - when I get an exclusive lock, no one can get a shared lock on the same row.
- Shared Lock:
  - when I access a row, I want to make sure no one is modifying it (can be read).
  - when I get a shared lock, no one can get an exclusive lock on the same row.
---

### Deadlock

- most dbms can detect deadlocks.
- in postgres the last transaction that entered a deadlock is the one that gets rolled back.

### Two Phase Locking

- double booking problem.
  - two transactions at the same time try to book the same room. (last one wins)
    - implementing 2-phase locking in postgres 
      - ```sql
        begin transaction;
        select * from seats where id = 14 for update; 
        ```
      
    - any other transaction trying to get a lock on the same row will be blocked until the first transaction is committed or rolled back.

---
## Avoiding offset in pagination

- `SELECT * FROM table OFFSET 110 LIMIT 10`
- offset means fetch and drop the first N rows.
- you fetch the first 120 rows, skipping the first 110 and returning the next 10 rows.
- inserting a new record at the beginning of the table will cause duplicate read for the same row.
  - a new row is inserted at the beginning after reading page 10, when page 11 is read, it will return a row from the previous page for that client.
- solution:
  Using `OFFSET` for pagination is a common pitfall. As you noted, it’s inefficient because the database must scan and discard  rows before reaching the target. More importantly, it is **unstable**; if data is added or deleted while a user is paginating, they will see duplicate entries or miss records entirely.

The industry-standard solution is **Keyset Pagination** (also known as the "Seek Method" or "Cursor Pagination").



## The Solution: Keyset Pagination

Instead of telling the database how many rows to skip, you tell it **where you left off** using the unique identifier (the "key") of the last record from the previous page.

### How it works:

1. **Sort by a unique key:** Usually a timestamp or a primary key (`id`).
2. **Filter using the last value:** Use a `WHERE` clause to fetch rows strictly greater than (or less than) the last value seen.
3. **Limit the result:** Fetch only the next page size.

---

## Step-by-Step Implementation

### Step 1: The Initial Request

For the first page, you don't have a "last value" yet. You simply fetch the first  records sorted by your key.

```sql
SELECT * FROM orders
ORDER BY id ASC
LIMIT 10;

```

*Assume the last row in this result set has an `id` of **110**.*

### Step 2: The Subsequent Request

When the client asks for "Page 2," they send back the last ID they saw (`110`). You use that to "seek" the next set of data.

```sql
SELECT * FROM orders
WHERE id > 110
ORDER BY id ASC
LIMIT 10;

```

### Step 3: Handling Non-Unique Columns

If you want to sort by something non-unique (like `created_at`), you must include the primary key as a "tie-breaker" to ensure the order is deterministic and no rows are skipped.

```sql
SELECT * FROM orders
WHERE (created_at, id) > ('2026-01-30 12:00:00', 110)
ORDER BY created_at ASC, id ASC
LIMIT 10;

```


## Why this is better

| Feature | Offset Pagination | Keyset Pagination |
| --- | --- | --- |
| **Performance** | Degrades as page number increases () | Consistent performance with indexes () |
| **Consistency** | Risk of duplicates/skips on data changes | Immune to insertions/deletions before the current page |
| **Deep Paging** | Extremely slow for large datasets | Fast regardless of how deep you go |
| **UX** | Supports "Go to page X" | Supports "Next/Previous" (standard for infinite scroll) |

> **Note:** For this to be fast, ensure you have an **index** on the column(s) used in your `ORDER BY` and `WHERE` clauses.

---

---

### Problem of working with large datasets


- db do alot of work for the execution plan, define which idnex to use, do the actual fetching, compsoe the result, then transmit the result to the client using tcp -> client have to wait for all the result to comeback, then the client need to have the memory to store all the result and this is sometimes absolutely impossible to do.

- Server side cursor is a way to solve this problem.

- cursor pros:
  - process rows in batches, save web application memory instead of storing all the rows in memory.
  - streaming them to another websocket ...
  - canceling
  - paging 
  - stored procedural.
- cursor cons:
  - stateful: there is a memory allocated for it in the db , there is a corresponding transaction pointing to this cursor , if u made another request to another server that process have no idea about this cursor.
    - you can do some tricks with proxy , you can make the web app understand that  a cursor involved and send some sort of varaible that stick you back not only to the same server but the same transaction that have the cursor.(very diffcult)
    - if you can do that, doing paging with this approach is much better way , a stateless paging .
  - long running transaction:
    - dbms cannot do indexing properly when there is a long running transaction.
    - you cannot do dml on the table that the has ana active transaction.
    - some write operations are not allowed on a table that has an active transaction.


- connection pooling:
  - a pattern of creating a pool of connections (usually tcp) to the database and reusing them by multiple clients.

---
### Partitioning
- break the table into smaller tables, and let the dbms decide which partition to look at based on the key. (where clause)
- the fastest way to query a table with billions of rows is avoiding quering a table with billion rows.
- Sure 🙂
  Here is the **extracted text from the image**, followed by a **clear explanation with examples**, all in English.

---

## Extracted Text

**Vertical vs Horizontal Partitioning**

* **Horizontal Partitioning** splits rows into partitions

  * Range or list or Hash (consist hashing) partitioning

* **Vertical Partitioning** splits columns into partitions

  * Large column (blob) that you can store in a slow access drive in its own tablespace

---


### 1️⃣ Horizontal Partitioning

**What it means:**
You split a table **by rows**.
Each partition has the **same columns** but **different rows**.

#### Common strategies:

* **Range partitioning** (by date, ID range, etc.)
* **List partitioning** (by specific values like country or status)

#### Example:

Table: `orders`

| order_id | order_date | customer_id |
| -------- | ---------- | ----------- |

Partitions:

* `orders_2023` → orders from 2023
* `orders_2024` → orders from 2024

A query like:

```sql
SELECT * FROM orders WHERE order_date >= '2024-01-01';
```

Only scans the `orders_2024` partition → **faster queries**.

#### When to use:

* Very large tables
* Queries frequently filter by date, range, or category
* Need better performance and easier data management

---

### 2️⃣ Vertical Partitioning

**What it means:**
You split a table **by columns**.
Each partition has **different columns**, usually sharing the same primary key.

#### Example:

Original table: `users`

| user_id | name | email | profile_image | bio |
| ------- | ---- | ----- | ------------- | --- |

Vertical partitioning:

* `users_core(user_id, name, email)`
* `users_profile(user_id, profile_image, bio)`

The large columns (`profile_image`, `bio`) are stored separately.

#### Why this helps:

* Most queries only need `users_core`
* Large columns (BLOBs) don’t slow down common queries
* Large data can be stored on slower/cheaper storage

---

## Quick Comparison

| Aspect     | Horizontal                  | Vertical                        |
| ---------- | --------------------------- | ------------------------------- |
| Splits     | Rows                        | Columns                         |
| Goal       | Performance & scalability   | Reduce I/O & isolate large data |
| Common use | Time-based or category data | Large or rarely-used columns    |
| Example    | Orders by year              | User profile images             |

---

### Simple Rule of Thumb

* **Horizontal = “more rows? split them”**
* **Vertical = “too many or heavy columns? split them”**

If you want, I can also explain this in **system design context**, **Spring Boot/JPA**, or **real production databases (Oracle, Postgres, MySQL)**.
   