---
layout: blog
banana: true
title:  Redis, Memcache differences and specific application scenarios and options
category: SOA/Test
date:   2017-07-30 10:06:42
background-image: https://icitum.github.io/web/soa-images/2017-08-01-24280498.jpg
tags:
- Redis
- memcache
- apache2
- owncloud
- Load balancing
- stackoverflow
---
# Introduction to Memcached

Memcached is a high-performance distributed memory cache server developed by Bard Fitzpatric of Danga Interactive of LiveJurnal. It is essentially a memory key-value database, but does not support data persistence, and all data is lost after the server is shut down. Memcached is developed using C language. On most POSIX systems such as Linux, BSD and Solaris, libevent is ready for use. On Windows, it also has an unofficial version available (http://code.jellycan.com/memcached/). Memcached has many client software implementations, including C/C++, PHP, Java, Python, Ruby, Perl, Erlang, Lua, and so on. Currently Memcached is widely used, in addition to LiveJournal, Wikipedia, Flickr, Twitter, Youtube, and WordPress.
 

In the Windows system, Memcached installation is very convenient, just download the executable from the above address and then run memcached.exe -d install to complete the installation. Under Linux and other systems, we first need to install libevent, and then get the source code, make && make install. By default, Memcached's server startup program is installed in the /usr/local/bin directory. When starting Memcached, we can configure different startup parameters for it.
 

## Memcache Configuration

The Memcached server needs to configure key parameters at startup. Here we take a look at the key parameters that Memcached needs to set at startup and the role of these parameters.
 

1 -p <num> The TCP listening port of Memcached. The default configuration is 11211.

2 -U <num> The UDP listening port of Memcached. The default configuration is 11211. When it is 0, UDP listening is disabled.

3 -s <file> UNIX socket path where Memcached listens;

4 -a <mask> Octal mask for accessing UNIX sockets, default configuration is 0700;

5 -l <addr> IP address of the server to listen to. The default is all network adapters.

6 -d starts the daemon for the Memcached server;

7 -r maximum core file size;

8 -u <username> The user running Memcached, if currently root, needs to use this parameter to specify the user;

9 -m <num> The amount of memory allocated to Memcached, in MB;

10 -M Instructs Memcached to return errors when memory is used instead of using the LRU algorithm to remove data records;

11 -c <num> maximum number of concurrent connections, the default configuration is 1024;

12 -v -vv -vvv Sets the level of detail of messages printed on the server, where -v only prints error and warning messages, -vv prints client commands and corresponding -vvv on -vv On the basis of it will also print memory state transition information;

13 -f <factor> is used to set the increment factor of the chunk size;

14 -n <bytes> The minimum chunk size. The default configuration is 48 bytes.

15 -t <num> The number of threads used by the Memcached server. The default configuration is 4;

16 -L tries to use large pages of memory;

17 -R The maximum number of requests for each event. The default configuration is 20;

18 -C disables CAS, CAS mode will bring 8 bytes of redundancy;

# Introduction to Redis

Redis is an open source key-value storage system. Similar to Memcached, Redis stores most of its data in memory. The supported data types include strings, hash tables, linked lists, collections, ordered collections, and related operations based on these data types. Redis is developed in C language and can be used without any external dependencies on most POSIX systems like Linux, BSD and Solaris. The client language supported by Redis is also very rich. Commonly used computer languages ??such as C, C#, C++, Object-C, PHP, Python, Java, Perl, Lua, Erlang, etc. all have available clients to access the Redis server. The current Redis application has been very extensive, domestic like Sina, Taobao, foreign like Flickr, Github, etc. are using Redis cache services.
Redis installation is very convenient, just get the source code from http://redis.io/download and make && make install. By default, Redis server startup programs and client programs are installed in the /usr/local/bin directory. When starting the Redis server, we need to specify a configuration file for it. By default, the configuration file is in Redis' source directory and the file name is redis.conf.

## Redis Configuration File

In order to have a direct understanding of Redis's system implementation, we first look at what the main parameters defined in the Redis configuration file and the role of these parameters.
 
1 daemonize no By default, redis does not run in the background. If you need to run in the background, change the value of the item to yes;

2 pidfile /var/run/redis.pid When Redis is running in the background, Redis will put the pid file in /var/run/redis.pid by default. You can configure it to another address. When running multiple redis services, you need to specify different pid files and ports;

3 port 6379 specifies the port on which redis runs. The default is 6379.

4 bind 127.0.0.1 Specifies that redis only receive requests from this IP address. If not set, all requests will be processed. It is best to set this item in the production environment;

5 loglevel debug Specifies the logging level, where Redis supports a total of four levels: debug, verbose, notice, warning, and defaults to verbose. Debug means recording a lot of information for development and testing. Verbose indicates that useful information is logged, but not as much as debug logs. Notice indicates ordinary verbose, which is often used in production environments. Warning indicates that only very important or serious information will be logged;

6 logfile /var/log/redis/redis.log Configures the log file address. The default value is stdout. If background mode is output to /dev/null;

7 databases 16 The number of available databases, the default value is 16, the default database is 0, the database range is 0-(database-1;

8 save 900 1 Save the data to disk in the form of save <seconds> <changes>, indicating how often the update operation will synchronize the data to the data file rdb. Equivalent to the conditions triggered capture snapshot, this can be more than one condition. Save 900 1 means that at least 1 key is changed within 900 seconds to save the data to the disk;

9 rdbcompression yes stored to the local database (persistent to the RDB file whether the data is compressed, the default is yes;

10 dbfilename dump.rdb local persistent database file name, the default value is dump.rdb;

11 dir ./ Working directory, the path where the database mirrored files are placed. The path and file name are configured separately because redis will write the current database state to a temporary file when the backup is performed. When the backup is completed, the temporary file is replaced with the file specified above. The temporary file and the backup file configured above will be placed in this specified path, and the AOF file will be stored under this directory. Note that a directory must be specified instead of a file.

12 slaveof <masterip> <masterport> Master-slave replication. Set this database as a secondary database for other databases. Set the IP address and port of the master service when this machine is a slave service. When Redis starts, it automatically synchronizes data from the master;

13 masterauth <master-password> When the master service is set up for password protection (a password set with requirepass), the slave service connects to the master's password;

14 slave-serve-stale-data yes When the slave loses connection to the host or the copying is in progress, there are two ways to run the slave library: if slave-serve-stale-data is set to yes (the default setting), the slave will continue The corresponding client's request. If slave-serve-stale-data is no, removing any request other than the INFO and SLAVOF commands will return an error "SYNC with master in progress";

15 repl-ping-slave-period 10 Send a PING from the library to the main library at an interval. Set this interval by repl-ping-slave-period. The default is 10 seconds.

16 repl-timeout 60 Set the main library bulk data transmission time or ping reply interval, the default value is 60 seconds, we must ensure that the repl-timeout is greater than repl-ping-slave-period;

17 requirepass foobared Sets the password to use before making any other assignments after the client connects. Because redis is so fast, an external user can perform 150K password attempts in one second on a good server, which means that you need to specify a very strong password to prevent brute-force hacking;

18 rename-command The CONFIG "" command renames a relatively dangerous command in a shared environment. For example, rename CONFIG to a character that is not easy to guess: # rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52. If you want to delete a command, simply rename it to a null character "": rename-command CONFIG "";

19 maxclients 128 Sets the maximum number of client connections at the same time. The default is unlimited. The number of client connections Redis can open at the same time is the maximum number of file descriptors that the Redis process can open. If you set maxclients 0, it means no restrictions. When the number of client connections reaches the limit, Redis will close the new connection and return the max number of clients reached error message to the client.

20 maxmemory <bytes> Specifies the maximum memory limit for Redis. Redis will load data into memory when it starts up. After reaching the maximum memory, Redis will try to clear expired or expired keys first, and Redis will also remove empty list objects. After this method is processed, the maximum memory setting is still reached and the write operation will no longer be possible, but the read operation can still be performed. Note: Redis' new vm mechanism stores the key in memory, and Value is stored in the swap area.

21 maxmemory-policy volatile-lru What will Redis choose to delete when memory reaches its maximum value? There are five ways to choose from: volatile-lru represents the use of the LRU algorithm to remove the set expiration key (LRU: recently used Leeast Recently Used), allkeys-lru represents the use of LRU algorithm to remove any key, volatile-random represents the shift In addition to setting a random key with an expired time, allkeys_random represents the removal of a random key, volatile-ttl represents the removal of a minor TTL, and noeviction represents the absence of any key. It simply returns a write error.
Note: For the above strategy, if there is no suitable key to remove, Redis will return an error when writing.

22 appendonly no By default, redis will asynchronously back up the database image to disk in the background, but this backup is very time consuming and the backup cannot be frequent. If conditions such as power cuts and plugs are occurring, then a relatively large range of data loss will result, so redis provides another more efficient database backup and disaster recovery method. After append only mode is enabled, redis will append every write request it receives to the appendonly.aof file. When redis restarts, it will restore the previous state from the file, but this will cause the appendonly.aof file to be too large, so redis also supports the BGREWRITEAOF directive to reorganize appendonly.aof. You can also enable asynchronous dumps and AOF at the same time. ;

23 appendfilename appendonly.aof AOF file name, default is "appendonly.aof";

24 appendfsync everysec Redis supports three strategies for synchronizing AOF files: no for no synchronization, system operations, always for every write operation, everysec for write operations, once per second, default is Everysec", which is the best compromise between speed and security.

25 slowlog-log-slower-than 10000 Records commands that exceed a specific execution time. Execution time does not include I/O calculations, such as connecting clients, returning results, etc., just the command execution time. You can set slow log with two parameters: one is the parameter slowlog-log-slower-than (recognition) that tells Redis how long it took to record, and the other is the length of the slow log. When a new command is recorded, the oldest command will be removed from the queue. The following time is in subtle micro units, so 1000000 represents one minute. Note that developing a negative number will turn off slow logging, and setting it to 0 will force every command to be logged;

26 hash-max-zipmap-entries 512 && hash-max-zipmap-value 64 When the hash contains more than the specified number of elements and the largest element does not exceed the criticality, the hash is encoded in a special way (significantly reduce memory usage To store, here you can set these two critical values.Redis Hash corresponds to the value inside is actually a HashMap, there are actually two different implementations in the actual.When the Hash member is less, Redis will use a similar one-dimensional array in order to save memory Compact storage, instead of using a true HashMap structure, the corresponding value redisObject encoding is zipmap. When the number of members increases, it will automatically be converted into a real HashMap, encoding at this time is ht;

27 list-max-ziplist-entries 512 list data type how many nodes will use the compact storage format of the following pointer;

28 list-max-ziplist-value 64 data type node value size less than the number of bytes will use compact storage format;

29 set-max-intset-entries 512 set data type internal data if all numeric, and contains the number of nodes below will be stored in a compact format;

30 zset-max-ziplist-entries 128 zsort Data Type How many nodes will use the compact memory format for pointers below;

31 zset-max-ziplist-value 64 zsort data type The node size value is less than the number of bytes in compact storage format.

32 activerehashing yes Redis will use 1 millisecond CPU time every 100 milliseconds to re-hash redis hash tables to reduce memory usage. When you use the scene, there is a very strict real-time needs, can not accept Redis from time to time to the request has a delay of 2 milliseconds, this configuration is no. If there is no such strict real-time requirement, it can be set to yes so that the memory can be released as soon as possible;

## Redis Common Data Types

Unlike data records where Memcached only supports simple key-value structures, Redis supports much richer data types. The most common data types are mainly five types: String, Hash, List, Set, and Sorted Set. Before describing these data types in detail, we first use a graph to understand how these different data types are described in Redis internal memory management.

### Redis Objects

Redis internally uses a redisObject object to represent all keys and values. The most important information of redisObject is shown in Figure 1. Type represents a data type of a value object, and encoding is a storage method of different data types in redis. For example, type=string represents a common string of values. Then the corresponding encoding can be raw or int. If it is int, it means that the actual redis internally store and represent the string according to the numeric class, of course, provided that the string itself can be expressed numerically, for example: "123" " 456" This string. Here need special instructions about the vm field, only to open the Redis virtual memory function, this field will really allocate memory, this feature is off by default. Through Figure1, we can see that Redis uses redisObject to represent all the key/value data is a waste of memory, of course, these memory management costs are mainly to provide a unified management interface for Redis different data types, the actual author also provides more This method helps us to save memory usage as much as possible. Let us first analyze the usage and internal implementation of these five data types one by one.

![Redis object](http://ot1cc1u9t.bkt.clouddn.com/17-8-1/96927364.jpg)

- String
 
Commonly used commands: set/get/decr/incr/mget, etc.;
Application Scenario: String is the most commonly used data type, and ordinary key/value storage can be classified as such;
Implementation: String storage within Redis is a string by default, which is referenced by redisObject. When it encounters incr, decr, etc. operations, it will be converted to a numeric value for calculation. The encoding field of redisObject is int.

- Hash

Commonly used commands: hget/hset/hgetall, etc.
Application Scenario: We want to store a user information object data, including user ID, user name, age, and birthday. We want to obtain the user's name or age or birthday through the user ID.
Implementation: Redis's Hash is actually an internally stored Value that is a HashMap and provides an interface to directly access the Map member. As shown in Figure 2, Key is the user ID and value is a Map. The key of this map is the attribute name of the member, and value is the attribute value. In this way, modification and access to data can be directly through the key of its internal Map (the key of the internal Map in Redis is field), that is, the corresponding attribute data can be manipulated through the key (user ID) + field (attribute tag). There are two ways to implement the current HashMap: When the number of HashMap members is small, Redis uses a one-dimensional array to compactly store memory instead of using a real HashMap structure. In this case, the redisObject of the corresponding value is encoded as Zipmap, will automatically turn into a real HashMap when the number of members increases, this time the encoding is ht.

### Redis's Hash data type

- List

Commonly used commands: lpush/rpush/lpop/rpop/lrange, etc.;
Application Scenario: Redis list has many application scenarios and is one of the most important data structures of Redis. For example, twitter's watchlist, fan list, etc. can all be implemented using Redis's list structure.
Implementation: The implementation of Redis list is a doubly linked list, which can support reverse lookup and traversal. It is more convenient to operate, but it brings some extra memory overhead. Many internal Redis implementations, including sending buffer queues, are also used. This data structure.

- Set
- 
Commonly used commands: sadd/spop/smembers/sunion;
Application Scenario: The function provided by Redis set is similar to that of list, which is a list function. The special point is that set can be automatically re-ranked. When you need to store a list of data, and you don't want duplicate data, set is a very A good choice, and set provides an important interface to determine if a member is within a set. This is also what list cannot provide;
Implementation: The internal implementation of set is a HashMap whose value is always null. The actual way is to calculate the hash quickly. This is why set can provide a reason to determine whether a member is in a collection.

- Sorted Set

Commonly used commands: zadd/zrange/zrem/zcard, etc.
Application Scenario: Redis sorted set uses a scenario similar to set. The difference is that set is not automatically ordered, and sorted set can be used to sort members by providing additional parameters of a score, and it is insert-ordered. That is, automatic sorting. When you need an ordered and non-repeating collection list, you can select the sorted set data structure. For example, twitter's public timeline can be stored as the score with published time. This way, the time is automatically sorted by time.
Implementation: Redis sorted set internal HashMap and SkipList to ensure the storage and ordering of data, HashMap is a member-to-score mapping, and the jump table is stored in all the members, the sorting basis is The score stored in the HashMap, using the structure of the jump table can obtain a relatively high search efficiency, and is relatively simple to implement.

### Redis's persistence

Although Redis is a memory-based storage system, it supports persistence of in-memory data, and it provides two major persistence strategies: RDB snapshots and AOF logs. We will introduce these two different persistence strategies separately below.

### Redis RDB Snapshot
 
Redis supports the persistence mechanism that saves a snapshot of the current data into a data file, which is an RDB snapshot. This method is very easy to understand, but how can a continuous write database generate snapshots? Redis draws on the copy on write mechanism of the fork command. When the snapshot is generated, the current process fork a child process, and then cycle all the data in the child process, the data is written as RDB file.

We can use Rdis's save command to configure the timing of RDB snapshot generation. For example, you can configure to generate a snapshot when there are 100 writes within 10 minutes, or you can configure a snapshot to be generated when there are 1000 writes within 1 hour. Multiple rules are implemented together. The definition of these rules is in the Redis configuration file. You can also use Redis' CONFIG SET command to set the rules during Redis runtime without restarting Redis.
The Redis RDB file will not be broken because its write operation is performed in a new process. When a new RDB file is generated, the child process generated by Redis will first write the data to a temporary file and then pass the atom. The rename system call renames the temporary file as an RDB file so that any time a failure occurs, the Redis RDB file is always available. At the same time, Redis's RDB file is also a part of Redis master-slave synchronization internal implementation.
However, we can clearly see that RDB has its drawbacks, that is, once the database has a problem, the data stored in our RDB file is not new, and the data from the last RDB file generation to the time when Redis is downtime is not available. All lost. Under certain services, this can be tolerated. We also recommend that these services be persisted using RDBs because the cost of starting RDBs is not high. But for other applications that require high data security and cannot tolerate data loss applications, RDBs are powerless, so Redis introduces another important persistence mechanism: AOF logging.

### Redis AOF Log

The full name of the AOF log is append only file, we can see from the name, it is an additional write log file. Unlike the general database binlog, the AOF file is plain text that is identifiable, and its contents are one by one Redis standard commands. Of course, not all commands to send Redis will be recorded in the AOF log, only those commands that will cause the data to be modified will be appended to the AOF file. Then each command to modify data generates a log, then AOF file is not very large? The answer is yes, AOF files will get bigger and bigger, so Redis provides a function called AOF rewrite. Its function is to regenerate an AOF file. The operation of a record in a new AOF file will only have one operation, and unlike an old file, it may record multiple operations on the same value. Its generation process is similar to that of RDB. It is also a fork process that directly traverses data and writes new AOF temporary files. In the process of writing a new file, all the write operation logs will still be written to the original old AOF file and will also be recorded in the memory buffer. When the operation is complete, the logs in all buffers are written to the temporary file at one time. Then call the atomic rename command to replace the old AOF file with the new AOF file.
AOF is a write file operation whose purpose is to write the operation log to disk, so it will also encounter the five processes of the write operation we mentioned above. Then how high is the security of writing AOF? In fact, this can be set. After writing write(2) to AOF in Redis, it will call fsync to write it to the disk, and control it through appendfsync option. The following three items of appendfsync, security strength Become stronger gradually.

- appendfsync no

When you set appendfsync to no, Redis does not actively invoke fsync to synchronize the contents of the AOF log to disk, so it all depends on the debugging of the operating system. For most Linux operating systems, fsync is performed every 30 seconds and the data in the buffer is written to disk.

- appendfsync everysec

When setting appendfsync to everysec, Redis will fsync every second to write the data in the buffer to disk. But when this fsync call is longer than 1 second. Redis will adopt a delayed fsync strategy and wait another second. That is, fsync is performed after two seconds. This time fsync will be performed no matter how long it will take. At this time, since the file descriptor is blocked during fsync, the current write operation will block. So the conclusion is that in the vast majority of cases, Redis will perform an fsync every second. In the worst case, an fsync operation takes place in two seconds. This operation is called a group commit in most database systems. It is a combination of multiple write operations that write logs to disk at once.

- appednfsync always

When appendfsync is set to always, fsync is called once for each write operation. At this time, the data is the safest. Of course, since fsync is executed every time, its performance will be affected.

#How to choose

I used Redis this time, it feels very convenient, but more doubt when choosing the memory database in the end when to choose redis, when to choose memcache, and then find the corresponding data below, is from the redis author's statement (stackoverflow Above.
>   You should not care too much about performances. Redis is faster per core with small values, but memcached is able to use multiple cores with a single executable and TCP port without help from the client. Also memcached is faster with big values in the order of 100k. Redis recently improved a lot about big values (unstable branch) but still memcached is faster in this use case. The point here is: nor one or the other will likely going to be your bottleneck for the query-per-second they can deliver.
You should care about memory usage. For simple key-value pairs memcached is more memory efficient. If you use Redis hashes, Redis is more memory efficient. Depends on the use case.
You should care about persistence and replication, two features only available in Redis. Even if your goal is to build a cache it helps that after an upgrade or a reboot your data are still there.
You should care about the kind of operations you need. In Redis there are a lot of complex operations, even just considering the caching use case, you often can do a lot more in a single operation, without requiring data to be processed client side (a lot of I/O is sometimes needed). This operations are often as fast as plain GET and SET. So if you don’t need just GEt/SET but more complex things Redis can help a lot (think at timeline caching).

## Some netizens translate as follows：

    There is no need to pay too much attention to performance. Since Redis only uses a single core and Memcached can use multiple cores, in comparison, on average each Redis has higher performance than Memcached when storing small data. In the data above 100k, Memcached performance is higher than Redis, although Redis recently also optimized the performance of large data storage, but it is slightly inferior to Memcached. Having said so much, the conclusion is that no matter which one you use, the number of requests processed per second will not become a bottleneck.

    You need to pay attention to memory usage. For simple data storage such as key-value, memory usage of memcache is higher. If you use a hash structure, redis memory usage will be higher. Of course, these are all dependent on the specific application scenario.

    When you need to focus on data persistence and master-slave replication, only redis has these two features. If your goal is to build a cache that does not lose data until after an upgrade or reboot, you can only choose redis.

    You should care about what you need to do. Redis supports a lot of complex operations, and even consider only the use of memory, you can often do a lot in a single operation, without having to read the data into the client (this will require a lot of IO operations. These complex operations Basically as fast as pure GET and POST operations, so when you don't just need GET/SET but more operations, redis will play a big role.

    The choice of both depends on the specific application scenario. If the data that needs to be cached is a simple structure such as key-value, I still use memcache in the project. It is also stable and reliable enough. When it comes to a series of complicated operations such as storage, sorting, etc., there is no doubt that redis is chosen.

# About the difference between redis and memcache：

## The difference between redis and memecache is that[2]：

## Storage method：

    Memecache stores all data in memory and hangs after power off. Data cannot exceed memory size
    Redis is partially on the hard disk, so that the durability of the data can be guaranteed and the data persistence. (Note: There are two kinds of persistence methods: Snapshot and AOF log. In practice, pay special attention to the configuration file snapshot parameters. Or it is very likely that the server will be fully loaded and dumped.

## Data support type：

    Redis is much more data-friendly than memecache.

## Use different underlying models：

    The new version of redis directly builds the VM mechanism itself, because the general system calls a system function, it will waste a certain amount of time to move and request.

## Different operating environments：

    Redis currently only supports Linux on the official line, thereby eliminating the need for support for other systems, so that it can better focus on the optimization of the system environment, although later Microsoft has a team to write a patch for it. But not on the trunk

## In conclusion，

For applications that have persistence requirements or require advanced data structures and processing, choose redis, other simple key/value storage, and choose memcache.