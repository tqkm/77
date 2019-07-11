Redis
---
Redis是基于c语言编写的key-value存储系统，运行与内存中，因此处理速度极快。可以用来当做缓存、非关系型数据库<br>
#### redis的数据结构
string、hash、list、set、zset，而memcached只支持string<br>
#### redis持久化策略
快照RDB与日志AOF，RDB定期拍摄快照，将redis的数据定期写入一个dump文件中，适合做为冷备份使用，是redis的默认持久化策略，AOF是用日志方式，当写入一条命令时，
就往日志中写入一条set语句，其策略有always，everysec，no。使用哪种持久化方式需要依场景而定
#### redis的淘汰策略
1. allkeys-random<br>
2. allkeys-lru<br>
3. volatile-random<br>
4. volatile-lru<br>
5. volatile-ttl 针对设置了expire的key，优先删除剩余时间少的key<br>
6. noeviction 不删除策略
#### redis作为缓存常见问题
1.首先缓存并不是越多越好
2.缓存穿透问题，简单来说，就是设置的缓存没有起到作用，请求都发到DB上，造成DB压力过大。解决方法有缓存空对象，同时设置失效时间
3.无底洞问题
#### redis快的原因
#### redis分片
#### redis集群操作
