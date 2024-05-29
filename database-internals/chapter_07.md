
## CHAPTER 7
### Log-Structured Storage

B-Trees as a typical example of mutable structure and Log-Structured Merge Trees (LSM Trees) as an example of an immutable structure. B-Trees are read-optimized. Writes to the B-Tree require locating a record on disk, and subsequent writes to the same page might have to update the page on disk multiple times. Reserved extra space for future updates and deletes increases space overhead.

Immutable LSM Trees use append-only storage and merge reconciliation, and B-Trees locate data records on disk and update pages at their original offsets in the file.

LSM Trees do not require locating the record on disk during write and do not reserve extra space for future writes. There is still some space overhead resulting from storing redundant records. In a default configuration, reads are more expensive, since multiple tables have to be accessed to return complete results.

In-place update storage structures are optimized for read performance after locating data on disk, the record can be returned to the client. This comes at the expense of write performance: to update the data record in place, it first has to be located on disk. On the other hand, append-only storage is optimized for write performance. Writes do not have to locate records on disk to overwrite them. However, this is done at the expense of reads, which have to retrieve multiple data record versions and reconcile them.

Because of the structure and construction approach taken by mutable B-Trees, most I/O operations during reads, writes, and maintenance are random. Each write operation first needs to locate a page that holds a data record and only then can modify it. B-Trees require node splits and merges that relocate already written records. After some time, B-Tree pages may require maintenance. Pages are fixed in size, and some free space is reserved for future writes. Another problem is that even when only one cell in the page is modified, an entire page has to be rewritten.

One of the most popular immutable on-disk storage structures, LSM Tree uses buffering and append-only storage to achieve sequential writes.

Log-structured merge trees take their name from log-structured filesystems, which write all modifications on disk in a log-like file

LSM Trees write immutable files and merge them together over time. These files usually contain an index of their own to help readers efficiently locate data. Even though LSM Trees are often presented as an alternative to B-Trees, it is common for B-Trees to be used as the internal indexing structure for an LSM Tree’s immutable files.

The word “merge” in LSM Trees indicates that, due to their immutability, tree contents are merged using an approach similar to merge sort. This happens during maintenance to reclaim space occupied by the redundant copies, and during reads, before contents can be returned to the user.

Two-component LSM Trees have only one disk component, comprised of immutable segments. The disk component here is organized as a B-Tree, with 100% node occupancy and read-only pages. Memory-resident tree contents are flushed on disk in parts. During a flush, for each flushed in-memory subtree, we find a corresponding subtree on disk and write out the merged contents of a memory-resident segment and disk-resident subtree into the new segment on disk. After the subtree is flushed, superseded memory-resident and disk-resident subtrees are discarded and replaced with the result of their merge, which becomes addressable from the preexisting sections of the disk-resident tree.

Multicomponent LSM Trees that have more than just one disk-resident table. 

It quickly becomes evident that after multiple flushes we’ll end up with multiple disk resident tables, and their number will only grow over time. Since we do not always know exactly which tables are holding required data records, we might have to access multiple files to locate the searched data. Having to read from multiple sources instead of just one might get expensive. To mitigate this problem and keep the number of tables to minimum, a periodic merge process called compaction (see “Maintenance in LSM Trees” on page 141) is triggered. Compaction picks several tables, reads their contents, merges them, and writes the merged result out to the new combined file. Old tables are discarded simultaneously with the appearance of the new merged table.

A priority queue is a data structure used for maintaining an ordered queue of items. While a regular queue retains items in order of their addition (first in, first out), a priority queue re-sorts items on insertion and the item with the highest (or lowest) priority is placed in the head of the queue. This is particularly useful for merge-iteration, since we have to output elements in a sorted order.

An operation that inserts the record to the database if it does not exist, and updates an existing one otherwise, is called an upsert. In LSM Trees, insert and update operations are indistinguishable, since they do not attempt to locate data records previously associated with the key in all sources and reassign its value, so we can say that we upsert records by default.

