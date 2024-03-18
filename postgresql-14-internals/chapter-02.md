## Chapter 1
### Introduction

The key feature of relational databases is their ability to ensure data consistency, that is, data correctness. It is a known fact that at the database level it is possible to create integrity constraints, such as NOT NULL or UNIQUE.

Transaction isolation levels are a measure of the extent to which transaction isolation succeeds. In particular, transaction isolation levels are defined by the presence or absence of the following phenomena:

Dirty Reads A dirty read occurs when a transaction reads data that has not yet been committed. For example, suppose transaction 1 updates a row. Transaction 2 reads the updated row before transaction 1 commits the update. If transaction 1 rolls back the change, transaction 2 will have read data that is considered never to have existed.

Nonrepeatable Reads A nonrepeatable read occurs when a transaction reads the same row twice but gets different data each time. For example, suppose transaction 1 reads a row. Transaction 2 updates or deletes that row and commits the update or delete. If transaction 1 rereads the row, it retrieves different row values or discovers that the row has been deleted.

Phantoms A phantom is a row that matches the search criteria but is not initially seen. For example, suppose transaction 1 reads a set of rows that satisfy some search criteria. Transaction 2 generates a new row (through either an update or an insert) that matches the search criteria for transaction 1. If transaction 1 reexecutes the statement that reads the rows, it gets a different set of rows.

A transaction is a set of operations that takes the database from one correct state to another correct state (consistency), provided that it is executed in full (atomicity) and without being affected by other transactions (isolation).

### Isolation Levels and Anomalies in SQL Standard

#### Lost Update
The lost update anomaly occurs when two transactions read one and the same table row, then one of the transactions updates this row, and finally the other transaction updates the same row without taking into account any changes made by the first transaction.

#### Dirty Reads and Read Uncommitted
The dirty read anomaly occurs when a transaction reads uncommitted changes made by another transaction.

#### Non-Repeatable Reads and Read Committed
The non-repeatable read anomaly occurs when a transaction reads one and the same row twice, whereas another transaction updates (or deletes) this row between these reads and commits the change. As a result, the first transaction gets different results.

#### Phantom Reads and Repeatable Read
The phantom read anomaly occurs when one and the same transaction executes two identical queries returning a set of rows that satisfy a particular condition, while another transaction adds some other rows satisfying this condition and commits the changes in the time interval between these queries. As a result, the first transaction gets two different sets of rows.

#### No Anomalies and Serializable
The standard also defines the Serializable level, which does not allow any anoma lies. It is not the same as the ban on lost updates and dirty, non-repeatable, and  phantom reads. The Serializable level must prevent any anomalies. It means that the application developer does not have to take isolation into account. If transactions execute correct operator sequences when run on their own, concurrent execution cannot break data consistency either.

|                  | LOST UPDATE | DIRTY READ | NON- REPEATABLE READ | PHANTOM READ | OTHER ANOMALIES |
|:----------------:|:-----------:|:----------:|:--------------------:|:------------:|:---------------:|
| READ UNCOMMITTED |             |     X      |          X           |      X       |        X        |
|  READ COMMITTED  |             |            |          X           |      X       |        X        |
| REPEATABLE READ  |             |            |                      |      X       |        X        |
|   SERIALIZABLE   |             |            |                      |              |                 |

#### Why These Anomalies?
The widely used two-phase locking protocol (2PL) requires transactions to lock the affected rows during execution and release the locks upon completion. In simplistic terms, the more locks a transaction acquires, the better it is isolated from other transactions. And consequently, the worse is the system performance, as transactions start queuing to get access to the same rows instead of running concurrently.

