# Schema:
- namespace to organize naming of the object.
- postgres create by default schema called public.
  - [schema_name].[object_name]

- when u access a table without specifying a schema name, pg searches for that table  by using the [schema search path], <br>which is a list of schemas to look in.
- The first schema in the search path is called the current_schema. 
- PostgreSQL will access the first matching table in the schema search path. 
  - If there is no match, it will return an error, even the name exists in another schema in the database.

- $ `SELECT current_schema;`

- To view the current search path, you use the SHOW command in psql tool:
  - $ `SHOW search_path;``

- change schema search path:
  - $ `SET search_path TO [schema_name];`

- Users can only access objects in the schemas that they own.
  - To allow users to access the objects in the schema that they do not own, you must grant the USAGE privilege of the schema to the users:
    - $ `GRANT USAGE ON SCHEMA schema_name TO role_name;`

- To allow users to create objects in the schema that they do not own, you need to grant them the CREATE privilege of the schema to the users:

  - $`GRANT CREATE ON SCHEMA schema_name TO user_name;`
  
  - $ `[CREATE|ALTER|DROP] shema_name;`

---
# TABLESPACE: 
- A tablespace is a location on the disk where PostgreSQL stores data files containing database objects e.g., indexes, and tables.

- pg uses a tablespace to map a logical name to a physical location on disk.

- PG comes with two default tablespaces:
    - pg_default tablespace stores user data.
    - pg_global tablespace stores global data.

- benefits:
  - if a partition on which the cluster was initialized is out of space, you can create a new tablespace<br> on a different partition
  and use it until you reconfigure the system.
  
  - you can use statistics to optimize database performance. 
      - For example, you can place the frequent access indexes or tables on devices that perform very fast e.g., solid-state <br>devices, and put the tables containing archive data which is rarely used on slower devices.
  
  - TO CREATE:
    - $ `CREATE TABLESPACE tablespace_name OWNER user_name LOCATION directory_path;`  
  
  - To create table, index, database in another tablespace other than 
  the default. specify it in the creation.
    - $ `CREATE DATABASE [table_name] TABLESPACE ts_primary;`
      
---
- Sequence[relkind = 'S']:
  - CREATE SEQUENCE [ IF NOT EXISTS ] sequence_name<br>
    [ AS { SMALLINT | INT | BIGINT } ]<br>
    [ INCREMENT [ BY ] increment ]<br>
    [MINVALUE minvalue | NO MINVALUE ]<br>
    [ MAXVALUE maxvalue | NO MAXVALUE ]<br>
    [ START [ WITH ] start ]<br>
    [ CACHE cache ]  ->  (determines how many sequence numbers are preallocated and stored in memory for faster access. One value can be generated at a time.  default 1)<br>
    [ [ NO ] CYCLE ]<br>
    [ OWNED BY { table_name.column_name | NONE } ]

  - when you use the SERIAL pseudo-type for a column of a table, behind the scenes, 
    PostgreSQL automatically creates a sequence associated with the column.
----
- If a column alias contains one or more spaces, you need to surround it with double quotes like this:
  - first_name || ' ' || last_name AS "full name",

---

- ORDER BY sort_expresssion [ASC | DESC] [NULLS FIRST | NULLS LAST]

---
- DISTINCT ON (cols,..) must match the prefix of cols in ORDER BY clause:
  - select first row in each group.
  - With DISTINCT ON, you tell PostgreSQL to return a single row for each distinct group defined by the ON clause. Which row in that group is returned is specified with the ORDER BY clause.

- https://www.geekytidbits.com/postgres-distinct-on/

---
- WHERE
    - The condition must evaluate to true, false, or unknown. It can be a boolean expression
      or a combination of boolean expressions using the AND and OR operators.
    - If you use column aliases in the SELECT clause, you cannot use them in the WHERE clause.
      -> because query evaluates as: FROM -> WHERE -> SELECT -> ORDER BY
---
- LIMIT row_count OFFSET row_to_skip;
  - If row_count is zero, the query returns an empty set. In case row_count is NULL, the query returns the same result set as it does not have the LIMIT clause.  
    
  - The statement first skips row_to_skip rows before returning row_count rows generated  by the query. If row_to_skip is zero, the statement will work like it doesn’t have the OFFSET clause.  
---
- FETCH is a SQL standard, LIMIT is not.
  - OFFSET start { ROW | ROWS }<br>    FETCH { FIRST | NEXT } [ row_count ] { ROW | ROWS } ONLY
    
    - start is an integer that must be zero or positive. By default, it is zero.
      - In case the start is greater than the number of rows in the result set, no rows are returned;
  - OFFSET clause must come before the FETCH clause in SQL:2008, IN postgres 
    any order of them  will work.
  
  - FETCH FIRST and FETCH NEXT do exactly the same thing. The reason both exist 
    because of the preceding OFFSET clause. Using the word FIRST combined with 
    OFFSET can be confusing to a human reader.
  - SELECT film_id, title FROM film ORDER BY title OFFSET 5 ROWS FETCH FIRST 5 ROW ONLY; *   
---
- % many
- _ one
- PostgreSQL supports the ILIKE operator that works like the LIKE operator.  In addition, the ILIKE operator matches value case-insensitively.
  
- ~~	LIKE
- ~~*	ILIKE
- !~~	NOT LIKE
- !~~*	NOT ILIKE

---
- Inner Join:
  - The inner join examines each row in the first table (basket_a). It compares the value 
    in the fruit_a column with the value in the fruit_b column of each row in the second 
    table (basket_b). If these values are equal, the inner join creates a new row that contains 
    columns from both tables and adds this new row the result set.
  
  - When you join a table to itself (a.k.a self-join), you need to use table aliases. 
    This is because referencing the same table multiple times within a query results in an error. 
 
- Left Outer Join, Right Outer Join, Full Outer Join.
---

# Data Types:
  - Big Serial is backed by a sequence.
    
---frontendmasters---

- INSERT 0 0
  - first zero it used to be an identifier for an objectId, which the don't use it anymore but it there because backward compatibility 

- problem with the offset:
  - if one user paginate the rows and in the some time some user delete row from the same page, <br> the first row in the next page will not be appeared to the first user when he go to the next page.
  - better approach is to page paginate them over a key, and instead using offset use WHERE with LIMIT <br> that also is has more performance than OFFSET with LIMIT
---
- `case 
  when predicate then
  else 'default val'
