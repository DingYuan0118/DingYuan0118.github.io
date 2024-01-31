# Redis 学习笔记

- [Redis 学习笔记](#redis-学习笔记)
  - [Redis Persistence](#redis-persistence)
  - [Redis Replica](#redis-replica)

## Redis Persistence

- redis 持久化主要依赖两种方式
  - RDB(Redis Database) file: 类似于 DB 快照，存储了当前所有 DB 数据
  - AOF(Append Only File): 类似于 **Mysql binlog** 记录的所有 **write command** 并有对应的 offset 记录 
- RDB 能够用于快速重启， AOF 则能够用于断连后重新 Sync 增量数据。通过结合 RDB 与 AOF 能够保证重启速度与数据完整性。
> https://redis.io/docs/management/persistence/

## Redis Replica
- 同步可分为三种情况
  - well-connected: 使用 stream 传输 command
  - partial resynchronization: 使用 AOF 传输网络断开后丢失的命令
  - full resynchronization: 使用 RDB 传输全量 DataSet 并使用 stream 进行进一步同步
- Redis 用 `(Replication ID, offset)` 元组来指示 DataSet 状态。拥有同样元组意味着拥有着完全一致的 DataSet
- 通常每个 Redis instance 拥有两个 Replication ID。当发生**从升主**事件时，会生成新的 Replication ID 并将旧 ID 记录至 Secondary Replication ID。这是为了防止当出现 redis instance 之间网络连接断开时，同时存在了两个 master, 如果不更改 Replication ID 将会违反 `拥有同样元组意味着拥有着完全一致的 DataSet` 的规定
> https://redis.io/docs/management/replication/