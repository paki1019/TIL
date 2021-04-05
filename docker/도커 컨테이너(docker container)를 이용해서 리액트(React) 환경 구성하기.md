# 도커 컨테이너(docker container)를 이용해서 리액트(React) 환경 구성하기

Docker-compose.yml과 Dockerfile를 통해 도커 환경을 구성

## Dockerfile
```
# base image 
FROM node:12.2.0-alpine 

# set working directory 
WORKDIR /app 

# add `/app/node_modules/.bin` to $PATH 
ENV PATH /app/node_modules/.bin:$PATH 

# install and cache app dependencies 
COPY package.json /app/package.json 
RUN npm install --silent 
RUN npm install react-scripts@3.0.1 -g --silent 

# start app 
CMD ["npm", "start"]
```

## Docker-Compose.yml
```
version: '3.7' 

services: 
    movie_app: container_name: movie_app 
    build: 
        context: . 
        dockerfile: Dockerfile 
    volumes:
        - '.:/app' 
        - '/app/node_modules' ports: 
        - '3001:3000' 
    environment:
        - NODE_ENV=development 
        - CHOKIDAR_USEPOLLING=true
```

docker-compose up 명령어를 수행하여 로컬 도커 환경에서 리액트 데몬을 구동