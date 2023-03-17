# Database

## [[MySQL]]

With B+Tree index:

- Primary indexes are often referred to as clustered indexes or another term that I prefer index-organized table. This means the primary index is the table.
- Secondary index the key is whatever column(s) you indexed and the value is a pointer to where the full row really live. The value of secondary index leaf pages are usually primary keys.

In MySQL updating a column that is not indexed will result in only updating the leaf page where the row is with the new value. No other secondary indexes need to be updated because all of them point to the primary key which didn’t change.

Data types: In MySQL choosing the primary key data type is critical, as that key will live in all secondary indexes. For example a UUID primary key will bloat all secondary indexes size causing more storage and read I/Os.

Undo log: When a transaction makes a change to a row, the change is written to the page in the shared buffer pool so the page where the row live always has the latest data. The transaction then logs information of how to undo the latest changes to row (enough info to construct the old state) in an undo log, this way concurrent transactions that still need the old state based on their isolation level must crack the undo log and construct the old row.

## [[PostgreSQL]]

In Postgres technically there is no primary index, all indexes are secondary and all point to system managed tuple ids in data pages loaded in the heap. The table data in the heap are unordered, unlike primary index leaf pages which are ordered. That is why Postgres tables are often referred to as “heap organized tables” instead of “index organized tables”.

It is important to note that updates and deletes in Postgres are actually inserts. Every update or delete creates a new tuple id and the old tuple id is kept for MVCC reasons.

In Postgres looking up any secondary index will only require one index lookup followed by a constant single I/O to the heap to fetch the page where the full row live. One B+Tree lookup is better than two lookups of course.

Postgres attempts to minimize random reads by using bitmap index scans, grouping the results into pages instead of tuples and fetching the pages from the heap in fewer I/Os possible. Later additional filtering is applied to present the candidate rows.

Data types: In Postgres the tuple id is fixed 4 bytes so the secondary indexes won’t have the UUID values but just the tids pointing the heap.

Undo log: Postgres does this very differently, each update, insert and delete gets a new copy of the row with a new tuple id with hints about what transaction id created the tuple and what transaction id deleted the tuple. So Postgres can safely write the changes to the data pages and concurrent transactions can read the old or new tuples based on their transaction id. Clever design.

## Readmore

1. [Postgres vs MySQL](https://medium.com/@hnasr/postgres-vs-mysql-5fa3c588a94e)
2. [Why Ubers switch to MySQL from Postgres](https://www.youtube.com/watch?v=_E43l5EbNI4)
3. [PostgreSQL process architecture](https://medium.com/@hnasr/postgresql-process-architecture-f21e16459907)