1. docker-compose up -d
2. docker network ls
>78f85795a716   airflow_default             bridge    local
 
> 855524aa290c   bridge                      bridge    local

> a76fb54d4ae7   db_default                  bridge    local

> 6e91d74e0d4c   db_master_slave_net-mysql   bridge    local

3. master, slave db의 통신을 위해서 내부 IP 주소를 알아낸다.
4. docker inspect 6e91d74e0d4c
ex)
```python
[
    {
        "Name": "db_master_slave_net-mysql",
        "Id": "6e91d74e0d4c963727f3a90dfe357ef16a82eff2dad305740d541265820cf07d",
        "Created": "2023-05-02T03:08:48.776368882Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.26.0.0/16",
                    "Gateway": "172.26.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "346bd6208d12b8862ab0fb9a667b8a3fcc678e801e4bf42859a45fe319f9b329": {
                "Name": "db_master_slave-db-master-1",
                "EndpointID": "c8a213583940d8a7baf745bc440117ebca61a3139f85dfe2f8ad30401120615b",
                "MacAddress": "02:42:ac:1a:00:03",
                "IPv4Address": "172.26.0.3/16", ###### 이 정보 기억하기!!!!
                "IPv6Address": ""
            },
            "44677364cd8f38d6f53e1c3ae444e9913707f688713c4a4fdc20701c27534f0f": {
                "Name": "db_master_slave-db-slave-1",
                "EndpointID": "ba81392c2608d0f24075f16d133c79c9084184ff340331c919e452f9c1e57430",
                "MacAddress": "02:42:ac:1a:00:02",
                "IPv4Address": "172.26.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "net-mysql",
            "com.docker.compose.project": "db_master_slave",
            "com.docker.compose.version": "2.17.2"
        }
    }
]

```
5.  docker exec -it db_master_slave-db-master-1 mysql -uroot -ppassword
```python
mysql> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000003 |      157 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
```

6. docker exec -it db_master_slave-db-slave-1 mysql -uroot -ppassword
```python
                            # 위에서 가져온 IP                                            # SHOW MASTER STATUS에서 나온 File      # SHOW MASTER STATUS에서 나온 Position
CHANGE MASTER TO MASTER_HOST='172.24.0.3', MASTER_USER='root', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000003', MASTER_LOG_POS=157, GET_MASTER_PUBLIC_KEY=1;

start slave;

show slave status\G;

#### show slave status\G 명령어를 통해 나온 값중 Slave_IO_Running, Slave_SQL_Running가 yes여야 성공
Master_Log_File: mysql-bin.000003
Read_Master_Log_Pos: 1554
Relay_Log_File: mysql-relay-bin.000002
Relay_Log_Pos: 1723
Relay_Master_Log_File: mysql-bin.000003
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
####
```

테스트 코드
1. docker exec -it db_master_slave-db-master-1 mysql -uroot -ppassword
2. create database test_db;
3. use test_db;
4. CREATE TABLE test_data (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  age INT NOT NULL
);
5. INSERT INTO test_data (name, age) VALUES ('Alice', 30);
INSERT INTO test_data (name, age) VALUES ('Bob', 28);
INSERT INTO test_data (name, age) VALUES ('Charlie', 22);
6. 마스터에 테이블 및 데이터 생성 완료 

1. docker exec -it db_master_slave-db-slave-1 mysql -uroot -ppassword
2. show databases; --> master에서 생성한 데이터베이스가 나와야함(test_db)
3. use test_db;
4. show tables;
 ```python
+-------------------+
| Tables_in_test_db |
+-------------------+
| test_data         |
+-------------------+
```
6. select * from test_data; --> 마스터에서 INSERT한 값이 slave에서 확인 가능
```python
mysql> select * from test_data;
+----+---------+-----+
| id | name    | age |
+----+---------+-----+
|  1 | Alice   |  30 |
|  2 | Bob     |  28 |
|  3 | Charlie |  22 |
+----+---------+-----+
```
