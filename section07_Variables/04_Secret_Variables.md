### Using Secret Variables in GitLab CI/CD to Push Docker Images to Docker Hub

In this lecture, we will learn how to securely use **GitLab CI/CD variables** to push a Docker image to Docker Hub.  

#### Prerequisite: Docker Image  
We’ll use the Docker image created during the **Runners lecture**. If you recall, we built a Docker image from a `Dockerfile` and stored it locally. Now, we’ll push this image to **Docker Hub** to make it publicly available.

#### Step 1: Set Up Docker Hub Account
To push an image to Docker Hub, you need a Docker Hub account. If you don’t have one:
1. Visit [Docker Hub](https://hub.docker.com/).
2. Sign up by filling in your details.  
   The process is straightforward and quick.

If you already have an account, log in. Check your **Repositories** section—you’ll see no repositories initially if your account is new, but we’ll create one as part of this lecture.

#### Step 2: Define the Pipeline Structure
Let’s create a GitLab CI/CD pipeline to:
1. Log in to Docker Hub.
2. Push the Docker image.  

We’ll start by creating a new job in the `.gitlab-ci.yml` file:

```yaml
push_image:
  script:
    # Placeholder for Docker login and push commands
    - echo "Placeholder for Docker commands"
```

#### Step 3: Secure Credentials with Variables
To log in to Docker Hub, you’ll need your **Docker username** and **password**. These credentials must be handled securely to avoid exposing sensitive information in your pipeline.  

GitLab provides **project-level variables** for securely storing such sensitive data. Let’s configure these variables:

1. Navigate to your **project settings** in GitLab.
   - Go to **Settings > CI/CD > Variables**.
   - Expand the **Variables** section.

2. **Add the Docker Username Variable**:
   - Click on **Add variable**.
   - Enter a **key** (e.g., `DOCKER_USERNAME`) and your Docker Hub username as the **value**.
   - Select the variable **type**:
     - **Variable**: A standard key-value pair. Suitable for most use cases, including storing usernames.
     - **File**: Stores sensitive data in a file format (used for tools like `kubectl` or AWS CLI). Not required for this use case.
   - Optional settings:
     - **Environment scope**: Leave this as `*` for now to make the variable available in all pipelines. We’ll discuss scoped environments later.
     - **Protect variable**: Check this box to restrict the variable to **protected branches** (e.g., `main` or `master`).
     - **Mask variable**: Enable masking to hide the variable's value in logs. Note: Masking requires the value to be at least 8 characters long.
   - Save the variable.

3. **Add the Docker Password Variable**:
   - Repeat the process above to create another variable (e.g., `DOCKER_PASSWORD`) for your Docker Hub password.  

At this point, the variables `DOCKER_USERNAME` and `DOCKER_PASSWORD` are securely stored and ready for use.

#### Step 4: Update the Pipeline Script
Now that the credentials are securely stored, update the pipeline job to log in to Docker Hub and push the image:

```yaml
push_image:
  script:
    # Log in to Docker Hub using credentials from GitLab variables
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

    # Push the image to Docker Hub
    - docker tag my-app-image:latest $DOCKER_USERNAME/my-app-image:latest
    - docker push $DOCKER_USERNAME/my-app-image:latest
```

**Explanation**:
- **`docker login`**: Logs in to Docker Hub using the credentials stored in `DOCKER_USERNAME` and `DOCKER_PASSWORD`.
- **`docker tag`**: Tags the local image (`my-app-image:latest`) with your Docker Hub repository name.
- **`docker push`**: Pushes the tagged image to Docker Hub.

#### Step 5: Variable Security Features
Let’s review the security features provided by GitLab for variables:

1. **Protect Variable**:
   - Ensures the variable is only available in pipelines running on protected branches (e.g., `main` or `master`).
   - For example, if you check this option and run a pipeline on a feature branch, the variable won’t be accessible.

2. **Mask Variable**:
   - Masks the variable value in job logs. For example:
     ```bash
     echo "$DOCKER_PASSWORD"
     ```
     Without masking, the password would appear in plain text in the logs. Masking prevents this, ensuring the value remains secure.

3. **File Type Variables**:
   - These variables are used when tools require input in the form of a file (e.g., configuration files for `kubectl` or AWS CLI).  
   - For this use case, we don’t need file type variables, but they are useful in scenarios where sensitive data needs to be passed as a file.

#### Step 6: Commit and Run the Pipeline
1. Commit the `.gitlab-ci.yml` file.
2. Push the changes to the repository.
3. Navigate to **CI/CD > Pipelines** in GitLab.
4. Trigger the pipeline and monitor the logs.  

The pipeline should:
- Log in to Docker Hub.
- Push the image to your Docker Hub repository.

#### What’s Next?

We’ve successfully stored sensitive credentials as GitLab CI/CD variables and used them to log in to Docker Hub securely. In the next lecture, we’ll continue writing the `.gitlab-ci.yml` file and explore further optimizations for securely managing pipelines.