In LSM Trees, the number of disk-resident tables is constantly growing, but can be reduced by triggering periodic compaction. Compaction picks multiple disk-resident tables, iterates over their entire contents using the aforementioned merge and reconciliation algorithms, and writes out the results into the newly created table.

Leveled compaction separates disk-resident tables into levels. Tables on each level have target sizes, and each level has a corresponding index number (identifier). Level-0 tables are created by flushing memtable contents. Tables in level 0 may contain overlapping key ranges. As soon as the number of tables on level 0 reaches a threshold, their contents are merged, creating new tables for level 1.

In size-tiered compaction, rather than grouping disk-resident tables based on their level, they’re grouped by size: smaller tables are grouped with smaller ones, and bigger tables are grouped with bigger ones.

When storing data on disk in an immutable fashion, we face three problems
Read amplification - Resulting from a need to address multiple tables to retrieve data,
Write amplification - Caused by continuous rewrites by the compaction process,
Space amplification - Arising from storing multiple records associated with the same key,

RUM (Read, Update & Memory) Conjecture states that reducing two of these overheads inevitably leads to change for the worse in the third one, and that optimizations can be done only at the expense of one of the three parameters.

Disk-resident tables are often implemented using Sorted String Tables (SSTables). As the name suggests, data records in SSTables are sorted and laid out in key order. SSTables usually consist of two components: index files and data files. Index files are implemented using some structure allowing logarithmic lookups, such as B-Trees, or constant-time lookups, such as hashtables. Since data files hold records in key order, using hashtables for indexing does not prevent us from implementing range scans, as a hashtable is only accessed to locate the first key in the range, and the range itself can be read from the data file sequentially while the range predicate still matches.

One of the interesting developments in the area of LSM Tree indexing is SSTable-Attached Secondary Indexes (SASI) implemented in Apache Cassandra. To allow indexing table contents not just by the primary key, but also by any other field, index structures and their life cycles are coupled with the SSTable life cycle, and an index is created per SSTable. When the memtable is flushed, its contents are written to disk, and secondary index files are created along with the SSTable primary key index. Since LSM Trees buffer data in memory and indexes have to work for memory-resident contents as well as the disk-resident ones, SASI maintains a separate in-memory structure, indexing memtable contents. During a read, primary keys of searched records are located by searching and merging index contents, and data records are merged and reconciled similar to how lookups usually work in LSM Trees. One of the advantages of piggybacking the SSTable life cycle is that indexes can be created during memtable flush or compaction.

A Bloom filter, conceived by Burton Howard Bloom in 1970 [BLOOM70], is a space-efficient probabilistic data structure that can be used to test whether the element is a member of the set or not. It can produce false-positive matches (say that the element is a member of the set, while it is not present there), but cannot produce false negatives (if a negative match is returned, the element is guaranteed not to be a member of the set). A Bloom filter can be used to tell if the key might be in the table or is definitely not in the table.

A Bloom filter uses a large bit array and multiple hash functions. Hash functions are applied to keys of the records in the table to find indices in the bit array, bits for which are set to 1. Bits set to 1 in all positions determined by the hash functions indicate a presence of the key in the set. During lookup, when checking for element presence in a Bloom filter, hash functions are calculated for the key again and, if bits determined by all hash functions are 1, we return the positive result stating that item is a member of the set with a certain probability. If at least one of the bits is 0, we can precisely say that element is not present in the set.

Bw-Tree is layered on top of a latch-free, log-structured, access-method aware (LLAMA) storage subsystem. This layering allows Bw-Trees to grow and shrink dynamically, while leaving garbage collection and page management transparent for the tree.

An alternative to stacking software layers is to skip all indirection layers and use the
hardware directly. For example, it is possible to avoid using a filesystem and flash
translation layer by developing for Open-Channel SSDs. This way, we can avoid at
least two layers of logs and have more control over wear-leveling, garbage collection,
data placement, and scheduling. One of the implementations that uses this approach
is LOCS (LSM Tree-based KV Store on Open-Channel SSD) [ZHANG13]. Another
example using Open-Channel SSDs is LightNVM, implemented in the Linux kernel
[BJØRLING17].