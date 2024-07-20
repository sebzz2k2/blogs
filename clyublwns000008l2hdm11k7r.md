---
title: "ACID properties of a relational database"
seoTitle: "acid properties"
seoDescription: "acid, database internals"
datePublished: Sat Jul 20 2024 16:05:28 GMT+0000 (Coordinated Universal Time)
cuid: clyublwns000008l2hdm11k7r
slug: acid-properties-of-a-relational-database
tags: programming, databases

---

Any operation that can possibly access or modify the contents of a database is called transactions. Database follows **ACID** properties in order to maintain the consistency of data before and after a given transaction. ACID is an acronym for Atomicity, Consistency, Isolation, and Durability. These principles are essential for ensuring reliable and secure database transactions.

## A - atomicity

Atomicity is a property of database by which a transaction is ensured to completed or rolled back to its initial state.

### Features of Atomicity

* Serializability ensures that a series of operations requested by a single user appear as a single operation to an outside observer, such as another process or query.
    
* Recoverability guarantees that a database engine will not produce partial results; a transaction will either complete fully or fail entirely.
    

### How is Atomicity achieved

One of the most easiest way to achieve atomicity is to use undo logs and redo logs. An **undo log** is a collection of undo records associated with a single read-write transaction. It contains information about how to undo the latest change by transaction to a primary key index. If another transaction requires to see the original state, it is retrieved from the undo log. Another way is by using a **redo-log**. Redo log is a disk-based data structure which is used during crash recovery to correct data written by incomplete transactions. **Two phase commit** is a common way to achieve atomicity in distributed database systems. Using this a commit only occurs only when all involved system says yes.

## C - consistency

A database system must remain consistent before and after transaction. Keeping data consistent means that any change of data in a single table must be reflected across all linked tables and entities as well. Only valid data will be written to database. Data that breaks the rules of consistency that transaction will be rolled back. A consistent read means that a snapshot of a database at a point in time will be presented before the read transaction started. All writes that occurred after the read transaction will not be presented. As it is difficult to ensure business logic consistency one must enforce consistency by serialisable transactions or by using explicit blocking locks.

## I - Isolation

Isolation is the state of separation. A good database must allow multiple transactions to be executed simultaneously and no data must have impact on one another.

## Levels of isolation

1. The **Uncommitted read isolation** can see data in a transaction.
    
2. The **Read committed isolation** can only see the data committed before the transaction before the query began. It never sees either uncommitted data changes committed by concurrent transactions during the query's execution.
    
3. The **Repeatable Read isolation** level only sees data committed before the transaction began; it never sees either uncommitted data or changes committed by concurrent transactions during the transaction's execution. However, each query does see the effects of previous updates executed within its own transaction, even though they are not yet committed.
    
4. The **Serializable isolation** level provides the strictest transaction isolation. This level emulates serial transaction execution for all committed transactions; as if transactions had been executed one after another, serially, rather than concurrently.
    

### The phenomena which are prohibited at various level of isolation are

1. A **dirty read** is a read when the database reads uncommitted data
    
2. Multiple values being retrieved from the same row in a database during the same transaction is known as **non repeatable read**.
    
3. Different collection of rows being returned for the same query in the same transaction is known as **phantom reads**.
    
4. The result of successfully committing a group of transactions is inconsistent with all possible orderings of running those transactions one at a time is called **serialization anomaly**
    
5. **Read Uncommitted**
    
    * Dirty Read: Yes
        
    * Non-repeatable Read: Yes
        
    * Phantom Read: Yes
        
    * Serialization Anomaly: Yes
        
6. **Read Committed**
    
    * Dirty Read: No
        
    * Non-repeatable Read: Yes
        
    * Phantom Read: Yes
        
    * Serialization Anomaly: Yes
        
7. **Repeatable Read**
    
    * Dirty Read: No
        
    * Non-repeatable Read: No
        
    * Phantom Read: Yes
        
    * Serialization Anomaly: Yes
        
8. **Serializable**
    
    * Dirty Read: No
        
    * Non-repeatable Read: No
        
    * Phantom Read: No
        
    * Serialization Anomaly: No When simultaneous or processes or users tries to manipulate data and the data does not contain any inconsistency, this is due to a feature called concurrency control. It is one of the way that a database guarantees isolation.
        

* Pessimistic concurrency control is when database assumes something that ought to go wrong will go wrong. This approach of database prevents blocking before it even occurs. Database uses read and write locks to avoid this station
    
* A read lock is when reads of the same item by multiple transactions are allowed but not a write, whereas a write lock is an exclusive lock that allows only one transaction hold it. This thereby blocks other transaction from updating the same database item.
    
* By assuming something can always go wrong the database does not allow a transaction to read/write on uncommitted database items
    
* Optimist control is when a transaction does not obtain locks on data they read or write. Unlike the pessimist approach the database checks for conflicts at the end of transaction
    
* The isolation level repeatable read can be achieved by optimistic approach.
    

## D - durability

Durability is property of database that guarantees that once committed data is not lost and is permanently available on disk.

### How does database achieve durability

1. Durability is property of database that guarantees that once committed data is not lost and is permanently available on disk.
    
2. Write ahead logging is when data is written to redo log before a transaction. Making the changes permanent. In case of failure the database system replays to redo log.
    
3. We can also periodically write the state of database into a disk called checkpoints. A less effort is required for data recovery.
    
4. RAID (Redundant Array of Inexpensive Disks) is used to integrate several drives into a single logical unit. RAID can be used to implement redundancy and ensure that data is durable even in the event of a disk failure.
    
5. Storing multiple copies of same data redundantly can also help in case of system failures.
    

---

#### Links

* [https://www.freecodecamp.org/news/how-databases-guarantee-isolation/](https://www.freecodecamp.org/news/how-databases-guarantee-isolation/)
    
* [http://www0.cs.ucl.ac.uk/staff/B.Karp/0133/f2021/lectures/0133-lecture6-2PC.pdf](http://www0.cs.ucl.ac.uk/staff/B.Karp/0133/f2021/lectures/0133-lecture6-2PC.pdf)
    
* [https://www.javatpoint.com/implementation-of-atomicity-and-durability-in-dbms](https://www.javatpoint.com/implementation-of-atomicity-and-durability-in-dbms)
    
* [https://www.webopedia.com/definitions/atomic-operation/](https://www.webopedia.com/definitions/atomic-operation/)
    
* [https://dev.mysql.com/doc/refman/8.0/en/](https://dev.mysql.com/doc/refman/8.0/en/)
    
* [https://www.postgresql.org/docs/current/app-psql.html](https://www.postgresql.org/docs/current/app-psql.html)
    
* [https://www.geeksforgeeks.org/acid-properties-in-dbms/](https://www.geeksforgeeks.org/acid-properties-in-dbms/)