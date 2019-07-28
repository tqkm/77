Redis
---
Redis是基于c语言编写的key-value存储系统，运行与内存中，因此处理速度极快。可以用来当做缓存、非关系型数据库、消息队列<br>
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
1.首先缓存并不是越多越好<br>
2.缓存穿透问题，简单来说，就是设置的缓存没有起到作用，请求都发到DB上，造成DB压力过大。解决方法有缓存空对象，同时设置失效时间<br>
3.无底洞问题<br>
#### redis快的原因
#### redis分片
#### redis集群操作

#### redis操作
zset 有序集合，可以实现排行，但是对于相同数据的无法以先到达的无法排到前面，可以加入时间戳(???)<br>
zadd key score member<br>
ex:<br> 
zadd rank 90 user1<br>
zadd rank 92 user2<br>
zadd rank 94 user3<br>
zadd rank 96 user4<br>
创建一个名为rank的有序集合<br>
查询分数 zscore rank user1   会返回90   zscore rank  user3  会返回94
排序 zrange rank 0 -1 将数据从低到高全部查询出来，不包含分数， zrange rank 0 -1 withscores 会将分数也返回  0，-1代表查询数据坐标，-1是全部查询<br>
zrevrange rank 0 -1 将数据从高到低全部查询出来<br>