end`

- ` case  colName
  when val then
  else 'default val'
end`

---
- group by 1 -> 1 refer to first expression in the select clause. 
  - [instead of rewriting it in case of complex expression]
---
- u can use case expression in aggregate function and in group by clause.

- PG: u can use filter function with aggregate: count (*) filter(where rating in ('R', NC-17))
---
# Replication:
- Replication is the process of copying data from one database server to another database server. The source database server  <br> is usually called the Primary Server, whereas the target database server is called the Standby Server.

- Models of replication
  - Single-master and multi-master replication:
    - In single-master replication (SMR) changes to table rows in a designated primary database server are <br>
  replicated to one or more standby database servers. The replicated tables in the standby <br>
  database are not permitted to accept any changes (except from the primary) and even if they do, <br> 
  changes are not replicated back to the primary server. This is also called unidirectional replication. <br>

  - In multi-master replication (MMR) changes to table rows in more than one designated primary <br>
  server are replicated to their counterpart tables in every other database. In this model, <br>
  conflict resolution schemes are often employed to avoid duplicate primary keys, for example. <br>
  This is also called bidirectional replication.

- Asynchronous and synchronous
  - In the synchronous mode of replication, transactions on the primary database are declared <br>
  complete only when the changes have been replicated to all the standby servers in addition <br>
  to the primary. All standby servers have to be available all the time for the transactions <br>
  to complete on the primary.

  - In the asynchronous mode the transactions on the primary server are declared complete when <br>
   the changes have been done on the primary server. These changes are replicated to the standby <br>
   servers later. In this mode, the standby servers can remain out of sync for a certain duration, <br>
    which is called the replication lag.

- Physical and logical
  - Physical replication deals with files and directories; it has no knowledge of what these <br>
  files and directories represent. It is done at the filesystem or disk level.

  - Logical replication on the other hand, deals with databases, tables, and DML operations. <br>
  It is therefore possible in logical replication to replicate a certain set of tables only. <br>
  This is done at the database-cluster level.

- What is WAL?
  - In PostgreSQL both physical and logical replication is based on write-ahead logging (WAL). <br>
  All changes made in PostgreSQL by every transaction are first saved in a log file and then <br>
   the result of the transaction is sent to the initiating client. Data files are not changed <br>
   on every transaction. This is a standard mechanism to prevent data loss in case of situations <br>
   like OS crash, hardware failure, PostgreSQL crash, etc. This mechanism is called write-ahead <br>
   logging and the log file is called the write-ahead log (WAL).<br>

  - Each change that the transaction performs (INSERT, UPDATE, DELETE, COMMIT) is written in the log<br>
    as a WAL record. WAL records are first written into an in-memory WAL buffer. On transaction commit,<br>
      the records are written into a WAL segment file on the disk.

  - The log sequence number (LSN) of a WAL record represents the location/position where it is saved in the log file. <br>
    - The LSN is used as a unique ID of the WAL record. Logically the transaction log is a file whose size is 2^64 bytes.
    - The LSN is therefore a 64-bit number represented as two 32-bit hexadecimal numbers separated by a /.

    - For example:
      - `SELECT pg_current_wal_lsn();`

    - In the event of a system crash, the database can recover committed transactions from the WAL. <br>
    While recovering, PostgreSQL starts recovery from the last REDO point or checkpoint.

    - A checkpoint is a point in the transaction log at which all data files have been updated to reflect <br>
    the information in the log. The process of saving the WAL records from the log file to the actual <br>
    data files is called checkpointing.

=
# WAL-based replication
- *Continuous WAL archiving*
  - Copying WAL files as they are generated into any location other than pg_wall subdirectory for<br>
   the purpose of archiving them is called WAL archiving. To archive, a script provided by the user<br>
    is invoked by PostgreSQL each time a WAL file is generated. The script can use scp command to copy the file<br>
     to one or more locations. The location can be an NFS mount. Once archived, the WAL segment files can<br>
      be used to recover the database to any specified point in time.

- *Log shipping-based replication: file level*
  - Log shipping is the process of copying log files to another PostgreSQL server for the purpose of creating another standby<br>
   server by replaying WAL files. This server is configured to be in recovery mode. The sole purpose of this server is to <br>
   apply any new WAL files as they arrive. This second server then becomes a warm backup of the primary PostgreSQL server <br>
   also termed as standby. The standby can also be configured to be a read replica, where it can also serve read-only queries. <br>
   This is called a hot standby.

- *Log shipping-based replication: block level*
  - Streaming replication improves the log shipping process. Instead of waiting for the WAL switch, the records are sent as <br>
  and when they are generated thus improving replication lag. The second improvement is that the standby server will connect <br>
  to the primary server over the network, using a replication protocol. The primary server can then send WAL records directly <br>
  over this connection without having to rely on scripts provided by the end user.<br>

#### How long should the primary retain WAL segment files?
  - Without any streaming replication clients, the server can discard/recycle the WAL segment file once the archive <br>
  script reports success (unless they are not required for crash recovery). In the presence of standby clients, however, <br>
  there is a problem: the server needs to keep WAL files for as long as the slowest standby needs them. If the standby <br>
  that was taken down for a while comes back online and asks the primary for a WAL file that the primary no longer has, <br>
  then the replication fails with an error similar to:

##### ERROR: requested WAL segment 00000001000000010000002D has already been removed<br>
  - The primary should therefore keep track of how far behind the standby is, and to not delete/recycle WAL <br>
    files that any standbys still need. This feature is provided through replication slots. Each replication<br>
    slot has a name which is used to identify the slot. Each slot is associated with:
      - (a) The oldest WAL segment file required by the consumer of the slot. WAL segment files later than this are not 
      deleted/recycled during checkpoints. (b) The oldest transaction ID required to be retained by the consumer of the slot. 
      Rows needed by any transactions later than this are not deleted by vacuum.

---

- Physical streaming replication
- Streaming replication is the process of sending WAL records as and when they are generated to another
 - PostgreSQL server for the purpose of creating a standby server by replaying WAL records. The standby server  is configured to be in recovery mode. The sole purpose of this server is to apply any new WAL records when they  arrive. This second server then becomes a warm backup of the primary server. The standby can also be configured  to be a read replica, where it can also serve read-only queries. This is called a hot standby.

- In streaming replication the standby server connects to the primary server and receives WAL records using a replication protocol. This provides two advantages when compared to log shipping:
  - 1  The standby server does not need to wait for the WAL file to fill up, hence the replication lag is improved.
  - 2  The dependency on the user-provided script and an intermediate shared storage between the servers is removed.

- WAL sender and WAL receiver
- A process known as the WAL receiver, running on the standby server connects—using the connection details provided in the  primary_conninfo parameter of postgresql.conf—to the primary server using a TCP/IP connection. In the primary server, another process, called the WAL sender, is in charge of sending the WAL records to the standby server as and when they are generated.

- The WAL receiver saves the WAL records in the WAL as though they were generated by the client activity of locally connected clients.
  - Once the WAL records reach the WAL segment files, the standby server constantly keeps replaying the WAL so that standby and primary are up to date.
---
- Logical replication
    - Physical streaming replication creates a byte-by-byte read-only replica of the primary server. The replica contains all the databases, tables, roles, tablespaces, etc., of the original. With streaming replication we  get all or nothing. But what if we want a replica of only a single table? This is where logical replication comes into play.

- Logical replication can replay DML operations happening on a subset of tables in a primary server on a standby server by:
  - Logically decoding the WAL records
  - Streaming them over to the standby server
  - Applying them to the table in the standby server in the correct transactional order

- Publication and subscription
  - Logical replication defines two entities: a publisher and a subscriber.
  - A publisher is a node that defines a certain group of tables (called a publication) to which a subscriber can subscribe,  by creating a subscription to receive the changes to that particular group of tables.


---
## Advanced SQL for Query Tuning and Performance Optimization
- Joining Table
  - 1- Nested Loop Join
    - Compare all rows in both tables to each other.
    - simple to implement but expensive.
  - 2- Hash Join
    - Calculate hash value of key and join based on matching hash values missing.
    - can be used in equality comparison.
  - 3- Sort Merge Join
    - Sort both tables and then join rows while taking advantage of the sorted order.
    - same as nested loop join but it stops when it is not possible to find a match.
      - Unlike nested loop joins, the driving table is processed only once.
- Partitioning data
  - A partition key is a column in the table that's used to determine which partition will store the data. 
    - It's common for a partition key to be based on time. For example, we could create one partition for each month and store data generated in a month in its corresponding partition. 
  - We can use indexes with partitions. 
    - Local indexes are used to improve the access time to rows in particular partitions. 
  - We can also have global indexes that provide faster lookups to any data in the table across all partitions. 
    - This kind of index is helpful if you need to filter data on something other than the partition key and when the data is spread across multiple partitions.
- Explain and Analyize
  - Cost is a measure of computation required to complete the step.
  - We also see the number of rows returned and the width of the row. This gives us an indication of how much data is returned.
  - sometimes adding additional steps will lead to less data being returned, but actually increase the time that a query takes to execute.
    - like adding where clause to the query. it will add filter step to the query plan.
  - even though we do have an index on a table, the index may not always be used. 
    - And again, the query plan builder is trying to build a plan which will take the least amount of time overall.<br> So it'll take into consideration time to, for example, look up data that's in an index and then go look up additional <br> data that's in a table. And so only when the conditions are highly selective are indexes used, when those <br>conditions are so selective that they reduce the number of rows returned sufficiently so that it basically makes <br>the query plan builder favor using an index rather than doing the brute force method of a fold table scan.
- Indexing
  - The big advantage of indexes is that they reduce the need for full scans.
  - Another factor that makes indexes so helpful for querying is that indexes tend to be smaller than their corresponding tables. This means that they're more likely to fit in memory. That's great news for querying, because reading data from memory is much faster than reading from hard disks, or even solid state drives, or SSDs.
  - some types
    - B-trees, or balance trees, are commonly used. 
      - B-trees are the most commonly used type of index. It's used when there are large number of distinct values in a column. This is called a high cardinality
    - Bitmap indexes are used when columns have few distinct values, and this is known as having a low cardinality. 
      - Bitmap indexes store a series of bits for indexed values. The number of bits used is the same as the number of distinct values in a column.
      - we can perform Boolean operations quickly. For example, anding two bitmaps is a fast operation. 
      - Bitmaps are used with low cardinality columns, especially when we want to filter on those columns or perform Boolean operations like and, or, or not. 
      - <b>DisAdvancage</b>: While bitwise operations are fast, updating bitmap indexes can be more time consuming than other indexes, so they tend to be used in read intensive use cases like data warehouses. 
      - Some databases like Oracle lets you create explicit bitmap indexes, but Postgres does not. Instead, Postgres will calculate bitmap indexes on the fly when they can be used to improve query performance.
    - Hash indexes are used when we need to look up values in a key value form. 
      - They're used only for equality comparisons. Hash values won't help if you want to filter on a range of values. 
      - email column is a good candidate for a hash index.
      - recent versions of Postgres have made improvements on hash indexes, and now, these hash indexes can be smaller in some cases than B-tree indexes but just as fast.
    - Now, Postgres also has specialized indexes, like geospatial, which are designed for specific data types.
      - GiST: The GiST index, which stands for generalized search tree, is not a single type of index. It's more like a framework for implementing custom indexe.
      - SP-GiST: The GiST index, which stands for generalized search tree, is not a single type of index. It's more like a framework for implementing custom indexe
      - GIN: used for text indexing. 
        - lookups with GIN are faster than with GiST, but the builds can be slower than they are with GiST
        - GIN indexes can be two to three times larger than GiST.
      - BRIN: a
        - Block range indexing Used for large data sets
        - Divides data into ordered blocks -> Keeps min and max values -> Search only blocks that may have match
  - Bloom Filters: 
    - Probabilistic and space-efficient data structure
    - Lossy representation of indexed attributes
    - May report false positives
    - Most useful when querying arbitrary combinations or a large number of attributes
    - B-tree indexes faster but require more indexes
    - CREATE INDEX cust_blm_idx ON customer_features USING bloom (c1, c2, c3, c4, c5, c6, c7, c8) WITH (length=160);
    - Chance of false positive decreases as length increases
    - Works with int4 and text data types
    - it's not part of the Postgres core: CREATE EXTENSION IF NOT EXISTS bloom;
- Materalized View
  - we'll have to update or refresh materialized views to capture changes to the sources of those materialized views.
    - there is a potential for inconsistencies between source tables and materialized views.
  - a good options when saving time on query response is more important than minimizing the amount of storage used.
  - You should only use materialized views if you can tolerate some potential inconsistencies, 
    - or you can ensure that the materialized views are refreshed after each update to the source.
    - `refresh materialized view {mv_name}`
    - If you need the materialized view updated any time the source table is updated, you can consider using triggers on the source table to update the materialized view.
- Statistics
  - statistics schema contains{PG_Stats is a module and a set of views that provide access to information that's stored in the PG_STATISTICS catalog.}
    - #rows, amount of storage used.
    - frequency of values; {fraction of null, #distinct values, frequenct values}
    - Distribution
      - histogram describe the spread of values in a column.
        - normal distribution: most values are close to the mean.
        - skewed distribution: most values are far from the mean.
          - positive skew: most values are less than the mean.
          - negative skew: most values are greater than the mean.
        - Histograms count the number of values in a range and give us a good way of representing the distribution of data.
    - How we get those statistics:
      - run ANALYZE command.
        - `ANALYZE [table_name]`
        - run automatically by AUTOVACUUM daemon or manually.
      - If you use just the VACUUM command, you'll reclaim some space and mark it so that it's available for reuse by the table. 
        - VACUUM full on a particular table, that will lock the table and it can reclaim even more space. This method does require more disk space than the non-full vacuums because actually what it does is it writes a new copy of the table and it doesn't release the old copy of the table until the operation's finished. 
        - VACUUM FULL is recommended only when a significant amount of space needs to be reclaimed from within the table. 
        - If you want to collect statistics while reclaiming data in a table, you can do that by running the `VACUUM (FULL, ANALYZE)` command on a particular table.  
      - REINDEX: if there are bugs in the system, you may come across corrupted indexes. Re-indexing collects unused pages in B-tree indexes, and you can re-index on a single index or on a table or an entire schema. 
  - pg_stat_statements, it's a feature for tracking, planning, and execution statistics of all the SQL statements that a server executes. Now, pg_stat_statements, basically, it needs to be turned on. It does take some overhead, so it's off by default, but we can actually turn it on by adding a pg_stat_statements to the shared_preload_libraries section in the Postgres configuration file. 
    - It includes information on, like, the user, the database, and the query ID, as well as the query text, so you actually get to see what the text of the query was. Then, there's also the plan statistics that are provided inside pg_stat_statements, and these include things like the number of times the query was planned and the total time that was spent on those planning operations.
  - it's really helpful, especially in production environments, to be able to understand the query plans that are being executed in production and especially for slow running queries: 
    - there is a module in postgres called auto explain that will automatically log query plans for slow running queries automatically.
    - to use it :
      - Load auto_explain module during session as superuser or load in postgresql.conf
    - auto_explain.log_min_duration is the minimum statement execution time that will cause the statement's plan to be logged
    - auto_explain.log_nested_statements causes nested statements to be included in logging
    - auto_explain.log_level selects the log level at which auto_explain will log the query plan, for example, INFO, WARNING, DEBUG1, DEBUG2..DEBUG5
  - PG_Stats is a module and a set of views that provide access to information that's stored in the PG_STATISTICS catalog.
- Join
  - Nested Join
    - they will work with any kind of join, using either equality, inequality, or range operators
    - The outer loop executes once, and the inner loop runs once for each row in the join table.
    - low overhead. We don't need to create other data structures or sort tables before looking for matches between foreign keys and primary keys. 
    - works well with small tables, especially if the large table has an index on the join column.
    - disadvantages:
      - nested loops can be slower than other join methods. 
      - If a table does not fit in memory, then we'll get an even bigger performance hit, because rows will have to be read from persistent storage. 
    - Indexes can improve the performance of nested loops. This is especially the case if your index is a covered index <br> and it's a good technique to keep in mind when tuning queries.
  - Hash Join
    - Part of the process of hash joining is building a table of hash values. The smaller of the two tables is used. 
    - The next step is called the probe phase. In this step, we calculate the hash value of the key in each row, <br>and then look up the corresponding value in the hash table. The hash table contains a pointer or a reference to <br>the location of the row we're joining to. 
    - It's important to note that hash joins only work with the equality conditions, <br>they don't help when using inequality operators in the join. 
  - Merge Join
    - sort the input table prior to looking up rows by key values. By sorting, the algorithm can take advantage of ordering of<br> the tables to reduce the number of rows that have to be checked. 
    - merge joins is that they are only used with equality join conditions. 
    - The time required to execute the join is based on table sizes and how long it takes to sort and scan. 
    - Merge joins are especially useful when large tables don't fit into memory because we can do fewer joins from persistent storage <br>than we would likely need to do if we were using a nested loop join.
  - Join-vs-Subqueries
    - same logical outcome
    - the advice is to use join because they are more efficient. but, Query plan builders are more effective at optimizing subqueries now.
      - so optimize for clearity, Both will work well in many cases. Opt for what makes intention clear.
- Partitioning
  - Horizontal vs Vertical partioning
    - Horizontal Partioning: 
      - split tables by rows into partitions 
      - treat each partition like a table
      - benefits
        - Limit scans to subset of partition
        - local index for each index (smaller index)
        - efficient bulk operations (subset or all) (adding/deleting is more efficient because indexes that need to maintain are small)
      - uses
        - Data Warehous (by time)
        - Timeseries data 
          - most likely query latest data
          - summarize data in older partitions
        - Naturally Partition Data in some are doesn't require time
          - Retailer, by geograpghy
          - Data science, by product category.
    - Vertical Partitioning
      - separate column into multiple tables
      - keep frequently queried columns together.
      - same PK.
      - benefits
        - increases #rows in data block
          - read less data to staisfy query, reduce I/O 
        - Global indexes for each partition
        - columnar data storage offer similar benefits.
      - uses
        - Data Warehous (partiton on groups of attr)
        - Many Attributes (wide variety of products, each with different attr)
        - Data Analytics
          - stats on subset of attr; after factor analysis(determine which attrs are most important)
  - Range Partitioning
    - horizontal partitioning. 
    - Partitions are created based on non-overlapping partition keys. 
    - partitioning key type 
      - time (most common)
      - numeric ranges  
      - alphabetic ranges. 
    - Partitions are bounded by minimum and maximum values on the partition key.
    - each partition can have its own indexes and constraints.
    - TO clause is not inclusive.
    - Use Cases:
      - when we typically query the latest data
      - perform comparative queries between periods of time, such as a report on this month's sales compared to last year's sales in the same month.
      - If you drop data after a certain amount of time, partitioning can make the deleting operation more efficient because the entire partition can be deleted.
    - ex: https://www.postgresql.org/docs/current/ddl-partitioning.html#:~:text=the%20measurements%20table.-,To%20use%20declarative,-partitioning%20in%20this

  - Partitioning By List
    - the partition values are defined in a list of values. 
    - The partition bounds, are the list of values allowed in the partition. 
    - we have to list the values of the partition key, that will be allowed in that partition. 
    - Use Cases:
      - when data logically groups into subgroups, based on the partition key values. For example, we could have a partition for clothing, and another for electronics. This works well <.<br>when most queries are within a partition. For example, we might report on clothing or electronics individually, but rarely compare the two. 
    - List partitioning is a good option, when your data is not so time-oriented, that date-based partitioning would make more sense.
  - By Hash (horizontal)
    - Use Cases:
      - We use hash partitions when the data does not logically group into subgroups
      - we want to maintain a fairly even distribution across partitions. 
      - there's no need for subgroup specific operations such as dropping a particular partition.
- Other Optimization Techniques
  - common table experession {with name AS [MATERIALIZED] () Q}
    - often used as an alternative to subqueries 'cause many of us find them often more readable and more easy to reuse than using subqueries. in long-running queries.
    - non-materialized or inline is the default in Postgres or at least it has been since Postgres 12.  
  - Hints
    - suggestions to a query plan builder.
    - inline suggestions are outside of normal SQL statements, they're are extra-SQL statements, and this practice really pushes the boundary between declarative and procedural code.
    - postgresql is not support hints but we can use SET to prevent the use of hash joins, or you can make sure that nested loops are enabled.
    - <b>it's better to update statistics with ANALYZE and VACCUM before trying hints</b> and try other optimization techniques like partitioning and materialized views before trying hints. Be sure a query plan is suboptimal before resorting to changing the query planner configuration
  - Parallel Execution
    - You can tell your execution plan is using parallel queries if there is a GATHER or GATHER MERGE in the query plan. 
    - All Steps below that are executed in parallel.
    - #parallel processes the execution plan will use is based on a couple of parameters; the max_parallel_workers and max_worker_process parameters. 
    - in order to execute some steps in parallel 
      - we need to have the max_parallel_workers_per_gatherer_parameter set to a value greater than zero. 
      - The dynamic_shared_memory_type_parameter must not be set to none
      - the database can't be running in single-user mode.
      - queries must not write data or lock rows. 
      - query must not be using a function that has been marked "parallel unsafe", which occurs with user defined functions. 
    - Parallel plans must exceed parallel_setup_cost and parallel_tuple_cost parameters. 
    - Parallel index scans only work with B-tree indexes. 
    - The inner side of nested loops and merged joins are executed sequentially. 
      - hash joins can be executed in parallel, but each process will create a copy of the hash table. This may be a consideration if you are generating large hash tables.
  - Miscellaneous tips
    - Create indexes on join columns or columns used in WHERE clauses.
    - Using covering indexes can save time.
    - when using function in a WHERE clause, create a functional index for the column.
    - Index Range Scan
      - keep the range as small as possible
      - Use equality conditions
      - LIKE 'ABC%' can use index, LIKE '%ABC%' cannot.
      - USE GIN index for full text search
      - Use index to avoid sorts with using ORDER BY
    - when filtering use continous conditions such as sysdate and sysdate+1
    - Create indexes on join columns or columns used in WHERE clauses.
  - Caching
    - The amount of data stored is dependent on the size of the shared_buffers data structure
    - PostgreSQL will evict data from the cache as needed
    - Explain plans show Hits (reads from cache) and Reads (reads from disk)
      - if you see a lot of misses for repated queries, you may need to increase the size of the shared_buffers parameter.
    - Shared memory buffers defaults to 128 MB
    - Larger shared buffers improve performance
    - Dedicate 25% of memory in your server to shared buffers
    - Shared buffer parameter can only be set when the server starts
---
# NEW
###
- The best architecture for a particular application depends on the business requirements, or the organizational requirements .

- we try to determine what's best for our particular application is we need to understand :
  - how much data needs to be stored, 
  - we need to understand the lifecycle of how long we're going to keep this data
  - who needs to access it. 
- the goal that we're focusing on is trying to identify how we provide storage services that meets the particular set of requirements we have before us .

### Key Questions about Data
- What data? 
  - Structural, semi.., 
  - entity relationship
- How Much Data ?
  - Current Volume, Rate of ingestion, rate of growth, Duration of lifecycle
- How is it used ?
  - Transactions
  - save monitoring data, 
  - saving data for compliance
  - Decision-making
#### What Data (Data modeling) ?
- Identify domains: subject areas defined by coherent business processes and related data. For example, sales, inventory, and logistics.

- Identify entities: the things that we're dealing with within each domain. For example, customers, orders, and order items.

- Model relationships: how do entities relate to each other? For example, a sales transaction relates to a customer and an order relates to multiple order items.

- Identify attributes: what information do we need to keep on each entity? For example, name, address, phone number, and email for a customer.

- Understand how data changes over time: we may need to model things differently in the future. 

#### how to plan volume
## Estimating future data size

### Summary

To estimate future data size, we need to consider the following:

* **Current data size:** How much data do we have today?
* **Domain-driven events:** These are events that occur within our business domains, such as sales, shipments, and IoT sensor readings. We need to understand the frequency and size of these events to estimate how much data they will generate in the future.
* **Expected rate of growth:** What other factors are likely to increase the size of our data in the future, such as new business initiatives or customer growth?

### Example

* **Current data size:** 1 petabyte
* **Domain-driven events:**
    * Sales: 10,000 sales per day, each sale generates 10 KB of data
    * Shipments: 1,000 shipments per day, each shipment generates 100 KB of data
    * IoT sensor readings: 1 million readings per day, each reading generates 100 bytes of data
* **Expected rate of growth:** 20% per year

**Estimated future data size:**

- By considering the current data size, domain-driven events, and expected rate of growth, we can estimate how much data we will need to store and process in the future. This information can help us to plan for the necessary infrastructure and resources.

## How is data used?

There are many different ways to use data, but here are just a few examples:

* **Interactive transactions:** This is when data is used to support real-time interactions with users, such as when you make a purchase online.
* **Data aggregation:** This is when data is combined and analyzed to identify trends and patterns. This is often used for business intelligence, data science, and machine learning applications.
* **Anomalous events and time series:** This is when data is used to identify unusual events or patterns over time. This can be used for fraud detection, predictive maintenance, and other applications.

### Conclusion

Data can be used in a variety of ways to improve decision-making, optimize processes, and create new products and services.

- 0202
## Data access patterns in scalable databases
Write-intensive applications require low latency for both writing and reading data, while data warehousing applications are more tolerant of higher latency but query larger data sets. It is important to understand the particular access patterns for the data that is stored in the system.

**Example:** Sales transaction data is both read and written frequently, so the database needs to be able to support both low latency and high throughput.


## Workloads and use cases in database scalability

Workloads are driven by the business's or organization's need for data and are often multi-step, involving multiple tasks that are performed on the data before and after it's stored in the database. Workloads also have dependencies on other workloads and maybe other data sources.

**Example:** A sales transaction workload might involve the following steps:

1. Insert the transaction data into the database.
2. Update the inventory levels for the products purchased.
3. Update the customer's account balance.
4. Send a confirmation email to the customer.

This workload depends on other workloads, such as the inventory management workload and the customer relationship management (CRM) workload. It also depends on other data sources, such as the product catalog and the customer master data.

When designing a scalable database, it is important to consider the workloads and use cases that the database will need to support. This includes understanding the following:

* The types of workloads that will be running on the database.
* The dependencies between workloads.
* The data sources that the workloads depend on.
* The performance requirements for each workload.

By understanding the workloads and use cases, you can design a database that can handle the expected load and meet the performance requirements.

## Security and compliance

Security: Confidentiality, availability, and integrity of data. Who can do what to which data?

Compliance: Institutional, industry, and government policies and laws that dictate what we can do with data.

## Data integrity

* Identity and access management
* Targeted integrity measures (e.g., message digest)
* Expansive integrity measures (e.g., audits)

- Expansive integrity is a set of measures that are used to ensure the integrity of data across an entire system. This can include measures such as auditing, logging, and checksums. Expansive integrity measures are typically used to detect and prevent data corruption, as well as to identify and investigate unauthorized access or changes to data.

- Targeted integrity is a set of measures that are used to ensure the integrity of specific data elements or subsets of data. This can include measures such as message digests, digital signatures, and encryption. Targeted integrity measures are typically used to protect sensitive or critical data, such as financial transactions or customer information.

## Data compliance

Data compliance is the set of rules that govern how we collect, use, and protect data. It is designed to protect individuals, organizations, and larger social groups and institutions.

Data compliance policies come from different places, including:

* Internal policies: Set by the decision makers in an organization.
* Industry-specific policies: Set by the participants in an industry.
* Government regulations: Specified by law.

Examples of data compliance policies include:

* HIPAA (US healthcare regulation)
* GDPR (EU privacy directive)
* PCI-DSS (payment credit card industry regulation)

The specific policies that apply to your organization will depend on your location and the data you store.

**Example:** A company that sells products online and accepts credit cards will need to comply with internal policies, industry-specific policies (such as PCI-DSS), and government regulations (such as GDPR).

## Analytical databases

- Analytical databases are structured databases designed for large-scale data analysis. They are not relational, so they lack features like indexes and transactions. Examples include Google BigQuery.

- Analytical databases are not suitable for applications like eCommerce that require transactions.
