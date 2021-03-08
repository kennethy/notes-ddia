# Designing Data-Intensive Applications (WIP)

Reading Notes for [Designing Data-Intensive Applications (Kleppmann)](https://www.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/)

## Chapter 1 - Reliable, Scalable, and Maintainable Applications

### Thinking About Data Systems

New tools for data storage and processing have emerged. They are optimized for a variety of different use cases, and they are combined to form a *data system* and *to provide a service*.

The data system should be:
- **Reliable:** The system should continue to work *correctly* even in the face of *adversity*.
- **Scalable:** As the system *grows*, there should be reasonable ways of dealing with that growth.
- **Maintainable:** Over time, different people will work on the system, and they should all be able to work on it *productively*.

### Reliability

A system is reliable if it works correctly even when things go wrong. Things that can go wrong are called *faults.* Systems that anticipate faults and can cope with them are called *fault-tolerant* or *resilient.*

Fault is usually defined as one component of the system deviating from its spec, whereas a failure is when the system as a whole stops providing the required service to the user. Fault-tolerance is exercised by deliberately triggering faults.

**Hardware Faults**

Instances such as hard disks crash, faulty RAM, power grid blackout, and etc are considered hardware faults. The prevention steps include adding redundancy to hardware components, dual power supplies, hot swappable CPUS, and datacenters with backup generators.

**Software Errors**

Systematic error that is harder to anticipate, and tend to cause more system failure than uncorrelated hardware faults. Prevention steps include carefully thinking about assumptions and interactions in the system; through testing; process isolation; process isolation; allowing processes to crash and restart; measuring/monitoring/analyzing system behaviour in production.

**Human Errors**

Humans are error-prone. Study has shown that configuration errors by operators were the leading causes of outages. 

- Prevent errors by designing admin interfaces that are easy to "do the right thing" and discourage "the wrong thing."
- Introduce *sandbox environments* where people can explore and experiment safely, using real data, without effecting real users.
- Test the system at all levels using unit tests, integration tests, and manual tests.
- Make it easy to rollback changes. Roll-out new code gradually and to a small subset of users.
- Clear monitoring in place with the use of performance metrics and error rates.

### Scalability

The system's ability to cope with increased load. We should consider our options of coping with the growth, and how we can add computing resources to handle the additional load.

**Describing Load**

Load parameters: describe the current load on the system. They vary based on the architecture of the system. (e.g. requests per second, ratio of reads to writes, active users count, cache hit rate, and etc)

Fan-out: it is used to describe the number of requests to other services that need to be made in order to serve one incoming requests.

**Describing Performance**

Investigate what happens when the load increases:
- How the system performs with an increase in a load parameter while keeping system resources unchanged?
- As the load parameter increases, how much do the system resources need to increase in order to keep the performance unchanged?

Throughput: the nunber of records we can process per second, or the total time it takes to run a job on a dataset of a certain size.

Latency: not to be confused with response time. It is the duration that a request is waiting to be handled.

Response time: the time between a client sending a request and receiving a response. It varies as the system handles a variety of requests. It should not be treated as a single number, but as a distribution of values that can be measured. Percentiles are preferred over arithmetic mean to gauage the average response time of a service.

50th percentile: or *p50*, is the *median*. For example, 200ms median response time means half of requests return in less than 200ms, and the other half take longer than that.

Tail latencies: high percentiles of response time. For example, 95th or p95. If the p95 is 1.5 seconds, then 95 out of 100 requests take less than 1.5 seconds, 5 out of 100 requests take 1.5 seconds or more.

Service level objects (SOL) and Service level agreements (SLAs): contracts that define the expected performance and availability of a service.

Head-of-line blocking: when a small number of slow requests hold up the processing of subsequent requests.

**Approaches for Coping with Load**

Vertical Scaling: moving to a more powerful machine. Tends to get expensive.

Hortizontal Scaling: distributing the load across multiple smaller machines.

Elastic System: when computing resources can be automatically be added when detecting a load increase.

### Maintainability

The majority of the cost of software is not in its initial development, but in its on-going maintenance.

- Operability: make it easy for operations team to keep the system running smoothly.
- Simplicity: make it easy for new engineers to understand the system.
- Evolvability: make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change.

**Operability: Making Life Easy For Operations**

Operations team is responsible for ensuring the system runs smoothly and resolve issues when they arise. Good operability means making routine tasks easy, allowing the operations team to focus their efforts on high-value activities.

- Good monitoring on system health
- Good support for automation and integration tools
- Good documentation on how the system behaves

**Simplicity: Managing Complexity**

Complexity grows and become difficult to understand as project gets larger. Manage complexity with good abstractions. Abstractions hide implementation details and encourage code reuse. Ulimately, the improve the quality of the software.

**Evolvability: Making Change Easy**

System requirements are constantly changing. *Evolvability*, or how easily a system can be modified, is closely linked to its simplicity and its abstractions. 

## Chapter 2 - Data Models and Query Languages

Most applications are built by layering multiple data models. From application level data structures, database level representations (JSON/XML/Graph/Relational), bytes in memory, and finally bytes in terms of electrical currents, and etc. Each layer hides the complexity below it.

### Relational Model Versus Document Model

In the relational model, data is organized into *relations/tables*, and each relation is an unordered collection *tuples/rows.* Its roots lie in *transaction processing* (records entry) and *batch processing (reporting).* Its goal was to hide the implementation detail behind a cleaner interface.

**The Birth of NoSQL**

Not Only SQL (NoSQL) is driven by:
- specialized query operations that are not well supported by the relational model
- desire for a more dynamic and expressive data model

**The Object-Relational Mismatch**

Most applications are done in object-oriented programming. It often requires a translation layer between the objects in the application code and the database model of tables, rows, and columns. (also known as *impedance mismatch.*)

Object-relational mapping (ORM) frameworks reduce the amount of boilerplate but they can't completely hide the differences between the two models.

Locality is an optimization strategy that group data that are likely to be used together. JSON, for instance, has better locality than typical multi-table schema.

**Many-to-One and Many-to-Many Relationships**

Reference data by ID to eliminate duplications. Removing duplicated information is the key behind database *normalization.*

### Relational Versus Document Database Today

Document data model provides schema flexibility, and models applications better, whereas relationship model provides better support for joins, and many-to-one and many-to-many relationships.

**Which data model leads to simpler application code?**

*Shredding* (splitting data into tables in the relational model) can often be cumbersome.  Use document model if the application has a document-like structure (i.e. a tree of one-to-many relationships, and the tree is loaded at once). 

Document model have tradeoffs. For example, nested item can only be referenced through access like, like "the second item in the list of positions for user 251."

**Schema flexibility in the document model**

There are no guarantees on what fields the documents may contain when arbitrary keys and values can be stored.

Schema-on-read: the structure of the data is implicit, and only interpreted when the data is read.

Schema-on-write: schema is explicit and the database ensures all written data conforms to it.

**Data locality for queries**

Locality is useful only if large parts of the document are used at the same time.

### Query Languages for Data

Declarative query language, like SQL, specify the pattern of the data we want — what conditions the data results must meet, and how you want the data to be transformed — but not how to achieve the goal, and it's up to the database system's query optimizer to decide how to do it.

It also have a better chance of getting faster in parallel execution because it specifies only the pattern of the results, not the algorithm that is used to determine the results.

### Decarative Queries on the Web

The advantages of declarative query languages are not limited to just databases. For example, HTML and CSS.

### MapReduce Querying

Logic of the query expressed based on the *map* function and the reduce function. The two functions must be *pure. They only use the data that is passed to them as input, and cannot perform additional databases queries.

### Graph-Like Data Models

It's often more natural to start modelling data as a graph when it becomes complex.

## Chapter 3 - Storage and Retrieval

### Data Structures That Power Your Database

The basic idea is that data is appended to a log-structured file, and the index associated to the key/value is the byte offset relative to the log file. Log file is splitted into smaller segments, and compaction is performed.

*Compaction* is when duplicated keys in the log are thrown away, and only the most recent update for each key is retained. Smaller segments would be merged as compactions make them smaller. The merging and compaction of segments is often done in a separate background thread.

Read request uses the old compaction until merge/compaction is complete. Old segments are deleted once the process completes. Writes are done to latest segments.

### Hash Indexes

Each segment maintains its own in-memory hash table of key to file offsets. Index lookups start from the latest segment.

Implementation details:
-  In practice, binary format is used.
-  Special deletion record (*tomberstone*) is appended to delete a key, discarded during the merge process.
-  Snapshot of each segment's hash map is stored on disk for fast crash discovery.
-  Only one writer thread is used, and data file segments are append-only/immutable.
-  Append-only writes as sequential writes are generally faster than random writes.

Limitations:
- hash table must fit in memory
- range queries are inefficient as each key needs to be looked up individually

### SSTables and LSM-Trees

Sequence of key-value pairs is sorted by key. This format is is called *Sorted String Table.*

Advantages over log segments with hash indexes:
- Simple and efficient merges similar to merge-sort algorithm.
- Not all keys need to be stored in memory.
- Reduces the I/O bandwidth use through block compression.

**Constructing and maintaining SSTables**

- Incoming writes are added to in-memory balaned tree data structure (memtable).
- Memtables written to disk when its size get to certain threshold.
- Serve a read request by first looking up the key in memtable, then in the most recent on-disk segment.
- Run merging and compaction process in the background from time-to-time.

Keep a separate append-only write-to-disk log for recovery in case of crash. Storage engines that are based on the principle of merging and compacting sorted files are often called LSM (*log-structured merge-tree*) storage engines.

**Performance Optimizations**

Lookup may be slow for non-existent key (lookup from memtable to on-disk segments). Optimize by using *boom filters*.

Compaction/Merge Strategies:
- Size-tiered: newer and smaller SSTables are successively merged into older and larger SSTables.
- Leveled-tiered: the key range is split up into smaller SSTables and older data is moved into separate "levels", which allows the compaction to proceed more incrementally and use less disk space. 

### B-Trees

Similar to SSTables, B-trees keep key-value pairs sorted by key. It breaks the database down into fixed-size *blocks* or *pages*, traditionally 4 KB in size, and read or write one page at a time.

Each page is identified using an address/location (on disk). Pages use the addresses to reference each other. One page is designated as the root, and a page contains several keys and references to child pages. The leaf page contains the value for each key or contains references to the pages where the value can be found.

*Branching factor* refers to the number of references on a page.

To update a key, the page containing the value of the key is looked up and the page found will be updated in place. 

To insert a key, it first finds the page whose range encompasses the new key and it will be added to the page. The page get split in half if it exceeds the limit size.

The tree is kept balanced for all of its operations.

**Making B-trees reliable**

The basic underlying write operation of a B-tree requires overwriting a page on disk with new data.

To make the databsae resilient to crashes, a *write-ahead-log* (WAL, aka *redo log*), is an append-only file to whcih every B-tree modification must be written before it can be applied to the pages of the tree itself.

### Comparing B-Trees and LSM-Trees

Rule of thumb: LSM-trees are typicall faster for writes, whereas B-trees are though to be faster for reads. Slower reads on LSM-trees because multiple data structures and SSTables need to be checked. Slower writes on B-trees because both the WAL and the page need to be updated.

**Advantages of LSM-Trees**

Write amplication: one write to the database resulting in multiple writes to the disk over the course of the database's lifetime.

- typically sustain higher write throughput than B-trees because lower write amplification (sequential writes.)
- can be compressed better since B-trees could leave some disk space unused due to fragmentation (splitted page with unused space)

**Downsides of LSM-Trees**

- compaction could interfere with the performance of ongoing reads and writes.
- due to the nature of having duplicate keys in multiple segments. B-trees offer stronger transactional semantics.

### Other Indexing Structures

Primary key: uniquely identifies one row in a relational table, or one document in a document database, or one vertex in a graph database.

**Storing values within the index**

Heap file: reference to the row stored elsewhere.

Clustered index: when the row is stored directly with the index.

Covering index (index with included columns): stores some of a table's columns with the index.

### Transaction Processing or Analytics

| Property |  Transaction Processing Systems  |  Analytic Systems (OLAP)  |
| --- | --- | --- |
| Main read pattern | small # of record per query, fetched by key | aggregate over large number of records |
| Main write pattern | random-access, low-latency writes from user input | bulk import or event stream |
| used by | end user/customer | internal analyst |
| what data represents | latest state of data | history of events that happened over time |
| dataset size | gigabytes to terabytes | terabytes to petabytes |

**Data Warehousing**

A data warehouse is a separate database that analysts can query without affecting OLTP operations. It's a read-only copy of the data in all the various OLTP systems in the company. Data is dumped periodically into the data warehouse.

**The divergence between OLTP databases and data warehouses**

The two databases are different internally and are optimized for different purposes.

### Stars and Snowflakes: Schemas for Analytics

Data warehouses are used in a formulaic style, known as *star schema* or *dimension modeling*. The name "star schema" comes from the fact that when the table relationships are visualized, the fact table is in the middle, surrounded by its dimension tables; the connections to these tables are like the rays of a star.

Fact table: represents an event, with columns (foreign key references to dimension tables) that represent the who, what, when, where, how, and why.

Snowflake schema: where dimensions are further broken down into subdimensions.

### Column-Oriented Storage

In most OLTP databases, storage is laid out in a row-oriented fashion: all the values from one row of a table are stored next to each other. All attributes on a row will need to be loaded as well. (load from disk into memory, and apply filter)

Column-oriented storage: instead of storing all values from one row together, store all the values from each column instead. The order of the column values are ordered based on the order of their corresponding rows.

