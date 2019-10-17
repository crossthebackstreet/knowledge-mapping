#### MySQL

##### InnoDB与MyISAM的区别
 - InnoDB支持事务，MyISAM不支持
 - InnoDB数据存储在共享表空间，MyISAM数据存储在文件中
 - InnoDB支持行级锁，MyISAM只支持表锁
 - InnoDB支持崩溃后恢复，MyISAM不支持
 - InnoDB支持外键，MyISAM支持
 - InnoDB不支持全文索引，MyISAM支持全文索引

