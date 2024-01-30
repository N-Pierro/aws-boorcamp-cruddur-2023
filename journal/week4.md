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

## Honeycomb

When creating a new dateset in honeycomb it will provide all this  installation instructions 
Add the following lines to the requirements.txt file [requirements.txt file](../backend-flask/requirements.txt)

```txt
flask
flask-cors
opentelemetry-api
opentelemetry-sdk
opentelemetry-exporter-otlp-proto-http
opentelemetry-instrumentation-flask
opentelemetry-instrumentation-requests
```
Install this dependencies. 

navigate to the backend-flask directory 

run the command: 
```sh
pip install -r requirements.txt
```
add to the app.py

```py
# app.py updates 
from opentelemetry import trace 
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TraceProvider 
from opentelemetry.sdk.trace.export import BatchSpanProcessor


# Initialize tracing and an exporter that can send data to Honeycomb
provider = TraceProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

# Initialize automatic instrumention with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```


A third-pary tool that serve this purpose is honeycomb. 
- Install create a honeycomb account
- Log into honeycomb
- create a new enviroment [screenshot, creating a new env in honeycomb](./screenshot.md)
- open the new enviroment and copy the api key
- in gitpod run the following code to export and the api key to gitpod env

### Note:
for security purposes it's best practice for a multiple services running on the backend, to hard-code this in the docker-compose file for the different services.
  
```sh
export HONEYCOMB_API_KEY="CArsB0ZoEIeets9QJcR8dM"
gp env HONEYCOMB_API_KEY="CArsB0ZoEIeets9QJcR8dM"
```
## Add the following env to the docker compose file 
for the backend service 
```Dockerfile
ervices:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      OTEL_SERVICE_NAME: "backend-flask"                                              #added line
      OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"                         #added line
      OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=$(HONEYCOMB_API_KEY)"             #added line 
    build: ./backend-flask
    ports:
    -  "4567:4567"
    volumes:
    -  ./backend-flask:/backend-flask
```


# Instrument XRay

AWS-X-Ray provides API's for managining debug traces and retrieving sevices maps and 

other data created by processing those traces.
