Connecting Redis with C Language Client (hiredis) -- Centos/Debian for Redis Experience Series
Keywords: Redis CentOS yum C

Key word: hiredis, cRedis, redis clients, redis client, C client, C Language, Centos, Debian

Hiredis is a very comprehensive C language version of redis interface library, supporting all commands, pipes and scripts. Huawei cloud distributed caching service Redis version supports hiredis client connection.

To connect Redis with C language client (hiredis), it is necessary to install compiler environment and hiredis first. Taking CentOS as an example, this paper introduces the construction of C client environment.

Step 0: Preparations
Huawei Cloud Buy an Elastic Cloud Server ECS (I chose CentOS 6.3), one Distributed cache instance (DCS for Redis), I chose a stand-alone example.

Note that ECS and cache instances are configured with the same VPC and security groups to ensure network interoperability.

Step 1: Install gcc, make, and hiredis
If the system does not have a built-in compilation environment, it can be installed in yum mode.

yum install gcc make

Download and unzip hiredis

wget https://github.com/redis/hiredis/archive/master.zip;

Compile and install after entering decompressed directory

make 

make install

After installation, you can try to connect.

Step 2: Connect Redis

About the use of hiredis, the official website of redis gives a detailed introduction to its use. Here is a simple example of connection, password authentication, set and get methods.

Edit connection demo instances, such as:

vim connRedis.c
````

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <hiredis.h>
int main(int argc, char **argv) {
     unsigned int j;
     redisContext *conn;
     redisReply *reply;
     if (argc < 3) {
             printf("Usage: example {instance_ip_address} 6379 {password}\n");
             exit(0);
     }
     const char *hostname = argv[1];
     const int port = atoi(argv[2]);
     const char *password = argv[3];
     struct timeval timeout = { 1, 500000 }; // 1.5 seconds
     conn = redisConnectWithTimeout(hostname, port, timeout);
     if (conn == NULL || conn->err) {
		if (conn) {
             printf("Connection error: %s\n", conn->errstr);
             redisFree(conn);
		} else {
             printf("Connection error: can't allocate redis context\n");
		}
     exit(1);
     }
     /* AUTH */
     reply = redisCommand(conn, "AUTH %s", password);
     printf("AUTH: %s\n", reply->str);
     freeReplyObject(reply);

     /* Set */
     reply = redisCommand(conn,"SET %s %s", "welcome", "Hello, Redis!");
     printf("SET: %s\n", reply->str);
     freeReplyObject(reply);
    
     /* Get */
     reply = redisCommand(conn,"GET welcome");
     printf("GET welcome: %s\n", reply->str);
     freeReplyObject(reply);
     
     /* Disconnects and frees the context */
     redisFree(conn);
     return 0;
}

````
Exit after saving and compile by executing the following commands:
````

gcc connRedis.c -o connRedis  -I /usr/local/include/hiredis -lhiredis

````
If there is an error, you can find the hiredis.h file path and modify the compilation command.

After compiling, an executable connRedis is obtained.

Test the following commands to test the connection:
````

 ./connRedis {redis_ip_address} 6379 {password}

````
When the following echoes are obtained, demo runs normally:
````

[root@amolserv]#  ./connRedis 192.168.0.171 6379 Heru+123
AUTH: OK
SET: OK
GET welcome: Hello,  Redis!
[root@amolserv]# 

````
Note that if the hiredis library file can not be found by running errors, you can refer to the following, copy the relevant files to the system directory, and add dynamic links.

````

mkdir /usr/lib/hiredis
cp /usr/local/lib/libhiredis.so.0.13 /usr/lib/hiredis/
mkdir /usr/include/hiredis
cp /usr/local/include/hiredis/hiredis.h /usr/include/hiredis/
echo '/usr/local/lib' >>/etc/ld.so.conf
ldconfig

````
The location of the so file and. h file above needs to be replaced by the actual file location.
