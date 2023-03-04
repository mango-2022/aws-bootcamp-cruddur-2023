# Week 2 — Distributed Tracing

## HoneyComb
#### Use HoneyConb for backend
#### Set up env vars: `HONEYCOMB_API_KEY` and `HONEYCOMB_SERVICE_NAME`
```
export HONEYCOMB_API_KEY="****"
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY="****"
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
```

#### Update `requirements.txt`
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```

#### Acquiring a Tracer & Creating Spans
[OpenTelemetry Doc](https://docs.honeycomb.io/getting-data-in/opentelemetry/python/)

![image](https://user-images.githubusercontent.com/93061568/222860518-8c54ce69-8115-4de1-8312-84ba75267450.png)

## X-Ray

![image](https://user-images.githubusercontent.com/93061568/222867341-664b909e-bb48-441a-89c1-cec790f7b8f8.png)

#### Instrument AWS X-Ray for Flask
```
export AWS_REGION="ca-central-1"
gp env AWS_REGION="ca-central-1"
```
![image](https://user-images.githubusercontent.com/93061568/222870199-b7f5a88e-e7c3-40b4-83b7-76e30a6ae67c.png)

![image](https://user-images.githubusercontent.com/93061568/222870320-64cb9844-6709-40ce-85fd-da0a3cacfc22.png)

![image](https://user-images.githubusercontent.com/93061568/222870447-c699e322-4cf4-43e5-903a-a3bc416ba4c6.png)

![image](https://user-images.githubusercontent.com/93061568/222873353-2ac4a3a7-5c9d-49ee-b9ef-0d38c80332b4.png)

![image](https://user-images.githubusercontent.com/93061568/222873347-93766ba1-2e16-4dac-b20d-7d196e29a24b.png)

#### Start a custom segment/subsegment
![image](https://user-images.githubusercontent.com/93061568/222875924-5f045e58-c965-4da3-989a-b32b9089e545.png)

## CloudWatch Logs
#### Update `requirements.txt`
```
watchtower
```
![image](https://user-images.githubusercontent.com/93061568/222917551-f76bdc87-1dec-44f8-922c-7bf449169a9f.png)

## Rollbar
#### Update `requirements.txt`
```
blinker
rollbar
```
![image](https://user-images.githubusercontent.com/93061568/222919129-4d494b73-09dd-4229-8993-1acce357c5e2.png)

I can access the rollbar test only if I hardcode the ROLLBAR_ACCESS_TOKEN
![image](https://user-images.githubusercontent.com/93061568/222920386-4270a31b-fc11-407d-bc2e-3f3d1f8fd201.png)

![image](https://user-images.githubusercontent.com/93061568/222920368-cadc72bb-ee3e-4b19-933c-406e2866aa26.png)



