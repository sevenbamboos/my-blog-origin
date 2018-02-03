---
title: Memo of Design Data-intensive Applications
categories: Programming
date: 2018-02-02 10:13:33
---

It's a summary and abstraction of "Design Data-intensive Applications" (DDIA), which is an excellent book covering a vast majority domain of database administration and distribute systems.

<!-- more -->

# Chapter 1. Reliable, scalable, and maintainable

## Latency and response time
Latency = network delay of request + wait time of be handled.

Response time = Latency + service time + network delay of response.

## Mean, median and percentile
To describe performance, mean (average) is not good enough for a typical response time.
Median is the performance of half of response.

Percentile is the performance of xx% of response. For example, p95 is the threshold that 95% responses are faster than that figure.

# Chapter 2. Data Model and query language

Document database targets self-contained documents and relationships between documents are rare.

Rational database can handle all kinds of relationships.

Graph database targets use cases where anything is potentially related to everything.

Rational database enforces an explicit schema (data processing on write), while NoSQL needs an implicit schema (handled on read by application code). The comparison looks like static-type language with the help of compiler and dynamic-type language needs runtime check by application code.

Rational database has another advantage in that the query language, SQL, is declarative and conform to standard. 

# Chapter 3. Storage and retrieval

## Two categories of storage engines
Online transaction processing (OLTP) handles a huge volume of requests and each of them asks for a small number of records using a key (with the help of index). Disk seek time is the bottleneck.

Online analytics processing (OLAP) handles requests that ask for many records. Disk bandwidth is the bottleneck.

## Two categories of OLTP storage engines
Log-structured which appends to files and deletes obsolete files, but never updates a file in place. It turns random-access writes into sequential writes on disk, which improves write throughput.

B-tree which treats disk as fixed-size pages and is used by most databases.

## Details of SSTable and LSM-tree
Keep sorted sequences of key-value pairs (called Sorted String Table SST) in memory. Periodically, SST segments are merged together in the way of mergesort. When the same key exists in multiple segments, use the value from the latest segment (because segments are created and written one at a time). The segments can be saved on disk. Anyway, there is a sparse index in memory, keeping offsets to some keys (not all the keys because it's not necessary).

To prevent data loss of SST (before being saved on disk), save a temporary log file (not in a sorted order because it should have too much data and the purpose is to replay them one by one to restore the lost SST).

### TODO Bloom filter
How to effectively perform the query of a non-existing key? _Bloom filter_ seems an memory-efficient data structure to handle the problem with set operations.
  
## Details of B-Tree
Break database into fixed-size pages (usually 4KB in size) and read or write one page at a time. Pages are on disk and there is a root page, from which a sparse index of all other pages can be found. Each page can be located from a reference from its parent page and values are kept leaf pages. 

Each page has a fixed size, which means at the beginning, there would be some unused space and when there is no more room for a new key-value (or key-reference), the page will be split into two. As a result, the whole tree remains balanced with a depth of O(lgn).

Please note that according to the insert order, the keys on a page are not necessary sequential. There can be holes that could be used in later insert.  

### Tip of date table
Design a date table with columns: pk, year, month, day, weekday, is_holiday. It has two advantages: efficient for query by time (year/month/day/weekday) and have a place to put additional date info like is_holiday.

### TODO R-trees
Multi-column index can be done by concatenated index, which explains why columns have to be in a fixed order in a query to make the index work.

Multi-dimensional index is a more general index. The idea is to translate multi-dimensional data into one figure to be used as an index. Refer to _R-trees_.

## Column-oriented storage
For a big table with many columns and not all of the columns are used in each query, store each column in a separate file. The advantage is column data can be compressed, especially for the major column that's used to sort and the column data is saved in an order.

It's less efficient for writing data to column-oriented storage. Normally, LSM-trees are kept in memory after a write and be persisted on disk by a background process.

# Chapter 4. Encoding and evolution

## JSON and XML
XML can't distinguish a number and a string that happens to have digits.

JSON can't distinguish integers and floating-point numbers. For Javascript, integers greater than power(2, 53) can't be exactly represented in double-precision floating-point number so it becomes inaccurate after the parsing.

Binary strings (bytes sequence without a character encoding) in JSON and XML can be encoded using Base64 with a penalty of increasing size of 33%.

## Binary encoding libraries
Thrift and Protocol Buffers require a schema as interface definition language (IDL) so that the encoded data only needs a numeric tag instead of name and type for each field.

Avro has writer's schema and reader's schema and they don't have to be the same. They should be compatible (for example new fields with default value).

## Type schema as a better documentation
Schema is used in encoding and decoding and therefore is guaranteed to be up-to-date and better than manually maintained documentation.

## Message broker over direct RPC
Served as a buffer when the recipient is unavailable or overloaded.

Decouple sender from recipient.

Allow one message to multiple recipients.

# Chapter 5. Replication

Single-leader: writes send to a single node (leader), read from any replica but could be stale if from nodes other than leader (followers). But all nodes can be eventually consistent. No need to worry about write conflicts.

Multi-leader: writes send to one of leaders. A typical use case is that there is a leader in each data center. Read-after-write consistency is that users should always see data submitted by themselves. 

Leaderless: for n replicas, every write is confirmed by w nodes and query r nodes for each read. When w + r > n, the result is supposed to be up-to-date (Quorum reads and writes). 

### Three-way merge
Suppose c is the common ancestor of a and b. Merge a and b to d is three-way merge.

### Tip of how to merge deleted record
Instead of remove the record from a data container, leaving a deletion marker in place is more convenient.

# Chapter 6. Partitioning

## Two approaches to partitioning
Key range: keys are sorted and each partition owns a range of keys. There is a risk of hot spots if application accesses keys close together. Normally, partitions are rebalanced by splitting the key range into two subranges when the data gets too big.

Hash: hash function applied to each key and each partition owns a range of hashes. No ordering of keys makes range query inefficient but makes distribute load evenly.

## Two methods of secondary index
Document-partitioned: local indexes. One partition needs to be updated on write, but read of the secondary index needs scatter/gather across all partitions.

Term-partitioned: global indexes. The pros and cons are the opposite way of document-partitioned.
 
# Chapter 7. Transaction


