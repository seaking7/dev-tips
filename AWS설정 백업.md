

## config
docker run -d --net host \
        --name config \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=configLog \
        seaking7/config-service:1.0

## config sh
PROCESS=sc-config-service
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


## discovery
docker run -d -p 8761:8761 \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=peer1" \
        --name discovery \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=discovery1 \
        seaking7/discovery-service:1.0

## gateway
.bashrc
export HOSTIP=`curl -w '\n' http://169.254.169.254/latest/meta-data/public-ipv4`


docker run -d -p 8000:8000 \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name gateway \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=gateway1 \
        seaking7/gateway-service:1.0


## content

docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name content \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=content1_1 \
        seaking7/content-service:1.0

## content chaos
docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=chaos-monkey" \
        -e "management.endpoint.chaosmonkey.enabled=true" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name content \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=content1_1 \
        seaking7/content-service:1.0


# report
docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod1" \
        -e "management.health.circuitbreakers.enabled=true" \
        -e "management.endpoint.health.show-details=ALWAYS" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name report \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=dockerLog \
        --log-opt awslogs-stream=report1_1 \
        seaking7/report-service:1.0




