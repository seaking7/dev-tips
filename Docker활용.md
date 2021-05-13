
# Docker 파일 다운
PS C:\Users\User> docker pull mysql:5.7

# Docker 실행(mysql)
PS C:\Users\User> docker run -d -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql:5.7
86ec4436387d5fd895c9ecc08caf34882f75891fc6006b8e89b01c5e2bfcf2ca

# Docker 로그 확인
PS C:\Users\User> docker logs 86ec4436387d
2021-05-13 12:39:36+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 5.7.34-1debian10 started.

# Docker 내부 접속
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

