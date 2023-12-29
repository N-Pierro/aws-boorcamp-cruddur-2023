# Week 2 — Distributed Tracing

## Container Service 

### Run python command 

```sh  
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python -m flask run --host=0.0.0.0 --port=4567
cd ..
```

- make sure to unlock the lock in the port tree
- copy the url to browser
- append to the url /api/activities/home
- you should now see the backend json file 


### ADD DOCKERFILE
```Dockerfile
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt

RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development 
EXPOSE ${PORT}
CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]
```
### BUILD CONTAINER 

```
docker build -t backend-flask ./backend-flask
```

### RUN CONRAINER 
run

```sh
# -e flag is used to pass enviromental variable 
docker run --rm -p 4567:4567 -it -e BACKEND_URL='*' -e FRONTEND_URL='*'  backend-flask
```
run in background mode 

```sh
docker run --rm -p 4567:4567 -d -e BACKEND_URL='*' -e FRONTEND_URL='*'  backend-flask
```
### Get the container images or running container ids
```sh
docker ps
docker images
```
### Send Curl to Test Server 
```
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"
```
