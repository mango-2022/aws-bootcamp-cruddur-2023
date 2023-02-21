# Week 1 — App Containerization

This week, I set up docker containers and add some front-end features to the app.

## Containerize Backend

### Add Dockerfile
Create a file "Dockerfile" in backend file:
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
![image](https://user-images.githubusercontent.com/93061568/220197983-386cafb1-f7d2-4b18-97f3-4efd80b8de0c.png)

### Run Flask
```
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```
![image](https://user-images.githubusercontent.com/93061568/220198494-6042aee9-7f95-4d1e-800d-8590627211d2.png)

Unlock the port on the port tab to access the json file:
![image](https://user-images.githubusercontent.com/93061568/220198440-80244259-7f1d-4744-b25d-4a974318430a.png)

### Build Container
![image](https://user-images.githubusercontent.com/93061568/220199374-f12a30b3-4d78-4be8-a7c9-4e6848da53f8.png)

Docker extension view:<br/>
![image](https://user-images.githubusercontent.com/93061568/220199670-ef466d60-f101-4dd0-89df-039a8624ac80.png)

### Run Container
![image](https://user-images.githubusercontent.com/93061568/220199857-d9ffaeb3-a361-414d-9e51-366007cd11c1.png)

Add env variables this time and check:
![image](https://user-images.githubusercontent.com/93061568/220200481-043f0783-4afe-4bbe-83b5-6c0740aefeb8.png)

![image](https://user-images.githubusercontent.com/93061568/220200805-4977e089-1e4c-4b76-b7d5-17f0e2a59a04.png)

### Get Container Images or Running Container Ids
![image](https://user-images.githubusercontent.com/93061568/220201177-16062178-cfa5-4b5b-9286-7b7989881834.png)

## Containerize Frontend

### Create Docker File
Create a file "Dockerfile" in frontend file:
```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

### Build Container
```
docker build -t frontend-react-js ./frontend-react-js
```

### Run Container
```
docker run -p 3000:3000 -d frontend-react-js
```

## Multiple Containers

### Create a docker-compose file
Create `docker-compose.yml` at the root folder.
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
Right click -> Compose up to build container:
![image](https://user-images.githubusercontent.com/93061568/220202705-0a4045a4-7bec-4020-9823-13e299307236.png)

Both frontend and backend portal are running:
![image](https://user-images.githubusercontent.com/93061568/220202750-cfc57615-a0f0-4c20-bc56-642b8be74c10.png)

## Adding DynamoDB Local and Postgres

### Postgres
```
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

To install the postgres client into Gitpod, edit `.gitpod.yml`
```
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

### DynamoDB Local
```
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```

### Test DynamoDB

#### Create a table
```
aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
```
![image](https://user-images.githubusercontent.com/93061568/220438297-f78e4e6c-8b14-4ac7-bc6a-f26ef3b4193d.png)

#### Create an Item
```
aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  
```
![image](https://user-images.githubusercontent.com/93061568/220438550-39a243f6-5b86-4fe3-a697-deef64b02418.png)

#### List Tables
```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```
![image](https://user-images.githubusercontent.com/93061568/220438682-60f3f980-75b7-4f10-b49c-99fbaf82bc7b.png)

#### Get Records
```
aws dynamodb scan --table-name Music --query "Items" --endpoint-url http://localhost:8000
```
![image](https://user-images.githubusercontent.com/93061568/220438843-a8a6367d-98c7-4f5e-817f-3c3ff3f33ba6.png)

## Front-End Notifications Features

### Test Home Page:
![image](https://user-images.githubusercontent.com/93061568/220429953-52ec19fc-16b2-428c-9d11-e57b86dcf0a6.png)

### Update notification endpoint & documentations
![image](https://user-images.githubusercontent.com/93061568/220431366-aeace44c-8d4f-4ec0-9bcf-9734f6819121.png)

### Test data retrieved from `/api/activities/notifications`
![image](https://user-images.githubusercontent.com/93061568/220433749-c1973766-bff9-4df1-9d22-23571623d102.png)

### Test Notifications Feed Page with the proper content
![image](https://user-images.githubusercontent.com/93061568/220435754-2630ae9b-47ae-43a9-86ca-e645408cea18.png)


