# Building a 3-Stages CI/CD Pipeline for a Node.js Server Using Docker and Docker Compose

This project demonstrates how to build a three-stage CI/CD pipeline for a Node.js application using Docker, Docker Compose, and GitLab CI/CD. The pipeline consists of three stages: **Build**, **Test**, and **Cleanup**.

---

## **Understanding the Components**
To fully understand how this project works, let’s break down the components and their roles in building, running, and testing a Node.js server.

### **1. Node.js Application**
The core of this project is a simple Node.js application using the Express framework. 

#### **Key Components:**
- **`index.js`:** Contains the server logic.
  - It sets up an Express server that listens on port `8080`.
  - Responds to GET requests at the root (`/`) with a success message.

```javascript
const express = require('express');
const app = express();
const port = 8080;

app.get('/', (req, res) => {
    res.send('Success! Your Node.js app is running.');
});

app.listen(port, () => {
    console.log(`App listening at http://localhost:${port}`);
});
```

- **`package.json`:** Defines project metadata, dependencies, and scripts.
  - **Dependencies:** Lists required packages (e.g., `express` for the web server).
  - **Scripts:** Includes commands for starting (`start`) and testing (`test`) the application.

```json
{
  "name": "artifacts_demo",
  "version": "1.0.0",
  "description": "A simple Node.js application for demonstrating GitLab artifacts.",
  "main": "index.js",
  "dependencies": {
    "express": "^4.17.1"
  },
  "scripts": {
    "start": "node index.js",
    "test": "echo 'No tests implemented yet'"
  }
}
```

### **2. Docker: Containerizing the Application**
Docker allows us to package the application and its dependencies into a container, ensuring consistent behavior across environments.

#### **How the Dockerfile Works:**
- **Base Image:** Uses `node:16`, an official Node.js image with the required runtime environment.
- **Working Directory:** Sets `/usr/src/app` as the directory inside the container where files will be stored.
- **Copy Files:**
  - Copies `package.json` to install dependencies first (to leverage Docker's caching).
  - Copies the rest of the application files.
- **Install Dependencies:** Runs `npm install` to install the dependencies listed in `package.json`.
- **Expose Port:** Makes port `8080` accessible from outside the container.
- **Command:** Specifies `npm start` to run the server when the container starts.

```Dockerfile
# Use an official Node.js runtime as a parent image
FROM node:16

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json (if available)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Expose the port your app runs on
EXPOSE 8080

# Start the application
CMD ["npm", "start"]
```

### **3. Docker Compose: Simplifying Multi-Container Management**
Docker Compose allows you to define and run multi-container Docker applications with a single command.

#### **Key Configuration in `docker-compose.yml`:**
- **Service Definition:**
  - Defines a service named `app` that represents the Node.js application.
  - Specifies `build: .`, which uses the Dockerfile in the current directory to build the image.
- **Port Mapping:**
  - Maps port `8080` inside the container to port `8081` on the host.
- **Volumes:**
  - Mounts the project directory on the host to `/usr/src/app` in the container, allowing live updates to the code.
  - Ensures `node_modules` inside the container are preserved.

```yaml
services:
  app:
    build: .
    ports:
      - "8081:8080"
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
```

### **4. GitLab CI/CD: Automating the Workflow**
GitLab CI/CD automates the process of building, testing, and cleaning up the Node.js application. The configuration is defined in `.gitlab-ci.yml`.

#### **Key Sections of `.gitlab-ci.yml`:**
- **Base Image:**
  - Uses `docker:latest` to provide the Docker CLI for building and running containers.
- **Docker-in-Docker Service:**
  - `docker:dind` runs a Docker daemon to support Docker commands inside the pipeline.
- **Stages:**
  - **Build:** Builds the Docker image using `docker-compose build`.
  - **Test:** Starts the application container and runs the `npm run test` command inside it.
  - **Cleanup:** Stops and removes containers and networks with `docker-compose down`.
- **Environment Variables:**
  - `DOCKER_DRIVER: overlay2` optimizes Docker’s layer management.

```yaml
image: docker:latest

services:
  - docker:dind

variables:
  DOCKER_DRIVER: overlay2

stages:
  - build
  - test
  - cleanup

build:
  stage: build
  script:
    - docker-compose build

test:
  stage: test
  script:
    - docker-compose up -d
    - docker-compose exec app npm run test

cleanup:
  stage: cleanup
  script:
    - docker-compose down
  when: always
```

### **Pipeline Flow:**
1. **Build Stage:**
   - Builds the Docker image from the Dockerfile using `docker-compose build`.
   - Verifies that the application can be containerized successfully.

2. **Test Stage:**
   - Starts the container using `docker-compose up -d`.
   - Executes the `npm run test` command to validate the application (currently, it outputs a placeholder message).

3. **Cleanup Stage:**
   - Stops and removes all running containers and associated networks to ensure no residual resources are left behind.

### **How Components Work Together**
1. **Codebase:** The `index.js` and `package.json` define the Node.js application and its dependencies.
2. **Dockerfile:** Containerizes the application for consistent behavior across environments.
3. **Docker Compose:** Simplifies multi-container setup, port mapping, and live code synchronization.
4. **GitLab CI/CD:** Automates the build-test-cleanup workflow, ensuring every change is validated in a controlled environment.

---

## **Why This Setup Matters**
1. **Automation:** Eliminates manual steps for building and testing the application.
2. **Consistency:** Ensures the same environment is used across development, testing, and production.
3. **Extensibility:** Provides a foundation for adding real tests, deployment stages, or additional services (e.g., databases).
4. **Resource Management:** Prevents resource leakage by cleaning up containers and networks after each pipeline run.

---

This project demonstrates how to integrate Dockerized applications into a CI/CD pipeline using GitLab, providing a streamlined process for building, testing, and maintaining a Node.js server.

