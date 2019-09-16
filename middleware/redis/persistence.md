##### Redis持久化
Redis可以通过三种方式对数据进行持久化
1. RDB  
  保存某个时间的全量数据快照，在Redis配置文件中通过save配置RDB持久化策略，如`save  600 1`表示如果在600s内有一条写入指令，则触发一次RDB持久化，save可以配置多条，这样可以平衡性能，如果不需要RDB持久化方式，通过添加`save ""`即可。另外，RDB持久化相关的配置还有`stop-writes-on-bgsave-error`，如果设置为yes，则表示在bgsave出现错误时停止写入，一般设置为yes，这样可以保证持久化数据一致性。`rdbcompression`表示是否对rdb文件进行压缩。在Redis中可以通过`save`和`bgsave`触发rdb持久化，save会阻塞Redis服务器进程，所以一般不使用这种方式持久化。bgsave会Fork出一个子进程进行快照，不会阻塞服务器进程，可以通过`lastsave`命令判断快照是否完成，lastsave返回一个毫秒级的UNIX时间戳，表示上次快照完成的时间，bgsave会调用源码中的rdbSaveBackground方法。

  bgsave的实现原理是，执行bgsave后，先检查是否有子进程正在进行快照，如果有则返回错误，如果没有，则调用源码中的rdbSaveBackground()，Fork子进程进程快照，实现Copy-on-Write。
 
  RDB优点：全量数据快照，文件小，恢复快  
  RDB缺点：全量数据快照会因为I/O影响性能；可能会丢失最后一次快照后的数据
 
2. AOF  
  即Append-Of-File，保存增删改指令，追加到AOF文件中。通过`appendonly yes`开启AOF持久化，`appendfilename`配置AOF文件，默认AOF文件为appendonly.aof，`appendfsync`配置持久化方式，可以配置`appendfsync always`，`appendfsync everysec`，`appendfsync no`三种方式。

  AOF优点：可读性好，数据不易丢失  
  AOF缺点：文件体积大，恢复时间长

3. RDB-AOF混合持久化  
由于RDB、AOF都各有优缺点，因此可以采用RDB-AOF混合持久化的方式
