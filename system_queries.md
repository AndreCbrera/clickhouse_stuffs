Certainly! Here are the most relevant queries from ClickHouse’s system tables to help you identify problems and frequent error types in your cluster, based strictly on the provided documentation.

---

## 🔍 **Essential System Queries for Cluster Health and Error Detection**

### 1. **View the Most Recent and Frequent Errors**
```sql
SELECT *
FROM system.errors
ORDER BY last_error_time DESC;
```
This shows the most recent errors, their codes, how many times they occurred, and the last error message.  
[See example and explanation](https://clickhouse.com/docs/knowledgebase/useful-queries-for-troubleshooting#view-the-most-recent-errors)

---

### 2. **List All Active Errors (Non-zero Occurrences)**
```sql
SELECT name, code, value
FROM system.errors
WHERE value > 0
ORDER BY value DESC;
```
This lists all error types that have occurred, sorted by frequency.  
[system.errors usage](https://clickhouse.com/docs/operations/system-tables/errors)  
[Community debugging insights](https://clickhouse.com/docs/community-wisdom/debugging-insights)

---

### 3. **Detailed Error Log (with Host and Time)**
```sql
SELECT *
FROM system.error_log
ORDER BY event_time DESC
LIMIT 100;
```
This provides a history of error events, including the host, error code, error name, and timestamp.  
[system.error_log](https://clickhouse.com/docs/operations/system-tables/system-error-log)

---

### 4. **Check for Errors in Query Execution**
```sql
SELECT
    event_time,
    query,
    exception_code,
    exception
FROM system.query_log
WHERE exception_code != 0
  AND type = 'QueryFinish'
ORDER BY event_time DESC
LIMIT 100;
```
This shows recent queries that ended with errors, including the error code and message.  
[system.query_log](https://clickhouse.com/docs/operations/system-tables/query_log)

---

### 5. **Monitor Replication Issues**
```sql
SELECT database, table, replica_name, absolute_delay, queue_size, inserts_in_queue
FROM system.replicas
WHERE absolute_delay > 60
ORDER BY absolute_delay DESC;
```
Detects replication lag and potential replication problems.  
[Community debugging insights](https://clickhouse.com/docs/community-wisdom/debugging-insights)

---

### 6. **Diagnose Replication Queue Problems**
```sql
SELECT database, table, replica_name, position, type, create_time, last_exception
FROM system.replication_queue
WHERE last_exception != ''
ORDER BY create_time DESC;
```
Shows replication tasks with exceptions, useful for diagnosing replication failures.  
[Community debugging insights](https://clickhouse.com/docs/community-wisdom/debugging-insights)

---

### 7. **Check for Stuck Mutations**
```sql
SELECT database, table, mutation_id, command, parts_to_do, is_done
FROM system.mutations
WHERE is_done = 0;
```
Identifies mutations (schema/data changes) that are not completed, which can indicate problems.  
[Community debugging insights](https://clickhouse.com/docs/community-wisdom/debugging-insights)

---

### 8. **Monitor Table Health (Too Many Parts)**
```sql
SELECT database, table, count() as part_count
FROM system.parts
WHERE active = 1
GROUP BY database, table
ORDER BY part_count DESC;
```
Tables with too many parts can cause performance issues and errors.  
[Community debugging insights](https://clickhouse.com/docs/community-wisdom/debugging-insights)

---

## 🛑 **Frequent Error Types**

You can find the most frequent error types by running:
```sql
SELECT name, code, value, last_error_message
FROM system.errors
WHERE value > 0
ORDER BY value DESC;
```
This will show you which error types are most common in your cluster, along with their error codes and the last error message.  
[system.errors](https://clickhouse.com/docs/operations/system-tables/errors)

---

> These queries, when run regularly, will help you detect and diagnose most operational problems and frequent errors in your ClickHouse cluster, as described in the official documentation and community best practices.

---

```sql
SELECT
    cluster,
    shard_num,
    replica_num,
    host_name,
    host_address,
    database_replica_name
FROM system.clusters
WHERE cluster = 'your_cluster_name'
ORDER BY shard_num, replica_num
```