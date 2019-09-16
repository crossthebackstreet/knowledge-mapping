##### Redis主从同步
Redis同步分为全量同步和增量同步两种，主从会先进行全量同步，之后进行增量同步。  
全量同步  
1. Slave发送sync命令到Master
2. Master调用bgsave()在后台启动一个进程，将Redis中的数据快照保存到文件中
3. Master将快照期间接收到的写命令缓存起来
4. Master快照完成后，将快照文件发送给Slave
5. Slave将数据从文件加载到内存中，使用新的AOF文件替换掉旧的AOF文件
6. Master将快照期间的写命令发送给Slave

增量同步
1. Master接收到用户的操作命令后，判断是否需要传播到Slave
2. 将操作记录追加到AOF文件中
3. 将操作传播到其他Slave
   - 对齐主从库
   - 往响应缓存写入指令
4. 将缓存中的数据发送给Slave
