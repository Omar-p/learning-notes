## Transaction Isolation Levels

Transaction isolation levels define how concurrent transactions interact in a database. They determine the visibility of uncommitted changes across transactions and help balance **data consistency**, **concurrency**, and **performance**.

> 💡 Higher isolation levels reduce concurrency anomalies but typically increase locking or use versioning mechanisms.

---

### 🔍 Key Concurrency Anomalies

| Anomaly                 | Description                                                                                                                                                   |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Dirty Read**          | Reading data modified by another transaction that hasn't been committed yet.                                                                                  |
| **Non-Repeatable Read** | Reading the same row multiple times within a transaction yields different results due to commits by other transactions.                                       |
| **Phantom Read**        | A query returns different sets of rows when re-executed within the same transaction because of new rows inserted or deleted by another committed transaction. |

#### 📌 Examples

<details>
<summary><strong>Dirty Read</strong></summary>

```sql
-- Transaction A
UPDATE Accounts SET balance = balance - 100 WHERE id = 123; -- Not committed

-- Transaction B
SELECT balance FROM Accounts WHERE id = 123; -- Sees balance = 900

-- Transaction A
ROLLBACK; -- Balance reverts to 1000

-- ❗ Transaction B read invalid data.
```

</details>

<details>
<summary><strong>Non-Repeatable Read</strong></summary>

```sql
-- Transaction A
SELECT name FROM Users WHERE id = 456; -- Reads "Alice"

-- Transaction B
UPDATE Users SET name = 'Alicia' WHERE id = 456; COMMIT;

-- Transaction A
SELECT name FROM Users WHERE id = 456; -- Now reads "Alicia"
```

</details>

<details>
<summary><strong>Phantom Read</strong></summary>

```sql
-- Transaction A
SELECT COUNT(*) FROM Products WHERE category = 'Electronics'; -- Returns 10

-- Transaction B
INSERT INTO Products (name, category) VALUES ('New Phone', 'Electronics'); COMMIT;

-- Transaction A
SELECT COUNT(*) FROM Products WHERE category = 'Electronics'; -- Returns 11
```

</details>

---

## 📊 SQL Isolation Levels (Weakest ➝ Strongest)

### 1. 🟡 `READ UNCOMMITTED`

* **Description**: Can read uncommitted changes from other transactions.
* **Allows**: Dirty Reads, Non-Repeatable Reads, Phantom Reads.
* **Use Case**: Rare; only for performance-critical, low-integrity scenarios (e.g., analytics dashboards).
* **Example**: Stock price feeds where stale/incorrect data is tolerable for performance.

---

### 2. 🟠 `READ COMMITTED`

* **Description**: Only reads committed data. Prevents dirty reads.
* **Allows**: Non-Repeatable Reads, Phantom Reads.
* **Use Case**: E-commerce apps; ensures users see stable data, but minor fluctuations (like stock count) are acceptable.
* **Example**:

  * User checks stock: sees 5 units.
  * Another user purchases 1 unit and commits.
  * First user rechecks: sees 4 units.

---

### 3. 🟢 `REPEATABLE READ`

* **Description**: Prevents dirty and non-repeatable reads by holding locks on rows read.
* **Allows**: Phantom Reads (in some systems).
* **Use Case**: Financial apps that require stable reads during multi-step calculations.
* **Example**:

  * Calculate total balance across user accounts.
  * Locks prevent other transactions from modifying those balances, but not from inserting new accounts.

---

### 4. 🔵 `SERIALIZABLE`

* **Description**: Strictest level. Transactions behave as if executed sequentially.
* **Prevents**: Dirty Reads, Non-Repeatable Reads, Phantom Reads.
* **Use Case**: High-integrity operations like money transfers, audit logging, and complex reporting.
* **Example**:

  * Transferring funds: ensures no interference with account balances during read/write.

---

### 🧪 Summary Table

| Isolation Level      | Dirty Read | Non-Repeatable Read | Phantom Read | Use Case        |
| -------------------- | ---------- | ------------------- | ------------ | --------------- |
| **Read Uncommitted** | ✅          | ✅                   | ✅            | Analytics       |
| **Read Committed**   | ❌          | ✅                   | ✅            | Online Stores   |
| **Repeatable Read**  | ❌          | ❌                   | ✅            | Financial Apps  |
| **Serializable**     | ❌          | ❌                   | ❌            | Banking, Audits |

---
