# DATABASES
---
Key terms:

    - Query Language: 
    A query language is the medium to talk to a data layer, now this layer can be present on different levels. It can be a database layer, or an abstraction layer consisting of the rules in which data can be read, or it can be in API level when talking to a server.

    - Query: 
    A query is a command written in a specific query language for a certain layer. Which usually contains CRUD operations ( Create, Read, Update, Delete).


---

## Datastore
Components of datastore

A traditional database model usually have two major components

    - Top half : Understanding the user query
    - Bottom half: Execution of the query.

Now the current NO-SQL databases just hold the bottom half, since they don't have a query engine or better to say they have a very thin layer of query engine.


## TOP Half

The main objective of this layer is to understand user's query & correctly execute it.

The optimizations made in this layer actually optimizes the transactions in the lower half. This is the reason why relational databases are faster as compared to non-relational databases.

The main development in this half contains the optimization of components of top half, and are majorly done in Relational Algebra & Calculus.

## Top Half components

### Parser


```cpp

AST* QueryEngine.parser(string query);
```

The only motto of this component is to parse the query. Not how to get the data.

The parser works with relational algebra, SQL is a calculus which has the same power as relational algebra, but with more degree of freedom, on how to execute a query.

Since relational algebra have unitary level execution and calculus have delta level operations.

The output of a parser is an Abstract Syntax Tree ( AST )

the AST differs from the parser tree, since some elements of parser tree is not necessarily be in AST.

The AST represents a query as a tree of constructs. Each of which is meaningful for the language.

For example:

```SQL 
SELECT A.a, A.b 
FROM A WHERE
A.a != 5
```
will be represented in AST as 

![AST](https://user-images.githubusercontent.com/25270515/193409035-9a285ee2-5594-4068-af59-d90aef85e907.png)

Now this AST still does not know how to get the data


### Planner

```cpp
QueryPlan QueryEngine.Planner( AST);
```

Planner takes the AST from the parser and makes it meaningful to the Database, by returning a query plan.

The planner returns a resolved_query_plan or a un_optimised_query_plan.

A query_plan simply means making the query contextually relevant to the database. And the planner does the same in a following manner.

1. Takes all the <b>named variables</b> & resolves them to the physical location inside the database. This is done via dB's meta data.

in simple terms consider the example below

```cpp

unordered_map<ASTNode,MemoryAddress> uMap;

AST ast -> (A,A.a,A.b) // named variables

uMap[A]     ->TableID#255
uMap[A.a]   ->T#255[0]
uMap[A.b]   ->T#255[1]

```

[NOTE] But the above is an unoptimised query plan.


2. Map the operators to the physical operators like ```table_scan, index_seek```, which can be executed given the physical location of the tables.


This plan will be guarantee correct, but slow, at-least for moderate  complex queries.


### Optimizer 

```cpp
QueryPlan QueryEngine.Optimizer(QueryPlan);
```

Takes the query plan, optimizes it and returns the query_plan.


ELI5 Optimizer: it reorders the query_plan to obtain the same output from planner's query_plan by reading less data from the disk or by moving less data in between the operations.


There are many ways to do the same, but most of them rely on metadata statistics( which is the recorded ways from past ) about the underlying data.
Or possibly running sample query to understand value distribution before the main query.

For example, the optimizer will try to understand better operation between a ```merge join``` vs  ```hash_join```

The optimizer does not necessarily gets it right all the time, that is why it is asked to <b> write optmized queries in SQL </b> 

## Bottom Half

The objective of this half is to efficiently read/write data & mutating the data present.

These objectives can be taken as 
    - Transactional property
    - Recovery guarantee
    - Storage efficiency 

When we talk about the major NoSQL dBs, it only contains the bottom half.

### Transactions 
    
    - Consistency: the guarantee of providing the same data for a transaction.

Now it depends on the dB to what level they provide consistency.
Most traditional dB provide serializable OR linearizable consistency.

For example:
A NoSQL provides a shard-level consistency.
Where as BigTable provides row level consistency.

## CONSISTENCY 

![Untitled-2022-10-02-0016](https://user-images.githubusercontent.com/25270515/193431881-54414706-2973-4a5c-8ba6-d61870e6b9ce.png)



NoSQL mostly provides Read committed consistency, which makes them harder to utilize in interactive applications.

### ACID properties

Atomicity

    - In plot it stands for all in or nothing. 
    - Either an action in whole will take place or it won't happen at all, if any of its dependency fails.

Consistency

    - Different from distributed system
    - A transaction can only take the database from one state to another, all dependent mutations that might be triggered must be completed before the transaction can be fully committed.

Isolation 

    - Transactions can't affect each other while running.
    - ELI5: A & B transactions takes place. So, it should look like A & B are executed sequentially, does not specify which one gets executed first.

Durability 

    - Written data will not be lost, even on system failure
    - Inception of distributed systems

### Logs

Logs are the key component that gives <b> Durability </b> in ACID.
Fundamentally a log is a write-ahead log. Means the result of a transaction is written in log before is is reflected or displayed in tables.
This gives the recover facility, since the data can be recovered from the log.
Also, allows to go back to a certain commit.
So, log should have before and after data state of a transaction.

### View OR the Storage

This is the actual container to store the data that is present.
One of the most common way to store the data is B+ tree.

Major difference between B tree and B+ tree: all the values are present in the leaf level.

<b>B+ Tree</b>
Better for higher read rates than write rates.

<b>LSM tree</b>

Log structure merge tree

Good for write heavy system, sequentialIO, and lower storage overhead.

![Untitled-2022-10-02-2050](https://user-images.githubusercontent.com/25270515/193473620-b1ae5bc9-e65e-4c5c-afbd-1351b105dc5f.png)

- In LSM tree all the writes are committed to an in-memory log.
- Periodically that record is sorted by ```recordID```, and small lookup table is constructed. The table & sorted logs are committed to the disk.
- As they build up, they are merged together to layer and layered commits.

When <b>READ</b> is requested
- Each log's lookup table is requested, to find the requested recorded.

LSM provide significantly better write throughput as compared to B+ tree.

But, this write-performance has a cost, i.e., write-amplification

<b>Write-Amplification</b>

For each log that is written, that would be re-written several number of times, as the data grows.

For this, reads can still be improved by memoization, but not writes.
