
# Docker 파일 다운
PS C:\Users\User> docker pull mysql:5.7

# Docker 실행(mysql)
PS C:\Users\User> docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.7
86ec4436387d5fd895c9ecc08caf34882f75891fc6006b8e89b01c5e2bfcf2ca

# Docker 로그 확인
PS C:\Users\User> docker logs 86ec4436387d
2021-05-13 12:39:36+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.34-1debian10 started.

# Docker 내부 접속( -it 는 interactive terminal 계속해서 터미널 사용)
PS C:\Users\User> docker exec -it infallible_tharp /bin/bash
root@86ec4436387d:/#
root@86ec4436387d:/# mysql -u root -p


# Docker 중단
PS C:\Users\User> docker stop 86ec4436387d
86ec4436387d


# Docker 삭제
PS C:\Users\User> docker container rm 86ec4436387d
86ec4436387d


# Dockerfile 작성
FROM openjdk:17-ea-11-jdk-slim
VOLUME /tmp
COPY target/sc-discovery-1.0.jar Discovery.jar
ENTRYPOINT ["java", "-jar", "Discovery.jar"]

# Dockerfile 작성문법
FROM baseImage      //베이스 이미지
ENV GOPATH $GOPATH:/go/src   //환경변수 선언
RUN command         //추가적으로 필요한 파일 다운로드
CMD ["echo", "hello"]  //컨테이너 시작시 실행 명령어
EXPOSE 9000   //Port를 외부로 mapping

# Docker build
C:\workspace\sc-discovery>docker build --tag seaking7/sc-discovery:1.0 .


# Docker build 확인
PS C:\Users\User> docker image ls        
REPOSITORY              TAG       IMAGE ID       CREATED         SIZE
seaking7/sc-discovery   1.0       88757d451779   4 minutes ago   442MB
mysql                   5.7       2c9028880e58   29 hours ago    447MB


# Docker hub에 push
PS C:\Users\User> docker push seaking7/sc-discovery:1.0


# Docker 삭제하고 다시 pull
PS C:\Users\User> docker rmi 88757d451779
Untagged: seaking7/sc-discovery:1.0
Untagged: seaking7/sc-discovery@sha256:58e24f6e7e01b3b98fdba8ee0274f98db5503f55b9e0f5fc6c625beebefec64d
Deleted: sha256:88757d451779ab4c837537981f86d425b24f1e4e0d41c2a87e8a2a144cd1506f
PS C:\Users\User> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
mysql        5.7       2c9028880e58   29 hours ago   447MB

PS C:\Users\User> docker pull seaking7/sc-discovery:1.0
1.0: Pulling from seaking7/sc-discovery
45b42c59be33: Already exists                                                                                            a91c0c19c848: Already exists                                                                                            0a37071eae8e: Already exists                                                                                            4aab1c15cfb1: Already exists                                                                                            Digest: sha256:58e24f6e7e01b3b98fdba8ee0274f98db5503f55b9e0f5fc6c625beebefec64d
Status: Downloaded newer image for seaking7/sc-discovery:1.0
docker.io/seaking7/sc-discovery:1.0

PS C:\Users\User> docker image ls
REPOSITORY              TAG       IMAGE ID       CREATED         SIZE
seaking7/sc-discovery   1.0       88757d451779   9 minutes ago   442MB
mysql                   5.7       2c9028880e58   29 hours ago    447MB


# Docker Bridge network 생성
PS C:\Users\User> docker network create --gateway 172.18.0.1 --subnet 172.18.0.0/16 ecommerce-network
0cac82d7dbf46d9a5267e875f38161da66bad40478b515ffb0dbbc872292c42f
PS C:\Users\User> docker network ls
NETWORK ID     NAME                DRIVER    SCOPE
29887f762dcc   bridge              bridge    local
0cac82d7dbf4   ecommerce-network   bridge    local
f7f387a9c85c   host                host      local
e407b9406d0c   none                null      local

# network 상세확인
PS C:\Users\User> docker network inspect ecommerce-network
[
    {
        "Name": "ecommerce-network",
        "Id": "0cac82d7dbf46d9a5267e875f38161da66bad40478b515ffb0dbbc872292c42f",
        "Created": "2021-05-15T02:42:27.8573309Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
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
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]


# Docker 실행
docker run -d -p 8761:8761 --network ecommerce-network \
 -e "spring.cloud.config.uri=http://config-service:8888" \
 --name discovery-service edowon0623/discovery-service:1.0


 # discovery Docker 실행
docker run -d -p 8761:8761 
 -e "spring.cloud.config.uri=http://100.51.0.209:8888"  \
 -e "spring.profiles.active=peer1"  \
 --name discovery-service seaking7/discovery-service:1.0


 docker build -t seaking7/discovery-service:1.0 .
docker push seaking7/discovery-service:1.0
docker pull seaking7/discovery-service:1.0

docker run -d -p 8761:8761 -e "spring.cloud.config.uri=http://100.51.0.209:8888" -e "spring.profiles.active=peer1"  --name discovery seaking7/discovery-service:1.0

docker run -f discovery

# apiGateway Docker 실행
docker build -t seaking7/gateway-service:1.0 .
docker push seaking7/gateway-service:1.0
docker pull seaking7/gateway-service:1.0

docker run -d -p 8000:8000 -e "spring.cloud.config.uri=http://100.51.0.209:8888" -e "spring.profiles.active=prod"  --name apigateway seaking7/gateway-service:1.0

docker run -f discovery


# content Docker 실행
docker build -t seaking7/content-service:1.0 .
docker push seaking7/content-service:1.0
docker pull seaking7/content-service:1.0


docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod" \
        -e "eureka.instance.hostname=$HOSTIP" \
        -e "logging.file=/home/ubuntu/log/content1.log" \
        --name content \
        -v /home/ubuntu/log:/home/ubuntu/log \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=contentLog \
        --log-opt awslogs-stream=content1 \
        seaking7/content-service:1.0


# execute Docker 실행
docker build -t seaking7/execute-service:1.0 .
docker push seaking7/execute-service:1.0
docker pull seaking7/execute-service:1.0

docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name execute \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=executeLog \
        --log-opt awslogs-stream=execute1 \
        seaking7/execute-service:1.0


# report Docker 실행
docker build -t seaking7/report-service:1.0 .
docker push seaking7/report-service:1.0
docker pull seaking7/report-service:1.0

docker run -d --net host \
        -e "spring.cloud.config.uri=http://100.51.0.209:8888" \
        -e "spring.profiles.active=prod" \
        -e "eureka.instance.hostname=$HOSTIP" \
        --name report \
        --log-driver=awslogs \
        --log-opt awslogs-region=ap-northeast-2 \
        --log-opt awslogs-group=reportLog \
        --log-opt awslogs-stream=report1 \
        seaking7/report-service:1.0
