

# Ubuntu 자바 설치
sudo apt install openjdk-11-jdk

# 한국시간으로 변경
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime


sudo hostnamectl set-hostname sc-config-service
sudo hostnamectl set-hostname content2

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
sudo apt-get upgrade
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable edge test"
sudo apt-get install docker.io

-- /usr/bin/docker.io 실행 파일을 /usr/local/bin/docker로 링크하여 사용
sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker


# Docker 권한부여
sudo usermod -a -G docker $USER
sudo service docker restart


# Zipkin 설치
~/zipkin$ curl -sSSL https://zipkin.io/quickstart.sh | bash -s



# Grapana 설치
Selecting previously unselected package grafana.
(Reading database ... 146758 files and directories currently installed.)
Preparing to unpack grafana_7.5.7_amd64.deb ...
Unpacking grafana (7.5.7) ...
Setting up grafana (7.5.7) ...
Adding system user `grafana' (UID 115) ...
Adding new user `grafana' (UID 115) with group `grafana' ...
Not creating home directory `/usr/share/grafana'.
### NOT starting on installation, please execute the following statements to configure grafana to start automatically using systemd
 sudo /bin/systemctl daemon-reload
 sudo /bin/systemctl enable grafana-server
### You can start grafana-server by executing
 sudo /bin/systemctl start grafana-server
Processing triggers for systemd (245.4-4ubuntu3.6) ...



# 실행쉘 참고
#!/bin/bash

PROCESS=sd-content
CLASSPATH=/home/ubuntu/${PROCESS}/${PROCESS}-1.0.jar;
export CLASSPATH

count=$(ps -ef | grep "app.name=$PROCESS" | grep -v grep | grep -v tail | wc -l)
if [ $count -eq 1 ]
then
        p_string=$(ps -ef | grep "app.name=$PROCESS" | grep -v grep | grep -v tail)
        pid=$(echo $p_string | awk '{print $2}')
        kill -9 $pid
        echo "$PROCESS is killed"
        sleep 3
fi

java -cp $CLASSPATH -Dprocess_id=$PROCESS -Dapp.name=$PROCESS -jar ${PROCESS}-1.0.jar >> log/${PROCESS}.log &
echo "$PROCESS is started."