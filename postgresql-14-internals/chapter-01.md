## Chapter 1
### Introduction

Unlike databases and schemas, which determine logical distribution of objects, tablespaces define physical data layout. A tablespace is virtually a directory in a file system. One and the same tablespace can be used by different databases, and each database can store data in several tablespaces. It means that logical structure and physical data layout do not depend on each other. Each database has the so-called default tablespace. All database objects are created in this tablespace unless another location is specified. System catalog objects related to this database are also stored there.

During cluster initialization, two tablespaces are created:
`pg_default` is located in the `PGDATA/base` directory. It is used as the default tablespace unless another tablespace is explicitly selected for this purpose.

`pg_global` is located in the `PGDATA/global` directory. It stores system catalog objects that are common to the whole cluster.

When creating a custom tablespace, you can specify any directory; PostgreSQL will create a symbolic link to this location in the `PGDATA/pg_tblspc` directory.

