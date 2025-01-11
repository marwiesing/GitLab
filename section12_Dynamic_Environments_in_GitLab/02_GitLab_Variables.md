Here's a more detailed and enhanced version of the transcript, filling in gaps and clarifying outdated or incomplete information:

---

### Workaround for Variable Resolution Issue in GitLab CI/CD

To address the problem of unresolved variables in GitLab pipelines, we implement a **workaround**: instead of defining variables in the `variables` section, we export them in the `before_script` section. This ensures that the variables are resolved first and then made available to subsequent scripts.

#### Step-by-Step Process:

1. **Moving Variables to `before_script`**:
   - Cut the problematic variables, such as `FEATURE_APP`, from the `variables` section.
   - Paste them into the `before_script` section of the `deploy-feature` job, using the `export` command.

   Example:
   ```bash
   export FEATURE_APP="feature-${CI_ENVIRONMENT_SLUG}"
   ```

2. **Syntax Adjustment**:
   - Modify the syntax slightly to ensure proper resolution. For instance:
     ```bash
     export HEROKU_FEATURE="heroku-${FEATURE_APP}"
     ```

   By doing this, the variables are resolved during the job's execution, bypassing GitLab's variable resolution issue.

---

### Creating Dynamic Heroku Applications

Before deploying the feature branch's Docker image to Heroku, a new Heroku application must be created dynamically—similar to how static environments were manually created earlier but now automated within the pipeline.

#### Steps for Dynamic Heroku App Creation:

1. **Using the Heroku CLI Alpine Docker Image**:
   - The `alpine-heroku-cli` Docker image is used to run Heroku CLI commands directly from the pipeline. This eliminates the need to install Heroku CLI separately.
   - Run the Docker container with:
     ```bash
     docker run -it --rm heroku/heroku:alpine
     ```

2. **Heroku App Creation Command**:
   - Use the `heroku create` command to create a new app. The app's name will be dynamically generated using the branch name (`CI_COMMIT_REF_NAME`).
     ```bash
     heroku create "${FEATURE_APP}" --buildpack heroku/nodejs
     ```

3. **Authentication**:
   - The Heroku CLI within the Docker container will authenticate the user using an API key stored in GitLab CI/CD secrets.
     ```bash
     docker run -e HEROKU_API_KEY=$HEROKU_API_KEY heroku/heroku:alpine heroku create
     ```

4. **Verifying the App**:
   - After successful creation, the app will be visible in the Heroku dashboard, accessible via the dynamically assigned URL.

---

### Updating the Deployment Job

Once the Heroku app is created, the subsequent steps involve pushing the Docker image to Heroku and releasing it.

1. **Push Docker Image**:
   - Tag the image using Heroku's naming conventions:
     ```bash
     docker tag my-app:latest registry.heroku.com/${FEATURE_APP}/web
     ```

   - Push the image to Heroku's registry:
     ```bash
     docker push registry.heroku.com/${FEATURE_APP}/web
     ```

2. **Release the App**:
   - Release the image to the Heroku app:
     ```bash
     heroku container:release web -a ${FEATURE_APP}
     ```

---

### Restricting Job Execution to Feature Branches

The `deploy-feature` job should only execute for branches prefixed with `feature-`. To implement this, add a condition to the job using GitLab's `only` directive with a regular expression.

```yaml
only:
  - /^feature-.*/
```

This ensures that the job runs exclusively for feature branches, preventing it from executing for unrelated branches like `main` or `develop`.

---

### Creating Dynamic Environments in GitLab

To define a dynamic environment for each feature branch:

1. **Environment Name**:
   - Use the predefined variable `CI_COMMIT_REF_NAME` to name the environment dynamically:
     ```yaml
     environment:
       name: review/${CI_COMMIT_REF_NAME}
       url: https://${FEATURE_APP}.herokuapp.com
     ```

   - The prefix `review/` helps distinguish dynamic environments in the GitLab Environments list.

2. **Environment URL**:
   - To ensure the URL is valid, use `CI_ENVIRONMENT_SLUG` (a URL-safe version of the environment name):
     ```yaml
     url: https://${CI_ENVIRONMENT_SLUG}.herokuapp.com
     ```

---

### Passing Variables Between Jobs

Since variables like `FEATURE_APP` need to be accessed by other jobs (e.g., `test-feature`), GitLab artifacts are used to pass them between jobs.

1. **Save Variables to a File**:
   - Store the variable in a `.env` file during the `deploy-feature` job:
     ```bash
     echo "FEATURE_APP=${FEATURE_APP}" > deploy_feature.env
     ```

2. **Define Artifacts**:
   - Save the `.env` file as an artifact:
     ```yaml
     artifacts:
       paths:
         - deploy_feature.env
   ```

3. **Set Dependencies**:
   - Specify the dependency in subsequent jobs (e.g., `test-feature`) to fetch the artifact:
     ```yaml
     dependencies:
       - deploy-feature
     ```

4. **Load the Variable**:
   - Load the `.env` file in the next job:
     ```bash
     export $(cat deploy_feature.env | xargs)
     ```

---

### Securing Feature Branches

To ensure sensitive variables (e.g., API keys) are only accessible to protected branches:
1. Protect all `feature-*` branches using wildcard protection:
   ```yaml
   protect:
     - /^feature-.*/
   ```

---

### Next Steps: Testing the Pipeline

With the setup complete:
1. Save and commit the pipeline configuration changes.
2. Run the pipeline for a feature branch.
3. Verify that:
   - A dynamic environment is created.
   - The feature branch is deployed to Heroku successfully.
   - The artifacts are passed correctly between jobs.

In the next lecture, we will test the pipeline and troubleshoot any issues that arise during the deployment process.

--- 

This detailed explanation fills in missing technical details, adds clarity to the steps, and ensures the transcript aligns with current best practices.