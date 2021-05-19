

# Ubuntu 자바 설치
sudo apt install openjdk-11-jdk

# 한국시간으로 변경
$ sudo rm /etc/localtime
$ sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime


sudo hostnamectl set-hostname sc-config-service
sudo hostnamectl set-hostname content-service

저장후 sudo reboot


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



# Docker 설치
ubuntu@ip-100-51-0-140:~$ sudo apt-get install docker.io

-- /usr/bin/docker.io 실행 파일을 /usr/local/bin/docker로 링크하여 사용
sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker

 sudo docker pull seaking7/sc-discovery:1.0


# Docker 권한부여
$ sudo usermod -a -G docker $USER
$ sudo service docker restart