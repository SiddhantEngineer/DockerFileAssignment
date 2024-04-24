# DockerFileAssignment

## Information

dockerfile for frontend is located in /frontend
dockerfile for backend is located in /backend

## frontend

### Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

ENV PORT=5173

EXPOSE 5173

CMD ["npm", "run", "dev"]
```

### Breakdown of the Dockerfile

---

```dockerfile
FROM node:18-alpine
```

This line specifies the base image for the Docker container. In this case, we're using the Node.js 18 image based on Alpine Linux, which is a lightweight Linux distribution. This image provides a minimal Node.js runtime environment, suitable for running Node.js applications efficiently.

---

```dockerfile
WORKDIR /app
```

This command sets the working directory inside the Docker container to /app. Subsequent commands will be executed relative to this directory.

---

```dockerfile
COPY package*.json ./
```

This line copies the package.json and package-lock.json files from the host machine to the /app directory inside the container. These files contain information about the Node.js application's dependencies.

---

```dockerfile
RUN npm install
```

This command runs npm install inside the container, which installs the dependencies listed in the package.json file. It ensures that all required Node.js modules are installed in the container environment.

---

```dockerfile
COPY . .
```

This command copies the rest of the application code (excluding package.json and package-lock.json) from the host machine to the /app directory inside the container. This includes all source code, configuration files, and static assets necessary for running the application.

---

```dockerfile
ENV PORT=5173
```

This line sets the environment variable PORT to 5173 inside the container. This variable specifies the port on which the Node.js application will listen for incoming HTTP requests.

---

```dockerfile
EXPOSE 5173
```

This command exposes port 5173 from the Docker container to allow external access. It informs Docker that the Node.js application running inside the container will be listening on port 5173 for incoming connections.

---

```dockerfile
CMD ["npm", "run", "dev"]
```

Finally, this command specifies the default command to be executed when the Docker container starts. In this case, it runs the npm run dev script, which typically starts a development server for the Node.js application.

---

In summary, this Dockerfile sets up a Docker container with Node.js environment, installs dependencies, copies the application code, sets environment variables, exposes ports, and specifies the default command to run the Node.js application.

## backend

### Dockerfile:

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

ENV PORT=5000

EXPOSE 5000

CMD ["npm", "start"]
```

As you can see from the code itself, most of the commands are same as frontend tier.
The only things being changed are port numbers and CMD for starting the app.

## compose

### Dockerfile

```yaml
services:
  mongodb:
    container_name: mongo
    image: mongo:latest
    ports:
      - "5707:27017"

  backend:
    container_name: 21bcp270backendcontainer
    image: 21bcp270backendimage
    build: ./backend
    ports:
      - "5001:5000"
    depends_on:
      - mongodb

  frontend:
    container_name: 21bcp270frontendcontainer
    image: 21bcp270frontendimage
    build: ./frontend
    env_file:
      - ./frontend/.env
    ports:
      - "5173:5173"
    depends_on:
      - backend
```

### Code Breakdown

#### MongoDB Service

```yaml
services:
  mongodb:
    container_name: mongo
    image: mongo:latest
    ports:
      - "5707:27017"
```

container_name: Specifies the name for the MongoDB container as mongo.

image: Specifies the Docker image for MongoDB, using the latest version tagged as mongo:latest.

ports: Maps port 5707 on the host machine to port 27017 on the MongoDB container, allowing external access to MongoDB.

#### Backend Service

```yaml
backend:
  container_name: 21bcp270backendcontainer
  image: 21bcp270backendimage
  build: ./backend
  ports:
    - "5001:5000"
  depends_on:
    - mongodb
```

container_name: Specifies the name for the backend container as 21bcp270backendcontainer.

image: Specifies the name for the Docker image to be built for the backend service, which will be tagged as 21bcp270backendimage.

build: Specifies the path to the Dockerfile for building the backend image, which is located in the ./backend directory.

ports: Maps port 5001 on the host machine to port 5000 on the backend container, allowing external access to the backend service.

depends_on: Defines a dependency on the mongodb service, ensuring that the backend service starts only after the MongoDB service is up and running.

#### Frontend Service

```yaml
frontend:
  container_name: 21bcp270frontendcontainer
  image: 21bcp270frontendimage
  build: ./frontend
  env_file:
    - ./frontend/.env
  ports:
    - "5173:5173"
  depends_on:
    - backend
```

container_name: Specifies the name for the frontend container as 21bcp270frontendcontainer.

image: Specifies the name for the Docker image to be built for the frontend service, which will be tagged as 21bcp270frontendimage.

build: Specifies the path to the Dockerfile for building the frontend image, which is located in the ./frontend directory.

env_file: Specifies the path to an environment file (./frontend/.env) containing environment variables for the frontend service.

ports: Maps port 5173 on the host machine to port 5173 on the frontend container, allowing external access to the frontend service.

depends_on: Defines a dependency on the backend service, ensuring that the frontend service starts only after the backend service is up and running.
