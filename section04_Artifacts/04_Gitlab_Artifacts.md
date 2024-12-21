### Detailed Explanation of Artifacts in GitLab CI/CD Pipelines (Docker and Docker-Compose Environment)

#### Introduction
Artifacts in GitLab CI/CD are the outputs or by-products produced by a job during its execution. These artifacts can be source code, dependencies, binaries, or other resources required by subsequent jobs in the pipeline. By default, GitLab’s jobs are independent and run in isolated environments, which means they do not share data. Artifacts allow jobs to share necessary data by storing outputs of one job and making them available to subsequent jobs.

This guide explains how to use artifacts to manage interdependent jobs in a GitLab CI/CD pipeline for a Node.js application using Docker and Docker-Compose.

---

### Understanding Default Behavior in GitLab Jobs

1. **Independent Job Execution**:
   - Each job in a pipeline runs in its own isolated instance.
   - Jobs do not share data or environment states by default.
   - At the end of each job, the environment is cleaned up, and all downloaded files or installed packages are destroyed.

2. **Problem Scenario**:
   - In a pipeline with a `build` job and a `test` job:
     - The `build` job successfully installs dependencies (e.g., the `express` framework).
     - However, when the `test` job runs, it fails because the dependencies installed in the `build` job are no longer available.

---

### Solution: Using Artifacts to Share Data Between Jobs

Artifacts solve the problem by storing outputs (e.g., dependencies or files) from one job and making them available to subsequent jobs in the pipeline. GitLab manages the storage and retrieval of artifacts automatically once configured in the pipeline.

#### Steps to Configure Artifacts

1. **Specify Artifacts in the Job**:
   - Add the `artifacts` keyword under the job that produces the data (e.g., the `build` job).
   - Use the `paths` keyword to define the files or directories to store as artifacts.

2. **Example Configuration**:
```yaml
stages:
  - build
  - test
  - cleanup

build:
  stage: build
  script:
    - docker-compose build
    - docker-compose run app npm install
  artifacts:
    paths:
      - node_modules/
      - package-lock.json

test:
  stage: test
  script:
    - docker-compose up -d
    - docker-compose exec app npm run test
  dependencies:
    - build

cleanup:
  stage: cleanup
  script:
    - docker-compose down
  when: always
```

#### Explanation:
- **Build Job**:
  - Uses Docker-Compose to build the application and run `npm install` to install dependencies.
  - Specifies `node_modules/` and `package-lock.json` as artifacts to be shared with subsequent jobs.

- **Test Job**:
  - Starts the application using Docker-Compose and runs tests.
  - Automatically downloads artifacts (dependencies) from the `build` job to ensure the environment is consistent.

- **Cleanup Job**:
  - Ensures all Docker containers are stopped and removed, keeping the environment clean.

---

### Optional: Setting Expiry for Artifacts

1. **Expire Artifacts After a Specific Duration**:
   - Use the `expire_in` keyword to define how long artifacts are stored before they are deleted.
   - If not specified, the default expiration is **30 days**.

2. **Example with Expiry**:
```yaml
build:
  stage: build
  script:
    - docker-compose run app npm install
  artifacts:
    paths:
      - node_modules/
      - package-lock.json
    expire_in: 1 week
```

- **Expire Artifacts in 1 Week**:
  - The `node_modules/` directory and `package-lock.json` will remain available for one week after being uploaded.

---

### Working with Artifacts

1. **Viewing Artifacts**:
   - Navigate to the job’s terminal output in the GitLab pipeline.
   - GitLab provides three options for interacting with artifacts:
     - **Keep**: Retain artifacts beyond the expiration date.
     - **Download**: Download artifacts to your local system.
     - **Browse**: View the contents of artifacts directly in the browser.

2. **Example Output**:
   - Artifacts such as `node_modules/` and `package-lock.json` are displayed with options to download or browse their contents.

---

### Key Points to Remember

1. **Artifacts Are Job Outputs**:
   - Artifacts represent data produced by a job that can be shared with other jobs in the pipeline.

2. **Automatic Download**:
   - Subsequent jobs automatically download artifacts from previous stages if configured correctly.

3. **Path Specification**:
   - All paths specified in `artifacts` are relative to the repository cloned during the job.

4. **Default Expiry**:
   - Artifacts expire after 30 days unless an `expire_in` duration is explicitly defined.

5. **Interdependent Jobs**:
   - Artifacts enable jobs in later stages to depend on the outputs of earlier jobs, making pipelines cohesive and efficient.

---

### Final Example: Complete Pipeline with Artifacts and Docker-Compose
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
    - docker-compose run app npm install
  artifacts:
    paths:
      - node_modules/
      - package-lock.json

test:
  stage: test
  script:
    - docker-compose up -d
    - docker-compose exec app npm run test
  dependencies:
    - build

cleanup:
  stage: cleanup
  script:
    - docker-compose down
  when: always
```

### Pipeline Behavior:
1. The `build` job installs dependencies inside a Docker container and stores them as artifacts (`node_modules/` and `package-lock.json`).
2. These artifacts are uploaded to the GitLab server after the `build` job completes.
3. The `test` job downloads the artifacts, ensuring that dependencies are available when running tests.
4. The `cleanup` job ensures that all containers and resources are cleaned up after the pipeline execution.

---

With this approach, you can effectively manage interdependent jobs in a Dockerized environment, leveraging artifacts for seamless CI/CD workflows.

