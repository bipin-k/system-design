
## CHAPTER 7
### Log-Structured Storage

B-Trees as a typical example of mutable structure and Log-Structured Merge Trees (LSM Trees) as an example of an immutable structure.

Immutable LSM Trees use append-only storage and merge reconciliation, and B-Trees locate data records on disk and update pages at their original offsets in the file.

In-place update storage structures are optimized for read performance [GRAEFE04]:
after locating data on disk, the record can be returned to the client. This comes at the
expense of write performance: to update the data record in place, it first has to be
located on disk. On the other hand, append-only storage is optimized for write per‐
formance. Writes do not have to locate records on disk to overwrite them. However,
this is done at the expense of reads, which have to retrieve multiple data record ver‐
sions and reconcile them.

Because of the structure and construction approach taken by mutable B-Trees, most
I/O operations during reads, writes, and maintenance are random. Each write opera‐
tion first needs to locate a page that holds a data record and only then can modify it.
B-Trees require node splits and merges that relocate already written records. After
some time, B-Tree pages may require maintenance. Pages are fixed in size, and some
free space is reserved for future writes. Another problem is that even when only one
cell in the page is modified, an entire page has to be rewritten.

One of the most popular immutable on-disk storage structures, LSM Tree uses
buffering and append-only storage to achieve sequential writes.

Log-structured merge trees
take their name from log-structured filesystems, which write all modifications on
disk in a log-like file

LSM Trees write immutable files and merge them together over
time. These files usually contain an index of their own to help read‐
ers efficiently locate data. Even though LSM Trees are often presen‐
ted as an alternative to B-Trees, it is common for B-Trees to be
used as the internal indexing structure for an LSM Tree’s immuta‐
ble files.
