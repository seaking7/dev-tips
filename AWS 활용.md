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


sudo hostnamectl set-hostname sc-config-service
sudo hostnamectl set-hostname content-service



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



# java 실행시 application.yml 파일 내용 편집
  -Dserver.port=9999

 # docker 생성시 편집
 -e "spring.rabbitmq.host=rabbitmq" 
