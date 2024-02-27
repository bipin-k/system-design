## CHAPTER 1
### Introduction and Overview

Column-oriented databases store data in columns as opposed to rows. These databases are best suited for OLAP (Online analytical processing) use cases. They provide the best performance when similar data is stored in columns on the disk. However, in this scenario, if we need to add a new row of customer data, this would prove to be resource-intensive since we have to access all the columns to add a new record in them.

Online transaction processing (OLTP) databases - These handle a large number of user-facing requests and transactions. Queries are often predefined and short-lived.

Online analytical processing (OLAP) databases - These handle complex aggregations. OLAP databases are often used for analytics and data warehousing, and are capable of handling complex, long-running ad hoc queries.

Hybrid transactional and analytical processing (HTAP) - These databases combine properties of both OLTP and OLAP stores.

Database management systems use a client/server model, where database system instances (nodes) take the role of servers, and application instances take the role of clients. Client requests arrive through the transport subsystem. Requests come in the form of queries, most often expressed in some query language. The transport subsystem is also responsible for communication with other nodes in the database cluster.

Upon receipt, the transport subsystem hands the query over to a query processor, which parses, interprets, and validates it. Later, access control checks are performed, as they can be done fully only after the query is interpreted.

The parsed query is passed to the query optimizer, which first eliminates impossible and redundant parts of the query, and then attempts to find the most efficient way to execute it based on internal statistics (index cardinality, approximate intersection size, etc.) and data placement (which nodes in the cluster hold the data and the costs associated with its transfer). The optimizer handles both relational operations required for query resolution, usually presented as a dependency tree, and optimizations, such as index ordering, cardinality estimation, and choosing access methods.

The query is usually presented in the form of an execution plan (or query plan): a sequence of operations that have to be carried out for its results to be considered complete. Since the same query can be satisfied using different execution plans that can vary in efficiency, the optimizer picks the best available plan.

The execution plan is handled by the execution engine, which collects the results of the execution of local and remote operations. Remote execution can involve writing and reading data to and from other nodes in the cluster, and replication.

Local queries (coming directly from clients or from other nodes) are executed by the storage engine. The storage engine has several components with dedicated
responsibilities:

Transaction manager - This manager schedules transactions and ensures they cannot leave the database in a logically inconsistent state.

Lock manager - This manager locks on the database objects for the running transactions, ensuring that concurrent operations do not violate physical data integrity.

Access methods (storage structures) - These manage access and organizing data on disk. Access methods include heap files and storage structures such as B-Trees or LSM Trees,

Buffer manager - This manager caches data pages in memory,
Recovery manager - This manager maintains the operation log and restoring the system state in case of a failure,

Together, transaction and lock managers are responsible for concurrency control (see“Concurrency Control” on page 93): they guarantee logical and physical data integrity while ensuring that concurrent operations are executed as efficiently as possible.

In-memory database management systems (sometimes called main memory DBMS) store data primarily in memory and use the disk for recovery and logging. Disk-based DBMS hold most of the data on disk and use memory for caching disk contents or as a temporary storage.

In-memory database systems maintain backups on disk to provide durability and prevent loss of the volatile data. Before the operation can be considered complete, its results have to be written to a sequential log file. To avoid replaying complete log contents during startup or after a crash, in-memory stores maintain a backup copy. The backup copy is maintained as a sorted disk-based structure, and modifications to this structure are often asynchronous (decoupled from client requests) and applied in batches to reduce the number of I/O operations. During recovery, database contents can be restored from the backup and logs.

Log records are usually applied to backup in batches. After the batch of log records is processed, backup holds a database snapshot for a specific point in time, and log contents up to this point can be discarded. This process is called checkpointing. It reduces recovery times by keeping the disk-resident database most up-to-date with log entries without requiring clients to block until the backup is updated.

Spatial locality is one of the Principles of Locality, stating that if a memory location is accessed, its nearby memory locations will be accessed in the near future.

To decide whether to use a column- or a row-oriented store, you need to understand your access patterns. If the read data is consumed in records (i.e., most or all of the columns are requested) and the workload consists mostly of point queries and range scans, the row-oriented approach is likely to yield better results. If scans span many rows, or compute aggregate over a subset of columns, it is worth considering a column-oriented approach.

wide column stores, such as BigTable or HBase, where data is represented as a multidimensional map, columns are grouped into column families (usually storing data of the same type), and inside each column family, data is stored row-wise. This layout is best for storing data retrieved by a key or a sequence of keys.

Vectorized instructions, or Single Instruction Multiple Data (SIMD), describes a class of CPU instructions that perform the same operation on multiple data points.

A database system usually separates data files and index files: data files store data records, while index files store record metadata and use it to locate records in data file.

Data files (sometimes called primary files) can be implemented as index-organized tables (IOT), heap-organized tables (heap files), or hash-organized tables (hashed files).

Data files (sometimes called primary files) can be implemented as index-organized tables (IOT), heap-organized tables (heap files), or hash-organized tables (hashed files).

Records in heap files are not required to follow any particular order, and most of the time they are placed in a write order. This way, no additional work or file reorganization is required when new pages are appended. Heap files require additional index structures, pointing to the locations where data records are stored, to make them searchable.

In hashed files, records are stored in buckets, and the hash value of the key determines which bucket a record belongs to. Records in the bucket can be stored in
append order or sorted by key to improve lookup speed.

Index-organized tables (IOTs) store data records in the index itself. Since records are stored in key order, range scans in IOTs can be implemented by sequentially scanning its contents.

Storing data records in the index allows us to reduce the number of disk seeks by at least one, since after traversing the index and locating the searched key, we do not have to address a separate file to find the associated data record.

When records are stored in a separate file, index files hold data entries, uniquely identifying data records and containing enough information to locate them in the data file. For example, we can store file offsets (sometimes called row locators), locations of data records in the data file, or bucket IDs in the case of hash files. In index-organized tables, data entries hold actual data records.

