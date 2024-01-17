# Week 4 — Postgres and RDS
We are going to us postgres and DynamoDB local, in future labs we can bring them in as containers and refrence them externally
Let's intergrate them into our docker compose file:

## Postgres
```yml
services:
  db:
    image: postgres:13-alpine
    restart: always
    enviroment:
      -  POSTGRES_USER=postgres
      -  POSTGRES_PASSWORD=password
    ports:
      -  '5432:5432'
    volumes:
      -  db:/var/lib/postgresql/data
  volumes:
    db:
      driver: local
```

## DynamoDB Local
```yml
services:
  dynamodb-local:
    # we need to add user:root to get this working
    user:root
    command: " -jar DynamoDBLocal.jar -sharedDB -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      -  "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

## Volumes 
directory volume mapping 
```yml
volumes:
  -  "./docker/dynamodb:/home/dynamodylocal/data"
```
named volume mapping 




# Distributed Tracing 

A third-pary tool that serve this purpose is honeycomb. 
- Install create a honeycomb account
- Log into honeycomb
- create a new enviroment [screenshot, creating a new env in honeycomb](journey/screenshots.md)
- open the new enviroment and copy the api key
- in gitpod run the following code to export and the api key to gitpod env
  
```sh
export HONEYCOMB_API_KEY="CArsB0ZoEIeets9QJcR8dM"
gp env HONEYCOMB_API_KEY="CArsB0ZoEIeets9QJcR8dM"
```
