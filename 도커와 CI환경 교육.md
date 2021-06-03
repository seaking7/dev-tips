
# Dockerfile
FROM node:10

#COPY되는 파일들 별도 보관
WORKDIR C:\workspace\nodejs-docker

COPY package.json ./
RUN npm install
#현재 경로 전체카피
COPY ./ ./

CMD ["node", "server.js"]


# package.json 작성
{
  "name": "nodejs-docker",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies":{
    "express":"4.17.1"
  },
  "author": "",
  "license": "ISC"
}

# server.js 작성
const express = require('express');

const PORT = 8080;

//APP
const app = express();
app.get('/', (req, res) => {
    res.send("Hello World 안녕하세요")    
});

app.listen(PORT);
console.log("Server is running test")



# 빌드 및 실행
docker build -t seaking7/nodejs .

docker run -d -p 5000:8080 -v C:\workspace\nodejs-docker\node_modules -v C:\workspace\nodejs-docker seaking7/nodejs

docker run -d -p 5000:8080 -v C:\workspace\nodejs-docker\node_modules -v C:\workspace\nodejs-docker seaking7/nodejs

# 도커 정리
docker container prune
docker image prune
docker network prune
docker volumn prune

docker system prune -a   // 전체 정리


# docker-compose.yml 작성(샘플1)
version: "3"
services:
    redis-server:
        image: "redis"
    node-app:
        build: .
        ports:
            - "5000:8080"

# docker-compose.yml 작성(샘플2)            
version: "3"
services:
    react:
        build:
            context: .
            dockerfile: Dockerfile.dev
        ports:
            - "3000:3000"
        stdin_open: true
    tests:
        build:
            context: .
            dockerfile: Dockerfile.dev
        command: ["npm", "run", "test"]


# docker-compose 실행
docker-compose up
docker-compose up --build
docker-compose down


# docker-react-app 을 github에 등록
소스있는 위치에서
   1 git init
   2 git add .
   3 git commit -m "init commit"
   5 git remote add origin https://github.com/seaking7/docker-react-app.git
   6 git remote -v
   7 git push origin master