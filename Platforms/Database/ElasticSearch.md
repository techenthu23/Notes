Lucene is a full text search engine and it creates indices on documents. In a paragraph or blob of text, every string is called a term and a sequence of terms is named as a field, and a sequence of fields is named a document. An index contains a sequence of documents and it indexes data as documents.

In books, we usually see an index where all the keywords are written and which helps us to find the actual content. This type of index is called an inverted-index where terms or strings are used to index documents.

# Architecture of elasticsearch

- An index contains one or multiple types.
- A type can be thought of as a table in a relational database.
- A type has one or more documents.
- There are one or more fields in the document.
- Fields are key value pairs.
- A cluster has one or more nodes and are identified by their names
- You can provide name to the node
- If we don't provide a name, a node ID is assigned, which is a random Universally Unique Identifier (UUID) and the node will choose its name as the first seven digits of the automatically generated node ID, which will remain unique for each node.
- It might happen that an index stores huge data and that it might exceed the hardware size of one node. For such cases, an index can be divided into shards.
- There are two types of shards – primary and replica
- Each document, when indexed, is first added to the primary shard and then to one or more replica shards.
- Each shard can have multiple replicas. This complete group is also known as a replication group
- The primary shard makes sure that the document and the operation is valid. If everything is alright, the operation is performed and then the primary shard replicates the same operation at the replica shards as well
- If there are more than one node set up for a cluster, replica shards will be on a different node.

- Document: This is the main data carrier used during indexing and searching, comprising one or more fields that contain the data we put in and get from Lucene.
- Field: This a section of the document, which is built of two parts: the name and the value.
- Term: This is a unit of search representing a word from the text. Each term points to the number of documents it is present in.
- Token: This is an occurrence of a term in the text of the field. It consists of the term text, start and end offsets, and a type.

Apache Lucene writes all the information to a structure called the inverted index. It is a data structure that maps the terms in the index to the documents and not the other way around as a relational database does in its tables. You can think of an inverted index as a data structure where data is term-oriented rather than document-oriented.
