##### Redis内存淘汰机制
在redis.conf中可以通过修改`maxmemory`设置Redis最大内存使用量，当使用的内存达到上限时，Redis会根据`maxmemory-policy`清除key，如果maxmemory-policy设定为`noeviction`，那么客户端要继续使用内存时，Redis将会返回error。  
Redis中定义了几种maxmemory-policy:
> volatile-lru -> Evict using approximated LRU among the keys with an expire set  
> allkeys-lru -> Evict any key using approximated LRU.  
> volatile-lfu -> Evict using approximated LFU among the keys with an expire set  
> allkeys-lfu -> Evict any key using approximated LFU.  
> volatile-random -> Remove a random key among the ones with an expire set.  
> allkeys-random -> Remove a random key, any key.  
> volatile-ttl -> Remove the key with the nearest expire time (minor TTL)  
> noeviction -> Don't evict anything, just return an error on write operations. 
 
 LRU, LFU, volatile-ttl都是用近似随机算法(approximated randomized algorithms)实现
