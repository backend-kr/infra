1. git clone https://github.com/confluentinc/ksql
2. docker-compose exec ksqldb-cli  ksql http://primary-ksqldb-server:8088

3.ksql-kafka container에서 하위 명령 입력으로 topic 생성
kafka-topics --create --topic [토픽이름] --bootstrap-server 127.0.0.1:29092 --partitions 3 --replication-factor 1

생성된 토픽 확인
kafka-topics --list --bootstrap-server 127.0.0.1:9092

