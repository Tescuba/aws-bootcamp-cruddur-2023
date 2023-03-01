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


