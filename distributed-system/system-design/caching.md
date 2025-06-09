# Caching Policies

Caching policies dictate how data is written to both the cache and the underlying persistent storage. Choosing the right policy is crucial for balancing performance, data consistency, and data durability.

---

## Caching Policies

### 1. Write-Through

* **Explanation:**
  Data is written simultaneously to both the cache and the persistent storage (e.g., database, disk). The write operation is considered complete only after both have recorded the data.

* **Process:**

  1. Application writes data.
  2. Data is written to the cache.
  3. Data is immediately written to the persistent storage.
  4. Confirmation is sent back to the application.

* **Use Cases:**

  * **High Data Consistency Requirements:** Suitable for financial transactions, user authentication data, or critical configurations.
  * **Read-Heavy Workloads with Frequent Writes:** Ensures fast reads post-write.

* **Efficiency (Pros):**

  * High data consistency and durability.
  * Simplified recovery on cache failure.
  * Cache is always warm for recent writes.

* **Cons:**

  * Higher write latency.
  * Increased network/I/O traffic.
  * Persistent storage can become a bottleneck.

---

### 2. Write-Back (Write-Behind)

* **Explanation:**
  Data is written to the cache and acknowledged immediately. The persistent storage is updated asynchronously.

* **Process:**

  1. Application writes data.
  2. Data is written to the cache.
  3. Confirmation is immediately sent back to the application.
  4. Later, data is written to persistent storage based on specific triggers.

* **Mechanisms for Asynchronous Persistence:**

  * **Timeout-Based:** Flush dirty data after a set interval.
  * **Event-Based:** Flush on shutdown, threshold, or explicit command.
  * **Replacement-Based:** Flush dirty data on eviction (e.g., LRU policy).

* **Use Cases:**

  * Write-heavy workloads where durability is less critical (e.g., logs, real-time analytics, sessions).
  * Absorbs bursty writes and smooths them out.

* **Efficiency (Pros):**

  * Extremely low write latency.
  * Coalesces multiple updates into fewer persistent writes.
  * Handles high throughput.

* **Cons:**

  * Risk of data loss on cache failure.
  * Recovery can be complex.
  * Temporary inconsistency between cache and DB.

---

### 3. Write-Around

* **Explanation:**
  Data is written directly to the persistent storage, bypassing the cache. Data only enters the cache on a subsequent read.

* **Process:**

  1. Application writes data.
  2. Data is written to persistent storage.
  3. Confirmation is sent back to the application.
  4. If later read, data is fetched from storage and cached.

* **Use Cases:**

  * Infrequently read or archival data.
  * Large datasets where cache space is valuable.

* **Efficiency (Pros):**

  * Prevents cache pollution.
  * Simpler architecture.
  * No need to track dirty data.

* **Cons:**

  * Higher read latency on first read after write.
  * Caching benefit only starts after first read.
  * No acceleration of write performance.

---

## Summary and Comparison

| Feature              | Write-Through                               | Write-Back (Write-Behind)                   | Write-Around                        |
| -------------------- | ------------------------------------------- | ------------------------------------------- | ----------------------------------- |
| **Write Flow**       | Cache & Persistent Storage (Sync)           | Cache (Async to Persistent Storage)         | Persistent Storage (Bypasses Cache) |
| **Write Latency**    | High (wait for both)                        | Very Low (only to cache)                    | Medium (same as persistent storage) |
| **Read Latency**     | Low (if cached)                             | Low (if cached)                             | High for first read, low after      |
| **Data Durability**  | High                                        | Low (risk of loss on crash)                 | High                                |
| **Data Consistency** | High (always in sync)                       | Temporary inconsistency possible            | High                                |
| **Cache Pollution**  | Can pollute with rarely read data           | Can pollute with rarely read data           | Minimal                             |
| **Best Use Case**    | High consistency, read-heavy w/ some writes | Write-heavy, low-latency, high availability | Infrequently read or large datasets |
| **Complexity**       | Low                                         | High (dirty tracking, flush logic)          | Low                                 |

---

## Write-Back Cache Example: Social Media User Profile Updates

**Scenario:** A social media platform uses a distributed cache (like Redis) in front of a relational DB (e.g., PostgreSQL) for user profiles.

### When a user updates their profile:

1. `PUT /users/{id}/profile` is called.
2. Data is written to the cache.
3. A success response is returned.
4. Cache marks this data as **dirty**.

---

### Triggers for Asynchronous Persistence

#### 1. Timeout-Based

* **Mechanism:**
  Background job flushes dirty data at intervals (e.g., every 30 seconds).

* **Example:**

  * 11:30:00 — User A updates their profile.
  * 11:30:10 — User B updates theirs.
  * 11:30:20 — User A updates again.
  * 11:30:30 — Flush job runs, writes both users’ profiles to DB.

* **Benefit:**
  Eventually consistent, good for frequently changing data.

* **Risk:**
  Cache failure before flush (e.g., 11:30:29) means data is lost.

---

#### 2. Event-Based

* **Mechanism:**
  Flush dirty entries on defined events.

* **Examples:**

  * **Shutdown:** Cache flushes all dirty data before termination.
  * **Threshold Reached:** E.g., 80% of cache entries are dirty.
  * **Explicit Trigger:** Admin or system sends flush command.

---

#### 3. Replacement-Based

* **Mechanism:**
  Dirty data is written to persistent storage just before eviction.

* **Example:**

  * Cache uses LRU policy.
  * A user profile marked as dirty is about to be evicted.
  * The system writes the profile to PostgreSQL before removal.

* **Benefit:**
  Efficient persistence for hot data, avoids unnecessary writes.

* **Risk:**
  Cold but important data might be lost if not evicted before failure.
