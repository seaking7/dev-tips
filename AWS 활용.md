# AWS EC2 서버(Amazon Linux2) 생성후 수행할 내용
java 8 설치 : sudo yum install -y java-1.8.0-openjdk-devel.x86_64
java 11 설치 : sudo amazon-linux-extras install java-openjdk11

sudo /usr/sbin/alternatives --config java

# Ubuntu 경우
sudo apt install openjdk-11-jdk

# 한국시간으로 변경
$ sudo rm /etc/localtime
$ sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime

# 서버 Hostname 변경
$ sudo vim /etc/sysconfig/network

아래 항목으로 이름 추가

HOSTNAME=webserver

저장후 sudo reboot


# 로컬 파일 아마존 S3에 백업
 1. AWS CLI 설치
   https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-windows.html#cliv2-windows-install
 2. aws configure : IAM에서 생성한 사용자 정보 입력
 3. 백업실행
$ aws s3 sync ./dev-tips/ s3://buckettest77/backup_test


# 로컬 파일 EC2에 업로드
scp -i  "../../aws_key/aws-key.pem" sc-discovery-0.0.1-SNAPSHOT.jar  ec2-user@3.34.141.48:/home/ec2-user/
scp -i  "aws-key" ../sc-api-gateway/target/sc-api-gateway-0.0.1-SNAPSHOT.jar  ec2-user@3.34.133.74:/home/ec2-user/
scp -i  "aws-key" ../sc-config-service/target/sc-config-service-0.0.1-SNAPSHOT.jar  ubuntu@3.36.120.174:/home/ubuntu

# rabbitMq 설치
https://openmind8735.com/aws/rabbitmq/2017/07/17/AWS-EC2%EC%97%90-RabbitMQ-%EC%84%9C%EB%B2%84%EC%98%AC%EB%A6%AC%EA%B8%B0/


ubuntu에 설치

sudo apt-get -qy update
sudo apt-get -qy install rabbitmq-server

sudo rabbitmqctl add_user taekyung taekyung
sudo rabbitmqctl set_user_tags taekyung administrator
sudo rabbitmqctl set_permissions -p / taekyung ".*" ".*" ".*"

sudo rabbitmqctl delete_user guest
sudo /etc/init.d/rabbitmq-server restart

sudo rabbitmq-plugins enable rabbitmq_management
sudo /etc/init.d/rabbitmq-server restart


# Kafka 설치
# 파일다운 및 압축해제
 tar xvzf kafka_2.13-2.7.0.tgz

# Zookeeper 및 Kafka 서버 구동
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties &

# 토픽 생성(quickstart-events) 및 확인
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic quickstart-events --partitions 1
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic quickstart-events
Topic: quickstart-events        PartitionCount: 1       ReplicationFactor: 1    Configs: segment.bytes=1073741824
        Topic: quickstart-events        Partition: 0    Leader: 0       Replicas: 0     Isr: 0

# Producer 실행
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic quickstart-events

# Consumer 실행
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic quickstart-events --from-beginning


# Ubuntu 에 Mariadb 설치
sudo apt-get update
sudo apt install mariadb-server
sudo apt-get install mariadb-client

-- root 패스워드 설정 등
sudo mysql_secure_installation

-- db 접속
sudo mysql -u root -p

create user 'taekyung'@'%' identified by '패스워드';

[mysql]> grant all privileges on mysql to taekyung@'%';
Query OK, 0 rows affected (0.000 sec)

[mysql]> flush privileges;
Query OK, 0 rows affected (0.000 sec)


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
