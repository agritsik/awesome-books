## 5. Replication


### Goals
1. Scalability
1. High-availability
1. Latency

### Master-Slave
> Default for relational databases, Mongo, Kafka, Rabbit etc

#### Async vs sync communication.
- Standard asynchronous replication
- Semi-synchronous replication
- Custom
> Fine, But What Choices Do I Have in MySQL? [link](https://www.percona.com/blog/2017/02/07/overview-of-different-mysql-replication-solutions/)

#### Handling Node Outages
- slave failure - catch-up recovery 
- master failure - via election
- discard unreplicated writes. Note, it leads to issues such as *Gihub issue with Mysql and Redis*

#### Implementation of replication logs
- Statement Based (default for mysql prior 5.1)
- WAL (Oracle, Postgre)
- Row-based (Mysql)
> Row-Based Replication Vs. Statement-Based Replication in MySQL [link](https://www.percona.com/blog/2017/02/07/overview-of-different-mysql-replication-solutions/)

### Replication Lag
