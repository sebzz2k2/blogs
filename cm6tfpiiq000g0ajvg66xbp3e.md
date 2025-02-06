---
title: "How Databases Handle Multiple Transactions"
datePublished: Thu Feb 06 2025 14:30:07 GMT+0000 (Coordinated Universal Time)
cuid: cm6tfpiiq000g0ajvg66xbp3e
slug: concrrency-control
tags: dbms, dbms-architecture, dbms-basics

---

Concurrency control is a set of technique by which a database system handles concurrently executing transactions. It is one of the key component in the transaction manager of a database system. It is tasked with ensuring that the concurrent transactions follow the [ACID](https://sebzz.hashnode.dev/acid-properties-of-a-relational-database) principles that a relational database system guarantees.

### Why do we need Concurrency Control?

To understand the need for concurrency control, we first need to define what a schedule and a serial schedule are. A schedule is a list of operations required to execute a set of transactions from a database perspective. When transactions are executed one after another without interleaving, such schedules are called serial schedules. Ideally, all schedules should be serial because this ensures strict adherence to the ACID (Atomicity, Consistency, Isolation, Durability) principles. However, modern computers have multiple cores, and databases are often distributed across multiple servers. This means that database systems need to handle multiple transactions concurrently to improve performance and resource utilization. A straightforward way to enforce a serial schedule is to wait for each transaction to commit before starting the next one. However, this approach has a major drawback—it leads to underutilized resources. In high-traffic scenarios, it would significantly increase transaction processing time, making the system inefficient.

### Key Conflict Scenarios

A transaction can have multiple read and write operations. For simplicity let us assume that each transaction either contain a read or a write operations. Now when two transactions run concurrently four possible scenarios can occur:

1. Read - Read (RR)
    
2. Read - Write (RW)
    
3. Write - Read (WR)
    
4. Write - Write (WW) We don’t need to worry about Read-Read (RR) transactions since they do not cause any conflicts. Reading the same data multiple times does not modify it, so it has no impact on consistency. However, the other three cases can lead to concurrency issues, so let’s explore them with examples.
    

##### Read - Write

Occurs when Transaction T1 reads a value, and Transaction T2 writes to it before T1 finishes. Example:

* T1: Reads balance = 1000 from an account.
    
* T2: Updates balance = 1200.
    
* T1: Still believes balance = 1000, leading to outdated or inconsistent data.
    

##### Write - Read

Occurs when Transaction T1 writes a value, and Transaction T2 reads it before T1 commits. Example:

* T1: Writes balance = 1200 but hasn’t committed yet.
    
* T2: Reads balance = 1200, assuming it’s final.
    
* T1: Later rolls back, meaning T2 read an uncommitted value, leading to the dirty read problem.
    

##### Write - Write

Occurs when both transactions write to the same value, potentially leading to lost updates. Example:

* T1: Updates balance = 1100.
    
* T2: Updates balance = 900, unaware of T1’s update.
    
* The final value could be either 1100 or 900, depending on which write happens last, potentially leading to lost data.
    

### Understanding Concurrency Control in Database Systems

One of the key deciding factors of a database system or database engine is how it manages concurrency control. Efficient concurrency control ensures data consistency and integrity while allowing multiple transactions to execute simultaneously. There are three widely used concurrency control mechanisms: Optimistic Concurrency Control (OCC), Multi-Version Concurrency Control (MVCC), and Pessimistic Concurrency Control (PCC).

**Optimistic Concurrency Control (OCC)**  
Optimistic Concurrency Control operates under the assumption that transaction conflicts are rare. Instead of blocking execution, OCC allows transactions to execute concurrently and validates their serializability before committing the results. This approach is widely used in modern database engines, such as WiredTiger, which employs OCC for document-level concurrency.  
OCC generally follows three phases:

1. Read Phase: The transaction collects dependencies (read sets) and potential side effects (write sets) while reading data.
    
2. Validation Phase: Before committing, the system checks if any concurrent transactions violate serializability. If conflicts are detected, the transaction is aborted.
    
3. Write Phase: If no conflicts are found, the transaction is committed, and changes are applied to the database state.  
    This approach is particularly effective for workloads with infrequent conflicts, as it reduces the overhead of locking mechanisms.
    

**Multi-Version Concurrency Control (MVCC)**  
Multi-Version Concurrency Control (MVCC) allows multiple versions of the same data to exist simultaneously, ensuring a consistent view of the database at a specific point in time. MVCC is commonly used in databases like PostgreSQL and MySQL (InnoDB) to enhance performance and reduce contention.  
MVCC can be implemented in different ways, including:

* Validation techniques, where only one of the conflicting transactions is allowed to commit.
    
* Lockless techniques, such as timestamp ordering, which ensures transactions execute in a predefined sequence.
    
* Lock-based approaches, such as two-phase locking (2PL), where locks are acquired and released in phases to maintain consistency.  
    MVCC provides a non-blocking read mechanism, making it an excellent choice for high-concurrency environments where multiple transactions need to read data without waiting for write locks to be released.
    

**Pessimistic Concurrency Control (PCC)**  
Pessimistic Concurrency Control, also known as Conservative Concurrency Control, operates by blocking or aborting transactions as soon as a potential conflict is detected. Unlike OCC, which assumes conflicts are rare, PCC proactively prevents them by restricting access to shared resources.  
PCC can be implemented using two main approaches:

1. Lock-Based Approach: Transactions acquire locks on database records, preventing other transactions from modifying locked records until the lock is released.
    
2. Non-Lock-Based Approach: Instead of using explicit locks, the system maintains a list of read and write operations and restricts execution based on these dependencies.  
    While PCC ensures strict consistency, it can lead to performance bottlenecks due to deadlocks, where two or more transactions wait indefinitely for each other to release locks. Proper deadlock detection and resolution mechanisms are essential to mitigate this issue.
    

---

#### **Conclusion**

Concurrency control is a critical aspect of database systems, influencing their efficiency, scalability, and consistency. **Optimistic Concurrency Control (OCC)** is best suited for workloads with minimal conflicts, **Multi-Version Concurrency Control (MVCC)** balances performance and consistency with non-blocking reads, and **Pessimistic Concurrency Control (PCC)** is ideal for highly transactional environments where preventing conflicts is a priority.