### Using Variables to Log In and Push Images to Docker Hub in GitLab CI/CD

In this lecture, we will complete our pipeline by logging into Docker Hub using GitLab CI/CD variables, tagging a Docker image, and pushing it to a Docker Hub repository.

#### Step 1: Add Variables to the YAML File

In the previous lecture, we stored our **Docker Hub username** and **password** securely as variables in GitLab’s project settings (`DOCKER_USERNAME` and `DOCKER_PASSWORD`). Now, let’s use those variables in the pipeline to log in to Docker Hub.

Here’s how the `.gitlab-ci.yml` file looks so far:

```yaml
push_image:
  script:
    # Log in to Docker Hub using variables
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
```

**Explanation**:
- **`docker login`**:
  - The `-u` flag specifies the username.
  - The password is securely piped using `--password-stdin` to avoid exposing it directly in the command.
- **GitLab Variables**:
  - `$DOCKER_USERNAME` and `$DOCKER_PASSWORD` are the secure variables we configured earlier.

When this script runs, it will log you into Docker Hub securely.

#### Step 2: Tag the Docker Image

Docker requires images to follow a specific naming convention when pushing them to Docker Hub. Unofficial or local images must be retagged with the repository name and tag format.

The syntax for tagging is:
```bash
docker tag <existing-image-name>:<tag> <docker-username>/<repository-name>:<tag>
```

Here’s how we add this step to the `.gitlab-ci.yml` file:

```yaml
push_image:
  script:
    # Log in to Docker Hub
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

    # Tag the Docker image
    - docker tag my-app:latest $DOCKER_USERNAME/my-app:latest
```

**Explanation**:
- **`docker tag`**:
  - `my-app:latest`: The name of the existing local image.
  - `$DOCKER_USERNAME/my-app:latest`: The new name for the image, including your Docker Hub username and repository name.
- If the tag (`:latest`) is not specified, Docker defaults to `latest`.

#### Step 3: Push the Image to Docker Hub

After tagging, the image is ready to be pushed to Docker Hub using the `docker push` command:

```yaml
push_image:
  script:
    # Log in to Docker Hub
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

    # Tag the Docker image
    - docker tag my-app:latest $DOCKER_USERNAME/my-app:latest

    # Push the image to Docker Hub
    - docker push $DOCKER_USERNAME/my-app:latest
```

**Explanation**:
- **`docker push`**:
  - Uploads the tagged image (`$DOCKER_USERNAME/my-app:latest`) to your Docker Hub repository.

#### Step 4: Ensure Prerequisites Are Met

Before committing and running the pipeline, check the following:
1. **GitLab Runner**:
   - Ensure that your local GitLab Runner is active and ready to execute jobs.
   - Use a command like `gitlab-runner status` to verify.
2. **Docker**:
   - Ensure that Docker is running on your system. Use a command like `docker info` to check.

#### Step 5: Commit and Execute the Pipeline

Now that everything is set, commit your changes to the `.gitlab-ci.yml` file and push them to the repository. This will trigger the pipeline.

Steps to verify:
1. Navigate to **CI/CD > Pipelines** in GitLab.
2. Check the pipeline logs:
   - The first step (`docker login`) should show a successful login.
   - The second step (`docker push`) should indicate that the image was successfully pushed to Docker Hub.

#### Step 6: Confirm on Docker Hub

Once the pipeline passes:
1. Log in to your **Docker Hub account**.
2. Navigate to your **Repositories** section.
3. Refresh the page to see your newly pushed image.

You should see:
- Repository: `my-app`
- Tag: `latest`
- Additional metadata, such as the image size and upload timestamp.

#### Summary

This pipeline demonstrates how to:
- Securely use GitLab CI/CD variables to handle sensitive information like Docker Hub credentials.
- Log in to Docker Hub.
- Tag and push a Docker image to a public repository.

By following this approach, you ensure that sensitive credentials are not exposed in your pipeline configuration or logs, adhering to best security practices.
