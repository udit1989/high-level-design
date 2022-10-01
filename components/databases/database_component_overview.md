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

A traditional database model usally have two major components

    - Top half : Understanding the user query
    - Bottom half: Execution of the query.

Now the current NO-SQL databases just hold the bottom half, since they don't have a query engine or better to say they have a very thin layer of query engine.


## TOP Half

The main objective of this layer is to understand user's query & correctly execute it.

The optimizations made in this layer actually optimizes the transactions in the lower half. This is the reason why relational databases are faster as compared to non-realtional databases.

The main development in this half contains the optimization of components of top half, and are majorly done in Relational Algebra & Calculus.

## Top Half components

### Parser
The only motto of this component is to parse the query. Not how to get the data.

The parser works with relational algebra, SQL is a calculus which has the same power as relational algebra, but with more degree of freedom, on how to execute a query.

Since realational algebra have unitary level execution and calculus have delta level operations.

The output of a parser is an Abstract Syntax Tree ( AST )

the AST differs from the parser tree, since some elements of parser tree is not necessarily be in AST.

The AST represents a query as a tree of constructs. Each of which is meaningful for the language.

For example:

```
SELECT A.a, A.b 
FROM A WHERE
A.a != 5
```
will be represented in AST as 

![AST](https://user-images.githubusercontent.com/25270515/193409035-9a285ee2-5594-4068-af59-d90aef85e907.png)

Now this AST still does not know how to get the data

```C++

AST* QueryEngine.parser(string query);
```