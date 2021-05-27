
# Kafka 설치
# 파일다운 및 압축해제
 tar xvzf kafka_2.13-2.7.0.tgz

export KAFKA_HEAP_OPTS="-Xmx400m -Xms400m"
vi config/server.properties
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://{aws ec2 public ip}:9092

# Zookeeper 및 Kafka 서버 구동
bin/zookeeper-server-start.sh config/zookeeper.properties &

bin/kafka-server-start.sh config/server.properties &

ubuntu@kafka-test:~/kafka_2.13-2.7.0$ bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
ubuntu@kafka-test:~/kafka_2.13-2.7.0$ bin/kafka-server-start.sh -daemon config/server.properties


jps

# 토픽 생성(quickstart-events) 및 확인
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic quickstart-events --partitions 3

bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic quickstart-events --partitions 3 --replication-factor 3  (복제)

bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

bin/kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic contents

bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic quickstart-events



Topic: quickstart-events        PartitionCount: 1       ReplicationFactor: 1    Configs: segment.bytes=1073741824
        Topic: quickstart-events        Partition: 0    Leader: 0       Replicas: 0     Isr: 0

# Producer 실행
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic quickstart-events

# Consumer 실행
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic quickstart-events --from-beginning



# Kafka Connect 설치
curl -O http://packages.confluent.io/archive/6.1/confluent-community-6.1.0.tar.gz
tar xvf confluent-community-6.1.0.tar.gz

토픽 목록확인
/confluent-6.1.0/bin$ ./kafka-topics --bootstrap-server localhost:9092 --list

# JDBC Connector 설치
https://docs.confluent.io/5.5.1/connect/kafka-connect-jdbc/index.html 페이지 Install the connector manually 에서 Download and extract the ZIP file
https://www.confluent.io/hub/confluentinc/kafka-connect-jdbc?_ga=2.10066865.547915084.1620221158-1930855515.1620221158
sudo apt install unzip
unzip confluentinc-kafka-connect-jdbc-10.1.1.zip

# Kafka connect 설정에 JDBC Connector lib 등록
vi /home/ubuntu/confluent-6.1.0/etc/kafka/connect-distributed.properties

최하단에 아래 내용 추가
plugin.path=/home/ubuntu/confluentinc-kafka-connect-jdbc-10.1.1/lib

# JDBC 파일 업로드
/home/ubuntu/confluent-6.1.0/share/java/kafka 경로에 Mariadb jdbc파일 업로드
mariadb-java-client-2.7.2.jar


# Kafka connector 실행
/confluent-6.1.0$ exit


# users 테이블 변경사항 topic 생성
http://52.79.165.211:8083/connectors 으로 POST메소드 호출

{
    "name":"my-source-connect",
    "config": {
        "connector.class" : "io.confluent.connect.jdbc.JdbcSourceConnector",
        "connection.url" : "jdbc:mysql://localhost:3306/mydb",
        "connection.user": "taekyung",
        "connection.password": "rlaxorud",
        "mode": "incrementing",
        "incrementing.column.name" : "id",
        "table.whitelist": "users",
        "topic.prefix": "my_topic_",
        "tasks.max":"1"
    }
}


# 등록확인 GET 호출
http://52.79.165.211:8083/connectors/my-source-connect/status

# 토픽 확인
/kafka_2.13-2.7.0$ bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

# consumer 확인
/kafka_2.13-2.7.0$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my_topic_users --from-beginning



# 토픽 변경사항 my_topic_users 테이블에 반영
http://52.79.165.211:8083/connectors 으로 POST메소드 호출

{
    "name":"my-sink-connect",
    "config": {
        "connector.class" : "io.confluent.connect.jdbc.JdbcSinkConnector",
        "connection.url" : "jdbc:mysql://localhost:3306/mydb",
        "connection.user": "taekyung",
        "connection.password": "rlaxorud",
        "auto.create":"true",
        "auto.evolve": "true",
        "delete.enabled" : "false",
        "tasks.max": "1",
        "topics": "my_topic_users"
    }
}

# DB(users)에 insert 수행시 my_topic_users 테이블에 동기화 확인가능


# Producer 실행하여 직접 입력
/kafka_2.13-2.7.0$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my_topic_users

{"schema":{"type":"struct","fields":[{"type":"int32","optional":false,"field":"id"},{"type":"string","optional":false,"field":"user_id"},{"type":"string","optional":false,"field":"pwd"},{"type":"string","optional":false,"field":"name"},{"type":"int64","optional":true,"name":"org.apache.kafka.connect.data.Timestamp","version":1,"field":"created_at"}],"optional":false,"name":"users"},"payload":{"id":5,"user_id":"admin5","pwd":"admin551","name":"Admin name5","created_at":1620321912000}}

-- my_topic_users테이블에만 데이터 입력확인



# Ec2 테스트
kafka 서버 /etc/hosts 설정 

컨슈머서버 /etc/hosts 설정



http://13.209.136.250:8083/connectors/ 으로 POST메소드 호출
{
    "name":"my-content-sink-connect",
    "config": {
        "connector.class" : "io.confluent.connect.jdbc.JdbcSinkConnector",
        "connection.url" : "jdbc:mysql://13.209.136.250:3306/execute",
        "connection.user": "taekyung",
        "connection.password": "rlaxorud",
        "auto.create":"true",
        "auto.evolve": "true",
        "delete.enabled" : "false",
        "tasks.max": "1",
        "topics": "contents"
    }
}