# Week 1 — App Containerization

This week, I set up docker container to app.

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
