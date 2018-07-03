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

> **MySQL** - Fine, But What Choices Do I Have in MySQL? [link](https://www.percona.com/blog/2017/02/07/overview-of-different-mysql-replication-solutions/)

> **MongoDB** - Secondaries apply operations from the primary asynchronously. [link](https://docs.mongodb.com/manual/replication/#asynchronous-replication)

#### Handling Node Outages
- slave failure - catch-up recovery 
- master failure - via election
- discard unreplicated writes. Note, it leads to issues such as *Gihub issue with Mysql and Redis*

> **MySQL** - TBD

> **MongoDB** - When a primary does not communicate with the other members of the set for more than the configured electionTimeoutMillis period (10 seconds by default), an eligible secondary calls for an election to nominate itself as the new primary. [link](https://docs.mongodb.com/manual/replication/#automatic-failover)

#### Implementation of replication logs
- Statement Based (default for mysql prior 5.1)
- WAL (Oracle, Postgre)
- Row-based (Mysql)
> Row-Based Replication Vs. Statement-Based Replication in MySQL [link](https://www.percona.com/blog/2017/02/07/overview-of-different-mysql-replication-solutions/)

> **MySQL** - TBD

> **MongoDB** - TBD

### Replication Lag
