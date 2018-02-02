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

TODO: How to effectively perform the query of a non-existing key? Bloom filter seems an memory-efficient data structure to handle the problem with set operations. What exactly the Bloom filter is?
  
## Details of B-Tree
Break database into fixed-size pages (usually 4KB in size) and read or write one page at a time. Pages are on disk and there is a root page, from which a sparse index of all other pages can be found. Each page can be located from a reference from its parent page and values are kept leaf pages. 
Each page has a fixed size, which means at the beginning, there would be some unused space and when there is no more room for a new key-value (or key-reference), the page will be splitted into two. As a result, the whole tree remains balanced with a depth of O(lgn).
Please note that according to the insert order, the keys one a page are not necessary sequential. There can be holes that could be used in later insert.  
