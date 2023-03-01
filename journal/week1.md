# Week 1 — App Containerization

## References

Good article for Debugging Connection Refused [https://pythonspeed.com/articles/docker-connection-refused/]

# VSCode Docker Extension

docker for VSCode makes it easy to work with Docker

[https://code.visualstudio.com/docs/containers/overview]

| Gitpod is preinstalled withs this extension

## Containerize Backend

## Run Python

```
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..

```
## Add Dockerfile

Create a file here: ``` bacend-flask/Dockerfile ```

```
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]

```
| docker container run is idiomatic, docker run is legacy syntax but is commonly used.

## Get Container Images or Running Container Ids

```
docker ps
docker images

```
## Send Curl to Test Server

```
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"

```

## Check Container Logs

```
docker logs CONTAINER_ID -f
docker logs backend-flask -f
docker logs $CONTAINER_ID -f

```

## Debugging adjacent containers with other containers

```
docker run --rm -it curlimages/curl "-X GET http://localhost:4567/api/activities/home -H \"Accept: application/json\" -H \"Content-Type: application/json\""

```

busybosy is often used for debugging since it install a bunch of thing

```
docker run --rm -it busybosy

```

## Gain Access to a Container

```
docker exec CONTAINER_ID -it /bin/bash

```

| You can just right click a container and see logs in VSCode with Docker extension

## Delete an Image

```
docker image rm backend-flask --force

```
| docker rmi backend-flask is the legacy syntax, you might see this is old docker tutorials and articles.

| There are some cases where you need to use the --force

## Containerize Frontend

## Run NPM Install

We have to run NPM Install before building the container since it needs to copy the contents of node_modules

```
cd frontend-react-js
npm i

```

## Create Docker File

Create a file here: ``` frontend-react-js/Dockerfile ```

```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]

```

## Build Container

```
docker build -t frontend-react-js ./frontend-react-js

```
## Run Container

```
docker run -p 3000:3000 -d frontend-react-js

```

## Multiple Containers

### Create a docker-compose file

Create ``` dlcker-compose.yml ``` at the root of your project

```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

```










