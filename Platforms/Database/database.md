---
title: Database
draft: false
---
- [**SQL-based vs NoSQL-based**](#sql-based-vs-nosql-based)
  - [The five critical differences of SQL vs NoSQL](#the-five-critical-differences-of-sql-vs-nosql)
  - [**SQL databases**](#sql-databases)
  - [**NoSQL databases**](#nosql-databases)
  - [**The Scalability**](#the-scalability)
  - [**The Structure**](#the-structure)
  - [Examples](#examples)
- [**The Best SQL Database Systems**](#the-best-sql-database-systems)
  - [**MySQL [**](#mysql-)
  - [**Oracle Database**](#oracle-database)
  - [**Microsoft SQL Server**](#microsoft-sql-server)
  - [**PostgreSQL**](#postgresql)
- [**NoSQL Non-Relational Database Systems**](#nosql-non-relational-database-systems)
  - [**MongoDB**](#mongodb)
  - [**Apache Cassandra**](#apache-cassandra)
  - [**Google Cloud BigTable**](#google-cloud-bigtable)
  - [**Apache HBase**](#apache-hbase)
- [Sharding](#sharding)
  - [What Is Data Sharding?](#what-is-data-sharding)
- [Why Shard a Database?](#why-shard-a-database)
- [The Perils of Manual Sharding](#the-perils-of-manual-sharding)
- [Common Auto-Sharding Architectures](#common-auto-sharding-architectures)
  - [Hash Sharding](#hash-sharding)
  - [Range Sharding](#range-sharding)
  - [Geo-Partitioning](#geo-partitioning)
- [Sharding in YugabyteDB](#sharding-in-yugabytedb)
  - [Hash Sharding](#hash-sharding-1)
  - [Range Sharding](#range-sharding-1)
  - [Geo-Partitioning](#geo-partitioning-1)
- [Summary](#summary)

A database (DB) is a collection of data that lives for a long time.  
A Database Management System (DBMS) is a system (software) that provides an interface to database for information storage and retrieval.

The common features of a DBMS includes

- capacity for large amount of data
- an easy to use interface language (SQL-structured query language)
- efficient retrieval mechanisms
- multi-user support
- security management
- concurrency and transaction control
- persistent storage with backup and recovery for reliability

The users of a database assume difference roles such as

- end user - application programmers that use the DB as a storage subsystem
- designer - application programmers and/or business analysts who design the layout of the DB
- administrator - operators who maintain the heath and efficiency of the DB
- implementor - programmers who maintain and develop the DBMS

The key concepts of database includes

- schema - the structure and the constraints of data
- data - the actual content of the DB representing information
- data definition language - used to specify the schema
- data manipulation and query language - used to change the data and query them

## **SQL-based vs NoSQL-based**

### The five critical differences of SQL vs NoSQL

1. SQL databases are relational, NoSQL are non-relational.
2. SQL databases use structured query language and have a predefined schema. NoSQL databases have dynamic schemas for unstructured data.
3. SQL databases are vertically scalable, NoSQL databases are horizontally scalable.
4. SQL databases are table based, while NoSQL databases are document, key-value, graph or wide-column stores.
5. SQL databases are better for multi-row transactions, NoSQL are better for unstructured data like documents or JSON

### **SQL databases**

SQL databases [use structured query language (SQL) for defining and manipulating data. On one hand, this is extremely [powerful: SQL is one of the most versatile and widely-used options available, making it a safe choice and especially great for complex queries. On the other hand, it can be restrictive. SQL requires that you use predefined schemas to determine the structure of your data before you work with it. In addition, all of your data must follow the same structure. This can require significant up-front preparation, and, as with Town A, it can mean that a change in the structure would be both difficult and disruptive to your whole system.

### **NoSQL databases**

NoSQL databases, on the other hand, have dynamic schemas for unstructured data, and data is stored [in many ways: They can be column-oriented, document-oriented, graph-based or organized as a KeyValue store. This flexibility means that:

- You can create documents without having to first define their structure
- Each document can have its own unique structure
- The syntax can vary from database to database, and
- You can add fields as you go.

### **The Scalability**

In most situations, SQL databases are vertically scalable, which means that you can increase the load on a single server by increasing things like CPU, RAM or SSD. NoSQL databases, on the other hand, are horizontally scalable. This means that [you handle more traffic by sharding, or adding more servers in your NoSQL database. It’s like adding more floors to the same building versus adding more buildings to the neighborhood. The latter can ultimately become larger and more powerful, making NoSQL databases the preferred choice for large or ever-changing data sets.

### **The Structure**

SQL databases are table-based, while NoSQL databases are either [document-based, key-value pairs, graph databases or wide-column stores. This makes relational SQL databases a better option for applications that require multi-row transactions - such as an accounting system - or for legacy systems that were built for a relational structure.

### Examples

SQL databases include [MySQL](https://www.xplenty.com/integrations/mysql/), [Oracle](https://www.xplenty.com/integrations/oracle/), [PostgreSQL](https://www.xplenty.com/integrations/postgresql/), and [Microsoft SQL Server](https://www.xplenty.com/integrations/microsoft-sql-server/).

NoSQL database examples include [MongoDB](https://www.xplenty.com/integrations/mongodb/), BigTable, Redis, RavenDB Cassandra, HBase, Neo4j and CouchDB.

## **The Best SQL Database Systems**

Now that we’ve established the key structural differences between SQL and NoSQL databases, let’s delve a little deeper into this topic by reviewing the best SQL and NoSQL database options available right now.

We'll start with SQL database systems. Keep in mind that the best SQL database systems now offer compatibility with NoSQL. Nevertheless, they still work best with relational SQL structures.

### **MySQL [**

Here are some [MySQL](https://www.mysql.com/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [benefits and strengths:

- **Owned by Oracle: [** Although MySQL is free and open-source, the database system is owned and managed by Oracle.
- **Maturity** : MySQL is an extremely [established database, meaning that there’s a huge community, extensive testing and quite a bit of stability.
- **Compatibility** : MySQL is available for all major platforms, including Linux, Windows, Mac, BSD, and Solaris. It also has connectors to languages like Node.js, Ruby, C#, C++, Java, Perl, Python, and PHP, meaning that it’s not limited to SQL query language.
- **Cost-effective** : The database is open-source and free.
- **Replicable** : The MySQL database can be replicated across multiple nodes, meaning that the workload can be reduced and the scalability and availability of the application can be increased.
- **Sharding** : While sharding cannot be done on most SQL databases, it can be done on MySQL servers. This is both cost-effective and good for business.
- **Who Should Use It? [** MySQL is a strong choice for any business that will benefit from its pre-defined structure and set schemas. For example, applications that require multi-row transactions - like accounting systems or systems that monitor inventory - or that run on legacy systems will thrive with the MySQL structure.

### **Oracle Database**

Another popular SQL database system, particularly with enterprise-level organizations, is Oracle Database. [Oracle Database](https://www.oracle.com/database/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [offers the following strengths and benefits:

- **Professionally developed and managed: [** Oracle develops and manages the Oracle Database system. As a commercial option, this relational database management system benefits from frequent updates and excellent customer support.
- **A Unique SQL "dialect": [** Oracle Database uses its own dialect of SQL known as PL/SQL (Procedural Language/SQL). This language differs in small ways from traditional SQL, primarily in how it deals with stored procedures, built-in functions, and variables.
- **Expensive: [** As a professionally developed and managed database system, Oracle is one of the most expensive options available.
- **Compatibility: [** Oracle Database is available for any operating system.
- **DBMS Organization: [** Oracle groups its objects by schemas that are a subset of database objects.
- **Large database sizes: [** Oracle can handle extremely [large databases, making it an excellent choice for enterprise companies with large data needs.
- **Easy to upgrade: [** With Oracle Database, you can complete an upgrade without a needing to overhaul the system completely.
- **Transaction control:**  [With Oracle, new database connections are new transactions. You can make rollbacks and changes because values won't change prior to commit.
- **Other benefits:**  [Oracle offers bitmap indexing, partitioning, function-based indexing, reverse-key indexing, and star query optimization.
- **Who Should Use It? [** Oracle Database is an excellent database choice, but the costs could prevent small-to-medium-sized organizations from taking advantage of it. For an enterprise organization that has large data needs and a generous budget, this solution could be a match.

### **Microsoft SQL Server**

[Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2019?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [is a popular option for small-to-medium-sized companies. It offers the following benefits and advantages:

- **Professionally developed and managed: [** Microsoft develops and manages the Microsoft SQL Server database system. As a commercial relational database management system, customers benefit from frequent updates and great user support.
- **A Unique SQL "Dialect": [** SQL Server employs its own dialect of SQL, called T-SQL (Transact SQL). Like Oracle, this differs from traditional SQL in how it handles built-in functions, stored procedures, and variables.
- **Compatibility:**  [SQL Server only works with Windows and Linux based systems.
- **Transaction control:**  [Since SQL Server has a separate execution of each command, it's hard to make adjustments mid-process when errors are found.
- **DBMS Organization: [** SQL Server organizes tables, procedures, and views according to database names.
- Easy to use: SQL Server has a reputation for being easy to use. According to [this reviewer](https://www.softwareadvice.com/bi/microsoft-sql-profile/?utm_source=xp&utm_medium=blog&utm_campaign=refresh): "_The interface is easy to understand, the error-checking is strong (and it actually [tells you what is wrong)._"
- **Excellent support:**  [As a Microsoft product, SQL Server includes live product support, and excellent documentation.
- **Other features:**  [SQL Server features some great tools and features like BI tools, Database Tuning Advisor, SQL Server Management Studio, and SQL Server Profiler.
- **Who Should Use It? [** Microsoft SQL Server is an excellent choice for small-to-medium-sized organizations that need a high-quality, professionally-managed database system with excellent support, but don't require the cost or scalability of an enterprise solution like Oracle.

### **PostgreSQL**

We listed [PostgreSQL](https://www.postgresql.org/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [last among the SQL DBMS's because it's a hybrid SQL/NoSQL database system that finds a middle-ground between these two options. PostgreSQL offers the following strengths and benefits:

- **Cost-effectiveness: [** PostgreSQL is a free and open-source database system. The PostgreSQL Global Development Group develops and manages the system.
- **Compatibility: [** PostgreSQL is available for a variety of operating systems, including HP-UX, FreeBSD, Linux, OpenBSD, NetBSD, OS X, Unix, Solaris, and Windows. It also offers support for the languages [.Net, C++, C, Java, Delphi, Perl, PHP, JavaScript (Node.js), Python, and Tsl.
- **ORDBMS: [** PostgreSQL is an "Object Oriented Database Management System" (ORDBMS), not simply a "Relational Database Management System (RDBMS). This means it serves as a hybrid between a strictly relational model (SQL) and a strictly object-oriented model (NoSQL).
- **User support: [** PostgreSQL doesn't have its own customer support, per se, but there is an active community that will readily provide free support. Moreover, excellent [paid support options](https://www.postgresql.org/support/professional_support/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [are available from third-party service providers.
- **High ACID Compliance:**  [PostgreSQL is known for offering the highest levels of atomicity, consistency, isolation, and durability. These are the four standards experts use to judge the quality of a database design. Learn more about ACID compliance [here](https://www.techopedia.com/definition/23949/atomicity-consistency-isolation-durability-acid?utm_source=xp&utm_medium=blog&utm_campaign=refresh).
- **Pure SQL:**  [Another benefit of PostgreSQL is the fact that it utilizes one of the purest forms of SQL available, as opposed to other database systems that often have unique variances.
- **Who Should Use It? [** As a hybrid between a relational database and an object-oriented database, PostgreSQL is excellent when your data doesn't mesh well with a perfectly relational model. It works great for extra-large databases and for performing complicated queries.

## **NoSQL Non-Relational Database Systems**

Now, let's move onto the various NoSQL non-relational database systems. These systems require a little more technical expertise to understand. We'll start with MongoDB.

### **MongoDB**

The following are some of the benefits and strengths of [MongoDB](https://www.mongodb.com/?utm_source=xp&utm_medium=blog&utm_campaign=refresh):

- **Free to use: [** Since October 2018, MongoDB's updates have been published under the [Server Side Public License (SSPL) v1](https://www.mongodb.com/licensing/server-side-public-license?utm_source=xp&utm_medium=blog&utm_campaign=refresh), and the database is [free to use.
- **Dynamic schema** : As mentioned, this gives you the flexibility to change your data schema without modifying any of your existing data.
- **Scalability** : MongoDB is horizontally scalable, which helps reduce the workload and scale your business with ease.
- **Manageability** : The database doesn’t require a database administrator. Since it is fairly user-friendly in this way, it can be used by both developers and administrators.
- **Speed** : It’s high-performing for simple queries.
- **Flexibility** : You can add new columns or fields on MongoDB without affecting existing rows or application performance.
- **Not Acid Compliant: [** As a NoSQL database, MongoDB is not ACID compliant. See PostgreSQL above for more about ACID compliance.
- **MongoDB Atlas (a new feature): [**MongoDB recently added [MongoDB Atlas global cloud database technology [](https://www.mongodb.com/cloud/atlas?utm_source=xp&utm_medium=blog&utm_campaign=refresh)to its offerings. This feature allows you to deploy fully-managed MongoDB via AWS, Azure, or GCP. MongoDB Atlas lets you use drivers, integrations, and tools to reduce the time required to manage your database. Here's the [pricing information from Atlas](https://www.mongodb.com/atlas-vs-amazon-documentdb/pricing?utm_source=xp&utm_medium=blog&utm_campaign=refresh).
- **Who Should Use It?**  [MongoDB is a good choice for businesses that have rapid growth or databases with no clear schema definitions (i.e., you have a lot of unstructured data). If you cannot define a schema for your database, if you find yourself denormalizing data schemas, or if your data requirements and schemas are constantly evolving - as is often the case with mobile apps, real-time analytics, content management systems, etc. - MongoDB can be a strong choice for you.

### **Apache Cassandra**

[Apache Cassandra](http://cassandra.apache.org/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [(or Cassandra DB) was originally a Facebook product, but in 2008, Facebook released it to the world as a free, open-source NoSQL database system. Here are some of Cassandra's benefits and strengths:

- **Free and Open-Source: [** After Facebook made Cassandra open-source, Apache took over the project in 2010.
- **Highly scalable: [** Cassandra benefits from a "masterless design." That means all of its nodes are identical, which creates operational simplicity, making it easy to scale up to a larger database architecture.
- **Active everywhere:**  [Users can write and read from all Cassandra nodes.
- **Fast writes and reads:**  [Cassandra's design speeds up read and write commands tremendously via its distributed, highly-available organization, even in the case of massive projects.
- **Not ACID Compliant: [** As a NoSQL database, Cassandra is not ACID compliant, instead Cassandra offers atomic, isolated and durable transactions with eventual consistency.
- **Support for SQL: [** Even though it's not ACID compliant, Cassandra does offer some support for SQL via SQL-like DDL, DML, and SELECT statements.
- **Poor with updating and deleting data: [** Cassandra is not optimized for updating and deleting data.
- **Offers excellent data protection: [** Cassandra features a commit log design that makes sure data isn't lost. It also features backup/restore which adds additional data protection.
- **Redundancy of data and node function:**  [Cassandra offers constant uptime and eliminates singular points of failure.
- **Who Should Use It? [** Cassandra is most popular for use with IoT (internet of things) technology because it offers fast, real-time insights. It excels at writing time-based log activities, error logging, and sensor data. If you need fast read and write processing, Cassandra could be your database. Cassandra is also good for those who want to work with SQL-like data types on a NoSQL database.

### **Google Cloud BigTable**

As a Google product, [Google Cloud BigTable](https://cloud.google.com/bigtable/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [is not free, but it comes with distinct advantages that may be worth [the price required to use it](https://cloud.google.com/bigquery/pricing?utm_source=xp&utm_medium=blog&utm_campaign=refresh). Now let's take a [look at the advantages of BigTable:

- **Low latency: [** According to Google, BigTable offers a consistent sub-10ms latency.
- **Replication:**  [Through replication, BigTable provides higher availability, durability, and resilience when zonal failures happen. [Replication also offers](https://cloud.google.com/bigtable/?utm_source=xp&utm_medium=blog&utm_campaign=refresh) ["high availability for live serving apps, and workload isolation for serving vs. analytics."
- **Machine learning: [** BigTable features a storage engine for use with machine learning applications.
- **Easy to integrate: [** Integrates well with open-source data analytics tools.
- **Highly scalable: [** Google BigTable can work with massive data sources in the hundreds of petabytes scale.
- **Fully managed with Integrations:**  [Like MongoDB Atlas, BigTable is fully managed, which reduces workload requirements. It also integrates instantly with many platforms, which streamlines the ETL processes required to load data.
- **Highly compatible with Google services:**  [As a Google product, BigTable integrates well with other services under the Google umbrella.
- **When Should You Use It? [** According to Google, BigQuery is great for fintech, IoT, and advertising technology as well as other use cases. For fintech, you can create a check for fraud patterns and watch real-time transaction information. You can also save and consolidate financial market data, trading activity, and more. For IoT, you can ingest and understand massive amounts of real-time [time series data recorded from sensors to create dashboards and valuable analytics. For advertising, you can gather large amounts of customer behavior data to find patterns that inform your marketing efforts.

### **Apache HBase**

As a database modeled after Google BigQuery, [Apache Hbase [](https://hbase.apache.org/book.html?utm_source=xp&utm_medium=blog&utm_campaign=refresh)was created to work with large datasets. Here are some of the benefits and strengths of HBase:

- **Open-source and free: [** Apache HBase is an open-source, free, NoSQL database system managed by Apache. It was modeled after Google Cloud BigTable (above), to offer BigTable-like features on top of the [Hadoop Distributed File System](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [(HDFS).
- **Massive tables:**  [HBase was specifically created to manage large datasets.
- **Scales across a cluster: [** Hbase is excellent at scaling across a [cluster](https://www.dummies.com/programming/big-data/data-science/clustering-algorithms-used-in-data-science/?utm_source=xp&utm_medium=blog&utm_campaign=refresh). Clusters relate to clustering algorithms, which are used to derive machine learning insights from data.
- **Data management: [** HBase organizes rows into "regions." The regions determine how the table will be divided across more than one node that make up a cluster. If one of the regions is too big, HBase automatically breaks it up to evenly distribute the load across more than one server.
- **Works with both unstructured and semi-structured data: [** As a NoSQL database, HBase is ideal for storing both semi-structured and structured information.
- **Consistency: [** HBase offers fast, consistent processing of read and write commands. After performing a write, all of the read requests on the data will produce the same response.
- **Failover:**  [HBase uses replication to offer failover, which reduces or eliminates the negative impact of a system failure on users.
- **Sharding:**  [HBase offers automatic and configurable [sharding](https://www.techopedia.com/definition/22041/sharding?utm_source=xp&utm_medium=blog&utm_campaign=refresh) [for tables.
- **When Should You Use It? [** The Apache HBase website advises to use HBase "when you need random, realtime read/write access to your big data." The database is designed to host massive tables of information that include billions of rows and millions of columns.

## Sharding

Enterprises of all sizes are embracing rapid modernization of user-facing applications as part of their broader digital transformation strategy. The relational database (RDBMS) infrastructure that such applications rely on suddenly needs to support much larger data sizes and transaction volumes. However, a monolithic RDBMS tends to quickly get overloaded in such scenarios. One of the most common architectural patterns used to scale an RDBMS is to “shard”

### What Is Data Sharding?

Sharding is the process of breaking up large tables into smaller chunks called [ **shards**  [that are spread across multiple servers. A [ **shard**  [is essentially a horizontal data partition that contains a subset of the total data set, and hence is responsible for serving a portion of the overall workload. The idea is to distribute data that can’t fit on a single node onto a [ **cluster**  [of database nodes. Sharding is also referred to as [ **horizontal partitioning**. The distinction between horizontal and vertical comes from the traditional tabular view of a database. A database can be split vertically — storing different table columns in a separate database, or horizontally — storing rows of the same table in multiple database nodes.

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/data-sharding-distributed-sql-1.png" alt="" class="wp-image-1008"></figure>

_Figure 1 : Vertical and Horizontal Data Partitioning (Source: Medium)_

## Why Shard a Database?

Business applications that rely on a monolithic RDBMS hit bottlenecks as they grow. With limited CPU, storage capacity, and memory, query throughput and response times are bound to suffer. When it comes to adding resources to support database operations, vertical scaling (aka scaling up) has its own set of limits and eventually reaches a point of diminishing returns.

On the other hand, horizontally partitioning a table means more compute capacity to serve incoming queries, and therefore you end up with faster query response times and index builds. By continuously balancing the load and data set over additional nodes, sharding also enables usage of additional capacity. Moreover, a network of smaller, cheaper servers may be more cost effective in the long term than maintaining one big server.

Besides resolving scaling challenges, sharding can potentially alleviate the impact of unplanned outages. During downtime, all the data in an unsharded database is inaccessible, which can be disruptive or downright disastrous. When done right, sharding can ensure high availability: even if one or two nodes hosting a few shards are down, the rest of the database is still available for read/write operations as long as the other nodes (hosting the remaining shards) run in different failure domains. Overall, sharding can increase total cluster storage capacity, speed up processing, and offer higher availability at a lower cost than vertical scaling.

## The Perils of Manual Sharding

Sharding, including the day-1 shard creation and day-2 shard rebalancing, when completely automated can be a boon to high-volume data applications. Unfortunately, monolithic databases like Oracle, PostgreSQL, MySQL, and even newer distributed SQL databases like Amazon Aurora do not support automatic sharding. This means manual sharding at the application layer has to be performed if you want to continue to use these databases. The net result is a massive increase in development complexity. Your application now has additional sharding logic to know exactly how your data is distributed, and how to fetch it. You also have to decide what sharding approach to adopt, how many shards to create, and how many nodes to use. And also account for shard key as well as even sharding approach changes if your business needs change.

One of the most significant challenges with manual sharding is uneven shard allocation. Disproportionate distribution of data could cause shards to become unbalanced, with some overloaded while others remain relatively empty. It’s best to avoid accruing too much data on a shard, because a hotspot can lead to slowdowns and server crashes. This problem could also arise from a small shard set, which forces data to be spread across too few shards. This is acceptable in development and testing environments, but not in production. Uneven data distribution, hotspots, and storing data on too few shards can all cause shard and server resource exhaustion.

Finally, manual sharding can complicate operational processes. Backups will now have to be performed for multiple servers. Data migration and schema changes must be carefully coordinated to ensure all shards have the same schema copy. Without sufficient optimization, database joins across multiple servers could highly inefficient and difficult to perform.

## Common Auto-Sharding Architectures

Sharding has been around for a long time, and over the years different sharding architectures and implementations have been used to build large scale systems. In this section, we will go over the three most common ones.

### Hash Sharding

Hash sharding takes a shard key’s value and generates a hash value from it. The hash value is then used to determine in which shard the data should reside. With a uniform hashing algorithm such as ketama, the hash function can evenly distribute data across servers, reducing the risk of hotspots. With this approach, data with close shard keys are unlikely to be placed on the same shard. This architecture is thus great for targeted data operations.

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/image2.png" alt="" class="wp-image-1600"></figure>

_Figure 2: Hash sharding [_

### Range Sharding

Range sharding divides data based on ranges of the data value (aka the keyspace). Shard keys with nearby values are more likely to fall into the same range and onto the same shards. Each shard essentially preserves the same schema from the original database. Sharding becomes as easy as identifying the data’s appropriate range and placing it on the corresponding shard.

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/image1.png" alt="" class="wp-image-1599"></figure>

_Figure 3 : Range sharding_

Range sharding allows for efficient queries that reads target data within a contiguous range or range queries. However, range sharding needs the user to apriori choose the shard keys, and poorly chosen shard keys could result in database hotspots.

A good rule-of-thumb is to pick shard keys that have large cardinality, low recurring frequency, and that do not increase, or decrease, monotonically. Without proper shard key selections, data could be unevenly distributed across shards, and specific data could be queried more compared to the others, creating potential system bottlenecks in the shards that get a heavier workload.

The ideal solution to uneven shard sizes is to perform automatic shard splitting and merging. If the shard becomes too big or hosts a frequently accessed row, then breaking the shard into multiple shards and then rebalancing them across all the available nodes leads to better performance. Similarly, the opposite process can be undertaken when there are too many small shards.

### Geo-Partitioning

In geo-based (aka location-aware) sharding, data is first partitioned according to a user-specified column that maps range shards to specific regions and the nodes in those regions. Inside a given region, data is then sharded using either hash or range sharding. For example, a cluster that runs across 3 regions in the US, UK, and the EU can rely on the Country\_Code column of the User table to map the user’s row to the nearest region that is in conformance with GDPR rules.

## Sharding in YugabyteDB

YugabyteDB is an auto-sharded, ultra-resilient, high-performance, geo-distributed SQL database built with inspiration from Google Spanner. It currently supports hash and range sharding. Geo-partitioning is an active work-in-progress feature. Each data shard is called a tablet, and it resides on a corresponding tablet server.

### Hash Sharding

For hash sharding, tables are allocated a hash space between 0x0000 to 0xFFFF (the 2-byte range), accommodating as many as 64K tablets in very large data sets or cluster sizes. Consider a table with 16 tablets as shown in Figure 4. We take the overall hash space [0x0000 to 0xFFFF), and divide it into 16 segments — one for each tablet.

 [

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/data-sharding-distributed-sql-4.png" alt="" class="wp-image-1011"></figure>

_Figure 4: Hash sharding in YugabyteDB_

In read/write operations, the primary keys are first converted into internal keys and their corresponding hash values. The operation is served by collecting data from the appropriate tablets. (Figure 3)

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/data-sharding-distributed-sql-5.png" alt="" class="wp-image-1010"></figure>

_Figure 5: Figuring out which tablet to use in YugabyteDB_

As an example, suppose you want to insert a key k, with a value v into a table as shown in Figure 6, the hash value of k is computed, and then the corresponding tablet is looked up, followed by the relevant tablet server. The request is then sent directly to that tablet server for processing.

<figure class="aligncenter"><img src="%7B%7B%20site.baseurl%20%7D%7D/assets/images/2021/02/data-sharding-distributed-sql-6.png" alt="" class="wp-image-1009"></figure>

_Figure 6 : Storing value of k in YugabyteDB_

### Range Sharding

SQL tables can be created with ASC and DESC directives for the first column of a primary key as well as first of the indexed columns. This will lead to the data getting stored in the chosen order on a single tablet. This single tablet can be either [split automatically](https://docs.yugabyte.com/latest/architecture/docdb-sharding/tablet-splitting/#automatic-tablet-splitting-beta) [(once it reaches certain size) or can be split manually whenever desired. For high performance workloads with well-known split points, the [`SPLIT AT`](https://github.com/YugaByte/yugabyte-db/issues/1486) [clause can be added at table creation time to specify the exact ranges.

### Geo-Partitioning

Row-level geo-partitioning is an active work in progress. [Design documentation](https://github.com/yugabyte/yugabyte-db/blob/master/architecture/design/ysql-row-level-partitioning.md) [and [current status](https://github.com/yugabyte/yugabyte-db/issues/1958) [are both available on GitHub.

## Summary

Data sharding is a solution for business applications with large data sets and scale needs. There are a variety of sharding architectures to choose from, each of which provides different capabilities. Before settling on a sharding architecture, the needs and workload requirements of your application must be mapped out. Manual sharding should be avoided in most circumstances given significant increase in application logic complexity
